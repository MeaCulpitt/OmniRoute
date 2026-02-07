# OmniRoute Validator Design: The Ground-Truth Authority

## 1. Scoring and Evaluation Methodology
The OmniRoute Validator utilizes a multi-stage pipeline to convert raw miner outputs into cryptographic weights. The process is governed by **Multi-Synapse Harmonic Scoring (MSHS)**, which balances technical route optimization with ecosystem interoperability.

### A. The Scoring Formula
The final score ($S$) for a miner is the harmonic mean of two distinct reward pools:
$$S = \frac{2 \cdot (S_{PoolA} \cdot S_{PoolB})}{S_{PoolA} + S_{PoolB}}$$

* **Pool A: Optimization Accuracy (80%):** Evaluates the efficiency of the logistics route (Cost, Time, Carbon). The Validator runs a local baseline optimization to determine the "Pareto Frontier" and scores miners based on their proximity to this frontier.
* **Pool B: Metagraph Bridge (20%):** Evaluates the miner's use of peer subnets (e.g., SN18 for weather, SN44 for vision). Miners must provide "Proof-of-Query" hashes that match the Validator's own metagraph state.

### B. Anti-Fragility & Exception Handling
The Validator serves as the fail-safe for the subnet.
* **Outage Detection:** Every epoch, the Validator pings bridge-targets. If >50% of the validator pool reports a target (e.g., SN18) as unreachable, the Validator triggers **"Degraded Mode,"** waiving the Pool B requirement and re-allocating its 20% weight to Pool A.
* **Service Gap Logic:** If a challenge requires a data type for which no high-fidelity subnet exists, the Validator marks that field as **"Exempt"** in the synapse metadata, protecting miners from unfulfillable requirements.

---

## 2. Evaluation Cadence
The Validator operates on a continuous, high-frequency cycle to ensure real-time logistics utility:

* **The Pulse (Every 10 Blocks):** The Validator performs a health check on the metagraph to identify active vs. exempt bridge targets.
* **The Challenge (Every 50 Blocks):** The Validator broadcasts a new **LogisticsSynapse** containing a unique set of shipment parameters (Origin, Destination, Constraints).
* **The Verification Window (200ms):** Miners must respond within this window. Late responses are automatically assigned a score of 0 to incentivize machine-speed reliability.
* **The Weight Sync (Every Tempo - 360 Blocks):** The Validator aggregates scores across all challenges in the tempo and submits the normalized weight vector to the Subtensor blockchain via Yuma Consensus.

---

## 3. Validator Incentive Alignment
Under the **dTAO (Dynamic TAO)** framework, the Validator’s success is directly tied to the real-world utility of the OmniRoute subnet
