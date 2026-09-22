# 🧬 CELLo Project — Research Repository

> **Official repository for documentation and experimental results** associated with the research ecosystem of the **CELLo** project, focused on the processing, segmentation, and automated classification of stain-free urine cytology images.

---

## 📌 Overview of the Three Research Pillars

This repository is structured around three foundational pillars, either published or currently in development:

1. **Paper 1 — Segmentation:** Classical and deep learning approaches for robust cell contour segmentation in bright-field microscopy (**VISAPP 2026**).
2. **Paper 2 — Classification (DeiT-III):** Advanced classification based on a DeiT-III backbone with DINO self-supervision, SERF activation, and Asymmetric Loss (**EMBC 2026**).
3. **Paper 3 — Classification (Multimodal CLIP-based):** Exploration of CLIP-based foundation models for advanced staging and classification (*In Preparation*).

---

## 🔬 1. Cell Segmentation (VISAPP 2026)

* **Publication:** *21st International Conference on Computer Vision Theory and Applications* (Marbella, Spain).
* **Paper:** *Robust Cell Segmentation in Urine Cytology Images for Bladder Cancer Diagnosis*
* **Authors:** Mariana L. Teixeira, Hugo S. Oliveira, Raquel L. Monteiro, Daniela Ferreira, Tania Pereira, Raphaël F. Canadas, and Hélder P. Oliveira.
* **Summary:** Study and benchmark of classical and deep learning approaches for cell segmentation in bright-field microscopy, evaluating cell contour consistency across clinical samples from the **São João University Hospital Center (CHUSJ)**, Porto.
* **📄 Access:** [Read publication online](https://lnkd.in/eJHgm2H9)

---

## ⚡ 2. Vision Transformer Classification — DINO-DeiT-III (EMBC 2026)

* **Publication:** *48th Annual International Conference of the IEEE Engineering in Medicine and Biology Society* (EMBC 2026).
* **Paper:** *Enhancing Cytological Staining-Free Image Classification with Robust Vision Transformers*
* **Session:** Tu.P3: Medical Imaging and Clinical Decision Support Systems (Poster)
* **Method Summary:** Combination of a **DeiT-III** backbone with **DINO** self-supervised training (student-teacher architecture). To address cytology challenges, it introduces:
  * The **SERF** (*Log-Softplus ERror Function*) activation in the attention layers.
  * **Asymmetric Loss (ASL)** to mitigate severe class imbalance.
* **Comparisons:** Evaluated against baselines (ResNet-50, ViT-16+SERF, ConvNeXtV2) across three datasets: **CELLo**, **ISIC 2019**, and **BTC**.

---

## 🤖 3. Multimodal CLIP-based Classification *(In Preparation)*

* **Current Status:** In development / Manuscript in preparation.
* **Method Summary:** Extension of the research into a **multimodal** paradigm, combining deep visual representations with structured and textual clinical data. Inspired by **CLIP**-type architectures, this pillar aims to empower fine-grained bladder cancer staging with enhanced clinical interpretability.

---

## 📂 Proposed Repository Structure

```text
├── data/                  # Statistics, descriptions, and cell counts (Supplementary Table 3)
├── src/
│   ├── segmentation/      # Segmentation implementations and scripts (Paper 1 - VISAPP)
│   ├── classification_vit/# DeiT-III, DINO, SERF, and ASL code (Paper 2 - EMBC)
│   └── classification_clip/ # Multimodal approach (Paper 3 - In preparation)
├── notebooks/             # Exploratory data analysis scripts
└── README.md              # Main documentation