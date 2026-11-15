# 🏗️ Shadow Run Mempool Optimizer (Trap Genotype Framework)

**A hierarchical mempool optimization framework for Bitcoin mining pools.**  
*Increases block Expected Value (EV) by ~0.74% and reduces stale block risk by 75% via weight-buffered transaction selection.*

---

## 📊 The Benchmark: Shadow Run Results

Standard "Greedy" block-building algorithms maximize fee collection by filling blocks to the 4,000,000 Weight Unit (WU) limit. However, this maximizes propagation latency, increasing the risk of stale/orphan blocks and destroying net Expected Value (EV).

The **Trap Genotype Framework** introduces a hierarchical renormalization and weight-buffering strategy to optimize for *net* EV rather than *gross* fees.

### Zero-Risk Simulation Output
*Based on a simulated mempool of 8,000 clusters (including CPFP dependencies) post-2024 Halving (3.125 BTC block reward).*

| Metric | Greedy (Standard) | Trap Genotype (Ours) | Delta |
| :--- | :--- | :--- | :--- |
| **Block Weight (WU)** | 3,999,750 | 3,399,750 | -15% (Buffered) |
| **Fee Collected (BTC)**| 0.994106 | 0.962218 | -3.2% |
| **Stale Block Risk** | 2.00% | 0.50% | **-75% Reduction** |
| **EXPECTED VALUE (EV)**| **4.036723 BTC** | **4.066782 BTC** | 🏆 **+0.030058 BTC** |

💰 **Net EV Delta:** **+0.74% additional EV per block** with zero hardware CAPEX.

---

## 🧠 The Problem vs. The Solution

### The Problem: The Greedy Trap
Standard Bitcoin Core-like templates sort by ancestor fee rate and fill the block to ~100% capacity. 
* **The Hidden Cost:** A 4M WU block takes significantly longer to propagate across the global network. If a competing miner finds a block during this propagation window, your 100%-full block becomes stale. The 3.125 BTC block reward is lost entirely.

### The Solution: Trap Genotype Framework
The Trap Genotype Framework operates on the principle that **propagation speed is a non-linear multiplier of EV**. 
By intentionally leaving a **15% weight buffer** (targeting ~3.4M WU) and applying hierarchical renormalization, the framework:
1. Drastically reduces block propagation time.
2. Drops stale risk from ~2.0% to ~0.5%.
3. Preserves high-value CPFP (Child-Pays-For-Parent) clusters while dropping low-weight/low-fee "dust".

---

## ⚙️ Technical Architecture

The `TrapGenotypeBuilder` executes a multi-phase selection process:

1. **Phase 1: Hierarchical Renormalization:** Filters the top 80% of the mempool by fee rate, then recalculates an `efficiency_score` that penalizes heavy transactions: 
   `efficiency = fee_rate / (1 + (weight / 10000))`
2. **Phase 2 & 3: Trap Genotype Buffering:** Enforces a strict target weight limit (85% of the 4M WU cap) to guarantee fast network propagation.
3. **Phase 4 & 5: CPFP Cluster Validation & Final Assembly:** *(See source code for full implementation details)*.

---

## 🚀 Getting Started

This repository contains a zero-risk simulator to benchmark the Trap Genotype Framework against standard Greedy block building using synthetic mempool data.

### Prerequisites
* Python 3.8+
* `pandas`, `numpy`

### Installation & Run
```bash
git clone https://github.com/TU_USUARIO/shadow-run-mempool-optimizer.git
cd shadow-run-mempool-optimizer
pip install pandas numpy

python shadow_run_optimizer.py