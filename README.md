# OmniRoute: Multi-Modal Freight Routing for Bittensor

OmniRoute optimizes freight routes across air, sea, rail, and road. Submit origin, destination, and constraints — receive a ranked set of route options with cost, time, and reliability estimates.

Miners compete to find optimal routes. Validators verify using reproducible baselines and real-world data feeds.

---

## The Problem

Multi-modal freight routing is hard:

- **Combinatorial explosion.** LA to Hamburg could go sea-direct, air-to-hub, rail-across-Asia, or dozens of hybrid paths.
- **Dynamic constraints.** Weather delays, port congestion, carrier availability, customs windows — all change daily.
- **Fragmented data.** Shipping schedules, rail networks, and trucking rates live in separate systems.
- **Expertise-gated.** Good routing requires domain knowledge most shippers don't have.

Current solutions are either expensive enterprise software (SAP, Oracle) or manual broker expertise.

---

## How OmniRoute Works

```
┌─────────────────────────────────────────────────────────────────┐
│                     OMNIROUTE PIPELINE                           │
├─────────────────────────────────────────────────────────────────┤
│  1. REQUEST         Origin, destination, cargo, constraints      │
│                     "40ft container, Shanghai → Rotterdam,       │
│                      arrive by March 15, minimize cost"          │
├─────────────────────────────────────────────────────────────────┤
│  2. DATA INGESTION  Miners pull schedules, weather, port status  │
│                     Public APIs + Bittensor subnets where useful │
├─────────────────────────────────────────────────────────────────┤
│  3. OPTIMIZATION    Miners compute candidate routes              │
│                     Balance cost, time, reliability, carbon      │
├─────────────────────────────────────────────────────────────────┤
│  4. SUBMISSION      Ranked route options with breakdowns         │
│                     Each leg: carrier, schedule, estimated cost  │
├─────────────────────────────────────────────────────────────────┤
│  5. VALIDATION      Validators score against baseline + verify   │
│                     data sources                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Worked Example

### Request

```json
{
  "request_id": "req_7x9k2",
  "origin": {"port": "CNSHA", "name": "Shanghai"},
  "destination": {"port": "NLRTM", "name": "Rotterdam"},
  "cargo": {"type": "container", "size": "40ft", "weight_kg": 18000},
  "constraints": {
    "arrive_by": "2026-03-15",
    "priority": "cost",
    "avoid": ["transhipment_colombo"]
  }
}
```

### Response

```json
{
  "request_id": "req_7x9k2",
  "routes": [
    {
      "rank": 1,
      "legs": [
        {"mode": "sea", "carrier": "Maersk", "vessel": "Madison Maersk", 
         "depart": "CNSHA 2026-02-18", "arrive": "NLRTM 2026-03-08",
         "route": "Shanghai → Singapore → Suez → Rotterdam"},
        {"mode": "truck", "carrier": "Local drayage", 
         "depart": "NLRTM 2026-03-08", "arrive": "Warehouse 2026-03-09"}
      ],
      "totals": {"days": 19, "cost_usd": 2850, "reliability": 0.94, "co2_kg": 890}
    },
    {
      "rank": 2,
      "legs": [
        {"mode": "rail", "carrier": "China Railway Express",
         "depart": "Shanghai 2026-02-20", "arrive": "Duisburg 2026-03-05"},
        {"mode": "truck", "carrier": "DB Schenker",
         "depart": "Duisburg 2026-03-05", "arrive": "Rotterdam 2026-03-06"}
      ],
      "totals": {"days": 14, "cost_usd": 4200, "reliability": 0.88, "co2_kg": 420}
    },
    {
      "rank": 3,
      "legs": [
        {"mode": "air", "carrier": "Emirates SkyCargo",
         "depart": "PVG 2026-02-16", "arrive": "AMS 2026-02-17"},
        {"mode": "truck", "carrier": "Local",
         "depart": "AMS 2026-02-17", "arrive": "Rotterdam 2026-02-17"}
      ],
      "totals": {"days": 2, "cost_usd": 12500, "reliability": 0.97, "co2_kg": 4200}
    }
  ],
  "data_sources": {
    "schedules": "MarineTraffic API (hash: 0x7a3f...)",
    "weather": "SN18 Zeus (hash: 0x8b4e...)",
    "port_congestion": "PortWatch public feed"
  }
}
```

**What the shipper gets:** Three options ranked by their stated priority (cost). Clear tradeoffs. Actionable.

---

## Data Sources

OmniRoute uses a mix of public APIs and Bittensor subnets:

| Data Type | Source | Notes |
|-----------|--------|-------|
| Vessel schedules | MarineTraffic, VesselFinder | Public/freemium APIs |
| Port congestion | PortWatch, Xeneta | Delay estimates |
| Weather/storms | SN18 (Zeus) or public APIs | Route risk assessment |
| Rail schedules | Operator APIs (DB, CRCE) | Eurasian land bridge |
| Air cargo | IATA, carrier APIs | Rates and availability |
| Road/last-mile | Google Maps, HERE | Drayage estimates |

**Bittensor integration is optional, not required.** Miners use whatever sources give them an edge. If SN18 provides better weather data than public APIs, miners will use it. Market forces, not mandates.

---

## Scoring

Validators evaluate miner responses on:

| Component | Weight | Measurement |
|-----------|--------|-------------|
| Feasibility | 30% | Routes are physically possible (schedules exist, connections work) |
| Optimality | 25% | How close to Pareto frontier on cost/time/reliability |
| Data freshness | 20% | Sources are current (not stale schedules) |
| Constraint satisfaction | 15% | Meets stated requirements (arrive_by, avoid, etc.) |
| Response time | 10% | Faster is better, within reason |

### Verification Method

Validators maintain their own baseline optimizer using the same public data sources. They:

1. Run the same request through their baseline
2. Verify miner's claimed schedules exist (spot-check against APIs)
3. Score miner routes relative to baseline + feasibility checks

This is **reproducible verification** — not subjective judgment.

---

## Documentation

| Document | Description |
|----------|-------------|
| [Incentive Mechanism](./docs/mechanism_design.md) | Scoring formulas and emission logic |
| [Miner Architecture](./docs/miner_design.md) | Data pipelines and optimization approaches |
| [Validator Architecture](./docs/validator_design.md) | Verification methods and consensus |
| [Business Rationale](./docs/business_logic.md) | Market opportunity and revenue model |
| [Go-To-Market](./docs/go_to_market.md) | Target customers and growth strategy |

---

## For Shippers

Query OmniRoute via API:

```python
from omniroute import Client

client = Client(api_key="...")

routes = client.optimize(
    origin="CNSHA",
    destination="NLRTM",
    cargo={"type": "container", "size": "40ft"},
    constraints={"arrive_by": "2026-03-15", "priority": "cost"}
)

print(routes[0].totals)  # {'days': 19, 'cost_usd': 2850, ...}
```

Pay per query. No staking required.

---

## For Miners

Earn TAO by:
1. Building effective route optimization engines
2. Maintaining fresh data source integrations
3. Responding quickly with feasible, high-quality routes

Better routes = higher scores = more emissions.

Hardware requirements are modest — this is data processing, not GPU compute.

---

## For Validators

Earn dividends by:
1. Running baseline optimization for comparison
2. Verifying miner data sources
3. Maintaining scoring consensus

---

## License

MIT

---
