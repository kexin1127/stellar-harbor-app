---
title: CLIP-DSAN Fusion for Unsupervised Domain Adaptation
date: '2025-09-01'
end_date: '2026-03-31'
summary: |
  Proposed a logits-space fusion framework that integrates CLIP and DSAN through high-confidence agreement pseudo-labels and learnable softmax weights for unsupervised domain adaptation.
tags:
  - Deep Learning
  - Transfer Learning
  - Domain Adaptation
  - Computer Vision
  - CLIP
  - DSAN

external_link: ''

math: true
---

**Supervisor:** Linqing Huang, Assistant Researcher, Shanghai Jiao Tong University

## Project Overview

This project studies **unsupervised domain adaptation (UDA)** for image classification under the setting of **labeled source-domain data + unlabeled target-domain data**. The core motivation is that CLIP provides strong **multimodal semantic priors** and robust zero-shot transfer ability, while DSAN improves cross-domain discrimination through **class-conditional subdomain alignment**. To combine these complementary strengths, I designed a lightweight fusion pipeline that learns how much to trust each model on a target task.

### Offline Fusion Workflow

The offline fusion pipeline consists of five main steps:

1. Run **CLIP** and **DSAN** separately on the target-domain samples and save their class probability outputs.
2. Read and parse the probability files produced by both models.
3. Select samples with **high-confidence agreement** as pseudo-labeled data.
4. Learn a **softmax-normalized fusion weight** by minimizing cross-entropy on the pseudo-labeled subset.
5. Apply the learned weight to all target samples and generate final fused predictions.

This design makes the method lightweight, interpretable, and easy to reproduce, since only the fusion weight is optimized while the outputs of CLIP and DSAN remain fixed.
## Motivation

In real-world visual recognition, domain shift caused by differences in device, style, background, illumination, and acquisition conditions often leads to a large performance drop when a classifier trained on one domain is deployed on another. This project explores whether a **foundation-model prior (CLIP)** and a **class-aware domain alignment model (DSAN)** can be fused in a simple and reproducible way to improve target-domain performance without using target labels.

## Method

### 1. Task Setting

Let the source domain be

$$
\mathcal{D}_s = \{(x_i^s, y_i^s)\}_{i=1}^{n_s},
$$

and the target domain be

$$
\mathcal{D}_t = \{x_j^t\}_{j=1}^{n_t}.
$$

For each target sample \(x\), CLIP and DSAN output class probability vectors

$$
p_c(x) \in \mathbb{R}^{K}, \qquad p_d(x) \in \mathbb{R}^{K}.
$$

The goal is to construct a fused prediction \(p_f(x)\) that achieves better target-domain accuracy than either individual model.

### 2. Logits-Space Fusion

Instead of directly averaging probabilities, I map both outputs into log-probability / logits space:

$$
l_c(x) = \log \big(p_c(x) + \varepsilon \big), \qquad
l_d(x) = \log \big(p_d(x) + \varepsilon \big),
$$

where \(\varepsilon > 0\) is a numerical stability term.

The fused logits are defined as

$$
l_f(x) = \alpha \, l_c(x) + (1-\alpha)\, l_d(x),
$$

and the final fused probability is

$$
p_f(x) = \mathrm{softmax}(l_f(x)).
$$

To ensure a valid and learnable fusion ratio, the weight is parameterized by softmax:

$$
\alpha = \frac{\exp(w_c)}{\exp(w_c)+\exp(w_d)}, \qquad
1-\alpha = \frac{\exp(w_d)}{\exp(w_c)+\exp(w_d)}.
$$

This means the whole fusion module only needs to learn **two scalar parameters**, making it lightweight, stable, and easy to reproduce.

### 3. High-Confidence Agreement Pseudo-Labels

To train the fusion weight without target labels, I construct pseudo-labels from samples where CLIP and DSAN agree and are both confident:

$$
\operatorname*{arg\,max} p_c(x) = \operatorname*{arg\,max} p_d(x) = \hat{y},
$$

and

$$
\max p_c(x) \ge \tau,\qquad \max p_d(x) \ge \tau.
$$

The threshold \(\tau\) is swept over \(0.7, 0.8, 0.9\) to analyze robustness.

### 4. Weight Learning Objective

On the pseudo-labeled subset, the fusion parameters are optimized with cross-entropy:

$$
\mathcal{L}(w)= -\sum_i \log p_f(x_i)[\hat{y}_i].
$$

Only the fusion parameters are updated; CLIP and DSAN predictions are frozen. This makes the optimization fast enough to run offline on saved CSV probability files.

## Experimental Setup

### Benchmark and Domain Description

The project evaluates the proposed method on three standard domain adaptation benchmarks:

- **Office-31**: includes **Amazon**, **DSLR**, and **Webcam**, where domain shift mainly comes from device type, image quality, and background variation.
- **PACS**: includes **Art Painting**, **Cartoon**, **Photo**, and **Sketch**, representing a much stronger style gap across domains.
- **ImageCLEF**: includes representative multi-domain transfer settings and is used to evaluate robustness under different domain-shift patterns.

These datasets cover both moderate and strong domain shifts, allowing a systematic comparison between **CLIP zero-shot classification**, **DSAN**, and the proposed **CLIP+DSAN fusion** method.


### Evaluation Protocol

- Compare **CLIP zero-shot**, **DSAN**, and **CLIP+DSAN fusion**
- Train fusion only on pseudo-labels from target-domain predictions
- Report classification **accuracy** on representative transfer pairs
- Sweep confidence thresholds \(\tau \in \{0.7, 0.8, 0.9\}\)

## Main Results

![Results overview](/media/projects/clip-dsan/results-overview.png)

The experiments show a clear pattern:

- On **Office-31**, the fusion model is competitive with or better than the single-model baselines on representative transfer pairs such as **A→D**, **C→D**, and **D→A**, while also improving more challenging directions such as **W→C**.
- On **PACS**, where style shift is much stronger, DSAN can degrade on some transfer pairs, while the fusion model learns to rely more on the more stable CLIP output and thus reduces negative transfer risk.
- On **ImageCLEF**, the fused model is better than or comparable to the individual models on most representative tasks and remains stable across different confidence thresholds.

Overall, the project demonstrates that **CLIP's multimodal semantic prior** and **DSAN's fine-grained subdomain alignment** are complementary, and that a very small learnable fusion layer can already provide consistent gains.

## Additional Result Visualization

![Relative gain](/media/projects/clip-dsan/relative-gain.png)

## My Contributions

- Proposed a **logits-space fusion framework** with high-confidence agreement pseudo-labels and softmax-normalized learnable weights.
- Reproduced the inference behavior of **CLIP zero-shot classification** and **DSAN subdomain alignment** under a unified evaluation pipeline.
- Built an **offline probability-fusion workflow** based on saved CSV outputs, enabling stable and reproducible experiments.
- Conducted systematic experiments on **Office-31**, **PACS**, and **ImageCLEF**, including threshold-sensitivity analysis and representative transfer-pair comparisons.
- Analyzed why fusion improves robustness, especially when one model suffers from domain-specific degradation or negative transfer.

## Technical Highlights

- **Simple and interpretable**: only two learnable scalar parameters
- **No target labels required**: training relies entirely on pseudo-labels
- **Reproducible offline pipeline**: easy to deploy on saved model outputs
- **Robust to threshold choice** within a reasonable range
- **Extensible** to future sample-wise or class-wise dynamic weighting

## Future Directions

The next step is to move from a task-level global scalar weight to more adaptive mechanisms, such as:

1. **Sample-wise or class-wise dynamic fusion**
2. **Confidence calibration** for CLIP and DSAN
3. **Joint training of fusion and feature alignment**
4. **Prompt engineering or learnable prompts** to further improve CLIP transfer quality