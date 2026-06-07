# LEAP-O Extension Pipeline

<p align="center">
  <img src="workflow image.png" alt="MediMate Overview" width="100%"/>
</p>
Project Description

**Inspired by:** [LEAP-O: Learning to Predict Dynamic Obstacles for Safe Trajectory Planning](https://par.nsf.gov/biblio/10670472)  
**Lab:** Intelligent Cyber-Physical Systems (iCPS) Laboratory — Prof. Truong Nghiem, UCF  
**Platform:** Google Colab (Python) | **Notebook:** `LEAP_O_Extension_Pipeline.ipynb`

---

## Overview

The **LEAP-O Extension Pipeline** is a reproducible Google Colab notebook that implements the core learning-and-planning framework from the LEAP-O paper. It enables a mobile robot to **learn obstacle motion online**, **predict future positions with uncertainty**, and **generate collision-free trajectories** in dynamic 2D environments — without prior knowledge of obstacle dynamics.

The pipeline bridges Gaussian Process (GP) regression, Recurrent Neural Networks (RNN), and receding-horizon stochastic optimization into a single end-to-end workflow suitable for future research extensions.

---

## Problem Statement

Autonomous robots must plan safe paths while minimizing travel cost and avoiding collisions. In dynamic environments, obstacle motions (e.g., pedestrians, vehicles) are unpredictable and only partially observable within a limited detection range \(R_d\). LEAP-O addresses this by learning obstacle displacements from streaming observations and embedding probabilistic predictions into trajectory planning.

---

## Workflow (6 Stages)

| Stage | Module | Description |
|-------|--------|-------------|
| **1** | Obstacle Simulation & Detection | Generates circular/sinusoidal obstacle trajectories (Paper Scenarios 1 & 2). Robot collects position measurements only when obstacle is within \(R_d = 20\) m. |
| **2** | GP Displacement Regression | Models horizontal/vertical displacements \(\varepsilon_x, \varepsilon_y\) using GPyTorch. Produces posterior mean and variance for each future time step (Eq. 6–9). |
| **3** | GRU-RNN Mean Refinement | A shared GRU network refines GP mean functions once sufficient data (\(n_0 = 6\)) is collected. Compared against a **GP-only baseline** (constant mean). |
| **4** | Uncertainty Propagation | Propagates displacement uncertainty to future obstacle positions \(\bar{p}^o_{i\|t}\) with growing variance over horizon \(H = 8\) (Eq. 11–13). |
| **5** | Receding-Horizon Planner | Formulates collision avoidance as a chance-constrained optimization with confidence \(\varsigma = 0.95\). Robot detours when predicted uncertainty regions overlap its path (Eq. 19). |
| **6** | Evaluation & Visualization | Computes RMSE between predicted and ground-truth obstacle positions. Plots uncertainty regions, reference path, and safe robot trajectory. |

---

## Key Results

- **GP+RNN outperforms GP-only** in prediction accuracy (mirrors Table I in the paper).
- Robot successfully avoids obstacles while returning to the reference path after clearance.
- Orange uncertainty ellipses shrink as more observations are collected online.

---

## Technical Stack

`Python 3.10` · `PyTorch` · `GPyTorch` · `NumPy` · `SciPy` · `Matplotlib` · Google Colab (GPU optional)

---

## Future Extension Hooks

| Extension | Implementation Path |
|-----------|---------------------|
| Multi-obstacle (Scenario 3) | Run predictor per obstacle; merge constraints in planner |
| Real-world sensor data | Replace simulator with CSV/ROS detection logs |
| Full NLP solver | Integrate CasADi or CVXPY for exact Eq. (19) formulation |
| Julia parity | Benchmark against [DDDynObs](https://github.com/EasternBoy/DDDynObs) reference |
| Abrupt motion patterns | Add jerk/acceleration-change test scenarios |
