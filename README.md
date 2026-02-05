# VACOS: Vehicle-Adjacent Construction Oversight System

**Target:** WACV 2026 | **Status:** Active Development (Phase 1)

## Why This Matters
Construction zones adjacent to active transportation corridors face a critical safety gap: **Visibility-Dependent Failure**. Standard optical sensors (Cameras/LiDAR) fail when obstructed by safety nets, scaffolding, and dust.
**VACOS** addresses this by bridging **Synthetic Aperture Radar (SAR)** sensing with **Geometric Deep Learning**. It introduces a **Physics-Aware Perception System** that "sees through" occlusion using structural inductive biases.

## Reviewer Guide (System Architecture)
Select the Design Document that matches your domain:

### 1. The Master Blueprint (Start Here)
👉 `docs/design/system_design_overview.md`
**Content:** High-Level Architecture, Bottlenecks, Trade-offs, and Engineering KPIs.

### 2. For Embedded Systems
👉 `docs/design/embedded_system_design.md` (Planned)
**Content:** **TinyEngine** implementation, C++ Custom Kernels, Sparsity-Aware Inference.

### 3. For Distributed Systems / MLOps
👉 `docs/design/distributed_system_design.md` (Planned)
**Content:** PyTorch DDP Training, Synthetic Data Pipeline (HDF5).

### 4. For Research / Algorithms
👉 `docs/design/geometry_aware_perception_system_design.md` (Planned)
**Content:** **Geometric Positional Encoding (GPE)**, 17-dim Feature Definitions.
