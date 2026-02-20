# OmniRoute: Business Logic & Market Rationale

---

## The Problem

Freight routing is hard:

- **Multi-modal** — sea, rail, air, road combinations
- **Multi-objective** — cost vs. time vs. reliability tradeoffs
- **Data-intensive** — requires current schedules, pricing, conditions
- **Valuable** — 2% savings on a $3000 container = $60/query ROI

**Current solutions are expensive:**

| Solution | Cost | Who Uses It |
|----------|------|-------------|
| Manual (freight forwarder) | 10-20% markup | Everyone |
| Enterprise software (SAP, Oracle) | $100K+/year | Large shippers |
| Digital forwarders (Flexport) | Premium pricing | Well-funded companies |

Small and mid-sized shippers get suboptimal routes because optimization is expensive.

---

## Why OmniRoute

**OmniRoute is an API:**

1. Shipper submits: origin, destination, cargo, constraints
2. Network returns: ranked route options with cost/time/reliability
3. Shipper picks and books

**What OmniRoute is:**
- A routing intelligence layer
- Pay-per-query pricing ($1-3/query)
- Real-time, current data

**What OmniRoute is not:**
- A freight forwarder (doesn't book cargo)
- A marketplace (doesn't quote carrier rates)
- Enterprise software (no contracts, no integration)

---

## The Benchmark

**What we measure:** Route quality

Each route is scored on:
- **Feasibility** — Does this route actually work?
- **Quality** — How close is it to the validator's baseline?
- **Freshness** — Is the data current?
- **Response Time** — How fast did the miner respond?

Miners compete to find the best routes. Validators ensure quality. The network produces better routes than any single optimizer could.

---

## Why Bittensor

### 1. Competitive Optimization

Miners compete to find better routes. Competition drives quality.

Centralized: One algorithm, one team.
Decentralized: Many miners, many approaches, best wins.

### 2. Data Freshness Incentives

Miners are rewarded for using current data. Stale caches = lower scores = less emissions.

### 3. No Single Point of Failure

One miner offline → others continue. Decentralized = resilient.

### 4. Aligned Incentives

| Stakeholder | Wants | Gets Paid When |
|-------------|-------|----------------|
| Miner | Maximize score | Routes are feasible, high-quality, fresh |
| Validator | Accurate scoring | Correctly evaluating miners |
| Shipper | Good routes | Validators ensure quality |

---

## Revenue Model

### Query Pricing

| Tier | Price | Use Case |
|------|-------|----------|
| Standard | $1/query | Single route, basic constraints |
| Complex | $3/query | Multi-modal, tight constraints |
| Bulk | $0.50/query | Pre-purchased 1000+ queries |

**Example ROI:**
- Container Shanghai → Rotterdam: $2500-4000
- 2% savings from better routing: $50-80
- OmniRoute query: $1-3
- **ROI: 20-80x**

### Revenue Split

| Recipient | Share |
|-----------|-------|
| Miners | 70% |
| Validators | 30% |

---

## Competitive Landscape

### Enterprise Software

- SAP TM, Oracle TMS — $100K+/year, enterprise only

**OmniRoute advantage:** Pay-per-use, no contracts

### Digital Forwarders

- Flexport, Forto — Premium pricing, they're the forwarder

**OmniRoute advantage:** Just intelligence, works with any forwarder

### Other Bittensor Subnets

No direct competitor in freight routing.

**Complementary:**
- SN18 (weather) — route risk data
- SN22 (search) — disruption detection

---

## Defensibility

### 1. Miner Expertise

Over time, miners develop specialized optimization for different corridors. Knowledge compounds.

### 2. Data Relationships

Miners build API integrations with schedules, carriers, ports. Relationships are sticky.

### 3. Network Effects

More queries → more emissions → better optimization → more queries.

---

## Path to Adoption

### Phase 1: Benchmark (Months 1-3)

- Launch with synthetic challenges
- Establish baseline route quality
- Onboard pilot shippers

### Phase 2: API (Months 3-6)

- Public API with payment
- Integration guides
- First paying customers

### Phase 3: Scale (Months 6-12)

- Bulk pricing
- Corridor specialization
- Partner with freight platforms

---

## Summary

| Question | Answer |
|----------|--------|
| What problem? | Freight routing is expensive, complex, valuable |
| Why Bittensor? | Competitive optimization, aligned incentives, no single point of failure |
| How revenue? | Pay-per-query ($1-3), 70/30 miners/validators |
| Defensibility? | Miner expertise, data relationships, network effects |

---

*Freight routing is a real problem. OmniRoute solves it.*
