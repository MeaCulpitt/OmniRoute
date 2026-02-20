# OmniRoute: Incentive & Mechanism Design

---

## 1. Emission and Reward Logic

### Scoring Components

| Component | Weight | What It Measures |
|-----------|--------|------------------|
| Feasibility | 30% | Routes are physically executable |
| Quality | 40% | How close to validator's baseline |
| Freshness | 20% | Data sources are current |
| Response Time | 10% | Speed of response |

### Route Scoring

Miners submit their 2 best routes. Each is scored separately, then combined:

```
Route 1 score = 75% of its component score
Route 2 score = 25% of its component score

Total score = Route 1 + Route 2
```

A route only earns points if it matches the baseline. A bad route gets 0.

### Emission Distribution

```
miner_score = (0.30 × feasibility) + (0.40 × quality) + (0.20 × freshness) + (0.10 × response_time)
miner_emission = (miner_score / sum(all_miner_scores)) × block_emission
```

---

## 2. Incentive Alignment for Miners and Validators

### Miner Incentives

- **Earn emissions** by submitting feasible, high-quality routes
- **Feasibility** — routes must actually work (schedules exist, connections valid)
- **Quality** — routes close to validator's baseline
- **Freshness** — using current data, not stale caches
- **Response Time** — faster responses earn more

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
| Submit impossible routes | Feasibility check (schedules must exist) |
| Submit stale data | Freshness check (timestamp + hash verification) |
| Submit late | Response must be within deadline (0 if late) |

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
4. Submit 2 best routes + data proofs (query hashes + timestamps)
```

### Validation

```
For each response:
1. Check feasibility — do schedules exist?
2. Score quality — compare each route to validator's baseline
3. Verify freshness — timestamp recent? query hash valid?
4. Score response time — proportional to deadline
```

### Scoring

```
Component score = (0.30 × feasibility) + (0.40 × quality) + (0.20 × freshness) + (0.10 × response_time)

Route 1 final = 0.75 × component_score
Route 2 final = 0.25 × component_score

Miner total = Route 1 + Route 2
```

### Quality Scoring

```python
def quality_score(miner_route, baseline_route):
    # Cost: lower is better
    cost_score = min(baseline_route.cost / miner_route.cost, 1.0)
    
    # Time: lower is better
    time_score = min(baseline_route.time / miner_route.time, 1.0)
    
    # Reliability: higher is better
    rel_score = min(miner_route.reliability / baseline_route.reliability, 1.0)
    
    # Weighted equally: cost 40%, time 35%, reliability 25%
    quality = (0.40 × cost_score) + (0.35 × time_score) + (0.25 × rel_score)
    
    return quality
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
