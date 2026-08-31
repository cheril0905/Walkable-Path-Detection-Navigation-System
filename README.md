# 🚶 Walkable Area Path Segmentation & Navigation System

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-EE4C2C.svg)](https://pytorch.org/)
[![DeepLabV3](https://img.shields.io/badge/Model-DeepLabV3%20ResNet50-brightgreen.svg)](https://pytorch.org/vision/stable/models.html)
[![Dataset](https://img.shields.io/badge/Dataset-Mapillary%20Vistas-orange.svg)](https://www.mapillary.com/dataset/vistas)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An end-to-end Computer Vision & Deep Learning solution for **Walkable Path Detection and Urban Navigation**. Utilizing **DeepLabV3 with a ResNet-50 backbone** trained on the **Mapillary Vistas** dataset (124 semantic classes), this system segments road and street scenes, maps class predictions to walkability scores, generates real-time heatmaps, and identifies walkable surface regions for autonomous navigation and visual assistance.

---

## 📌 Features

- **Semantic Segmentation Pipeline**: Fine-tuned DeepLabV3 architecture with a ResNet-50 backbone to perform pixel-level multi-class segmentation on complex street-level imagery.
- **124-Class Compact Remapping**: Maps original Mapillary Vistas class IDs to compact IDs (0–123) for efficient GPU tensor processing and loss calculation.
- **Walkability Score Integration**: Utilizes a customized `walkability_meta.json` taxonomy mapping binary walkability indices ($1.0$ for walkable areas like sidewalks, crosswalks, footways, pedestrian areas, curb cuts, driveways; $0.0$ for non-walkable obstacles, vehicles, vegetation, sky, etc.).
- **Heatmap & Overlay Generation**: Converts raw segmentation masks into binary walkability heatmaps and RGB mask overlays for visual verification.
- **Textual Walkable Object Summaries**: Automatically detects and logs present walkable terrain features into output text files (e.g., `walkable_objects_output.txt`).
- **Real-World Video & Image Inference**: Supports evaluation on real-world photo samples and video feeds.

---

## 🛠️ Repository Structure

```text
.
├── mapillary-vistas/
│   ├── meta.json               # Mapillary Vistas dataset class definitions (124 classes)
│   ├── walkability_meta.json   # Walkability scores per class ID
│   ├── LICENSE.md              # Dataset license information
│   └── README.md               # Dataset overview
├── model.ipynb                 # Training notebook: Dataset loader, model initialization, training loop & saving
├── usage.ipynb                 # Inference notebook: Loading weights, predicting masks, generating heatmaps & textual reports
├── output.ipynb                # Visualization & evaluation notebook: RGB overlays & walkability masks
├── .gitignore                  # Git ignore rules (excludes large weights, datasets, real/ and rvids/)
└── README.md                   # Project documentation
```

> **Note**: Heavy directories (`real/`, `rvids/`, dataset images under `mapillary-vistas/training/`, `validation/`, `testing/`) and model weights (`*.pth`) are excluded via `.gitignore` to keep the repository lightweight.

---

## 🚨 Handling Large Files (> 100 MB)

GitHub has a strict **100 MB single file size limit**. The trained PyTorch model checkpoint file `deeplabv3_resnet50_trained.pth` is approximately **168.5 MB**, which exceeds this limit.

### Recommended Solution (Option A): External Model Storage
1. Upload `deeplabv3_resnet50_trained.pth` to a cloud storage platform (Google Drive, OneDrive, Hugging Face Hub, or GitHub Releases).
2. Place the downloaded `.pth` file inside the `mapillary-vistas/` directory before running inference:
   ```text
   mapillary-vistas/deeplabv3_resnet50_trained.pth
   ```
3. Update the download link in this README for collaborators or users.

### Alternative Solution (Option B): Git LFS (Large File Storage)
If you prefer pushing the model file directly using **Git LFS**:
```bash
# Install Git LFS
git lfs install

# Track PyTorch model files
git lfs track "mapillary-vistas/deeplabv3_resnet50_trained.pth"
git lfs track "*.pth"

# Add .gitattributes to git
git add .gitattributes
```

---

## ⚡ Prerequisites & Installation

### 1. Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
cd YOUR_REPOSITORY_NAME
```

### 2. Environment Setup
Create a virtual environment and activate it:

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
Install PyTorch and essential computer vision libraries:

```bash
pip install torch torchvision --extra-index-url https://download.pytorch.org/whl/cu118
pip install numpy pillow matplotlib opencv-python tqdm
```

---

## 🚀 Model Architecture & Training Summary

- **Backbone**: ResNet-50 (Pre-trained on ImageNet)
- **Segmentation Head**: DeepLabV3 with Atrous Spatial Pyramid Pooling (ASPP)
- **Classifier Output**: 124-channel output convolution (`Conv2d(256, 124, kernel_size=1)`)
- **Loss Function**: `CrossEntropyLoss()`
- **Optimizer**: Adam (`lr=1e-4`)
- **Training Epochs**: 10 Epochs
- **Final Performance**:
  - Epoch 1 Loss: Train `1.9556` | Val `1.2647`
  - Epoch 10 Loss: Train `0.4669` | Val `0.6568`

---

## 💻 How to Run

### 1. Training the Model (`model.ipynb`)
Run `model.ipynb` to load training images/masks, process RGB-to-compact ID remappings, execute training over 10 epochs, and output `deeplabv3_resnet50_trained.pth`.

### 2. Running Inference (`usage.ipynb`)
Open `usage.ipynb` to:
- Load the pre-trained `deeplabv3_resnet50_trained.pth` model.
- Execute inference on an image from the dataset or custom input.
- Generate binary walkability heatmaps.
- Export detected walkable class names to `walkable_objects_output.txt`.

### 3. Visualizing Results (`output.ipynb`)
Run `output.ipynb` to render RGB mask overlays on original input images to visually verify path boundaries.

---

## 📤 Adding & Pushing to GitHub

Follow these steps to initialize git, ignore heavy files, and push your repository to GitHub:

### Step 1: Initialize Git & Stage Files
```bash
git init
git add .
```

### Step 2: Check Staged Files
Verify that `real/`, `rvids/`, and `*.pth` are **NOT** staged:
```bash
git status
```

### Step 3: Commit Changes
```bash
git commit -m "Initial commit: Walkable Area Path Segmentation System"
```

### Step 4: Link Remote Repository & Push
Replace `YOUR_USERNAME` and `YOUR_REPOSITORY_NAME` with your actual GitHub details:
```bash
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
git push -u origin main
```

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
