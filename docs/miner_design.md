# OmniRoute Subnet: Miner Design

## 1. Miner Tasks
The OmniRoute Miner is a high-performance forecasting node responsible for the following:
* **Data Synthesis:** Orchestrating real-time feeds from AIS, satellite weather, and port manifest APIs.
* **Inference Execution:** Solving `LogisticsSynapse` challenges by predicting delivery times and risk factors.
* **Model Evolution:** Continuously retraining local models to adapt to shifting global supply chain conditions.
* **Uptime Management:** Maintaining 24/7 availability to avoid "Trust Decay" in the validator's moving average.

## 2. Expected Input → Output Format
Miners must process and return data according to the standardized protocol.

### Input Format (LogisticsSynapse)
| Field | Type | Description |
| :--- | :--- | :--- |
| `shipment_id` | `str` | Unique tracking identifier. |
| `current_location` | `List[float]` | `[latitude, longitude]` of the asset. |
| `destination` | `List[float]` | `[latitude, longitude]` of the destination. |
| `transit_type` | `str` | Mode of transport (e.g., "Maritime", "Rail"). |

### Output Format (Inference Response)
| Field | Type | Description |
| :--- | :--- | :--- |
| `predicted_eta` | `str` | ISO 8601 formatted arrival time. |
| `risk_score` | `float` | 0.0 to 1.0 probability of a "Risk Event." |
| `reasoning` | `str` | Human-readable logic for the prediction. |

## 3. Performance Dimensions
Validators utilize a multi-objective scoring function to rank miners. Failure to maintain top-tier performance leads to UID pruning.

### A. Accuracy (Weight: 0.70)
* **Metric:** Mean Absolute Error (MAE).
* **Target:** Error rate < 2% of total transit time.
* **Logic:** The difference between the `predicted_eta` and the actual `Ground Truth` timestamp recorded by the validator.

### B. Latency (Weight: 0.20)
* **Metric:** Response Time (ms).
* **Target:** < 1,500ms.
* **Logic:** Speed is a proxy for compute efficiency. Miners that respond faster than the network median receive a tiered multiplier.

### C. Reliability/Consistency (Weight: 0.10)
* **Metric:** Moving Average Error Variance.
* **Target:** Stable, low-deviation performance across 1,000 blocks.
* **Logic:** Discourages "lucky guessing" or adversarial behavior. Consistent miners build higher V-Trust scores.
