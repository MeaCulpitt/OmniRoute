# OmniRoute: Validator Architecture & Verification

OmniRoute validators score miner responses using reproducible baselines and data verification. The goal: reward routes that are feasible, optimal, and based on current data.

---

## Validator Responsibilities

1. **Broadcast challenges** — Generate freight routing requests
2. **Run baseline optimization** — Compute reference routes for comparison
3. **Verify miner responses** — Check feasibility, optimality, data sources
4. **Set weights** — Score miners and commit to chain

---

## Verification Pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                  VALIDATOR PIPELINE                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. CHALLENGE       Generate and broadcast routing request       │
│                                                                  │
│  2. BASELINE        Run own optimizer on same request            │
│                     Store reference routes + frontier            │
│                                                                  │
│  3. COLLECT         Receive miner responses within deadline      │
│                                                                  │
│  4. VERIFY          For each response:                           │
│                     - Check feasibility (schedules exist)        │
│                     - Score optimality (vs baseline frontier)    │
│                     - Verify data freshness (spot-check sources) │
│                     - Check constraint satisfaction              │
│                     - Record latency                             │
│                                                                  │
│  5. SCORE           Compute weighted score per miner             │
│                                                                  │
│  6. COMMIT          Submit weights to chain                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Scoring Components

### 1. Feasibility (30%)

**Question:** Can this route actually be executed?

**Verification:**

```python
async def verify_feasibility(route):
    for leg in route.legs:
        if leg.mode == "sea":
            # Check vessel/voyage exists
            schedule = await marine_traffic.get_voyage(
                vessel=leg.vessel,
                voyage=leg.voyage
            )
            if not schedule:
                return 0.0  # Fabricated
            
            # Check dates match
            if abs(schedule.departure - leg.depart.date) > timedelta(hours=12):
                return 0.0  # Wrong schedule
        
        elif leg.mode == "rail":
            # Check train exists
            train = await rail_api.get_train(leg.train)
            if not train:
                return 0.0
        
        elif leg.mode == "air":
            # Check flight exists
            flight = await flight_api.get_flight(leg.flight)
            if not flight:
                return 0.0
        
        # Check connection times
        if not valid_connection(leg, next_leg):
            return 0.5  # Tight connection penalty
    
    return 1.0
```

**Key checks:**
- Vessel/flight/train exists in public schedules
- Departure dates are accurate
- Connection times are realistic (min 4 hours sea-to-truck, 2 hours air-to-truck)
- Ports are operational

### 2. Optimality (25%)

**Question:** How good are these routes compared to what's possible?

**Method:**

Validators run their own baseline optimizer on each challenge. This produces a Pareto frontier of efficient routes.

```python
def compute_baseline_frontier(challenge):
    # Run validator's own optimization
    data = gather_data(challenge.origin, challenge.destination)
    routes = optimizer.compute(data, challenge.constraints)
    
    # Build Pareto frontier (cost vs time vs reliability)
    frontier = []
    for route in routes:
        dominated = False
        for other in routes:
            if dominates(other, route):
                dominated = True
                break
        if not dominated:
            frontier.append(route)
    
    return frontier

def dominates(a, b):
    """Route a dominates b if better on all dimensions."""
    return (a.cost <= b.cost and 
            a.time <= b.time and 
            a.reliability >= b.reliability and
            (a.cost < b.cost or a.time < b.time or a.reliability > b.reliability))
```

**Scoring miner routes:**

```python
def score_optimality(miner_routes, baseline_frontier):
    scores = []
    
    for route in miner_routes:
        # Find closest point on frontier
        min_distance = float('inf')
        for frontier_route in baseline_frontier:
            distance = normalized_distance(route, frontier_route)
            min_distance = min(min_distance, distance)
        
        # On frontier = 1.0, far inside = 0.0
        score = max(0, 1.0 - min_distance)
        scores.append(score)
    
    return mean(scores)

def normalized_distance(route, frontier_point):
    """Distance in normalized cost-time-reliability space."""
    cost_diff = (route.cost - frontier_point.cost) / MAX_COST
    time_diff = (route.time - frontier_point.time) / MAX_TIME
    rel_diff = (frontier_point.reliability - route.reliability)
    
    return sqrt(cost_diff**2 + time_diff**2 + rel_diff**2)
```

**Important:** Miners don't need to match validator routes exactly. They need routes that are *on or near* the efficiency frontier. Different routes can score equally well.

### 3. Data Freshness (20%)

**Question:** Is the miner using current data?

**Method:** Spot-check claimed data sources.

```python
async def verify_freshness(claimed_sources):
    scores = []
    
    # Sample 3 sources to check
    for source in random.sample(claimed_sources, min(3, len(claimed_sources))):
        
        # Check timestamp is recent
        age_hours = (now() - source.timestamp).total_seconds() / 3600
        
        if age_hours > 24:
            scores.append(0.3)  # Stale
        elif age_hours > 6:
            scores.append(0.7)  # Aging
        else:
            scores.append(1.0)  # Fresh
        
        # Verify query hash matches real data
        if source.provider in VERIFIABLE_PROVIDERS:
            actual_data = await query_provider(source.provider, source.query_params)
            if hash(actual_data) != source.query_hash:
                scores[-1] = 0.0  # Fabricated
    
    return mean(scores)
```

**Verifiable providers:** APIs where validators can replay queries to verify hashes.

**Non-verifiable:** Some data changes too fast to replay. Accept timestamp + hash as proof.

### 4. Constraint Satisfaction (15%)

**Question:** Does the route meet shipper requirements?

```python
def score_constraints(route, constraints):
    checks = []
    
    # arrive_by: hard constraint
    if "arrive_by" in constraints:
        if route.arrival_date <= constraints["arrive_by"]:
            checks.append(1.0)
        else:
            checks.append(0.0)  # Miss = zero
    
    # avoid: hard constraint
    if "avoid" in constraints:
        avoided = constraints["avoid"]
        route_ports = [leg.port for leg in route.legs]
        if not any(p in avoided for p in route_ports):
            checks.append(1.0)
        else:
            checks.append(0.0)
    
    # priority: soft constraint
    if "priority" in constraints:
        if routes_ranked_correctly(route, constraints["priority"]):
            checks.append(1.0)
        else:
            checks.append(0.5)  # Wrong ranking, partial credit
    
    return mean(checks) if checks else 1.0
```

### 5. Response Time (10%)

**Question:** How quickly did the miner respond?

```python
def score_latency(response_ms, deadline_ms):
    if response_ms > deadline_ms:
        return 0.0  # Late = zero
    
    # Linear: 0ms = 1.0, deadline = 0.0
    return 1.0 - (response_ms / deadline_ms)
```

---

## Worked Example

### Challenge

```json
{
  "challenge_id": "ch_9x3m2",
  "origin": {"port": "USLA"},
  "destination": {"port": "JPYOK"},
  "cargo": {"type": "container", "size": "40ft"},
  "constraints": {"arrive_by": "2026-03-01", "priority": "time"},
  "deadline_ms": 30000
}
```

### Validator Baseline

Validator computes reference routes:
- Direct sea (14 days, $3200)
- Transpacific express (11 days, $4100)
- Air (2 days, $15000)

Frontier: all three are non-dominated (different cost-time tradeoffs).

### Miner Response

Miner submits:
1. Transpacific express (11 days, $4100)
2. Direct sea (14 days, $3200)
3. Air freight (2 days, $14800)

### Scoring

| Component | Score | Notes |
|-----------|-------|-------|
| Feasibility | 1.0 | All schedules verified |
| Optimality | 0.95 | Routes match frontier; air route slightly cheaper than baseline |
| Freshness | 0.9 | 2/3 sources fresh, 1 source 8 hours old |
| Constraints | 1.0 | All arrive before deadline, ranked by time correctly |
| Latency | 0.75 | 7.5 seconds response (deadline 30s) |

**Total:**
```
S = (0.30 × 1.0) + (0.25 × 0.95) + (0.20 × 0.9) + (0.15 × 1.0) + (0.10 × 0.75)
  = 0.30 + 0.2375 + 0.18 + 0.15 + 0.075
  = 0.9425
```

---

## Challenge Generation

### Synthetic Challenges

Pre-defined route corridors for consistent benchmarking:

```python
ROUTE_CORRIDORS = [
    # Major trade lanes
    {"origin": "CNSHA", "destination": "NLRTM", "name": "Asia-Europe"},
    {"origin": "CNSHA", "destination": "USLAX", "name": "Transpacific East"},
    {"origin": "USNYC", "destination": "NLRTM", "name": "Transatlantic"},
    {"origin": "BRSSZ", "destination": "SGSIN", "name": "South America-Asia"},
    
    # Land bridge
    {"origin": "CNXAN", "destination": "DEHAM", "name": "China-Europe Rail"},
    
    # Regional
    {"origin": "JPYOK", "destination": "KRPUS", "name": "Intra-Asia"},
    {"origin": "NLRTM", "destination": "PLGDN", "name": "Intra-Europe"},
]

def generate_synthetic_challenge():
    corridor = random.choice(ROUTE_CORRIDORS)
    
    # Random constraints
    days_ahead = random.randint(14, 45)
    priority = random.choice(["cost", "time", "reliability"])
    
    return Challenge(
        origin=corridor["origin"],
        destination=corridor["destination"],
        cargo=random_cargo(),
        constraints={
            "arrive_by": today() + timedelta(days=days_ahead),
            "priority": priority
        },
        deadline_ms=30000
    )
```

### Organic Challenges

Real shipper queries:

```python
async def route_shipper_request(request):
    # Convert to challenge
    challenge = Challenge.from_request(request)
    
    # Broadcast to miners
    responses = await broadcast_challenge(challenge)
    
    # Score all responses
    scores = [score_response(r, challenge) for r in responses]
    
    # Return best to shipper
    best_idx = argmax(scores)
    await deliver_to_shipper(request.shipper_id, responses[best_idx])
    
    return list(zip(responses, scores))
```

---

## Consensus

Most scoring is deterministic:

| Component | Deterministic? | Notes |
|-----------|----------------|-------|
| Feasibility | Yes | Schedules exist or they don't |
| Constraints | Yes | Met or not |
| Latency | Yes | Timestamps |
| Freshness | Mostly | Timestamp checks are objective |
| Optimality | Mostly | Depends on baseline algorithm |

### Baseline Algorithm Agreement

Validators must use consistent baseline approaches:
- Same data sources (MarineTraffic, etc.)
- Same optimization algorithm (or equivalent results)
- Same Pareto frontier calculation

**Reference implementation** provided to ensure consistency.

### V-Trust

Validators who deviate from consensus see trust decay:

```python
def update_v_trust(validator):
    consensus_scores = stake_weighted_median(all_validator_scores)
    deviation = mean_absolute_error(validator.scores, consensus_scores)
    
    if deviation < 0.05:
        validator.v_trust += 0.01
    elif deviation > 0.15:
        validator.v_trust -= 0.05
    
    validator.v_trust = clamp(validator.v_trust, 0.0, 1.0)
```

Low V-Trust = lower dividend share.

---

## Technical Requirements

### Hardware

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 8-core | 16-core |
| RAM | 16GB | 32GB |
| Storage | 100GB SSD | 250GB SSD |
| Network | 200Mbps | 1Gbps |

Higher than miners because validators must:
- Run baseline optimization for every challenge
- Query data sources for verification
- Score all miner responses

### Software

- Python 3.10+
- Bittensor validator SDK
- Optimization library (OR-Tools, NetworkX)
- PostgreSQL (challenge history)
- Redis (caching)

### API Subscriptions

Validators need the same data access as miners to verify responses:
- MarineTraffic (or equivalent)
- Rail schedule APIs
- Port status feeds

---

## Cadence

| Event | Frequency |
|-------|-----------|
| Challenge broadcast | Every 50 blocks (~10 min) |
| Response window | 30 seconds |
| Scoring | Immediately after window closes |
| Weight commitment | Every 360 blocks (~1 hr) |

---

## Anti-Gaming Detection

### Fabricated Schedules

**Signal:** Vessel/voyage doesn't exist in MarineTraffic.

**Action:** Feasibility = 0.

### Stale Data

**Signal:** Data sources older than 24 hours.

**Action:** Reduced freshness score.

### Hash Spoofing

**Signal:** Query hash doesn't match replayed API response.

**Action:** Freshness = 0 for that source.

### Copied Responses

**Signal:** Multiple miners submit identical routes with identical timestamps.

**Action:** Only first response scored; others flagged.

---
