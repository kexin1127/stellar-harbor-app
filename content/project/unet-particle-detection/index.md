---
title: Subatomic Particle Center Detection with Heatmap Regression and Enhanced U-Net
date: '2025-01-01'  # Replace with the actual project start date if needed
summary: |
  Developed an improved U-Net for sub-pixel localization of subatomic particle centers from 64×64 detector-like heatmaps, achieving strong robustness in dense and overlapping scenarios.
tags:
  - Deep Learning
  - Computer Vision
  - U-Net
  - Heatmap Regression
  - Scientific Imaging
  - Subpixel Localization

external_link: ''

---

## Project Overview

This project focuses on **sub-pixel center localization** in dense two-dimensional detector-like images. Traditional methods such as **Center of Gravity (CoG)** and density-based clustering degrade rapidly when clusters overlap or when noise obscures spatial boundaries. To address this limitation, we developed an **enhanced U-Net** that predicts Gaussian-like center heatmaps directly from 64 × 64 grayscale inputs, enabling more accurate localization in crowded conditions.

## Problem Setting

The task is motivated by **subatomic particle detection** and other scientific imaging scenarios in which accurate center localization is essential for downstream reconstruction. Each input sample is a simulated **64 × 64 heatmap**, where multiple Gaussian-like clusters are superimposed to emulate detector responses. The goal is to recover the center coordinates of all clusters with high efficiency, low bias, and sub-pixel precision.

## Method

The project is built around an **improved U-Net** for heatmap regression. Compared with a standard U-Net, the final model introduces:

- **Dilated convolution in the bottleneck layer** to enlarge the receptive field without significantly increasing parameter count
- **Dropout regularization (20%)** to reduce overfitting
- A **hybrid loss** combining **mean squared error (MSE)** and **binary cross-entropy (BCE)** for more stable training
- **Gaussian target heatmaps** with reduced \(\sigma = 0.5\) to improve localization precision

Instead of directly regressing point coordinates, the model predicts a per-pixel likelihood map of possible cluster centers, which is better suited for dense and overlapping scenarios.

## Model Variants Compared

To evaluate architectural choices, the project compares several approaches:

- **Center of Gravity (CoG)** as a classical baseline
- **Original U-Net**
- **U2-Net**
- **UNet++**
- **Improved U-Net** (final proposed model)

This comparison highlights how architectural refinements affect localization performance under increasing occupancy.

## Experimental Setup

The dataset consists of simulated detector-like heatmaps generated with Gaussian-like charge distributions. Training uses mixed-density samples, while the main test setting focuses on **10,000 simulated images with 30 clusters per image**. Additional evaluation is conducted across a wide range of occupancies, from **1 to 80 clusters per image**, to assess robustness under progressively denser conditions.

### Evaluation Metrics

Model performance is measured using:

- **Efficiency**
- **Fake Rate**
- **Bias (px)**
- **Resolution (including outliers)**
- **Core Resolution**

These metrics jointly reflect detection accuracy, localization precision, and robustness to outliers.

## Main Results

The proposed **Improved U-Net** achieves the best overall performance among all compared methods on the 30-cluster benchmark:

- **Efficiency:** 84.56%
- **Fake Rate:** 2.06%
- **Bias:** 0.0732 px
- **Resolution (incl. outliers):** 0.1080 px
- **Core Resolution:** 0.0269 px

Compared with CoG and other U-Net variants, the improved model delivers substantially better detection efficiency, lower false positives, and stronger sub-pixel localization precision.

### Qualitative Visualization

![Qualitative localization example](/media/projects/subatomic-center/qualitative-localization.png)

*Visualization of predicted versus actual cluster centers in a high-occupancy scenario. The figure highlights the model’s ability to localize overlapping clusters with strong spatial accuracy.*

## Performance Across Occupancy Levels

The model remains robust across different densities:

- **Efficiency** decreases gradually from **99.76%** at 1 cluster per image to **62.95%** at 80 clusters per image
- **Bias** increases from **0.0394 px** to **0.1265 px** as density rises
- **Core Resolution** remains below **0.06 px** even at the highest tested density

These results indicate that the model is effective not only in moderate-density settings but also in highly crowded scenes.

## Training Dynamics

![Training epochs vs performance](/media/projects/subatomic-center/training-epochs-performance.png)

*Training-epoch analysis shows that efficiency improves rapidly in early epochs and gradually saturates, while fake rate remains low and decreases slightly with continued training.*

## My Contributions

- Participated in the development and refinement of an **enhanced U-Net architecture** for dense center localization
- Helped evaluate the model against **classical CoG** and multiple **U-Net variants**
- Contributed to experiments on simulated detector-like heatmaps under varying occupancy levels
- Analyzed performance using **efficiency, fake rate, bias, and resolution** metrics
- Supported the study of **sub-pixel localization performance** for particle reconstruction tasks

## Significance

This project demonstrates that **heatmap regression with an enhanced U-Net** can provide an accurate and computationally efficient solution for dense localization problems. Although motivated by particle detector imagery, the framework is also applicable to broader tasks such as:

- scientific imaging
- microscopy
- astronomy
- dense point localization in noisy environments

## Future Work

Possible next steps include:

1. Extending the method to **real TPC detector data**
2. Improving robustness in **ultra-high-density** conditions
3. Scaling to **larger input resolutions**
4. Exploring post-processing methods such as **watershed refinement**
