# OmniRoute: Incentive & Mechanism Design

OmniRoute rewards miners for producing feasible, optimal freight routes. Validators verify using reproducible baselines and data source checks.

---

## Emission Logic

Unlike subnets with fixed emission splits, OmniRoute uses a **single unified score**. All emissions flow to route quality — no artificial separation.

### Scoring Components

| Component | Weight | What It Measures |
|-----------|--------|------------------|
| Feasibility | 30% | Routes are physically executable |
| Optimality | 25% | Proximity to cost/time/reliability frontier |
| Data freshness | 20% | Sources are current, not stale |
| Constraint satisfaction | 15% | Meets shipper requirements |
| Response time | 10% | Faster responses, within deadline |

**Total: 100%**

---

## Scoring Formula

```
S_miner = (0.30 × feasibility) + 
          (0.25 × optimality) + 
          (0.20 × freshness) + 
          (0.15 × constraints) + 
          (0.10 × latency)
```

All components range 0.0 to 1.0.

### Weight Setting

```
W_miner = S_miner / sum(all_miner_scores)
emissions = W_miner × block_emissions
```

---

## Component Details

### 1. Feasibility (30%)

**Question:** Can this route actually be executed?

**Checks:**
- Do the vessels/flights/trains exist?
- Do the schedules align (no impossible connections)?
- Are the ports/terminals operational?
- Is the cargo type allowed on stated carriers?

**Scoring:**
```python
def score_feasibility(route):
    legs = route.legs
    valid_legs = 0
    
    for leg in legs:
        if verify_schedule_exists(leg):
            if verify_connection_time(leg, next_leg) >= MIN_TRANSFER:
                valid_legs += 1
    
    return valid_legs / len(legs)
```

**Binary failure:** If any leg is impossible, feasibility = 0.

### 2. Optimality (25%)

**Question:** How good is this route compared to what's possible?

**Method:** Validators maintain a baseline optimizer. For each challenge, they compute their own Pareto frontier across cost, time, and reliability.

```
┌─────────────────────────────────────────────────────────────────┐
│                    PARETO FRONTIER                               │
│                                                                  │
│  Cost ($)                                                        │
│    │                                                             │
│    │    ★ Air (fast, expensive)                                 │
│    │                                                             │
│    │         ★ Rail (medium)                                    │
│    │                                                             │
│    │              ★ Sea (slow, cheap)                           │
│    │                                                             │
│    └──────────────────────────────────────────── Time (days)    │
│                                                                  │
│  Routes ON the frontier = optimal                                │
│  Routes INSIDE = dominated (worse on all dimensions)            │
└─────────────────────────────────────────────────────────────────┘
```

**Scoring:**
```python
def score_optimality(miner_routes, baseline_frontier):
    scores = []
    
    for route in miner_routes:
        # Distance to nearest frontier point
        distance = min_distance_to_frontier(route, baseline_frontier)
        
        # Normalize: on frontier = 1.0, far inside = 0.0
        score = max(0, 1.0 - (distance / MAX_DISTANCE))
        scores.append(score)
    
    return mean(scores)
```

**Key insight:** Miners don't need to match the validator's exact routes. They need to find routes that are *on or near* the efficiency frontier.

### 3. Data Freshness (20%)

**Question:** Is the miner using current data or stale caches?

**Method:** Validators spot-check claimed data sources.

```python
def score_freshness(route, claimed_sources):
    checks = []
    
    for source in sample(claimed_sources, k=3):
        # Verify source was queried recently
        if source.query_hash in recent_api_logs:
            if source.timestamp > (now() - MAX_STALE_HOURS):
                checks.append(1.0)
            else:
                checks.append(0.5)  # Stale but real
        else:
            checks.append(0.0)  # Fabricated
    
    return mean(checks)
```

**Stale data penalty:** Using week-old schedules when current ones exist.

**Fabrication penalty:** Claiming data sources that weren't actually queried.

### 4. Constraint Satisfaction (15%)

**Question:** Does the route meet the shipper's requirements?

**Checks:**
- `arrive_by`: Does the route arrive before deadline?
- `avoid`: Are excluded ports/carriers avoided?
- `priority`: Is the ranking consistent with stated priority?
- `cargo_type`: Are special requirements (reefer, hazmat) handled?

**Scoring:**
```python
def score_constraints(route, constraints):
    satisfied = 0
    total = len(constraints)
    
    for constraint in constraints:
        if constraint.type == "arrive_by":
            if route.arrival_date <= constraint.value:
                satisfied += 1
        elif constraint.type == "avoid":
            if constraint.value not in route.all_ports:
                satisfied += 1
        # ... etc
    
    return satisfied / total
```

**Hard vs soft:** Some constraints are binary (arrive_by), others are preferences (priority).

### 5. Response Time (10%)

**Question:** How quickly did the miner respond?

**Scoring:**
```python
def score_latency(response_ms, deadline_ms):
    if response_ms > deadline_ms:
        return 0.0  # Late = zero
    
    # Linear scale: faster is better
    return 1.0 - (response_ms / deadline_ms)
```

**Deadline:** Challenges specify a response window (e.g., 30 seconds for complex routes, 5 seconds for simple).

**Why only 10%:** Speed matters but quality matters more. Don't incentivize garbage-fast responses.

---

## Worked Example

### Challenge

```json
{
  "origin": "CNSHA",
  "destination": "NLRTM",
  "cargo": {"type": "container", "size": "40ft"},
  "constraints": {"arrive_by": "2026-03-15", "priority": "cost"},
  "deadline_ms": 30000
}
```

### Miner Response

Returns 3 routes: sea (cheapest), rail, air.

### Validator Evaluation

| Component | Score | Notes |
|-----------|-------|-------|
| Feasibility | 1.0 | All schedules verified, connections valid |
| Optimality | 0.9 | Sea route matches frontier; rail slightly dominated |
| Freshness | 0.85 | 2/3 sources current; 1 source 3 days old |
| Constraints | 1.0 | All routes arrive before deadline, ranked by cost |
| Latency | 0.7 | Responded in 9 seconds (deadline was 30) |

**Total Score:**
```
S = (0.30 × 1.0) + (0.25 × 0.9) + (0.20 × 0.85) + (0.15 × 1.0) + (0.10 × 0.7)
  = 0.30 + 0.225 + 0.17 + 0.15 + 0.07
  = 0.915
```

Strong performance. This miner earns proportional emissions.

---

## Anti-Gaming Mechanisms

### Schedule Fabrication

**Attack:** Claim vessels/routes that don't exist.

**Defense:** Feasibility check against public schedule APIs. Fabricated schedules = 0 feasibility = near-zero score.

### Data Source Spoofing

**Attack:** Claim to use fresh data while using stale caches.

**Defense:** Validators sample-check claimed sources. Spoofing = 0 freshness score.

### Frontier Gaming

**Attack:** Submit routes just outside the validator's frontier to appear optimal.

**Defense:** Validators use multiple baseline algorithms. Gaming one baseline won't game all.

### Latency Spam

**Attack:** Respond instantly with low-quality routes to capture latency bonus.

**Defense:** Latency is only 10% of score. Bad routes lose 90%.

### Constraint Cheating

**Attack:** Ignore constraints to find "better" routes.

**Defense:** Constraint satisfaction is binary for hard constraints. Miss arrive_by = 0 on that component.

---

## Challenge Generation

### Synthetic Challenges

Pre-built routes for consistent benchmarking:

```python
SYNTHETIC_ROUTES = [
    {"origin": "CNSHA", "destination": "NLRTM", "difficulty": "standard"},
    {"origin": "USLA", "destination": "JPYOK", "difficulty": "transpacific"},
    {"origin": "BRSSZ", "destination": "DEHAM", "difficulty": "southern_hemisphere"},
    # Multi-modal emphasis
    {"origin": "CNXAN", "destination": "PLGDN", "mode_hint": "rail", "difficulty": "eurasian_land"},
]

def generate_synthetic():
    template = random.choice(SYNTHETIC_ROUTES)
    constraints = random_constraints(template.difficulty)
    return Challenge(
        origin=template.origin,
        destination=template.destination,
        constraints=constraints
    )
```

### Organic Challenges

Real shipper queries routed through the network:

```python
async def handle_shipper_request(request):
    challenge = Challenge.from_request(request)
    
    responses = await broadcast_to_miners(challenge)
    scored = [(r, score(r)) for r in responses]
    best = max(scored, key=lambda x: x[1])
    
    await deliver_to_shipper(request.shipper_id, best[0])
    return scored
```

---

## Validator Economics

### Baseline Maintenance

Validators must maintain:
- Schedule API integrations (MarineTraffic, VesselFinder, etc.)
- Baseline route optimizer
- Data freshness verification logic

**Cost:** API subscriptions + compute for optimization.

**Revenue:** Validator dividends from emissions.

### Consensus

Most scoring is deterministic:
- Feasibility: schedules exist or they don't
- Constraints: met or not
- Latency: timestamps

Optimality requires baseline agreement. Validators who deviate significantly see V-Trust decay.

---

## Cadence

| Event | Frequency |
|-------|-----------|
| Challenge broadcast | Every 50 blocks (~10 min) |
| Response deadline | 30 seconds (standard), 60 seconds (complex) |
| Scoring | Immediately after deadline |
| Weight commitment | Every 360 blocks (~1 hr) |

---
