# OmniRoute: Miner Architecture & Operations

OmniRoute miners are **route optimizers**. They ingest freight requests, query data sources for schedules and conditions, compute optimal routes, and return ranked options.

This is data processing and optimization — not GPU compute.

---

## The Job

When a challenge arrives:

1. **Parse** the request (origin, destination, cargo, constraints)
2. **Query** data sources for current schedules, rates, conditions
3. **Compute** candidate routes across modes (sea, air, rail, road)
4. **Rank** routes according to shipper's priority
5. **Submit** structured response with data source proofs

---

## Worked Example: Full Pipeline

### Challenge Received

```json
{
  "challenge_id": "ch_8k2m9",
  "origin": {"port": "CNSHA", "name": "Shanghai"},
  "destination": {"port": "NLRTM", "name": "Rotterdam"},
  "cargo": {"type": "container", "size": "40ft", "weight_kg": 18000},
  "constraints": {
    "arrive_by": "2026-03-15",
    "priority": "cost",
    "avoid": ["LKCMB"]
  },
  "deadline_ms": 30000
}
```

### Step 1: Parse & Plan

```python
def plan_query_strategy(challenge):
    origin = challenge.origin.port  # CNSHA
    dest = challenge.destination.port  # NLRTM
    
    # Determine relevant modes
    modes = []
    if is_port(origin) and is_port(dest):
        modes.append("sea")
    if has_rail_connection(origin, dest):
        modes.append("rail")
    if challenge.cargo.weight_kg < 5000:
        modes.append("air")  # Only for lighter cargo
    modes.append("road")  # Always needed for first/last mile
    
    return modes  # ["sea", "rail", "road"]
```

### Step 2: Query Data Sources

```python
async def gather_data(origin, dest, modes, constraints):
    data = {}
    
    # Sea schedules
    if "sea" in modes:
        data["sea"] = await query_marine_traffic(
            origin=origin, 
            dest=dest,
            date_range=(today(), constraints.arrive_by)
        )
        # Hash: 0x7a3f...
    
    # Rail schedules (China-Europe)
    if "rail" in modes:
        data["rail"] = await query_china_railway_express(
            origin=origin,
            dest=nearest_rail_hub(dest)
        )
        # Hash: 0x8b4e...
    
    # Weather/disruptions
    data["weather"] = await query_weather(
        route_region="asia_europe",
        days_ahead=30
    )
    # Could be SN18 or public API
    
    # Port congestion
    data["congestion"] = await query_port_status([origin, dest])
    
    return data
```

### Step 3: Compute Routes

```python
def compute_routes(data, constraints):
    candidates = []
    
    # Option 1: Direct sea
    for sailing in data["sea"]["sailings"]:
        if sailing.arrives_before(constraints.arrive_by):
            if not uses_avoided_ports(sailing, constraints.avoid):
                route = build_route([
                    SeaLeg(sailing),
                    TruckLeg(sailing.destination, constraints.destination)
                ])
                candidates.append(route)
    
    # Option 2: Rail + truck
    for train in data["rail"]["departures"]:
        arrival_hub = train.destination  # e.g., Duisburg
        if train.arrives_before(constraints.arrive_by - timedelta(days=2)):
            route = build_route([
                RailLeg(train),
                TruckLeg(arrival_hub, constraints.destination)
            ])
            candidates.append(route)
    
    # Option 3: Air (if cargo allows)
    if data.get("air"):
        for flight in data["air"]["flights"]:
            route = build_route([
                AirLeg(flight),
                TruckLeg(flight.destination, constraints.destination)
            ])
            candidates.append(route)
    
    return candidates
```

### Step 4: Rank by Priority

```python
def rank_routes(candidates, priority):
    for route in candidates:
        route.totals = calculate_totals(route)
        # {days: 19, cost_usd: 2850, reliability: 0.94, co2_kg: 890}
    
    if priority == "cost":
        return sorted(candidates, key=lambda r: r.totals["cost_usd"])
    elif priority == "time":
        return sorted(candidates, key=lambda r: r.totals["days"])
    elif priority == "reliability":
        return sorted(candidates, key=lambda r: -r.totals["reliability"])
    else:
        # Balanced: weighted score
        return sorted(candidates, key=lambda r: balanced_score(r.totals))
```

### Step 5: Submit Response

```json
{
  "challenge_id": "ch_8k2m9",
  "routes": [
    {
      "rank": 1,
      "legs": [
        {
          "mode": "sea",
          "carrier": "Maersk",
          "vessel": "Madison Maersk",
          "voyage": "AE7-W0923",
          "depart": {"port": "CNSHA", "date": "2026-02-18T14:00Z"},
          "arrive": {"port": "NLRTM", "date": "2026-03-08T06:00Z"},
          "transhipments": ["SGSIN"]
        },
        {
          "mode": "road",
          "carrier": "Van der Helm",
          "depart": {"port": "NLRTM", "date": "2026-03-08T10:00Z"},
          "arrive": {"location": "Final warehouse", "date": "2026-03-08T16:00Z"}
        }
      ],
      "totals": {
        "days": 18,
        "cost_usd": 2850,
        "reliability": 0.94,
        "co2_kg": 890
      }
    },
    {
      "rank": 2,
      "legs": [
        {
          "mode": "rail",
          "carrier": "China Railway Express",
          "train": "X8044",
          "depart": {"station": "Shanghai Minhang", "date": "2026-02-20T00:00Z"},
          "arrive": {"station": "Duisburg", "date": "2026-03-05T12:00Z"}
        },
        {
          "mode": "road",
          "carrier": "DB Schenker",
          "depart": {"station": "Duisburg", "date": "2026-03-05T14:00Z"},
          "arrive": {"port": "NLRTM", "date": "2026-03-06T08:00Z"}
        }
      ],
      "totals": {
        "days": 14,
        "cost_usd": 4200,
        "reliability": 0.88,
        "co2_kg": 420
      }
    }
  ],
  "data_sources": [
    {"type": "sea_schedule", "provider": "MarineTraffic", "query_hash": "0x7a3f...", "timestamp": "2026-02-08T23:55:00Z"},
    {"type": "rail_schedule", "provider": "CRCE", "query_hash": "0x8b4e...", "timestamp": "2026-02-08T23:55:02Z"},
    {"type": "weather", "provider": "SN18", "query_hash": "0x9c5d...", "timestamp": "2026-02-08T23:55:01Z"},
    {"type": "port_status", "provider": "PortWatch", "query_hash": "0xa1b2...", "timestamp": "2026-02-08T23:55:03Z"}
  ],
  "response_ms": 8420
}
```

---

## Data Sources

### Required Integrations

| Data Type | Sources | Update Frequency |
|-----------|---------|------------------|
| Ocean schedules | MarineTraffic, VesselFinder, carrier APIs | Daily |
| Port congestion | PortWatch, Xeneta, UNCTAD | Hourly |
| Rail schedules | China Railway Express, DB Cargo, RZD | Weekly |
| Air cargo | IATA CASS, carrier APIs | Daily |
| Weather/storms | SN18 (Bittensor) or NOAA, Windy | Hourly |
| Road/drayage | Google Maps, HERE, local carriers | Real-time |

### Bittensor Subnets

Using Bittensor subnets is **optional but advantageous**:

| Subnet | Use Case | Why It Helps |
|--------|----------|--------------|
| SN18 (Zeus) | Weather forecasting | Storm delays, route risk |
| SN22 (Desearch) | News/disruption search | Port strikes, closures |

Miners who integrate useful subnets may get better data than public APIs — that's their competitive edge.

### Data Source Proofs

Every data query must include:
- Provider name
- Query hash (reproducible request signature)
- Timestamp

Validators spot-check these against actual API logs.

---

## Optimization Approaches

### Simple: Enumerate & Filter

```python
def simple_optimizer(data, constraints):
    # Generate all feasible routes
    all_routes = enumerate_routes(data)
    
    # Filter by constraints
    valid = [r for r in all_routes if meets_constraints(r, constraints)]
    
    # Sort by priority
    return rank_routes(valid, constraints.priority)
```

Works for simple routes. Doesn't scale.

### Better: Graph Search

```python
def graph_optimizer(data, constraints):
    # Build multimodal transport graph
    graph = build_graph(
        ports=data["ports"],
        schedules=data["schedules"],
        connections=data["connections"]
    )
    
    # Find k-shortest paths
    paths = k_shortest_paths(graph, origin, destination, k=20)
    
    # Evaluate and filter
    valid = [p for p in paths if meets_constraints(p, constraints)]
    
    return rank_routes(valid, constraints.priority)
```

Handles complexity better. Standard algorithms (Dijkstra, A*).

### Advanced: Multi-Objective Optimization

```python
def pareto_optimizer(data, constraints):
    # Build solution space
    graph = build_graph(data)
    
    # Multi-objective search (cost, time, reliability)
    pareto_set = nsga2_search(
        graph=graph,
        objectives=["cost", "time", "reliability"],
        constraints=constraints
    )
    
    # Return Pareto frontier ranked by priority
    return rank_by_priority(pareto_set, constraints.priority)
```

Finds the actual efficiency frontier. More compute, better results.

---

## Performance Scoring

| Component | Weight | How Miners Win |
|-----------|--------|----------------|
| Feasibility | 30% | Verify schedules exist before submitting |
| Optimality | 25% | Better optimization algorithms |
| Freshness | 20% | Real-time data, not stale caches |
| Constraints | 15% | Parse and apply constraints correctly |
| Latency | 10% | Fast data pipelines, efficient compute |

---

## Technical Requirements

### Hardware

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 4-core | 8-core |
| RAM | 8GB | 16GB |
| Storage | 20GB SSD | 50GB SSD |
| Network | 50Mbps | 200Mbps |

This is **not GPU work**. The bottlenecks are:
- API query latency
- Graph search efficiency
- Data freshness

### Software

- Python 3.10+
- Async HTTP client (aiohttp, httpx)
- Graph library (NetworkX, igraph)
- Bittensor SDK (for subnet integration)

### API Subscriptions

| Provider | Cost | Necessity |
|----------|------|-----------|
| MarineTraffic | $50-500/month | Required for ocean |
| VesselFinder | Free tier available | Backup/supplement |
| PortWatch | Free tier | Port congestion |
| Weather API | Free-$50/month | Route risk |

**Startup cost:** ~$100/month for basic coverage.

---

## Economics

### Earning Emissions

```
S_miner = weighted_score(feasibility, optimality, freshness, constraints, latency)
W_miner = S_miner / sum(all_miner_scores)
emissions = W_miner × block_emissions
```

### Costs

| Cost | Amount | Notes |
|------|--------|-------|
| API subscriptions | $100-500/month | Depends on coverage |
| Compute | $20-50/month | VPS sufficient |
| Bittensor registration | 1 TAO | One-time |

### Break-Even

Depends on:
- Total emissions to subnet
- Number of competing miners
- Your relative score

Rough estimate: Top 20% of miners are profitable at typical subnet emissions.

---

## Operational Tips

### Data Freshness

- Cache schedules locally, but refresh frequently
- Mark cache timestamps; validators check freshness
- Stale data = low freshness score

### Constraint Handling

- Parse constraints carefully; missing one = score penalty
- `avoid` constraints are common; maintain blocked port/carrier lists
- `arrive_by` is a hard constraint; late = 0 on that component

### Latency Optimization

- Pre-fetch common route corridors (Asia-Europe, Transpacific)
- Parallelize API queries
- Keep optimization algorithms warm

### Fallback Strategy

- If primary data source fails, have backups
- Partial response > no response
- Log failures for debugging

---
