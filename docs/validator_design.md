# OmniRoute: Validator Design

---

## Validator Tasks

Validators evaluate miner submissions:

1. **Broadcast challenges** — Send routing challenges to miners
2. **Run baseline optimizer** — Compute reference routes
3. **Score miner responses** — Evaluate on feasibility, quality, freshness, response time
4. **Commit weights** — Submit scores to chain

---

## Input → Output

### Input (Miner Submission)

```json
{
  "challenge_id": "ch_8k2m9",
  "routes": [
    {
      "rank": 1,
      "legs": [...],
      "totals": {"days": 18, "cost_usd": 2850, "reliability": 0.94}
    },
    {
      "rank": 2,
      "legs": [...],
      "totals": {"days": 14, "cost_usd": 4200, "reliability": 0.88}
    }
  ],
  "data_proofs": [
    {"provider": "MarineTraffic", "query_hash": "0x7a3f...", "timestamp": "2026-02-20T10:00:00Z"}
  ],
  "response_ms": 8420
}
```

### Output (Scores)

```json
{
  "challenge_id": "ch_8k2m9",
  "miner_uid": 42,
  "scores": {
    "route_1": {"feasibility": 1.0, "quality": 0.90, "freshness": 1.0, "response_time": 0.72},
    "route_2": {"feasibility": 1.0, "quality": 0.70, "freshness": 0.8, "response_time": 0.72},
    "total_score": 0.855
  }
}
```

---

## Scoring Components

| Component | Weight | How to Score |
|-----------|--------|--------------|
| Feasibility | 30% | Check schedules exist, connections valid |
| Quality | 40% | Compare to baseline (cost, time, reliability) |
| Freshness | 20% | Verify query hashes and timestamps |
| Response Time | 10% | Proportional to deadline |

**Feasibility gate:** If Feasibility = 0, total score = 0.

---

## Quality Scoring

```python
def score_quality(miner_route, baseline):
    cost_score = min(baseline.cost / miner_route.cost, 1.0)
    time_score = min(baseline.time / miner_route.time, 1.0)
    rel_score = min(miner_route.reliability / baseline.reliability, 1.0)
    
    # Weighted: cost 40%, time 35%, reliability 25%
    return (0.40 × cost_score) + (0.35 × time_score) + (0.25 × rel_score)
```

---

## Validator Baseline

Each validator runs their own optimizer:

```python
def compute_baseline(challenge):
    data = gather_data(challenge.origin, challenge.destination)
    baseline_routes = my_optimizer.solve(challenge)
    return baseline_routes[0]
```

---

## Evaluation Cadence

| Event | Frequency |
|-------|-----------|
| Challenge broadcast | ~10 min |
| Response deadline | 30-60s |
| Scoring | After deadline |
| Weight commit | ~1 hour |

---

## Validator Incentives

- **Stake-based emissions** — More TAO staked = more emissions
- **Better baselines** — Better algorithms = more accurate scoring
- **Market removes poor validators** — Delegators leave bad validators

---
