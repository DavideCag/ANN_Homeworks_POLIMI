# Artificial Neural Networks and Deep Learning – Politecnico di Milano (2024/25)
This repository contains the homework assignments developed for the Artificial Neural Networks and Deep Learning course at Politecnico di Milano.
The work covers two main tasks in computer vision: image classification and semantic segmentation, focusing on model design, training strategies, and performance evaluation.
A detailed description of the methodologies, experiments, and overall development process is provided in the reports included in each homework folder.

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

## 📂 Homework 2 – Mars Terrain Semantic Segmentation

### 📌 Problem

This homework focuses on solving a **multi-class semantic segmentation problem** on Mars terrain images.  
The objective is to assign a class label to each pixel of the image.

The main challenges include:
- strong **class imbalance at pixel level**
- presence of **outliers and corrupted masks**
- difficulty in modeling the **background class**
- limited dataset size

---

### 📊 Dataset

The dataset consists of:

- **2,615 grayscale images**
- Resolution: **64 × 128 pixels**
- **5 pixel classes**

Initial class distribution:

<img width="568" height="393" alt="Screenshot 2026-04-08 at 12 56 05" src="https://github.com/user-attachments/assets/fe8795e7-fe7a-417f-aaa3-2b6f4f2a714a" />

---

### 🔍 Method Overview

The adopted workflow includes:

1. Dataset inspection and cleaning  
2. Preprocessing and normalization  
3. Loss function engineering  
4. Class imbalance handling  
5. Data augmentation  
6. Model design and architectural improvements  
7. Training optimization and evaluation  

---

### 🧹 Data Preprocessing

#### Outlier Removal

During dataset analysis, corrupted samples were identified:

- Multiple images shared the same incorrect mask (not matching the input image)
- All samples with this mask were removed

---

#### Brightness Adjustment

To improve consistency across images:

- Implemented an **AutoBrightness function**
- Normalized pixel intensity across the dataset

This reduced variability due to exposure differences and improved model stability.

---

### ⚖️ Handling Class Imbalance

To address pixel-level imbalance:

- **Fixed class weights** were introduced in the loss function
- Weights computed based on pixel distribution

This allowed the model to focus more on underrepresented classes.

---

### 🔁 Data Augmentation

Data augmentation was implemented using **Albumentations**, ensuring consistency between images and masks.

Tested approaches:

- Heavy augmentation → degraded performance  
- Light augmentation → limited improvement  
- Final choice:
  - **Horizontal flip + small rotations**

This provided a good balance between variability and label consistency.

---

### 🧠 Model Architecture

The final solution is based on a **modified U-Net architecture**, including:

- Encoder–decoder structure  
- **Residual connections**  
- **Squeeze-and-Excitation (SE) blocks**  
- Deeper convolutional layers  
- **ConvTranspose upsampling**  
- **L2 regularization**

Output layer:
- Softmax for multi-class segmentation  

---

### ⚙️ Loss Functions

Several losses were tested:

- Dice Loss  
- Focal Loss  
- Pixel-wise Crossentropy  
- Lovasz Softmax  

Best configurations:

- **0.2 Crossentropy + 0.8 Focal Loss**
- **0.5 Dice Loss + 0.5 Focal Loss**

Important design choice:
- **background class excluded from loss computation**
- resulted in **significant performance improvement**

---

### ⚙️ Optimization

- Optimizers:
  - Adam  
  - **AdamW (best)**  

- Learning rate schedulers:
  - CosineDecayRestarts  
  - **PolynomialDecay (best performance)**  
  - ExponentialDecay  
  - InverseTimeDecay (discarded)

---

### 🧪 Experiments

We evaluated multiple configurations:

- Loss combinations  
- Learning rate schedulers  
- Brightened vs standard datasets  
- Model complexity  

Additional methods tested:
- attention mechanisms  
- ASPP modules  
- bidirectional LSTM  
- ensemble models  

These approaches did not outperform the final model.

---

### 📈 Results

<img width="484" height="383" alt="Screenshot 2026-04-08 at 12 56 48" src="https://github.com/user-attachments/assets/5e8a0e1d-7c5a-4a92-9a31-e25e36dbab20" />

---

### ✅ Final Solution

The best-performing solution is:

**Modified U-Net + CrossEntropy/Focal Loss + Polynomial LR Scheduler**

This configuration achieved:
- strong generalization  
- improved segmentation quality  
- better handling of class imbalance  

---

