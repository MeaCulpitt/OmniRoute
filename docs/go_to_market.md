# OmniRoute: Go-To-Market Strategy

OmniRoute launches as a B2B API for freight route optimization. The GTM strategy targets freight forwarders first (they have routing needs and technical capability), then expands to direct shippers.

---

## Phase 1: Freight Forwarder Pilots (Months 1-3)

### Why Forwarders First

| Segment | Routing Volume | Technical Capability | Decision Speed |
|---------|----------------|----------------------|----------------|
| Freight forwarders | High (route daily) | Have dev teams | Fast |
| Mid-size shippers | Medium | Limited tech | Medium |
| Enterprise shippers | High | Complex procurement | Slow |

Forwarders are ideal first customers:
- They optimize routes constantly
- They have API integration capability
- They can measure value immediately
- Successful forwarders become distribution channel to their shipper clients

### Target Profile

**Ideal early customer:**
- 10-50 employees
- 500-5,000 shipments/year
- No enterprise TMS
- Tech-forward (uses digital tools)
- Focus on specific corridors (Asia-Europe, Transpacific)

**Examples:** Niche digital forwarders, regional specialists, new market entrants.

### Outreach Strategy

**Direct contact:**
- LinkedIn outreach to operations leads
- Freight industry Slack/Discord communities
- Trade publication coverage (FreightWaves, The Loadstar)

**Value proposition:**
> "Route optimization that used to require enterprise software. Pay per query. No contracts."

### Pilot Structure

| Element | Details |
|---------|---------|
| Duration | 30 days |
| Queries | 100 free |
| Support | Direct Slack channel |
| Success metric | Used 50+ queries, positive feedback |

### Phase 1 Goals

- 10 forwarder pilots
- 5 convert to paying
- Testimonials for marketing

---

## Phase 2: API Launch & Pricing (Months 3-6)

### Public API

```
POST api.omniroute.io/v1/optimize
Authorization: Bearer {api_key}

{
  "origin": "CNSHA",
  "destination": "NLRTM",
  "cargo": {"type": "container", "size": "40ft"},
  "constraints": {"arrive_by": "2026-03-15", "priority": "cost"}
}
```

Response: Ranked route options with cost/time/reliability.

### Pricing

| Tier | Price | Included |
|------|-------|----------|
| Free | $0 | 10 queries/month, rate-limited |
| Starter | $49/month | 100 queries |
| Professional | $199/month | 500 queries |
| Business | $499/month | 2,000 queries |
| Enterprise | Custom | Unlimited, SLA, support |

**Pay-as-you-go:** $1.50/query after plan limit.

### Payment

- Fiat (Stripe): Credit card, invoicing for enterprise
- Crypto (optional): TAO, USDC at 10% discount

**No staking required.** Crypto-native pricing creates adoption friction. Offer it as a discount, not a requirement.

### Phase 2 Goals

- 50 paying customers
- $10K MRR
- <5% churn

---

## Phase 3: Corridor Specialization (Months 6-9)

### Focus Corridors

Miners naturally specialize. Market this:

| Corridor | Volume | Specialization Value |
|----------|--------|----------------------|
| China → Europe | Highest | Rail vs sea tradeoffs, Suez alternatives |
| Transpacific | High | West Coast port congestion, schedule reliability |
| Intra-Asia | Growing | Transshipment optimization |
| South America → Asia | Underserved | Limited direct options, complex routing |

### Corridor Landing Pages

```
omniroute.io/asia-europe
omniroute.io/transpacific
omniroute.io/intra-asia
```

Each page:
- Corridor-specific worked examples
- Current conditions (port delays, schedule changes)
- Testimonials from corridor specialists

### Miner Incentives

Bonus emissions for underserved corridors:
- South America routes: 1.2x multiplier
- Africa routes: 1.3x multiplier
- New corridor coverage: Discovery bonus

---

## Phase 4: Platform Integrations (Months 9-12)

### Target Platforms

| Platform Type | Examples | Integration Value |
|---------------|----------|-------------------|
| TMS (mid-market) | Cargowise, Descartes | Embedded optimization |
| Freight marketplaces | Freightos, Shifl | Route suggestions |
| Visibility platforms | Project44, FourKites | Predictive routing |

### Integration Model

```
┌─────────────────────────────────────────────────────────────────┐
│                    PLATFORM INTEGRATION                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  PLATFORM           OMNIROUTE            SHIPPER                 │
│     │                   │                    │                   │
│     │── Request ───────▶│                    │                   │
│     │                   │── Optimize ───────▶│                   │
│     │◀── Routes ────────│                    │                   │
│     │                   │                    │                   │
│     │─────────── Display in platform UI ────▶│                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Revenue share:** Platform pays wholesale ($0.75/query), marks up to users.

### Phase 4 Goals

- 2-3 platform integrations
- 50% of queries via platforms
- $50K MRR

---

## Marketing Channels

### Content Marketing

| Content Type | Frequency | Purpose |
|--------------|-----------|---------|
| Route corridor reports | Monthly | SEO, thought leadership |
| Disruption alerts | As needed | Relevance, email list |
| Case studies | Quarterly | Social proof |
| API tutorials | Ongoing | Developer adoption |

**Example content:**
- "Shanghai-Rotterdam: Rail vs Sea in Q1 2026"
- "Suez Congestion: Alternative Routes Analysis"
- "How [Forwarder] Cut Transit Time 3 Days with OmniRoute"

### Industry Presence

| Channel | Approach |
|---------|----------|
| FreightWaves | Guest articles, podcast |
| The Loadstar | News coverage |
| LinkedIn | Operations/logistics audience |
| Freight conferences | Sponsor small events initially |

### Developer Marketing

| Channel | Approach |
|---------|----------|
| GitHub | Open-source SDK, examples |
| Dev.to / Medium | Technical tutorials |
| Hacker News | Launch announcement |
| API directories | RapidAPI, ProgrammableWeb |

---

## Sales Process

### Self-Serve (Starter/Professional)

1. Sign up on website
2. Get API key
3. Free tier (10 queries)
4. Upgrade when ready

**Conversion funnel:**
- Landing page → Sign up: 5%
- Sign up → First query: 60%
- First query → Paid: 20%

### Sales-Assisted (Business/Enterprise)

1. Demo request form
2. 30-minute call
3. Pilot (100 free queries)
4. Contract negotiation
5. Onboarding

**Target:** Companies with 500+ shipments/year.

---

## Competitive Positioning

### Message

**For freight forwarders:**
> "Optimize every shipment without enterprise software costs. API-first route intelligence."

**For shippers:**
> "Know your options before you book. Real-time multi-modal route comparison."

**For platforms:**
> "Add route optimization to your product. White-label API, revenue share."

### Differentiation

| Competitor Says | OmniRoute Says |
|-----------------|----------------|
| "Enterprise-grade TMS" | "Pay per query, no contracts" |
| "Full-service forwarding" | "Just the intelligence—use any forwarder" |
| "AI-powered optimization" | "Competing algorithms, verifiable quality" |

---

## Success Metrics

### Phase 1 (Months 1-3)

| Metric | Target |
|--------|--------|
| Pilot customers | 10 |
| Queries processed | 1,000 |
| Pilot → paid conversion | 50% |

### Phase 2 (Months 3-6)

| Metric | Target |
|--------|--------|
| Paying customers | 50 |
| MRR | $10K |
| Queries/month | 5,000 |

### Phase 3 (Months 6-9)

| Metric | Target |
|--------|--------|
| Paying customers | 150 |
| MRR | $30K |
| Corridor coverage | 10 major lanes |

### Phase 4 (Months 9-12)

| Metric | Target |
|--------|--------|
| Platform integrations | 3 |
| MRR | $50K |
| Queries/month | 50,000 |

---

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Low initial query quality | Extensive pilot testing before public launch |
| Slow forwarder adoption | Generous free tier, direct sales support |
| Platform integration delays | Prioritize self-serve growth in parallel |
| Price sensitivity | Demonstrate ROI clearly (savings > cost) |

---

## Summary

**Months 1-3:** Freight forwarder pilots. Prove quality. Get testimonials.

**Months 3-6:** Public API launch. Self-serve pricing. First revenue.

**Months 6-9:** Corridor specialization. Content marketing. Scale customers.

**Months 9-12:** Platform integrations. Revenue share. Distribution leverage.

Start narrow (forwarders), prove value, expand.

---
