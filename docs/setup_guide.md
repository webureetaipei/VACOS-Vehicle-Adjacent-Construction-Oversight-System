# VACOS Environment Setup Guide

## 1. Prerequisite: Hybrid System Setup (Task 01)
Before starting, ensure your host meets the following requirements:
* **Host OS:** Windows 10/11 (Required for SketchUp Pro).
* **Subsystem:** WSL2 enabled with Ubuntu 20.04/22.04 LTS (For Training/Simulation).
* **GPU:** NVIDIA RTX GPU (Driver 535.xx+ installed on Windows Host).
* **Networking:** Ensure WSL2 allows localhost connections.

### Project Structure Initialization (Part of Task 01)
Running the initialization script (Task 01) creates the standard research directory structure:
* `src/sim`: Physics-aware simulation engine (Open3D).
* `src/tiny_vacos`: C++ Embedded Engine Port.
* `src/vacos_model`: Deep Learning Models.

## 2. Simulation Engine Dependencies (Task 02)
We utilize a Hybrid workflow: SketchUp (Windows) for Modeling, Open3D (WSL2) for Ray-casting.

### 2.1 Windows Side (Modeling)
* Install SketchUp Pro (or compatible version).
* Ensure Ruby API access is enabled for export scripts.

### 2.2 WSL2 Side (Physics Engine)
Install system-level dependencies for headless rendering:
```bash
sudo apt-get update
sudo apt-get install -y libgl1-mesa-glx libosmesa6-dev build-essential
```

## 3. Python & CUDA Environment Setup (Task 03)
We use Miniconda to manage the deep learning environment.

### 3.1 Create Environment
```bash
# Create environment
conda create -n vacos-edge python=3.9 -y
conda activate vacos-edge

# Install PyTorch with CUDA 12.1
conda install pytorch torchvision pytorch-cuda=12.1 -c pytorch -c nvidia

# Install Simulation & Math Libraries
pip install open3d numpy scipy scikit-learn ray[default] tqdm h5py
```

### 3.2 Verification (Milestone Check)
```bash
python -c "import torch; print(f'CUDA Available: {torch.cuda.is_available()}')"
```
**Expected Output:** ✅ CUDA Available: True

## 4. Headless Simulation Configuration (Task 04)
**Target:** Run ray-casting simulation on remote servers/WSL without GUI.

### 4.1 Headless Configuration
Open3D requires specific EGL configuration for headless rendering on WSL2.
```bash
export PYOPENGL_PLATFORM=egl
python -c "import open3d; print('Open3D Headless Init Success')"
```
**Expected Output:** ✅ No errors, clean exit.
