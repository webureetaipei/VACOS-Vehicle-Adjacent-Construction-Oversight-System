# VACOS System Design Overview

**Status:** Draft (Phase 1) | **Author:** Tsung Lung Yang | **Target:** WACV 2026

This document outlines the architectural decisions, trade-offs, and optimization strategies for the VACOS project. It serves as the master blueprint (High-Level Design).

## 1. Embedded Optimization: The "Tiny-Edge" Architecture
**Goal:** Deploy Point Transformer models on edge devices (e.g., Jetson Orin) with **<50ms latency** and **<512MB RAM**.

### 1.1 Core Implementation Strategy
We reject standard heavy runtimes in favor of a customized C++ approach.
* **Zero-Malloc Runtime (Static Arena):**
    * **Mechanism:** We implement a Linear Memory Arena. All point cloud buffer offsets are pre-calculated.
    * **Benefit:** Eliminates runtime allocation overhead and memory fragmentation.
* **Patch-Based Streaming Inference:**
    * **Mechanism:** Instead of processing the full scene (N=1M points), we stream overlapping 3D patches.
    * **Benefit:** Reduces peak memory from $O(N^2)$ global attention to $O(P^2)$ local attention.
* **Sparsity-Aware Computation:**
    * **Mechanism:** Using GPE's `Density` feature to detect and skip empty patches (e.g., safety nets, air).
    * **Benefit:** Reduces FLOPs by ~40% in sparse construction scenes.
* **Custom C++ Kernels:**
    * **Mechanism:** Hand-written CUDA kernels for KNN search and PCA (GPE extraction).

### 1.2 Bottlenecks & Trade-offs
* **Bottleneck Solved:** **The Memory Wall.** Standard Transformers on point clouds typically incur >4GB VRAM overhead; our engine targets <512MB.
* **Trade-off:** **Global Context vs. Memory.**
    * *Decision:* We sacrifice Global Self-Attention (too heavy) in favor of **Global Positional Encoding (GPE)** features + Local Patch Attention to achieve edge feasibility.

## 2. Perception System: SAR + GPE
**Goal:** Solve "Occlusion Blindness" in standard vision systems.

### 2.1 Model Architecture
* **Input:** SAR-Derived Point Cloud (XYZ + Intensity + Speckle Noise).
* **GPE Module (Ours):** Extracts 17 explicit geometric features (Linearity, Planarity, Verticality).
* **Reasoning:** Geometry (Structure) is invariant to SAR noise, whereas Texture is not. GPE acts as a "Physics Filter".

### 2.2 Bottlenecks & Trade-offs
* **Bottleneck Solved:** **Sim-to-Real Gap.** Pure deep learning overfits to synthetic noise.
* **Trade-off:** **Feature Engineering vs. End-to-End.**
    * *Decision:* We use explicit features (GPE) to ensure the model learns *physics* (shapes) rather than *artifacts*, improving robust transferability.

## 3. Distributed Infrastructure
**Goal:** Scale training to process 300+ dense point cloud scenes efficiently using multi-GPU clusters.

### 3.1 Distributed Architecture
* **Distributed Data Parallel (DDP):**
    * **Mechanism:** Utilizes `torch.distributed` with NCCL backend to synchronize gradients across multiple GPUs.
    * **Benefit:** Linearly scales training throughput; enables processing of our large-scale synthetic dataset.
* **Gradient Accumulation:**
    * **Mechanism:** Accumulates gradients over $k$ micro-batches before a robust optimizer step.
    * **Benefit:** Simulates a large effective batch size (e.g., 64 or 128) even when GPU VRAM limits physical batch size to 1 or 2 scenes per card.
* **Decoupled Data Loading (Producer-Consumer):**
    * **Mechanism:** Uses shared-memory caching and pre-fetching workers to decouple HDF5 I/O from GPU computation.
    * **Benefit:** Prevents GPU starvation (idle time) caused by heavy geometric feature extraction (GPE) on the CPU.

### 3.2 Bottlenecks & Trade-offs
* **Bottleneck Solved:** **Effective Batch Size Limitations.** Point clouds are memory-heavy ($N \approx 10^5$), limiting standard batch training. Gradient accumulation solves this without requiring enterprise-grade 80GB GPUs.
* **Trade-off:** **Synchronization Overhead vs. Convergence.**
    * *Decision:* We synchronize gradients less frequently (large accumulation steps) to reduce network overhead, accepting a slightly delayed weight update trajectory which is mitigated by careful learning rate scheduling.

## 4. Data Engine Strategy: The "Physics-Aware" Dataset
**Goal:** Auto-generate high-quality synthetic data to overcome the "Real-World Data Scarcity" and "Safety constraints".

### 4.1 Data Pipeline
* **Generation (SketchUp):** Modeling rigorous structural classes (Beams, Columns).
* **Simulation (Open3D):** Dual-modality Ray-casting (Optical vs. SAR).
* **Storage (HDF5):** Data is serialized into HDF5 chunks, optimized for high-throughput I/O during DDP training.

### 4.2 Bottlenecks & Trade-offs
* **Bottleneck Solved:** **I/O Blocking.** Loading thousands of small .obj/.ply files kills GPU training utilization.
    * *Solution:* Aggregate data into large HDF5 chunks (2GB each) for sequential reads.
* **Trade-off:** **Simulation Fidelity vs. Speed.**
    * *Decision:* We use Ray-casting with statistical noise (Fast) instead of full Maxwell equation solving (Too Slow), validated by statistical distribution checks (Task 07-B).

## 5. Success Criteria (Engineering KPIs)
We define the project's success through strict quantitative metrics.

### 5.1 System Performance KPIs
| Component | Metric | Target | Baseline (PyTorch) |
| :--- | :--- | :--- | :--- |
| **TinyEngine** | End-to-End Latency | **< 50ms** | ~200ms |
| **TinyEngine** | Peak Memory | **< 512MB** | > 4GB |
| **Data Engine** | Generation Rate | **10 Scenes/min** | Manual |

### 5.2 Research Success Metrics (WACV)
* **Recall (High Occlusion):** Achieve **> 85% Recall** on non-fixed hazards under high occlusion (vs. < 30% for Optical).
* **Sim-to-Real Validity:** Noise distribution matching real SAR datasets with > 90% correlation.
