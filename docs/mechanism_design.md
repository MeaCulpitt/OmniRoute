# OmniRoute Subnet: Incentive & Mechanism Design

## Emission and Reward Logic
OmniRoute utilizes the **Dynamic TAO (dTAO)** emission model, a core feature of the Bittensor 2026 architecture. Every block, the subnet receives a portion of the global TAO supply based on the market-driven demand for its specific **Alpha token ($\alpha$)**. 

* **Emission Split:** Rewards are distributed according to the standard Bittensor protocol: **41% to Miners**, **41% to Validators**, and **18% to the Subnet Owner**.
* **Weighted Rewards:** TAO is not distributed equally. It is allocated using a **Softmax distribution** across consensus-adjusted weights. Miners in the top decile of predictive accuracy receive exponentially higher rewards, while low-tier miners face potential reward dilution or pruning.
* **Recycle Mechanism:** To register on the OmniRoute subnet, participants must "recycle" (burn) a dynamic amount of TAO. This ensures that only miners with sufficient capital and confidence in their "effort" enter the network, creating a natural floor for the quality of participants.

## Incentive Alignment for Miners and Validators
The mechanism is designed to create a "Nash Equilibrium" where the most profitable action for any participant is to provide the highest quality intelligence.

* **Miners (Incentivized Accuracy):** Miners act as competitive AI labs. Because rewards are tied to predictive accuracy (Lower Mean Absolute Error), miners are incentivized to invest in superior hardware (NVIDIA H200s/B200s) and high-fidelity data feeds (Satellite, IoT, and Private Port APIs).
* **Validators (Incentivized Integrity):** Validators are rewarded based on **V-Trust (Validator Trust)**. To maximize their dividends, validators must reach consensus with the majority of the stake-weighted network. If a validator attempts to "shill" a poor-performing miner or manipulate scores, their weight will be ignored by the Yuma Consensus, and their rewards will be slashed.
* **Staker Alignment:** As the subnet's predictive power saves the industry money, external logistics firms stake TAO into OmniRoute's Alpha token. This increases the total emission pool, making it more profitable for miners to provide even deeper intelligence.

## Mechanisms to Discourage Low-Quality or Adversarial Behavior
OmniRoute employs several rigorous defenses to maintain the integrity of the "Global Logistics Brain":

* **Commit-Reveal Scheme:** To prevent "Weight Copying" (where a miner simply copies the $ETA$ of a top-performing peer), miners must submit a cryptographic hash of their prediction first. The actual prediction is revealed only after the "Commit" window closes.
* **Synthetic Probing:** Validators periodically inject "Synthetic Shipments" into the data stream—shipments where the outcome is already known to the validator. If a miner fails to predict these known outcomes accurately, it reveals a lack of model depth or a "guessing" strategy, resulting in an immediate score penalty.
* **Dynamic Pruning:** In the competitive slot environment, the bottom 5% of miners (based on a rolling 7-day average of accuracy) are automatically deregistered. This ensures that stagnant models are replaced by new, potentially more innovative miners.

## Proof of Intelligence and Proof of Effort
This design qualifies as a **Proof of Intelligence (PoI)** because the task—forecasting global logistics—is computationally non-trivial and cannot be solved by brute-force hashing. 

* **Intelligence:** Miners must perform "Multi-Modal Feature Fusion," combining structured data (GPS coordinates) with unstructured data (weather patterns, geopolitical news, and port congestion sentiment) to generate a single, high-accuracy $ETA$.
* **Effort:** The "Proof of Effort" is demonstrated through the continuous maintenance of low-latency API connections and the constant retraining of models to account for "Black Swan" events. A miner who does not exert continuous effort to update their data ingestion will be rapidly out-benchmarked and pruned.

## High-Level Algorithm
The OmniRoute operational loop follows a strict block-synced cadence:

1.  **Task Assignment:** Validators broadcast a `LogisticsSynapse` challenge to the subnet's Axons, containing a batch of anonymized shipment IDs, current coordinates, and destination targets.
2.  **Submission:** Miners process the challenge and return an **Inference Packet** containing:
    * `predicted_eta`: ISO 8601 timestamp.
    * `risk_score`: A float (0.0 - 1.0) representing the probability of a delay > 12 hours.
    * `confidence_interval`: The model's internal variance for that specific prediction.
3.  **Validation:** Validators hold these predictions until "Ground Truth" is established (either through a port ping or final delivery confirmation).
4.  **Scoring:** Validators apply a **Logarithmic Error Penalty**:
    $$Score = \max(0, 1 - \log_{10}(1 + |Actual_{time} - Predicted_{time}|))$$
5.  **Reward Allocation:** The resulting scores are normalized across the subnet and submitted to the Subtensor blockchain. The **Yuma Consensus** aggregates these weights to trigger the automatic distribution of TAO and Alpha tokens.
