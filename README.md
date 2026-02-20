# OmniRoute

OmniRoute optimizes freight routes across sea, rail, air, and road. Shippers submit routing requests with constraints (arrive by date, cost priority, ports to avoid), and the network returns ranked options with detailed breakdowns. Miners compete to find optimal routes using public shipping schedules, port congestion data, and weather feeds. Validators verify feasibility against real schedules and score routes against a reproducible baseline. The API charges per query — no enterprise contracts, no staking requirements. Target customers are mid-market freight forwarders and shippers who need optimization without six-figure software costs. Better routes, pay-per-use pricing, verifiable quality.

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
│  2. DATA INGESTION  Miners pull schedules, weather, port status│
│  3. OPTIMIZATION    Miners compute candidate routes             │
│  4. SUBMISSION      2 best routes with breakdowns               │
│  5. VALIDATION      Validators score against baseline            │
└─────────────────────────────────────────────────────────────────┘
```

---

## Worked Example

### Request

```json
{
  "origin": {"port": "CNSHA", "name": "Shanghai"},
  "destination": {"port": "NLRTM", "name": "Rotterdam"},
  "cargo": {"type": "container", "size": "40ft"},
  "constraints": {"arrive_by": "2026-03-15", "priority": "cost"}
}
```

### Response

```json
{
  "routes": [
    {
      "rank": 1,
      "legs": [
        {"mode": "sea", "carrier": "Maersk", "depart": "CNSHA 2026-02-18", "arrive": "NLRTM 2026-03-08"},
        {"mode": "truck", "carrier": "Local drayage", "arrive": "Warehouse 2026-03-09"}
      ],
      "totals": {"days": 19, "cost_usd": 2850, "reliability": 0.94}
    },
    {
      "rank": 2,
      "legs": [
        {"mode": "rail", "carrier": "China Railway Express", "depart": "Shanghai 2026-02-20", "arrive": "Duisburg 2026-03-05"},
        {"mode": "truck", "carrier": "DB Schenker", "arrive": "Rotterdam 2026-03-06"}
      ],
      "totals": {"days": 14, "cost_usd": 4200, "reliability": 0.88}
    }
  ]
}
```

---

## Scoring

| Component | Weight | Measurement |
|-----------|--------|-------------|
| Feasibility | 30% | Routes are physically possible |
| Quality | 40% | How close to validator's baseline |
| Freshness | 20% | Data sources are current |
| Response Time | 10% | Faster is better |

**Feasibility gate:** If Feasibility = 0, total score = 0.

Miners submit 2 routes:
- Route 1 = 75% of its score
- Route 2 = 25% of its score

---

## For Shippers

```python
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

**70%** of emissions go to miners.

---

## For Validators

Earn dividends by:
- Running baseline optimization
- Verifying miner data sources
- Accurate scoring

**30%** of emissions go to validators.

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

## License

MIT
