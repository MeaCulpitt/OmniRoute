# OmniRoute: Business Rationale

OmniRoute provides freight route optimization as a service. Shippers submit routing requests, miners compete to find optimal routes, validators verify quality. The result: better routes than manual planning, cheaper than enterprise software.

---

## The Problem

### Multi-Modal Routing Is Hard

Shipping a container from Shanghai to Rotterdam could go:
- **Direct sea:** 30 days, cheapest
- **Sea via Suez express:** 25 days, moderate cost
- **China-Europe rail:** 15 days, higher cost
- **Air to hub + truck:** 3 days, expensive

Each option has tradeoffs. Finding the best route requires:
- Current vessel schedules
- Rail capacity and booking windows
- Port congestion data
- Weather forecasts
- Carrier pricing

Most shippers don't have this. They rely on freight forwarders who may or may not optimize.

### Current Solutions Are Expensive

| Solution | Cost | Who Uses It |
|----------|------|-------------|
| Manual (freight forwarder) | 10-20% markup | Everyone |
| Enterprise software (SAP, Oracle) | $100K+/year | Large shippers |
| Digital forwarders (Flexport) | Premium pricing | Well-funded companies |

Small and mid-sized shippers get suboptimal routes because optimization is expensive.

### The Gap

No affordable, real-time route optimization for the long tail of shippers.

---

## The Solution

OmniRoute provides **route optimization as an API**:

1. Shipper submits: origin, destination, cargo, constraints
2. Network returns: ranked route options with cost/time/reliability
3. Shipper picks and books

**What OmniRoute is:**
- A routing intelligence layer
- Pay-per-query pricing
- Real-time, current data

**What OmniRoute is not:**
- A freight forwarder (doesn't book or move cargo)
- A marketplace (doesn't quote carrier rates)
- Enterprise software (no contracts, no integration)

---

## Market Size

### Global Freight Routing

| Segment | Annual Shipments | Addressable |
|---------|------------------|-------------|
| Container shipping | 150M TEU | Primary target |
| Air cargo | 60M tonnes | Secondary |
| Rail freight | 10M+ containers (China-Europe alone) | Growing |

### Serviceable Market

Focus on shippers who:
- Move 100-10,000 containers/year (mid-market)
- Don't have enterprise TMS software
- Currently rely on freight forwarder judgment

**Estimate:** 50,000+ companies globally, 20M+ routing decisions/year.

### Revenue Potential

At $1-5 per routing query:
- 1M queries/year = $1-5M revenue
- 10M queries/year = $10-50M revenue

This is **before** considering premium tiers for complex routes.

---

## Why Decentralized?

### 1. Competitive Optimization

Miners compete to find better routes. Competition drives quality.

Centralized: One algorithm, one team optimizing.
Decentralized: Many miners, many approaches, best one wins.

### 2. Data Freshness Incentives

Miners are rewarded for using current data. Stale caches = lower scores = less emissions.

Centralized: Data freshness depends on internal discipline.
Decentralized: Economic incentive to stay current.

### 3. No Single Point of Failure

If one miner goes offline, others continue serving.

Centralized: Outage = no service.
Decentralized: Resilient by design.

### 4. Cost Structure

Miners absorb API subscription costs and compete on efficiency.

Centralized: Provider bears all infrastructure cost.
Decentralized: Cost distributed across network.

---

## Revenue Model

### Query Pricing

| Tier | Price | Use Case |
|------|-------|----------|
| Standard | $1/query | Single route, basic constraints |
| Complex | $3/query | Multi-modal, tight constraints |
| Bulk | $0.50/query | Pre-purchased blocks of 1000+ |

**Why this works:**
- Freight forwarder markup on a container: $200-500
- OmniRoute query cost: $1-3
- If OmniRoute saves even 2% on route cost, it pays for itself

### Example Economics

Container Shanghai → Rotterdam:
- Typical cost: $2,500-4,000
- 2% savings from better routing: $50-80
- OmniRoute query cost: $1-3
- **ROI: 20-80x**

### Revenue Distribution

| Recipient | Share | Rationale |
|-----------|-------|-----------|
| Miners | 60% | Route computation |
| Validators | 25% | Verification infrastructure |
| Protocol | 15% | Development, maintenance |

---

## Competitive Landscape

### Enterprise Software

**SAP TM, Oracle TMS, Blue Yonder**

- Pros: Full-featured, integrated with ERP
- Cons: $100K+/year, long implementation, complex

**OmniRoute advantage:** No contracts, no integration, pay per use.

### Digital Forwarders

**Flexport, Forto, Sennder**

- Pros: Modern UX, visibility, managed service
- Cons: Premium pricing, they're the forwarder (not just optimization)

**OmniRoute advantage:** Just the intelligence layer. Use with any forwarder.

### Traditional Forwarders

**DHL, Kuehne+Nagel, DB Schenker**

- Pros: Relationships, full service
- Cons: Optimization varies by account rep, opaque pricing

**OmniRoute advantage:** Transparent optimization anyone can access.

### Other Bittensor Subnets

No direct competitor in freight routing.

Complementary subnets:
- SN18 (weather) — data source for route risk
- SN22 (search) — disruption detection

---

## Defensibility

### 1. Miner Expertise

Over time, miners develop specialized optimization for different corridors. This knowledge compounds.

### 2. Data Relationships

Miners build API integrations with schedule providers, carriers, ports. These relationships are sticky.

### 3. Network Effects

More queries → more emissions → more miners → better optimization → more queries.

### 4. Verification Complexity

Validators maintain baseline optimizers and verification infrastructure. Non-trivial to replicate.

---

## Risk Factors

### Data Availability

**Risk:** Key data sources (schedules, pricing) become restricted or expensive.

**Mitigation:** Multiple redundant sources. Public data (AIS, port calls) is hard to restrict.

### Enterprise Competition

**Risk:** SAP/Oracle add pay-per-query options.

**Mitigation:** They're focused on enterprise. Long tail isn't their market.

### Quality Consistency

**Risk:** Route quality varies too much across miners.

**Mitigation:** Validator baseline ensures minimum quality. Bad routes get scored low.

### Crypto Adoption Barrier

**Risk:** Shippers don't want to pay in crypto.

**Mitigation:** Fiat payment gateway. Users pay USD, protocol handles conversion.

---

## Path to Revenue

### Phase 1: Prove Quality (Months 1-3)

- Launch with synthetic challenges
- Establish baseline route quality
- Onboard first pilot customers (freight forwarders)

### Phase 2: API Launch (Months 3-6)

- Public API with fiat payment option
- Integration guides for forwarders
- First paying customers

### Phase 3: Scale (Months 6-12)

- Bulk pricing for high-volume users
- Corridor specialization (Asia-Europe, Transpacific)
- Partnership with freight platforms

### Phase 4: Expansion (Year 2+)

- Premium tiers (customs optimization, carbon scoring)
- Carrier rate integration (quote, not just route)
- Booking integration with select carriers

---

## Summary

OmniRoute brings route optimization to the long tail of shippers who can't afford enterprise software.

**For shippers:** Better routes at $1-3/query.

**For forwarders:** Tool to improve service without building in-house.

**For the network:** Real-world utility with measurable value.

The decentralized structure creates competitive pressure for quality and freshness that centralized alternatives lack.

---
