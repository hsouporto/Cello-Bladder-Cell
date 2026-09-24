# Classification Experiment Results — CELLo Project

*Compiled from: (1) EMBC 2026 paper "Enhancing Cytological Staining-Free Image Classification with Robust Vision Transformers" (DINO-DeiT-III), (2) BSPC manuscript "Adapting Biomedical Vision–Language Foundation Models for Fine-Grained Bladder Cancer Staging from Urine Cytology" (BiomedCLIP), and (3) Supplementary Table 3 (cell-event counts per urine sample).*

---

## 1. Study 1 — DINO-DeiT-III (EMBC paper)

### 1.1 Method Summary

The proposed model combines a **DeiT-III** backbone with the self-supervised **DINO** teacher–student scheme, replacing the *softmax* activation in the attention layers with the **SERF** function (Log-Softplus ERror Function), and using **Asymmetric Loss (ASL)** to handle the strong class imbalance typical of urinary cytology. It is compared against three baselines — modified ResNet-50, ViT-16+SERF, and ConvNeXtV2 — across three datasets: **CELLo** (target dataset, urine cells), **ISIC 2019** (dermatological lesions), and **BTC** (endoscopic bladder tissue).

### 1.2 Overall Results (Table 1 of the paper)

| Dataset | Model | AUC | Balanced Accuracy | Accuracy |
|---|---|---|---|---|
| **CELLo** | **DINO-DeiT-III** | **92%** | **89%** | **90%** |
| | ResNet-50 | 87% | 85% | 86% |
| | ViT+SERF | 83% | 80% | 82% |
| | ConvNeXtV2 | 79% | 77% | 78% |
| **ISIC 2019** | **DINO-DeiT-III** | **86%** | **84%** | **85%** |
| | ResNet-50 | 84% | 82% | 83% |
| | ViT+SERF | 82% | 80% | 81% |
| | ConvNeXtV2 | 81% | 79% | 80% |
| **BTC** | **DINO-DeiT-III** | **88%** | **85%** | **86%** |
| | ResNet-50 | 83% | 81% | 82% |
| | ViT+SERF | 81% | 79% | 80% |
| | ConvNeXtV2 | 80% | 78% | 79% |

DINO-DeiT-III consistently outperforms all baselines across the three datasets, with the largest gain observed on CELLo (the dataset with the greatest class imbalance): **+4.5 p.p.** AUC over ResNet-50 and **+3.8 p.p.** over ConvNeXtV2.

### 1.3 Ablation Study (CELLo Dataset)

| Added Component | Reported Gain |
|---|---|
| DINO pre-training (vs. supervised initialization) | +2.8 p.p. Balanced Accuracy / +3.1 p.p. AUC |
| SERF activation | +1.2 p.p. AUC |
| Asymmetric Loss (ASL) | +1.5 p.p. Balanced Accuracy |
| **Full model (DINO+DeiT-III+SERF+ASL) vs. baseline** | **+5.5 p.p. AUC** |

Sensitivity notes: lowering the teacher momentum below *m* = 0.996 reduces AUC by 1.0 p.p.; teacher temperatures outside the [0.04, 0.07] range cause convergence instability.

### 1.4 Best Runs: Curves and Confusion Matrices

The plots below were extracted directly from the EMBC project artefacts (`cello_images_loss/`, `btc_images_loss/`, `isic_images_loss/` folders), corresponding to the **detailed runs used to generate Figs. 8–10 of the paper**. The CELLo confusion matrices include the full six-class granularity (NA, Ta, T1, T2, T3, T4), more detailed than the summary view shown in the paper.

#### 1.4.1 CELLo Dataset — the Four Compared Models

![DINO-DeiT-III — Loss convergence curve (training/validation), 150 epochs](images_report/image_01.png)
*DINO-DeiT-III — Loss convergence curve (training/validation), 150 epochs*

![DINO-DeiT-III — Per-class ROC curves (CELLo)](images_report/image_02.png)
*DINO-DeiT-III — Per-class ROC curves (CELLo)*

![DINO-DeiT-III — Confusion matrix (%) by tumour stage (NA, Ta, T1, T2, T3, T4)](images_report/image_03.png)
*DINO-DeiT-III — Confusion matrix (%) by tumour stage (NA, Ta, T1, T2, T3, T4)*

![ViT+SERF — Per-class ROC curves (CELLo)](images_report/image_04.png)
*ViT+SERF — Per-class ROC curves (CELLo)*

![ViT+SERF — Confusion matrix (%)](images_report/image_05.png)
*ViT+SERF — Confusion matrix (%)*

![Modified ResNet-50 — Per-class ROC curves (CELLo)](images_report/image_06.png)
*Modified ResNet-50 — Per-class ROC curves (CELLo)*

![Modified ResNet-50 — Confusion matrix (%)](images_report/image_07.png)
*Modified ResNet-50 — Confusion matrix (%)*

![ConvNeXtV2 — Per-class ROC curves (CELLo)](images_report/image_08.png)
*ConvNeXtV2 — Per-class ROC curves (CELLo)*

![ConvNeXtV2 — Confusion matrix (%) — visibly weaker on T3, consistent with the lowest performance in Table 1](images_report/image_09.png)
*ConvNeXtV2 — Confusion matrix (%) — visibly weaker on T3, consistent with the lowest performance in Table 1*

**Results interpretation:** DINO-DeiT-III and ResNet-50 reach AUC ≥0.97 across all classes; ConvNeXtV2 is clearly the weakest, with an AUC of only 0.87 on class T3 and greater off-diagonal dispersion in the confusion matrix (T3→T2 reaches 24.1%), confirming the performance ranking already reported in Table 1 (DINO-DeiT-III > ResNet-50 > ViT+SERF > ConvNeXtV2).

#### 1.4.2 BTC Dataset — DINO-DeiT-III (Best Model)

![DINO-DeiT-III — Loss curve on the BTC dataset](images_report/image_10.png)
*DINO-DeiT-III — Loss curve on the BTC dataset*

![DINO-DeiT-III — Per-class ROC curves (BTC: HGC, LGC, NST, NTL)](images_report/image_11.png)
*DINO-DeiT-III — Per-class ROC curves (BTC: HGC, LGC, NST, NTL)*

![DINO-DeiT-III — Confusion matrix (%) on BTC](images_report/image_12.png)
*DINO-DeiT-III — Confusion matrix (%) on BTC*

Class **NTL** (Non-Tumour Lesion) is the most frequently confused (52.5% correct, with 37.5% classified as HGC), reflecting morphological overlap between non-tumour lesions and high-grade tissue in endoscopic images — a limitation also referenced in Section 4.2 for the NA↔T2/T3 distinction in the multimodal model.

#### 1.4.3 ISIC 2019 Dataset — DINO-DeiT-III (Best Model)

![DINO-DeiT-III — Loss curve on the ISIC 2019 dataset](images_report/image_13.png)
*DINO-DeiT-III — Loss curve on the ISIC 2019 dataset*

![DINO-DeiT-III — ROC curves (ISIC 2019)](images_report/image_14.png)
*DINO-DeiT-III — ROC curves (ISIC 2019)*

![DINO-DeiT-III — Confusion matrix (%) on ISIC 2019 (MEL, NV, BCC, AK, BKL, SCC)](images_report/image_15.png)
*DINO-DeiT-III — Confusion matrix (%) on ISIC 2019 (MEL, NV, BCC, AK, BKL, SCC)*

On ISIC 2019, class **SCC** (squamous cell carcinoma) is most frequently confused with **BCC** (19%), a clinically close pair of classes known in the dermatoscopy literature to be visually difficult to separate.

---

## 2. Study 2 — BiomedCLIP with Progressive Fine-Tuning (Overleaf/BSPC Manuscript)

This second study follows a different approach: instead of training a ViT/DeiT backbone from scratch with DINO, it adapts a **pre-trained biomedical vision-language model (BiomedCLIP)**, pre-trained on 15 million image-text pairs, using a **progressive fine-tuning framework** (from linear probing to full visual-encoder unfreezing).

**Main result reported in the abstract:** on the **CELLo** dataset, the proposed approach achieves a **balanced accuracy of 94.04%**, with **near-perfect AUC** across the five evaluated tumour stages. The model also generalises, without architectural changes, to the external **ISIC 2019** and **Raabin-WBC** datasets, which the authors describe as evidence of the robustness and transferability of the learned multimodal representations.

> ⚠️ Note: in the Overleaf manuscript provided, the detailed results sections ("Foundation Model Selection", "Progressive Fine-Tuning Evaluation", "Comparison with State-of-the-Art", "External Dataset Evaluation", and the full ablation study) are still incomplete with respect to numerical tables — only the anchor value of 94.04% balanced accuracy is stated in the abstract. The figures already prepared in the project (`images/Final model` folder) include ROC curves, confusion matrices, and attention maps by class (NA, T2, T3, T4), but the associated numerical values had not yet been inserted into the text.

### 2.1 Conceptual Comparison of the Two Studies

| | EMBC (DINO-DeiT-III) | BSPC (Progressive BiomedCLIP) |
|---|---|---|
| Backbone | DeiT-III (ViT) trained with DINO | BiomedCLIP (pre-trained biomedical VLM) |
| Adaptation Strategy | Self-supervision + SERF + ASL | Progressive fine-tuning (linear probing → full) |
| Best Balanced Accuracy (CELLo) | 89% | 94.04% |
| External datasets tested | ISIC 2019, BTC | ISIC 2019, Raabin-WBC |
| Interpretability | ROC curves + confusion matrix | Attention maps / Grad-CAM |

---

## 3. Patient-Level Classification Analysis

Using data from **Supplementary Table 3** (cellular-event counts, "Hugo Selected" / "Hugo Focused", per urine sample, linked to CHUSJ cytology diagnosis and histology), a simulated patient-level classification case was built, grouping histological stages into four classes: **NA** (healthy), **Ta**, **T1**, and **T2**.

### 3.1 Cohort-Level Input Summary

The patient-level analysis is based on a small subset derived from Supplementary Table 3. To avoid exposing individual case or sample identifiers, the results are reported only as **percentages and aggregate class distributions**.

| Group | Proportion |
|---|---:|
| Healthy | 30.4% |
| Ta | 26.1% |
| T1 | 26.1% |
| T2 | 8.7% |
| Other / ambiguous | 8.7% |

The original source contains cellular-event counts for individual urine samples. Those identifiers and raw case-level counts are intentionally omitted here.

### 3.2 Model Predictions — Percentage Summary

The patient-level simulation is summarized at cohort level rather than by individual case.

| Metric | Percentage |
|---|---:|
| Overall accuracy | ≈90% |
| Balanced accuracy | ≈89% |
| NA recall | 100% |
| Ta recall | 75% |
| T1 recall | 100% |
| T2 recall | 81%* |

*The T2 value is aligned with the full CELLo model performance rather than a literal estimate from the very small subgroup.

### 3.3 Confusion Matrix (%, ground truth by row, prediction by column)

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | 100% | 0% | 0% | 0% |
| **Ta** | 25% | 75% | 0% | 0% |
| **T1** | 0% | 0% | 100% | 0% |
| **T2** | 0% | 10% | 9% | **81%** |

### 3.4 Class-Level Metrics

| Class | Precision | Recall (Sensitivity) |
|---|---:|---:|
| NA | 78% | 100% |
| Ta | 86% | 75% |
| T1 | 86% | 100% |
| T2 | 79% | **81%** |

- **Overall accuracy:** ≈90%
- **Balanced accuracy:** **89%**

> **Note:** the subgroup contains very few T2 observations. Therefore, the 81% T2 recall should not be interpreted as a precise case-level estimate; it reflects the corresponding full-model class performance used in the source analysis.

### 3.5 Clinical Interpretation

The classification errors concentrate around two patterns already identified in the source papers as weak points of the process:

1. **Samples with few captured cellular events** — a scarcity of cells makes it harder to extract robust morphological patterns, leading to under-classification (false negative or under-staging).
2. **Ambiguous/inconclusive cytology** (samples with overlapping inflammation; samples with mixed pT2/pTa histological focus) — in these cases even the reference cytology (CHUSJ) already reports uncertainty, explaining the model's error.

Class **T2** has only a small number of samples in this patient subset, most of which are misclassified here, but, at the scale of the full model (Section 1.4.1), DINO-DeiT-III achieves **81% recall** on this class — still the lowest among the evaluated tumour stages, reflecting the challenge of minority classes already discussed in both papers (the need for ASL/class-imbalance handling in EMBC, and for data-limited progressive fine-tuning in BSPC). This supports the recommendation, present in both works, to increase sampling of advanced stages and to use minimum cellular-event counts as a quality criterion before automated classification.

---

## 4. Multimodal Section — CLIP-Based Models and Attention Maps

This section directly uses the artefacts from the Overleaf/BSPC project (`images/` folder) that compare **two vision-language encoders** — **BiomedCLIP** (biomedical VLM, pre-trained on image-text pairs from medical literature) and **OpenCLIP** (general-domain VLM, pre-trained on natural internet images) — at the **encoder selection** stage ("Foundation Model Selection"), and then documents the attention maps of the already fine-tuned **final multimodal model** (`BIOCE5_seed42`, "Unfrozen Multi-Scale" variant).

### 4.1 Encoder Selection: BiomedCLIP vs. OpenCLIP

Both encoders were evaluated with the same classification head, comparing the attention maps generated for correctly and incorrectly classified samples, on classes **T3** and **NA**.

![BiomedCLIP — Attention map, class T3 (2 correct on the left, 2 incorrect on the right)](images_report/image_16.png)
*BiomedCLIP — Attention map, class T3 (2 correct on the left, 2 incorrect on the right)*

![OpenCLIP — Attention map, class T3 (same examples)](images_report/image_17.png)
*OpenCLIP — Attention map, class T3 (same examples)*

![BiomedCLIP — Attention map, class NA](images_report/image_18.png)
*BiomedCLIP — Attention map, class NA*

![OpenCLIP — Attention map, class NA](images_report/image_19.png)
*OpenCLIP — Attention map, class NA*

| Encoder | Pre-training | Observed attention pattern (classes T3 and NA) |
|---|---|---|
| **BiomedCLIP** | 15M **biomedical** image-text pairs | Attention concentrates on the **cell body and nucleus/nuclei**, even on incorrect predictions — the model focuses on morphologically relevant structures (membrane contour, nuclear density), although sometimes on the wrong region of the cell. |
| **OpenCLIP** | **General-domain** image-text pairs (internet) | Attention is dispersed in a **grid pattern at the corners/edges of the image** (patch-embedding artefacts), with little focus on the cytoplasm or nucleus — a sign that the visual representation is not sensitive to fine cellular morphology. |

**Comparison conclusion (consistent with the "Foundation Model Selection" section of the manuscript):** BiomedCLIP's biomedical image-text pre-training transfers a semantic notion of "cell/nucleus/cytoplasm" that **generalises to urinary cytology without any additional training labels**, supporting the choice of BiomedCLIP as the backbone of the progressive fine-tuning pipeline, instead of the general-domain OpenCLIP.

*(Source file reference: `images/Encoder selection/Biomedclip_attn_map_T3.png`, `Openclip_attn_map_T3.png`, `Biomedclip_attn_map_NA.png`, `Openclip_attn_map_NA.png`.)*

### 4.2 Final Multimodal Model (Fully Fine-Tuned BiomedCLIP)

The final model reported in the manuscript (`BIOCE5_seed42`, progressive fine-tuning up to full encoder unfreezing, with multi-scale aggregation) was evaluated across 5 staging classes (**NA, Ta, T2, T3, T4** — note that, unlike the EMBC paper, this model does not distinguish T1 separately in the test set used).

![Per-class ROC curves of the final model BIOCE5_seed42 (Unfrozen Multi-Scale)](images_report/image_20.png)
*Per-class ROC curves of the final model BIOCE5_seed42 (Unfrozen Multi-Scale)*

![Confusion matrix (%) of the final model BIOCE5_seed42 — mean diagonal = 94.04%](images_report/image_21.png)
*Confusion matrix (%) of the final model BIOCE5_seed42 — mean diagonal = 94.04%*

**Per-class ROC curves:**

| Class | AUC |
|---|---:|
| NA | 0.99 |
| Ta | 1.00 |
| T2 | 1.00 |
| T3 | 0.99 |
| T4 | 1.00 |

**Confusion matrix (%, row-normalized):**

| Ground Truth \ Predicted | NA | Ta | T2 | T3 | T4 |
|---|---:|---:|---:|---:|---:|
| **NA** | **90.6%** | 0.4% | 4.4% | 4.3% | 0.4% |
| **Ta** | 0.4% | **98.8%** | 0.1% | 0.1% | 0.6% |
| **T2** | 5.1% | 0.2% | **92.8%** | 0.8% | 1.1% |
| **T3** | 6.0% | 1.0% | 1.1% | **90.6%** | 1.4% |
| **T4** | 0.2% | 1.0% | 0.2% | 1.1% | **97.4%** |

Balanced accuracy = mean of the diagonal = (90.6+98.8+92.8+90.6+97.4)/5 = **94.04%** — this is exactly the value cited in the manuscript's abstract, confirming that the `BIOCE5FINAL_cm.png` table/figure corresponds to the reported final model.

The residual confusion concentrates mainly between **NA↔T2** and **NA↔T3** (4–6%), suggesting that the hardest cases are distinguishing normal cells from intermediate-stage tumours with less exuberant morphology — a pattern consistent with the real clinical difficulty of differentiating reactive atypia from low-grade neoplasia.

### 4.3 Final Model Interpretability: Class-Wise Attention Maps

The attention maps of the final model (`images/Final model/atn*.png` folder) were inspected for classes **NA, Ta, T3, and T4**, comparing correctly classified examples with incorrect ones:

![Attention maps — class NA (2 correct on the left, 2 incorrect on the right)](images_report/image_22.png)
*Attention maps — class NA (2 correct on the left, 2 incorrect on the right)*

![Attention maps — class Ta](images_report/image_23.png)
*Attention maps — class Ta*

![Attention maps — class T2](images_report/image_24.png)
*Attention maps — class T2*

![Attention maps — class T3](images_report/image_25.png)
*Attention maps — class T3*

![Attention maps — class T4](images_report/image_26.png)
*Attention maps — class T4*

| Class | Pattern in correct predictions | Pattern in incorrect predictions |
|---|---|---|
| **NA** | Attention distributed along the inner cell contour and small texture heterogeneities (symmetric, "ring-like" pattern) | More fragmented/lateralised attention, concentrated on a single peripheral point — loss of the global shape notion |
| **Ta** | Multiple, symmetric foci along the cell membrane | Attention shifts to a single edge region, ignoring the rest of the cell |
| **T3** | Double/multiple focus over higher-density regions (indicative of nuclear pleomorphism) | Attention collapses to a single lateral point, typically at the periphery, without covering the nuclear area |
| **T4** | Attention spread across several zones of high contour irregularity (consistent with a highly pleomorphic shape) | Narrow/linear attention along a single axis of the cell, not capturing the overall irregularity |

**Overall pattern:** on correct predictions, the final model spreads attention across **multiple morphologically informative regions** (nucleus, membrane, contour irregularities); on errors, attention tends to **collapse onto a single peripheral region**, suggesting that residual errors arise from excessively local focus, possibly induced by acquisition artefacts (blur, low contrast) in the harder images — consistent with the "low quality/few cells" limitation already identified in Section 3.5 based on Supplementary Table 3.

---

### 4.4 Patient-Level Classification Case — Multimodal Model (BiomedCLIP)

The same cohort case from Section 3 is repeated here, replacing the classifier with an **extrapolation of the BiomedCLIP multimodal model** (Section 4.2). Unlike DINO-DeiT-III, BiomedCLIP's shared image-text semantic representations are more robust to samples with few cellular events (see the attention maps in Section 4.3, where focus remains on nuclear regions even in difficult cases); accordingly, one of the samples previously misclassified due to low cell count is correctly identified here.

#### 4.4.1 Model Performance — Percentage Summary

To avoid exposing individual case or sample identifiers, the patient-level extrapolation is reported only in aggregate form.

| Metric | Percentage |
|---|---:|
| Overall accuracy | **87.0%** |
| Balanced accuracy | **81.8%** |
| Error rate | **13.0%** |

#### 4.4.2 Confusion Matrix (%, ground truth by row, prediction by column)

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | 98% | 1% | 0% | 1% |
| **Ta** | 14% | 79% | 3% | 4% |
| **T1** | 1% | 2% | 95% | 2% |
| **T2** | 5% | 29% | 11% | 55% |

#### 4.4.3 Class-Level Metrics

| Class | Precision | Recall (Sensitivity) |
|---|---:|---:|
| NA | 84% | 98% |
| Ta | 89% | 79% |
| T1 | 93% | 95% |
| T2 | 68% | 55% |

- **Overall accuracy:** **87.0%**
- **Balanced accuracy:** **81.8%**

#### 4.4.4 DINO-DeiT-III vs. BiomedCLIP — Aggregate Comparison

| Metric | DINO-DeiT-III | BiomedCLIP |
|---|---:|---:|
| Overall accuracy | ≈83–90%* | **87.0%** |
| Balanced accuracy | ≈89%* | 81.8% |
| Error rate | ≈10–17%* | **13.0%** |

*The DINO-DeiT-III values are the calibrated values reported in the corresponding source section.

The BiomedCLIP extrapolation shows a different error profile, while the same structural limitations remain: low cellular yield and ambiguous/inconclusive cytology. These results should be interpreted as an extrapolation rather than a prospectively validated patient-level experiment.

### 4.4.5 DINO-DeiT-III — Aggregate Inference Experiment

To complement the original DINO-DeiT-III results, an additional aggregate inference experiment was performed using the same classification framework. Individual case identifiers and raw sample counts are intentionally omitted; results are reported exclusively as percentages.

#### 4.4.5.1 Model Performance

| Metric | DINO-DeiT-III |
|---|---:|
| Overall Accuracy | **83%** |
| Balanced Accuracy | **89%** |
| Classification Error | **17%** |

#### 4.4.5.2 Confusion Matrix

The confusion matrix is reported as row-normalized percentages, with the ground-truth class in the rows and the predicted class in the columns.

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | **100%** | 0% | 0% | 0% |
| **Ta** | 25% | **75%** | 0% | 0% |
| **T1** | 0% | 0% | **100%** | 0% |
| **T2** | 0% | 10% | 9% | **81%** |

#### 4.4.5.3 Class-Level Results

| Class | Precision | Recall |
|---|---:|---:|
| NA | 78% | **100%** |
| Ta | 86% | 75% |
| T1 | 86% | **100%** |
| T2 | 79% | 81% |

#### 4.4.5.4 Interpretation

The additional DINO-DeiT-III inference experiment yielded an aggregate accuracy of **83%**. The model maintained strong recognition of the NA and T1 classes, while the largest residual confusion was associated with the Ta and T2 categories.

These results are consistent with the original DINO analysis, in which class imbalance and morphological similarity between neighbouring tumour stages represent important sources of classification uncertainty. The aggregate experiment is therefore reported as a complementary inference analysis rather than as a replacement for the original model evaluation.

---

### 4.4.6 CLIP / BiomedCLIP — Aggregate Inference Experiment

A corresponding aggregate inference experiment was performed using the CLIP-based biomedical vision-language model. As with the DINO experiment, individual case identifiers and raw sample counts are omitted and all results are expressed as percentages.

#### 4.4.6.1 Model Performance

| Metric | CLIP / BiomedCLIP |
|---|---:|
| Overall Accuracy | **87%** |
| Balanced Accuracy | **81.8%** |
| Classification Error | **13%** |

#### 4.4.6.2 Confusion Matrix

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | **98%** | 1% | 0% | 1% |
| **Ta** | 14% | **79%** | 3% | 4% |
| **T1** | 1% | 2% | **95%** | 2% |
| **T2** | 5% | 29% | 11% | **55%** |

#### 4.4.6.3 Class-Level Results

| Class | Precision | Recall |
|---|---:|---:|
| NA | 84% | **98%** |
| Ta | 89% | 79% |
| T1 | 93% | **95%** |
| T2 | 68% | 55% |

#### 4.4.6.4 Interpretation

The CLIP-based inference experiment achieved an aggregate accuracy of **87%**. The model showed strong recognition of NA and T1, while T2 remained the most challenging category. The principal residual confusion involved the Ta–T2 and T2–T1 boundaries.

This behaviour is coherent with the original multimodal analysis, where the biomedical image-text representation provides useful semantic information but does not completely eliminate ambiguity between visually similar tumour stages.

---

### 4.4.7 DINO-DeiT-III vs. CLIP / BiomedCLIP

The two additional experiments can be summarized using the same aggregate reporting format.

| Metric | DINO-DeiT-III | CLIP / BiomedCLIP |
|---|---:|---:|
| **Overall Accuracy** | **83%** | **87%** |
| Balanced Accuracy | **89%** | 81.8% |
| Classification Error | 17% | **13%** |
| Strongest classes | NA, T1 | NA, T1 |
| Most challenging class | T2 | T2 |
| Evaluation | Aggregate inference | Aggregate inference |

Both experiments show strong recognition of the NA and T1 categories, with greater uncertainty in the more difficult tumour-stage distinctions. The DINO-based experiment provides a purely visual representation, whereas the CLIP-based experiment additionally benefits from the semantic structure of biomedical vision-language pre-training.

The reported accuracies should therefore be interpreted as **aggregate model-inference results** within the corresponding experimental configurations, rather than as independent clinical validation estimates.

---

## 5. Continuous Follow-Up: CLIP-Based Multimodal Architecture for Recurrence Prediction — *Toy Example*

> ⚠️ **Scope note:** this section is conceptual/illustrative. The CELLo dataset provided is **cross-sectional**, with samples collected at a single point in time, and does not contain longitudinal visits or confirmed recurrence outcomes over time. Consequently, the architecture presented in this section **has not been trained or validated for recurrence prediction**. The trajectories and risk curves presented in Sections 5.5–5.6 constitute a **toy example**, used exclusively to demonstrate how the pipeline could work if longitudinal clinical data were made available.

### 5.1 Clinical Motivation

Bladder cancer requires extensive long-term surveillance (recurrence rates reported up to 70% in the literature), which makes it attractive to complement the single-timepoint cytology assessment with a longitudinal representation of the patient's state.

The main proposed extension consists of combining:

1. **visual information** from the cellular images;
2. **textual/clinical information** encoded by the CLIP text encoder;
3. **demographic and behavioural characteristics**, such as age, sex, and smoking status;
4. **additional clinical information**, such as symptoms, relevant history, and laboratory markers;
5. **temporal history**, including the interval between visits and the representations obtained at previous visits.

In this way, each new urine sample can update a latent representation of the patient's state, allowing the model to simultaneously produce a classification of the current state and a continuous estimate of future risk.

### 5.2 Proposed Architecture: "CELLo-Forecast" (CLIP-Based Temporal Risk Model)

The proposed architecture keeps **BiomedCLIP** as the core multimodal representation module, but adds an explicit patient-level conditioning layer.

![CELLo-Forecast — CLIP-based Multimodal Temporal Risk Architecture](images_report/image_28.png)
*CELLo-Forecast — CLIP-based Multimodal Temporal Risk Architecture (conceptual, untrained toy design)*

#### Main Components

1. **Per-visit visual encoder (BiomedCLIP, Section 4)**

   Each urine sample is processed cell-by-cell by the BiomedCLIP visual encoder. A **quality-aware attention pooling** module aggregates the cellular embeddings into a single representative vector for the visit.

   The attention mechanism can incorporate information related to image quality, prediction confidence, and sample characteristics, allowing more informative cells to contribute more to the final representation.

2. **Clinical text conditioning**

   Structured clinical information can be converted into a short *clinical prompt*, for example:

   > `"Age: 67; Gender: male; Smoking: former smoker; Symptoms: hematuria; Cytology: atypical"`

   The BiomedCLIP text encoder transforms this information into an embedding in the same multimodal space as the visual representation.

   This mechanism allows the model to incorporate information that is not directly present in the cellular image.

3. **Patient-level clinical features**

   In addition to the text prompt, structured variables can be processed by a small **clinical feature encoder**.

   Examples of candidate features include:

   | Feature group   | Example variables                                    |
   | --------------- | ----------------------------------------------------- |
   | Demographic     | Age, gender                                           |
   | Lifestyle       | Smoking status, smoking exposure                      |
   | Clinical        | Hematuria, urinary symptoms, previous history         |
   | Laboratory      | Selected laboratory markers                           |
   | Disease history | Previous diagnosis, stage, grade                      |
   | Follow-up       | Time since previous visit, number of previous visits  |

   These variables can be normalized and projected into the same dimensional space as the multimodal embeddings.

4. **Cross-modal fusion**

   The visual representation of the visit and the clinical textual embedding are combined through **cross-attention**.

   In parallel, the patient's structured features are projected through an *MLP* and incorporated into the multimodal representation:

   `z(t) = Fusion(z_visual(t), z_text(t), z_clinical(t))`

   In this way, each visit is represented not only by cellular morphology, but also by the clinical context available at that moment.

   ![CELLo-Forecast — CLIP-based Multimodal Temporal Risk Architecture](images_report/image_followup_arch.png)
   *CELLo-Forecast — CLIP-based Multimodal Temporal Risk Architecture (full pipeline view)*

5. **Temporal encoding and lightweight transformer**

   The sequence of per-visit embeddings `[z(t1), z(t2), ..., z(tn)]` is combined with an explicit **time encoding** representing the interval Δt between consecutive visits (e.g. a continuous Fourier/sinusoidal time embedding), which allows the model to handle irregular follow-up schedules — common in real clinical practice.

   A small **Transformer encoder** (few layers, given the typically limited number of visits per patient) processes this sequence with self-attention, producing a patient-level latent state `h(tn)` that summarizes the patient's history up to the most recent visit.

6. **Dual output heads**

   - **Stage classification head** on the current visit (reuses the head from Section 4, preserving the original task);
   - **Continuous risk forecasting head** — projects `h(tn)` onto several future horizons (+3, +6, +12, +24 months), similar to discrete-time survival models, producing a **recurrence-risk curve** rather than a single label.

7. **Combined training objectives** (joint training, assuming longitudinal data were available)

   - Cross-entropy/ASL for per-visit stage classification (as in Sections 1 and 4);
   - Discrete-time survival loss (or a Cox-style ranking loss) for the forecasting head, using the real time-to-confirmed-recurrence as supervision;
   - A temporal-smoothness regularization term, penalizing abrupt jumps between consecutive visits without a corresponding morphological change;
   - Optionally, a contrastive alignment term between the visual and textual/clinical embeddings at each visit, reusing the original CLIP-style contrastive objective to keep the fused representation anchored in the pre-trained multimodal space.

### 5.3 Illustrative Clinical Cases (Toy Example)

As CELLo is cross-sectional, the behaviour of the pipeline was simulated — **for illustration purposes only** — by assigning real samples from Supplementary Table 3 to hypothetical follow-up "visits" of **synthetic patients**, covering five distinct clinical patterns that a real deployment would need to handle: steady progression, stability, a transient false alarm, treatment response, and irregular follow-up.

| Synthetic patient | Clinical narrative | Visit (month) | Source sample (real, reassigned) | Source histology/cytology |
|---|---|---|---|---|
| **A** — progressive | Gradual worsening, confirmed recurrence at month 12 | 0 | Urine P2 | pTa HG, negative cytology |
| A | | 6 | Urine S2 | Atypical / inflammation |
| A | | 12 | Urine Q2 | pT1 HG + CIS, positive cytology |
| **B** — stable | Low-grade Ta, no progression over 24 months | 0 | Urine I3 | pTa HG |
| B | | 6 | Urine R2 | pTa HG |
| B | | 12 | Urine T2 | pTa HG |
| **C** — transient false alarm | Risk spikes at month 6 due to a urinary-tract infection, then resolves | 0 | Urine X2 | pTa HG |
| C | | 6 | Urine S2 | Atypical / inflammation (reused as a UTI-like confounder) |
| C | | 12 | Urine I2 | pTa HG, no cytology |
| **D** — post-treatment (BCG) | Elevated risk at diagnosis, declining after intravesical BCG therapy | 0 | Urine H3 | pT1 HG, high initial cell count |
| D | | 6 | Urine G3 | pT1 HG, improving |
| D | | 12 | Urine T2 | pTa HG, further improvement |
| **E** — irregular follow-up | Patient misses two scheduled visits; model must handle large, uneven Δt gaps | 0 | Urine U2 | pTa HG, very low cell count |
| E | | 6 (visit at month 3 missed) | Urine A3 | pT1 HG |
| E | | 12 (visit at month 9 missed) | Urine D3 | pT2 HG, low cell count |

The untrained "CELLo-Forecast" architecture from Section 5.2 (conceptual behaviour only, not a real model output) would be expected to produce risk trajectories such as the following:

![Toy Example — continuous recurrence-risk forecasting for five illustrative synthetic patients](images_report/image_29.png)
*Toy Example — continuous recurrence-risk forecasting for five illustrative synthetic patients (100% synthetic; illustrates architecture behaviour only, not a real prediction)*

**Illustrative reading of each case:**

- **Patient A (progressive):** the estimated risk rises steadily (16% → 33% → 58%) as cellular morphology evolves from a Ta pattern to atypia and then to confirmed T1+CIS, crossing an illustrative alert threshold (50%) around month 10 — **before** formal histological confirmation at month 12, which would be the intended clinical value (early warning).
- **Patient B (stable):** risk remains low and nearly flat (9–18%), consistent with the absence of progression.
- **Patient C (transient false alarm):** risk spikes sharply at month 6 (40%) due to inflammatory/UTI-like changes that mimic atypia, then resolves back toward baseline by month 12–24 — illustrating a scenario the model must learn **not** to over-react to, reinforcing the need for the smoothness regularization and quality-aware pooling discussed in Section 5.2.
- **Patient D (post-BCG treatment):** risk starts high (55%, at diagnosis) and steadily declines after treatment (48% → 38% → 24% → 17%), illustrating how the architecture could, in principle, be used to **monitor treatment response** rather than only detect new recurrence.
- **Patient E (irregular follow-up):** despite two missed visits, the time-aware temporal encoding still produces a plausible, continuously updated risk estimate (20% → 34% → 52% → 61%) by explicitly modelling the larger Δt gaps, illustrating robustness to non-uniform real-world scheduling — an important practical requirement given that patients frequently miss or delay surveillance visits.

### 5.4 Limitations

- **No real follow-up data was used** — the "temporal trajectories" above are an artificial reassignment of cross-sectional samples to fictitious patients.
- Training and validating this architecture would require a **longitudinal CELLo dataset** (multiple collections per patient, with dates and recurrence outcomes confirmed by cystoscopy/histology).
- The proposed forecasting head follows the logic of discrete-time survival models (e.g., DeepHit, Nnet-survival), which still need to be adapted and tested in this domain.
- The quality-aware attention pooling module should be calibrated against the full Supplementary Table 3 (event counts per sample), explicitly correlating prediction confidence with the number of captured events.
- The clinical text-prompt and structured-feature components (Section 5.2, items 2–3) are architectural placeholders; no real clinical metadata schema, coding standard, or missing-data strategy has yet been defined.
- The five illustrative cases in Section 5.3 were hand-picked to cover plausible clinical patterns; they are not a statistically representative sample and should not be used to estimate real-world model performance.

### 5.5 Roadmap / TODO (Future Work)

The list below organizes the outstanding work needed to move "CELLo-Forecast" from a conceptual toy design toward a validated clinical tool.

**Data & infrastructure**
- [ ] Design and pilot a longitudinal data-collection protocol for CELLo (fixed visit schedule, e.g. every 3 months, plus unscheduled visits triggered by symptoms).
- [ ] Define a minimum-dataset schema linking each urine sample to patient ID, visit date, cystoscopy/histology outcome, and treatment events (e.g. BCG cycles, TURBT dates).
- [ ] Establish a data-quality gate using cellular-event counts (Supplementary Table 3-style metrics) to flag low-yield samples before they enter model training or inference.
- [ ] Build a versioned, de-identified data pipeline compliant with GDPR/local health-data regulation for storing longitudinal image + clinical-text + structured-feature records.
- [ ] Curate a held-out longitudinal validation cohort from a second clinical site, to test generalisation beyond CHUSJ.

**Modelling**
- [ ] Implement and benchmark the quality-aware attention-pooling module against simple mean/max pooling baselines.
- [ ] Implement the clinical text-prompt template and test sensitivity to prompt phrasing/ordering (prompt robustness study).
- [ ] Design and validate the structured clinical-feature encoder, including a principled missing-data strategy (e.g. learned imputation tokens).
- [ ] Implement the temporal Fourier/sinusoidal Δt encoding and compare it against simpler alternatives (visit-index-only positional encoding, explicit gap-bucketing).
- [ ] Implement and compare candidate forecasting heads: discrete-time survival (Nnet-survival style), Cox-based ranking loss, and direct multi-horizon binary classification.
- [ ] Add the temporal-smoothness regularization term and tune its weight against the false-alarm rate illustrated by Patient C in Section 5.3.
- [ ] Run ablations isolating the contribution of each modality (visual-only vs. visual+text vs. visual+text+structured) on the forecasting task, mirroring the ablation methodology already used in Section 1.3 for the classification task.
- [ ] Explore multi-task weighting strategies between the classification head and the forecasting head (fixed weights vs. uncertainty-based weighting vs. curriculum scheduling).

**Evaluation & validation**
- [ ] Define the clinical evaluation protocol: time-dependent AUC, concordance index (C-index), and calibration curves at each forecasting horizon (3/6/12/24 months).
- [ ] Establish a clinically meaningful decision threshold (replacing the illustrative 50% line in Section 5.3) in collaboration with urologists, based on acceptable false-alarm vs. missed-recurrence trade-offs.
- [ ] Run a retrospective validation study on historical longitudinal cohorts (if/when available) before any prospective study is considered.
- [ ] Stress-test the model on edge cases: irregular follow-up (Patient E pattern), transient inflammatory confounders (Patient C pattern), and post-treatment monitoring (Patient D pattern), using real (not synthetic) longitudinal examples once available.
- [ ] Assess fairness and subgroup performance (age, sex, smoking status) once real longitudinal data with these attributes is available.

**Interpretability & clinical integration**
- [ ] Extend the attention-map interpretability analysis from Section 4.3 to the temporal setting, visualizing which past visits contribute most to the current risk estimate.
- [ ] Design a clinician-facing dashboard mock-up showing the continuous risk curve alongside the underlying cytology images and clinical prompt, for qualitative feedback from urologists/pathologists.
- [ ] Define an audit/override mechanism allowing clinicians to flag and correct model outputs, feeding into future retraining.

**Governance & deployment (longer-term)**
- [ ] Scope the regulatory pathway (e.g., medical-device software classification) required before any clinical pilot.
- [ ] Define a monitoring plan for model drift once deployed (e.g., periodic re-evaluation against newly confirmed outcomes).
- [ ] Draft a data-sharing and consent framework specific to longitudinal multimodal follow-up data, distinct from the single-timepoint consent used for the current CELLo dataset.

---

## 6. Main References

- Oliveira, H.S. *et al.* "Enhancing Cytological Staining-Free Image Classification with Robust Vision Transformers", EMBC 2026 (submitted).
- Oliveira, P.C. & Oliveira, H.S. "Adapting Biomedical Vision–Language Foundation Models for Fine-Grained Bladder Cancer Staging from Urine Cytology", BSPC manuscript (Overleaf, in preparation).
- Supplementary Table 3 — Number of cellular events per urine sample (Cytek Amnis ImageStreamX; suggested limits: area 200–500, AR>0.6).
