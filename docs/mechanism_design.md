# OmniRoute Incentive & Mechanism Design: The World Brain Logistics Layer

## 1. Emission and Reward Logic: Resilient Multi-Synapse Harmonic Scoring (MSHS)
OmniRoute utilizes the 2026 Multiple Incentive Mechanism (Multi-IM) architecture to drive the transition from siloed computation to an interconnected "World Brain." Emissions are split into two harmonic pools with a dynamic fallback protector to ensure network stability regardless of external metagraph conditions.

### Pool A: The Optimization Engine (80% Base Allocation)
Rewards the **Optimization Intelligence** of the miner.
* **Focus:** The quality and efficiency of the final logistics solution (Cost, Time, and Carbon impact).
* **Validation:** Based on the mathematical optimality of the proposed route compared to the validator's baseline.

### Pool B: The Metagraph Bridge (20% Base Allocation)
Rewards **Ecosystem Interoperability** by requiring miners to use peer subnets as their primary "truth sensors."
* **Pluggable Source Examples:** 
    * **Atmospheric Inputs:** Using outputs from **SN18 (Zeus)** or equivalent weather subnets for flight-path safety.
    * **Visual Inputs:** Using **SN44 (Score)** or equivalent Vision AI subnets to process real-time terminal feeds for port congestion and yard density.
* **The Anti-Fragility & Availability Protocol:** To protect miner rewards, Pool B is subject to a dynamic waiver:
    * **Verified Outage:** If Validator consensus (>50%) determines a bridge-target is offline, the requirement is waived.
    * **Structural Gap:** If a challenge requires data for which no specialized subnet currently exists on Bittensor, the requirement is marked "Exempt."
    * **Re-Allocation:** In any "Exempt" or "Outage" state, the 20% weighting is automatically collapsed into Pool A.

---

## 2. Incentive Alignment for Miners and Validators
* **Miners:** Incentivized to build **Metagraph-Native** solvers. They are rewarded for looking "inward" to other subnets for their data needs rather than relying on centralized legacy APIs. This creates buy-side pressure for the entire ecosystem.
* **Validators:** Act as **Network Orchestrators**. They audit both the route optimality and the provenance of the data, ensuring miners are actually utilizing the peer-to-peer outputs of the metagraph.

---

## 3. Mechanisms to Discourage Low-Quality or Adversarial Behavior
* **The Bridge Tax:** If a bridge-target is active and available but the miner fails to provide a "Proof-of-Query," they forfeit their Pool B multiplier, even if the route is technically sound.
* **Anti-Oracle Spoofing:** Validators cross-reference miner inputs against the actual state of the bridge-target subnets. Discrepancies lead to immediate weight slashing.
* **Logarithmic Complexity Buffer:** To prevent "spamming" of simple routes, complex multi-modal shipments (Air -> Sea -> Rail) are weighted significantly higher than single-leg transfers.

---

## 4. Proof of Intelligence and Proof of Effort
* **Proof of Intelligence:** Requires the cognitive synthesis of disparate subnet outputs (Weather, Vision, Infrastructure) into a single, unified logistics resolution. Solving a global Traveling Salesman Problem (TSP) with dynamic, real-time constraints is an NP-hard challenge requiring sophisticated neural-heuristic hybrids.
* **Proof of Effort:** Maintaining active, low-latency bridges to multiple shifting subnet sources requires constant infrastructure maintenance and computational "work." The MSHS 200ms latency requirement ensures miners must exert significant effort in hardware and connectivity optimization.

---

## 5. High-Level Algorithm: The Resilient MSHS Pipeline
1.  **Task Assignment:** Validators broadcast a **Logistics Challenge** containing multi-modal shipment parameters and constraints.
2.  **Availability Pulse:** Validators identify currently active peer subnets and check for any **Structural Gaps** or **Outages** to set the epoch's reward weighting.
3.  **Metagraph Resolution (Miner):** Miners fetch real-time outputs from identified bridge-targets (e.g., SN18, SN44) to inform the optimization engine.
4.  **Submission:** Miners submit the optimized route (Pool A) + the cryptographic **Inter-Subnet Proofs** (Pool B).
5.  **Validation:**
    * **Step 1:** Validator checks route optimality and feasibility.
    * **Step 2:** Validator verifies the "Bridge Proofs" against the peer subnets (unless the Availability Pulse triggered a waiver).
6.  **Scoring & Allocation:** Rewards are distributed via Yuma Consensus based on the 80/20 harmonic split (or 100/0 in Degraded/Exempt states).
