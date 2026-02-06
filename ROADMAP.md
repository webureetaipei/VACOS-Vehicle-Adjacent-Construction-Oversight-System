# VACOS Project Roadmap: The 90 Tasks to WACV

**Status:** Active | **Goal:** WACV 2026 Submission | **Core Tech:** SAR-GPE, TinyEngine (C++), Distributed DDP

## Phase 1: Physics-Aware Data Engine (Tasks 01-15)
- [x] **Task 01:** Project initialization, Git setup, and System Architecture Design.
- [x] **Task 02:** Procedural Generation Engine: Unit Cell (Columns + Beams logic).
- [ ] **Task 03:** Procedural Generation Engine: Full Building Assembly (Floors, Slabs, Walls).
- [ ] **Task 04:** Procedural Generation Engine: Occlusion Layers (Scaffolding & Nets).
- [ ] **Task 05:** Procedural Generation Engine: Hazard Injection (Debris & Clutter entropy).
- [ ] **Task 06:** Occlusion configuration definition (Low/Med/High parameters).
- [ ] **Task 07:** Implement Dual-Modality Ray-casting (Optical vs. SAR Physics in Open3D).
- [ ] **Task 07-B:** Simulation Sanity Check (Noise Histogram Validation vs. Real SAR).
- [ ] **Task 08:** Batch generation of 6 stratified datasets (300 scenes).
- [ ] **Task 09:** Auto-labeling logic implementation (Semantic ID mapping).
- [ ] **Task 10:** Feature extraction: LSE (Linearity/Planarity) implementation.
- [ ] **Task 11:** Feature extraction: GLE (Global Location) implementation.
- [ ] **Task 12:** Feature extraction: DPE (Density/Distance) implementation.
- [ ] **Task 13:** Feature extraction: OD (Overlap Disambiguation) implementation.
- [ ] **Task 14:** `GeometryEngine` class encapsulation & optimization.
- [ ] **Task 15:** QC & Visualization of synthetic SAR artifacts.

## Phase 2: Core Experiments & Distributed Training (Tasks 16-40)