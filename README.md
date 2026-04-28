# Logistics Stress-Test: Stochastic Policy Engine

Logistics operations often run blind. A fleet dispatcher managing 5–15 vehicles and 50–200 daily deliveries cannot know, at decision time, whether a specific routing choice will cause a cascade of late deliveries. This project provides a risk-free environment to stress-test operating policies before deploying them in the real world.

---

## 🚩 The Problem: The "Average Case" Trap

Most logistics software optimizes for the **expected case**—average traffic, no breakdowns, and smooth demand. However, real-world failures happen in the **tail of the distribution**.

1.  **Uncertainty is Ignored:** Planning for "average" conditions leads to total failure during the **p95 case** (the 19th-worst day out of 20), where SLA penalties accrue.
2.  **Blind Policy Adoption:** Companies choose between "Static" or "Dynamic" routing based on intuition or competitors rather than simulated data.
3.  **Misidentified Bottlenecks:** Managers often invest in more vehicles when the real issue is traffic variability or poor buffer-time management.
4.  **Static Optimization:** Actionable parameters like "buffer time" are set once and forgotten, as real-world A/B testing would take months.

---

## 🚀 The Solution: A Four-Layered Approach

### 1. Stochastic Simulation Engine
Instead of one "ideal" answer, the engine runs **1,000 Monte Carlo simulations**. Each run independently samples variables to capture the heavy "right tail" of logistics risks:
* **Traffic Delays:** Lognormal distribution (captures severe, occasional congestion).
* **Demand Spikes:** Poisson process (captures random order arrivals).
* **Mechanical Failure:** Bernoulli draws per trip (captures random breakdowns).

### 2. Policy Side-by-Side Comparison
The engine implements and compares three distinct operating strategies against identical scenarios:
* **Static:** Fixed assignment with no mid-operation adaptation.
* **Dynamic:** Reassigns vehicles if modeled delay risk exceeds a configurable threshold.
* **Priority:** Reorders the dispatch queue by delivery value and urgency (Express first).

### 3. Sensitivity Analysis
The system "sweeps" parameters (fleet size, traffic variability, breakdown probability) across their full range. It ranks variables by their **p95 delay delta**, showing managers exactly which lever (e.g., "Buffer Time" vs. "Fleet Size") actually moves the needle on performance.

### 4. Grid Search Optimization
The engine runs an **81-configuration grid search** over the two most actionable dispatch controls: **Buffer Time %** and **Reroute Threshold %**. It calculates a weighted score: 
$$Score = (0.6 \times p95\text{ delay}) + (0.4 \times \text{cost proxy})$$
This identifies the "sweet spot" for efficiency in minutes, not months.

---

## 🛠️ Technical Architecture

| Component | Logic |
| :--- | :--- |
| **Data Layer** | 7 synthetic routes with distance, travel time, and variability parameters. |
| **Scenario Engine** | Preset configurations for **Normal, Peak Demand, Storm,** and **Crisis** modes. |
| **Sim Engine** | Event-driven Monte Carlo modeling vehicle queuing and capacity constraints. |
| **Optimization** | Automated grid search to minimize the tradeoff between lateness and cost. |
| **Metrics** | Real-time calculation of p95/p99 delay, SLA compliance, and fuel/penalty costs. |

---

## 📈 How to Explore
1.  **Simulation Tab:** Run the baseline to see your current p95 delay metrics.
2.  **Policy Comparison:** Compare the tradeoff curves of Static vs. Dynamic routing.
3.  **Sensitivity Analysis:** Identify whether traffic or fleet size is your primary bottleneck.
4.  **Optimization:** Let the engine recommend the ideal Buffer/Reroute combination for your specific scenario.
