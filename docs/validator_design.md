# OmniRoute Subnet: Validator Design

## 1. Scoring and Evaluation Methodology
Validators are the arbiters of truth within OmniRoute, ensuring that only the most accurate predictive models are rewarded.

### The Scoring Equation
A miner's total score ($W$) for a given epoch is a weighted average of three components:
- **Accuracy (70%):** Exponentially decayed score based on the delta between `predicted_eta` and `Actual_Arrival`.
- **Risk Prediction (20%):** A binary or probabilistic score rewarding the correct identification of "Black Swan" events or bottlenecks.
- **Latency (10%):** A speed bonus for miners who return inference results within the first 500ms of the challenge window.

### Weight Clipping & Yuma Consensus
To prevent collusion, OmniRoute utilizes **Stake-Weighted Median** scoring. Outlier weights from any single validator are "clipped" if they deviate significantly from the consensus of the top 64 validators by stake.

## 2. Evaluation Cadence
Logistics validation is inherently asynchronous. The validator manages this through a dual-buffer system:
- **Challenge Interval:** Challenges are issued every 100 blocks to ensure continuous miner engagement.
- **Ground Truth Verification:** Scores are held in a "Pending" state until real-world delivery confirmation (AAT) is received via trusted Oracles or IoT integrations.
- **Weight Submission:** Finalized weight vectors are committed to the blockchain every 360 blocks (1 Tempo).

## 3. Validator Incentive Alignment
Validators are incentivized to perform high-fidelity audits through three primary mechanisms:
- **V-Trust Optimization:** Validators maximize their dividends by aligning their scoring with the honest consensus. Misalignment results in "Slashed Dividends."
- **Alpha Token Appreciation:** High-quality validation improves the subnet's utility, driving demand for the OmniRoute Alpha token ($\alpha$), which validators earn as their primary reward.
- **Commercial API Rights:** Validators with high stake can monetize their "Gateway Access," selling the subnet's predictive outputs to 3rd-party logistics providers.

## 4. Hardware & Security Requirements
- **Stake Requirement:** Minimum 1,000 TAO (or equivalent Alpha) to set weights.
- **Oracle Integration:** Must maintain secure, low-latency hooks into global shipping APIs (e.g., Terrestrial AIS, FlightRadar24).
- **Security:** Validators must use `commit-reveal` weight submission to prevent "Weight Copying" by adversarial peers.
