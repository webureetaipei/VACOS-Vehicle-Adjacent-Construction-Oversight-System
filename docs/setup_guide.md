# VACOS Environment Setup Guide

**Status:** Updated for Phase 1 (Procedural Generation)

## 1. System Prerequisites (Task 01)
Before starting, ensure your host meets the following requirements to support the hybrid development workflow.

* **Host OS:** Windows 10/11 (Development & Visualization).
* **Subsystem:** WSL2 enabled with Ubuntu 20.04/22.04 LTS (Core Execution Environment).
* **GPU:** NVIDIA RTX GPU (Driver 535.xx+ installed on Windows Host).

### Project Structure Initialization
The standard research directory structure established in Task 01:
* `src/sim`: Contains the Python Procedural Generation Engine (`generate_structure.py`).
* `src/tiny_vacos`: Reserved for C++ Embedded Engine.
* `src/vacos_model`: Reserved for PyTorch Models.
* `data/raw_sketchup`: Stores generated `.obj` artifacts (despite the name, these are now Python-generated).

## 2. Procedural Engine Environment (Task 02)
Instead of manual modeling, we use a Python-based pipeline. This section details how to set up the environment to run the **Unit Cell Generator**.

### 2.1 Create Conda Environment
We use Miniconda to manage dependencies. Run the following commands in your **WSL2 terminal**:

```bash
# 1. Create the environment
conda create -n vacos-edge python=3.9 -y

# 2. Activate the environment
conda activate vacos-edge
```
### 2.2 Install Core Dependencies
Install the geometry processing libraries required for the Procedural Engine.

```bash
# Install Trimesh (Geometry), NumPy (Math), and Scipy (Sparse Matrices)
pip install trimesh numpy scipy
```

### 2.3 Verification: Generating the Unit Cell
To verify the setup is correct, run the generation script created in Task 02.

```bash
# Run the generator
python src/sim/generate_structure.py
```

**Expected Output:**

🏗️ Generating Structure...

✅ Created 2 Columns at (0,0) and (6.0,0)

✅ Created Beam connecting them (Length: 5.40m)

🎉 Scene exported to: data/raw_sketchup/task02_structure.obj