# Artificial Neural Networks and Deep Learning – Politecnico di Milano (2024/25)
This repository contains the homework assignments developed for the Artificial Neural Networks and Deep Learning course at Politecnico di Milano.
The work covers two main tasks in computer vision: image classification and semantic segmentation, focusing on model design, training strategies, and performance evaluation.

### Team Members
- Cagnazzo Davide
- Currò Dossi Federica
- Scanu Gianluca
- Secco Davide

### Team Name: 3Brown1Green
---

## 📂 Homework 1 – Blood Cell Image Classification

### 📌 Problem

The objective of this project is to solve a **multi-class image classification problem** on blood cell images.  
Given an input image, the model must correctly classify it into one of **8 different cell types**.

The main challenges of the task include:
- strong **class imbalance**
- presence of **outliers and noisy samples**
- high **intra-class variability**

The final model combines ConvNeXt architectures with strong augmentation techniques to achieve robust generalization.
---

### 📊 Dataset

The dataset consists of:

- **13,759 RGB images**
- Resolution: **96 × 96 pixels**
- **8 classes of blood cells**

The initial class distribution was highly imbalanced:

<img width="331" height="237" alt="Screenshot 2026-04-08 at 12 36 58" src="https://github.com/user-attachments/assets/6e2d7a63-bd91-40d0-aab5-9ee930764582" />


After preprocessing and cleaning:
- Final dataset size: **11,959 images**

---

### 🔍 Method Overview

The solution follows a structured pipeline:

1. Dataset analysis and cleaning  
2. Preprocessing and feature enhancement  
3. Handling class imbalance  
4. Data augmentation  
5. Transfer Learning + Fine-Tuning  
6. Model comparison and selection  

---

### 🧹 Data Preprocessing

#### Outlier Detection

To remove noisy samples, clustering was performed on image embeddings:

- Feature extraction using **ResNet50 (without classification head)**
- Dimensionality reduction:
  - PCA
  - t-SNE
- Clustering using **K-Means**

Clusters containing anomalous samples were manually inspected and removed.

---

#### Background Removal

To improve feature extraction, irrelevant background components were removed:

- Thresholding → segmentation  
- Dilation → fill gaps  
- Erosion → remove noise
   
<img width="261" height="171" alt="Screenshot 2026-04-08 at 12 36 33" src="https://github.com/user-attachments/assets/ee7b2b07-a849-4ee2-b7d5-a314198c99d6" />

---

### ⚖️ Handling Class Imbalance

Different strategies were explored:

- **Fixed class weights**
- **Dynamic class weights**
- **SMOTE**

Findings:
- Dynamic weights improved learning but introduced instability  
- SMOTE did not improve final performance after fine-tuning  

The final solution relied mainly on **strong data augmentation**.

---

### 🔁 Data Augmentation

#### Offline Augmentation

- Rotations, flips, translations  
- Color transformations  
- Advanced techniques:
  - CutOut  
  - MixUp  
  - GridMask  

Final dataset size:
- ~40,000 images with nearly balanced classes  

---

#### Online Augmentation (Final Choice)

- Random transformations applied at training time  
- Includes:
  - AugMix  
  - RandomCutout  
  - color and geometric transformations  

This approach significantly improved generalization and reduced overfitting.

---

### 🧠 Models and Training Strategy

A two-stage training approach was adopted:

#### Transfer Learning
- Pretrained CNN backbone  
- Frozen feature extractor  

#### Fine-Tuning
- Unfreezing deeper layers  
- Refining high-level features  

---

### 🧪 Models Evaluated

- **ResNet152 (offline augmentation)**
- **ConvNeXtSmall (offline)**
- **ConvNeXtLarge (offline)**
- **ConvNeXtSmall (online)**
- **ConvNeXtXLarge (online)**

---

### ⚙️ Training Setup

- Loss functions:
  - Categorical Cross-Entropy  
  - Focal Loss (discarded)  

- Optimizers:
  - Adam  
  - **AdamW (best)**  

- Learning rate:
  - CosineDecayRestarts scheduler  

---

### 📈 Results

<img width="651" height="201" alt="Screenshot 2026-04-08 at 12 35 53" src="https://github.com/user-attachments/assets/78c39309-a127-47a1-82cd-60906389f59a" />

---

### ✅ Final Solution

The best-performing model is:

**ConvNeXtXLarge with online data augmentation**

This combination achieved:

- **Test Accuracy: 86%**
- strong generalization performance  
- reduced overfitting  

---

