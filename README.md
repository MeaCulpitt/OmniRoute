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
│                     "40ft container, Shanghai → Rotterdam,      │
│                      arrive by March 15, minimize cost"         │
├─────────────────────────────────────────────────────────────────┤
│  2. DATA INGESTION  Miners pull schedules, weather, port status│
│                     Public APIs + Bittensor subnets where useful │
├─────────────────────────────────────────────────────────────────┤
│  3. OPTIMIZATION    Miners compute candidate routes             │
│                     Balance cost, time, reliability             │
├─────────────────────────────────────────────────────────────────┤
│  4. SUBMISSION      2 best routes with breakdowns              │
│                     Each leg: carrier, schedule, cost            │
├─────────────────────────────────────────────────────────────────┤
│  5. VALIDATION      Validators score against baseline          │
│                     Verify data sources                        │
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
    "priority": "cost"
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
         "depart": "CNSHA 2026-02-18", "arrive": "NLRTM 2026-03-08"},
        {"mode": "truck", "carrier": "Local drayage",
         "depart": "NLRTM 2026-03-08", "arrive": "Warehouse 2026-03-09"}
      ],
      "totals": {"days": 19, "cost_usd": 2850, "reliability": 0.94}
    },
    {
      "rank": 2,
      "legs": [
        {"mode": "rail", "carrier": "China Railway Express",
         "depart": "Shanghai 2026-02-20", "arrive": "Duisburg 2026-03-05"},
        {"mode": "truck", "carrier": "DB Schenker",
         "depart": "Duisburg 2026-03-05", "arrive": "Rotterdam 2026-03-06"}
      ],
      "totals": {"days": 14, "cost_usd": 4200, "reliability": 0.88}
    }
  ],
  "data_proofs": [
    {"provider": "MarineTraffic", "query_hash": "0x7a3f...", "timestamp": "2026-02-20T10:00:00Z"}
  ]
}
```

**What the shipper gets:** Two options ranked by priority (cost). Clear tradeoffs. Actionable.

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

---

## Scoring

Validators evaluate miner responses on:

| Component | Weight | Measurement |
|-----------|--------|-------------|
| Feasibility | 30% | Routes are physically possible |
| Quality | 40% | How close to validator's baseline |
| Freshness | 20% | Data sources are current |
| Response Time | 10% | Faster is better |

### Route Scoring

Miners submit their 2 best routes:

```
Route 1 = 75% of its score
Route 2 = 25% of its score

Total = Route 1 + Route 2
```

---

## Documentation

| Document | Description |
|----------|-------------|
| [Incentive Mechanism](./docs/mechanism_design.md) | Scoring formulas and emission logic |
| [Miner Architecture](./docs/miner_design.md) | Data pipelines and optimization |
| [Validator Architecture](./docs/validator_design.md) | Verification methods |
| [Business Rationale](./docs/business_logic.md) | Market opportunity |
| [Go-To-Market](./docs/go_to_market.md) | Growth strategy |

---

## For Shippers

```python
from omniroute import Client

routes = client.optimize(
    origin="CNSHA",
    destination="NLRTM",
    cargo={"type": "container", "size": "40ft"},
    constraints={"arrive_by": "2026-03-15", "priority": "cost"}
)

print(routes[0].totals)  # {'days': 19, 'cost_usd': 2850}
```

Pay per query. No staking required.

---

## For Miners

Earn TAO by finding better routes:
- Feasible routes (schedules exist)
- High quality (close to baseline)
- Fresh data (current schedules)
- Fast response

70% of emissions go to miners.

---

## For Validators

Earn dividends by:
- Running baseline optimization
- Verifying miner data sources
- Accurate scoring

30% of emissions go to validators.

---

## License

MIT
