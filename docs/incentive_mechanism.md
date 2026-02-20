# OmniRoute: Incentive & Mechanism Design

---

## 1. Emission and Reward Logic

### Scoring Components

| Component | Weight | What It Measures |
|-----------|--------|------------------|
| Feasibility | 35% | Routes are physically executable |
| Quality | 45% | How close to validator's baseline |
| Freshness | 20% | Data sources are current |

### Emission Distribution

```
miner_score = (0.35 × feasibility) + (0.45 × quality) + (0.20 × freshness)
miner_emission = (miner_score / sum(all_miner_scores)) × block_emission
```

---

## 2. Incentive Alignment for Miners and Validators

### Miner Incentives

- **Earn emissions** by submitting feasible, high-quality routes
- **Feasibility** — routes must actually work (schedules exist, connections valid)
- **Quality** — routes close to validator's baseline
- **Freshness** — using current data, not stale caches

### Validator Incentives

- **Earn emissions** by correctly evaluating miner submissions
- **Stake-based** — emissions proportional to stake (Bittensor consensus)
- **Better baselines** — validators with better algorithms get more accurate scores over time

### Alignment

| Miner Wants | Validator Wants | Aligned By |
|-------------|-----------------|------------|
| High scores | Accurate scoring | Both want feasible, quality routes |
| More emissions | More stake | Quality → emissions |
| Better algorithms | Better baselines | Competition drives improvement |

---

## 3. Mechanisms to Discourage Low-Quality or Adversarial Behavior

| Attack | Defense |
|--------|---------|
| Memorize common routes | 60% synthetic challenges (infinite variety) |
| Fabricate data | Query hash verification (validators can replay) |
| Copy top miner | PMU collapse (similar solutions get reduced scores) |
| Submit impossible routes | Feasibility check (schedules must exist) |
| Submit stale data | Freshness check (timestamp + hash verification) |

### PMU (Pareto-Maximal Update)

Similar solutions collapse to prevent copycat mining:

```python
def apply_pmu(scores, routes):
    adjusted = scores.copy()
    for i, r1 in enumerate(routes):
        for j, r2 in enumerate(routes):
            if i != j and routes_similar(r1, r2):
                adjusted[i] *= 0.95
    return adjusted
```

---

## 4. Proof of Intelligence / Proof of Effort

### Proof of Effort

Miners must:
- Query real data sources (not return cached routes)
- Generate diverse route options
- Meet constraints (arrive_by, avoid ports, cargo compatibility)

### Proof of Intelligence

High-quality routes require:
- Multi-modal optimization (sea + rail + air + road)
- Trade-off reasoning (cost vs. time vs. reliability)
- Constraint satisfaction
- Real-time data integration

A miner that memorizes routes fails synthetic challenges. A miner that actually optimizes wins emissions.

---

## 5. High-Level Algorithm

### Task Assignment

```
1. Validator broadcasts challenge (origin, destination, cargo, constraints)
2. All miners receive challenge simultaneously
3. Miners have 30-60 seconds to respond
```

### Submission

```
For each miner:
1. Query data sources (schedules, pricing, conditions)
2. Generate candidate routes
3. Rank by priority (cost / time / reliability)
4. Submit routes + data proofs (query hashes + timestamps)
```

### Validation

```
For each response:
1. Check feasibility — do schedules exist?
2. Score quality — compare route to validator's baseline
3. Verify freshness — timestamp recent? query hash valid?
4. Apply PMU collapse
```

### Scoring

```
feasibility = 1.0 if all schedules exist, else 0.0
quality = distance_to_baseline(miner_route, validator_baseline)
freshness = 1.0 if query_hash valid + timestamp < 24h, else 0.0

score = (0.35 × feasibility) + (0.45 × quality) + (0.20 × freshness)
```

### Reward Allocation

```
miner_emission = (miner_score / sum(all_miner_scores)) × block_emission
```

---

## 6. Validator Baseline

Each validator runs their own optimization algorithm:

```python
def validator_baseline(challenge):
    routes = my_optimizer.solve(
        origin=challenge.origin,
        destination=challenge.destination,
        cargo=challenge.cargo,
        constraints=challenge.constraints
    )
    return routes
```

Miners are scored against this baseline. Over time:
- Validators with better algorithms → more accurate scoring → higher trust
- Market淘汰 validators with poor baselines

---

## 7. Challenge Cadence

| Event | Frequency |
|-------|-----------|
| Challenge broadcast | Every 50 blocks (~10 min) |
| Response deadline | 30s standard, 60s complex |
| Weight commitment | Every 360 blocks (~1 hr) |

### Challenge Mix

| Type | Frequency |
|------|-----------|
| Synthetic | 60% |
| Real shipper | 30% |
| Edge cases | 10% |

---

*Ship it. Test it. Iterate.*
