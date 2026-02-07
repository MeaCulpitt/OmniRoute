# OmniRoute Miner Design: The Decentralized Logistics Intelligence (DLI) Node

## 1. Miner Tasks: The DLI Pipeline
An OmniRoute Miner operates as a high-frequency forecasting and optimization engine. Its primary objective is to synthesize raw logistics requests with real-time metagraph data to produce the most efficient possible transit path.

### Task 1: Multi-Synapse Signal Selection (The Strategic Bridge)
Miners are incentivized to move beyond siloed data. To maximize competitive performance, miners selectively query external subnets to gather environmental context that traditional logistics systems lack. 

* **Atmospheric Intelligence:** Strategically querying weather subnets (e.g., SN18) to calculate the risk of flight-path safety or sea-state delays.
* **Port-Side Visual Proofs:** Utilizing Vision AI subnets (e.g., SN44) to process satellite or terminal feeds for real-time verification of port congestion or yard density.
* **Predictive Alpha Discovery:** Miners must determine which subnets provide the highest "Alpha" (predictive edge) for a specific route. For example, a miner may prioritize port-side vision for a sea-freight challenge but pivot to weather intelligence for an air-freight request to gain a scoring advantage over competitors.

### Task 2: Temporal Inference & Manifold Resolution
Once the most relevant signals are ingested, the miner runs a local **Heuristic-Neural Hybrid model** to solve the multi-modal Traveling Salesman Problem (TSP). The miner generates a logistics manifold representing the optimal balance of speed, cost, and reliability across different transit legs (Air, Sea, Rail, Road).

### Task 3: Local Resilience & Graceful Degradation
While the Validator governs the global emission state, the Miner is responsible for local stability and adaptation:
* **Local Outage Detection:** Miners must implement timeout logic for all external queries. If a chosen signal source fails to respond within 100ms, the miner must pivot to a "Safe Mode" optimization using local caches or secondary data to protect their Pool A Accuracy score.
* **Redundant Sourcing:** Elite miners maintain "Hot Swaps" for data sources. If a primary signal source is lagging, the miner should query a secondary verified source to maintain their Bridge Fidelity multiplier and avoid latency penalties.

---

## 2. Expected Input → Output Format
OmniRoute utilizes the `LogisticsSynapse` for all communication between neurons.

### Input Format (LogisticsSynapse)
The miner receives a high-dimensional request packet:
```json
{
  "shipment_id": "UUID-V4",
  "origin": {"lat": 34.05, "lon": -118.24},
  "destination": {"lat": 40.71, "lon": -74.00},
  "transit_mode": ["sea", "rail", "last-mile"],
  "priority": "high",
  "constraints": ["no-cold-chain", "customs-bonded"]
}
