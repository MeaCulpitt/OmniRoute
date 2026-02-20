# OmniRoute: Miner Design

---

## Miner Tasks

Miners are route optimizers. They:

1. Receive challenges (synthetic + real shipper queries)
2. Query data sources for schedules/pricing
3. Compute optimal routes
4. Submit 2 best routes with data proofs (query hashes)

---

## Input → Output

### Input (Challenge)

```json
{
  "challenge_id": "ch_8k2m9",
  "origin": {"port": "CNSHA", "name": "Shanghai"},
  "destination": {"port": "NLRTM", "name": "Rotterdam"},
  "cargo": {"type": "container", "size": "40ft", "weight_kg": 18000},
  "constraints": {
    "arrive_by": "2026-03-15",
    "avoid": ["LKCMB"]
  },
  "deadline_ms": 30000
}
```

### Output (Submission)

```json
{
  "challenge_id": "ch_8k2m9",
  "routes": [
    {
      "rank": 1,
      "legs": [
        {"mode": "sea", "carrier": "Maersk", "vessel": "Madison Maersk", "voyage": "AE7-W0923"},
        {"mode": "road", "carrier": "Van der Helm"}
      ],
      "totals": {"days": 18, "cost_usd": 2850, "reliability": 0.94}
    },
    {
      "rank": 2,
      "legs": [
        {"mode": "rail", "carrier": "China Railway Express", "train": "X8044"},
        {"mode": "road", "carrier": "DB Schenker"}
      ],
      "totals": {"days": 14, "cost_usd": 4200, "reliability": 0.88}
    }
  ],
  "data_proofs": [
    {"provider": "MarineTraffic", "query_hash": "0x7a3f...", "timestamp": "2026-02-20T10:00:00Z"},
    {"provider": "RailAPI", "query_hash": "0x8b4e...", "timestamp": "2026-02-20T10:00:01Z"}
  ],
  "response_ms": 8420
}
```

---

## Performance Dimensions

| Dimension | Weight | How to Maximize |
|-----------|--------|----------------|
| Feasibility | 30% | Verify schedules exist before submitting |
| Quality | 40% | Routes close to validator's baseline (cost, time, reliability) |
| Freshness | 20% | Use current data, include query hashes |
| Response Time | 10% | Respond faster within deadline |

---

## Challenge Types

| Type | Frequency | Description |
|------|-----------|-------------|
| Synthetic | 60% | Procedurally generated |
| Real | 30% | Actual shipper queries |
| Edge Cases | 10% | Unusual routes |

---

## Scoring

Each route scored on 4 components. Then:

```
Route 1 = 75% of its score
Route 2 = 25% of its score

Total = Route 1 + Route 2
```

**Feasibility gate:** If Feasibility = 0, total = 0.

---

## How to Win

1. **Query real data** — Not cached routes
2. **Find good routes** — Close to baseline on cost/time/reliability
3. **Submit 2 routes** — Best counts 75%, second counts 25%
4. **Be fast** — Response time adds up
5. **Use fresh data** — Query hashes prove it

---

## Miner Economics

| Cost | Revenue |
|------|---------|
| API subscriptions | Emissions based on quality scores |
| Compute | Higher quality = more emissions |
| Infrastructure | Better algorithms = more emissions |

---

*Focus on solving the routing problem. The benchmark rewards quality.*
