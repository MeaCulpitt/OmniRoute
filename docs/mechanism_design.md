# OmniRoute Incentive & Mechanism Design: The World Brain Logistics Layer

## 1. Emission and Reward Logic: Resilient Multi-Synapse Harmonic Scoring (MSHS)
OmniRoute utilizes the 2026 Multiple Incentive Mechanism (Multi-IM) architecture to drive the transition from siloed computation to an interconnected "World Brain." 

### Pool A: The Optimization Engine (80% Base Allocation)
Rewards the **Optimization Intelligence**.
* **Focus:** The quality of the final logistics solution (Route Efficiency, Cost, and Carbon impact).
* **Validation:** Does the proposed route work? Is it the most efficient possible path given the constraints?

### Pool B: The Metagraph Bridge (20% Base Allocation)
Rewards **Ecosystem Interoperability**.
* **Focus:** Leveraging the outputs of the Bittensor metagraph as the primary data sources for the route.
* **The "Bridge" Requirement:** Miners are rewarded for using other subnets as their verifiable inputs. This ensures that OmniRoute's intelligence is powered by the Bittensor network, not centralized third-party APIs.
* **Pluggable Source Examples:**
    * **Atmospheric Inputs:** Using **SN18 (Zeus)** outputs for real-time flight-path safety and weather-routing.
    * **Infrastructural Inputs:** Leveraging **SN44 (Score)** to process satellite or terminal camera feeds for real-time verification of port congestion, quay crane availability, and yard container density.
* **The Anti-Fragility Fallback:** If a validator consensus (>50%) determines that a bridge-target subnet is offline, the Pool B requirement is waived, and 100% of rewards are re-allocated to Pool A to maintain network continuity.

---

## 2. Incentive Alignment for Miners and Validators
* **Miners:** Incentivized to build **Metagraph-Native** solvers. Instead of looking "outward" to legacy APIs, miners are rewarded for looking "inward" to other subnets for their data needs.
* **Validators:** Act as **Network Orchestrators**. They audit not just the route, but the provenance of the data, ensuring miners are actually utilizing the peer-to-peer outputs of the metagraph.

---

## 3. Mechanisms to Discourage Low-Quality or Adversarial Behavior
* **The Bridge Tax:** If a miner submits a route that relies on data but cannot provide a "Proof-of-Query" from an internal Bittensor subnet, they forfeit their Pool B multiplier, even if the route is technically sound.
* **Anti-Oracle Spoofing:** Validators verify that the miner's inputs match the current state of the bridge-target subnets (e.g., SN18). Discrepancies lead to immediate weight slashing.

---

## 4. Proof of Intelligence and Proof of Effort
* **Proof of Intelligence:** Requires the cognitive synthesis of disparate subnet outputs (Weather, Storage, Logistics) into a single, unified resolution. 
* **Proof of Effort:** Maintaining active, low-latency bridges to multiple shifting subnet sources is a physical and computational "work" requirement that prevents passive or static mining.

---

## 5. High-Level Algorithm
1.  **Task Assignment:** Validators broadcast a **Logistics Challenge**.
2.  **Bridge Pulse:** Validators identify which peer subnets are currently viable "truth sources" for the challenge.
3.  **Metagraph Resolution (Miner):** Miners fetch real-time outputs from identified subnets (e.g., SN18, SN75) to calculate the route.
4.  **Submission:** Miners submit the route (Pool A) + the **Inter-Subnet Proofs** (Pool B).
5.  **Validation:** Validator checks route optimality and verifies that the miner actually utilized the outputs of the specified peer subnets.
6.  **Scoring & Allocation:** Rewards are distributed based on the 80/20 harmonic split.
