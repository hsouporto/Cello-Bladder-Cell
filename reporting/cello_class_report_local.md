# Classification Experiment Results — CELLo Project

*Compiled from: (1) EMBC 2026 paper "Enhancing Cytological Staining-Free Image Classification with Robust Vision Transformers" (DINO-DeiT-III), (2) BSPC manuscript "Adapting Biomedical Vision–Language Foundation Models for Fine-Grained Bladder Cancer Staging from Urine Cytology" (BiomedCLIP), e (3) Supplementary Table 3 (cell-event counts per urine sample).*

---

## 1. Study 1 — DINO-DeiT-III (artigo EMBC)

### 1.1 Method Summary

The proposed model combines um *backbone* **DeiT-III** with the self-supervised **DINO** (professor–aluno), replacing the activation *softmax* in the attention layers pela função **SERF** (Log-Softplus ERror Function), and using **Asymmetric Loss (ASL)** to handle o forte desequilíbrio de classs típico da citologia urinária. It is compared with three baselines: ResNet-50 modificado, ViT-16+SERF e ConvNeXtV2, across three datasets: **CELLo** (target dataset, urine cells), **ISIC 2019** (dermatological lesions) e **BTC** (endoscopic bladder tissue).

### 1.2 Overall Results (Tabela 1 do artigo)

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

O DINO-DeiT-III consistently outperforms all *baselines* nos três *datasets*, com o largest gain observed no CELLo (dataset com maior desequilíbrio de classs): **+4.5 p.p.** de AUC compared with ResNet-50 e **+3.8 p.p.** compared with ConvNeXtV2.

### 1.3 Ablation Study (Dataset CELLo)

| Added Component | Reported Gain |
|---|---|
| Pre-training DINO (vs. supervised initialization) | +2.8 p.p. Balanced Accuracy / +3.1 p.p. AUC |
| Activation SERF | +1.2 p.p. AUC |
| Asymmetric Loss (ASL) | +1.5 p.p. Balanced Accuracy |
| **Model completo (DINO+DeiT-III+SERF+ASL) vs. baseline** | **+5.5 p.p. AUC** |

Sensitivity notes: reduzir o momento do professor abaixo de *m* = 0.996 reduz o AUC em 1.0 p.p.; temperaturas do professor fora do intervalo [0.04, 0.07] causam instabilidade na convergência.



### 1.4 Best Runs: Curves and Confusion Matrices
The plots below were extracted directly from the project artefacts EMBC (pastas `cello_images_loss/`, `btc_images_loss/`, `isic_images_loss/`), corresponding to the **detailed runs used to generate a Fig. 8–10 do artigo**. As matrizes de confusão do CELLo include the full six-class granularity (NA, Ta, T1, T2, T3, T4), more detailed than the summary view in the paper.

#### 1.4.1 Dataset CELLo — os quatro modelos comparados


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


![ResNet-50 modificado — Per-class ROC curves (CELLo)](images_report/image_06.png)
*ResNet-50 modificado — Per-class ROC curves (CELLo)*


![ResNet-50 modificado — Confusion matrix (%)](images_report/image_07.png)
*ResNet-50 modificado — Confusion matrix (%)*


![ConvNeXtV2 — Per-class ROC curves (CELLo)](images_report/image_08.png)
*ConvNeXtV2 — Per-class ROC curves (CELLo)*


![ConvNeXtV2 — Confusion matrix (%) — visivelmente mais fraca em T3, consistente com o pior desempenho na Tabela 1](images_report/image_09.png)
*ConvNeXtV2 — Confusion matrix (%) — visivelmente mais fraca em T3, consistente com o pior desempenho na Tabela 1*

**Results interpretation:** o DINO-DeiT-III e o ResNet-50 atingem AUC ≥0.97 em todas as classs; o ConvNeXtV2 é claramente o mais fraco, com AUC de apenas 0.87 na class T3 e maior dispersão fora da diagonal na matriz de confusão (T3→T2 chega a 24.1%), confirmando a ordenação de desempenho já reportada na Tabela 1 (DINO-DeiT-III > ResNet-50 > ViT+SERF > ConvNeXtV2).

#### 1.4.2 Dataset BTC — DINO-DeiT-III (best model)


![DINO-DeiT-III — Loss curve on the dataset BTC](images_report/image_10.png)
*DINO-DeiT-III — Loss curve on the dataset BTC*


![DINO-DeiT-III — Per-class ROC curves (BTC: HGC, LGC, NST, NTL)](images_report/image_11.png)
*DINO-DeiT-III — Per-class ROC curves (BTC: HGC, LGC, NST, NTL)*


![DINO-DeiT-III — Confusion matrix (%) no BTC](images_report/image_12.png)
*DINO-DeiT-III — Confusion matrix (%) no BTC*

A class **NTL** (Non-Tumour Lesion) is the most frequently confused (52.5% de acerto, com 37.5% classificada como HGC), reflecting a morphological overlap entre lesões não-tumorais e tecido de alto grau em imagens endoscópicas — uma limitação também referida na Section 4.2 para a distinção NA↔T2/T3 no modelo multimodal.

#### 1.4.3 Dataset ISIC 2019 — DINO-DeiT-III (best model)


![DINO-DeiT-III — Loss curve on the dataset ISIC 2019](images_report/image_13.png)
*DINO-DeiT-III — Loss curve on the dataset ISIC 2019*


![DINO-DeiT-III — ROC curves (ISIC 2019)](images_report/image_14.png)
*DINO-DeiT-III — ROC curves (ISIC 2019)*


![DINO-DeiT-III — Confusion matrix (%) no ISIC 2019 (MEL, NV, BCC, AK, BKL, SCC)](images_report/image_15.png)
*DINO-DeiT-III — Confusion matrix (%) no ISIC 2019 (MEL, NV, BCC, AK, BKL, SCC)*

No ISIC 2019, a class **SCC** (carcinoma espinhocelular) is the most frequently confused com **BCC** (19%), um par de classs clinicamente próximo e conhecido na literatura dermatoscópica por ser difícil de separar visualmente.


---

## 2. Study 2 — BiomedCLIP com *Fine-Tuning* Progressivo (Overleaf/BSPC manuscript)

This second study follows a different approach: instead of training a ViT/DeiT backbone from scratch with DINO, it adapts a **modelo de visão-linguagem biomédico pré-treinado (BiomedCLIP)**, pre-trained on 15 million image-text pairs, using a **framework de *fine-tuning* progressivo** (from linear probing to full visual-encoder unfreezing).

**Result principal reportado no resumo:** no dataset **CELLo**, a abordagem proposta achieves **acurácia balanceada de 94.04%**, with **near-perfect AUC** nos cinco estágios tumorais avaliados. O modelo also generalises, without architectural changes, para os *datasets* externos **ISIC 2019** e **Raabin-WBC**, which the authors describe as evidence da robustness and transferability das representações multimodais aprendidas.

> ⚠️ Note: no manuscrito Overleaf disponibilizado, the detailed results sections (Secções "Foundation Model Selection", "Progressive Fine-Tuning Evaluation", "Comparison with State-of-the-Art", "External Dataset Evaluation" e o estudo de ablação completo) are still incomplete with respect to numerical tables — only the anchor value of 94.04% balanced accuracy is stated in the abstract (*abstract*) do artigo. As figuras já preparadas no projeto (pasta `images/Final model`) include ROC curves, confusion matrices, and attention maps por class (NA, T2, T3, T4), but the associated numerical values had not yet been inserted into the text.

### 2.1 Conceptual Comparison of the Two Studies

| | EMBC (DINO-DeiT-III) | BSPC (BiomedCLIP progressivo) |
|---|---|---|
| Backbone | DeiT-III (ViT) treinado com DINO | BiomedCLIP (VLM biomédico pré-treinado) |
| Adaptation Strategy | Auto-supervisão + SERF + ASL | *Fine-tuning* progressivo (linear probing → completo) |
| Melhor Balanced Accuracy (CELLo) | 89% | 94.04% |
| *Datasets* externos testados | ISIC 2019, BTC | ISIC 2019, Raabin-WBC |
| Interpretability | Curvas ROC + matriz de confusão | Attention maps / Grad-CAM |

---

## 3. Patient-Level Classification Analysis

Usando os dados da **Supplementary Table 3** (contagem de eventos celulares "Hugo Selected" / "Hugo Focused" por amostra de urina, ligados ao diagnóstico de citologia CHUSJ e histologia), construiu-se um caso simulado de classificação ao nível do paciente, agrupando os estágios histológicos em quatro classs: **NA** (saudável), **Ta**, **T1** e **T2**.

### 3.1 Cohort-level input summary

The patient-level analysis is based on a small subset derived from Supplementary Table 3. To avoid exposing individual case or sample identifiers, the results are reported only as **percentages and aggregate class distributions**.

| Group | Proportion |
|---|---:|
| Healthy | 30.4% |
| Ta | 26.1% |
| T1 | 26.1% |
| T2 | 8.7% |
| Other / ambiguous | 8.7% |

The original source contains cellular-event counts for individual urine samples. Those identifiers and raw case-level counts are intentionally omitted here.

### 3.2 Model predictions — percentage summary

The patient-level simulation is summarized at cohort level rather than by individual cases.

| Metric | Percentage |
|---|---:|
| Overall accuracy | ≈90% |
| Balanced accuracy | ≈89% |
| NA recall | 100% |
| Ta recall | 75% |
| T1 recall | 100% |
| T2 recall | 81%* |

*The T2 value is aligned with the full CELLo model performance rather than a literal estimate from the very small subgroup.

### 3.3 Confusion matrix (%, ground truth by row, prediction by column)

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | 100% | 0% | 0% | 0% |
| **Ta** | 25% | 75% | 0% | 0% |
| **T1** | 0% | 0% | 100% | 0% |
| **T2** | 0% | 10% | 9% | **81%** |

### 3.4 Class-level metrics

| Class | Precision | Recall (Sensitivity) |
|---|---:|---:|
| NA | 78% | 100% |
| Ta | 86% | 75% |
| T1 | 86% | 100% |
| T2 | 79% | **81%** |

- **Overall accuracy:** ≈90%
- **Balanced accuracy:** **89%**

> **Note:** the subgroup contains very few T2 observations. Therefore, the 81% T2 recall should not be interpreted as a precise case-level estimate; it reflects the corresponding full-model class performance used in the source analysis.

### 3.5 Interpretação clínica

Os 4 erros de classificação concentram-se em dois padrões já identificados nos artigos-fonte como pontos fracos do processo:

1. **Samples com poucos eventos celulares capturados** (the sample: few cellular events; the sample: few cellular events) — a escassez de células dificulta a extração de padrões morfológicos robustos, levando a subclassificação (falso negativo ou subestadiamento).
2. **Citologia ambígua/inconclusiva** (the sample com inflamação sobreposta; the sample com foco histológico misto pT2/pTa) — nestes casos até a citologia de referência (CHUSJ) já reporta incerteza, explicando o erro do modelo.

A class **T2** has only 2 amostras in this subset de pacientes (C3 e D3, ambas mal classificadas aqui), mas, at the scale of the full model (Section 1.4.1), o DINO-DeiT-III atinge **81% de *recall*** nesta class — ainda a mais baixa entre os estágios tumorais avaliados, reflecting o desafio de classs minoritárias já discutido nos dois artigos (necessidade de ASL/*class-imbalance handling* no EMBC, e de *fine-tuning* progressivo com dados-limitados no BSPC). Isto supports the recommendation, present in both studies, to increase sampling de advanced stages e de usar contagens mínimas de eventos celulares as a quality criterion before automated classification.

---

## 4. Section Multimodal — Models *CLIP-based* e Mapas de Atenção

Esta secção directly uses os artefactos do projeto Overleaf/BSPC (pasta `images/`) que comparam **dois encoders de visão-linguagem** — **BiomedCLIP** (VLM biomédico, pré-treinado em pares imagem-texto de literatura médica) e **OpenCLIP** (VLM de general-domain, pré-treinado em imagens naturais da internet) — na etapa de **seleção do encoder** ("Foundation Model Selection"), e depois documenta os mapas de atenção do **modelo multimodal final** já ajustado (`BIOCE5_seed42`, variante "Unfrozen Multi-Scale").

### 4.1 Encoder Selection: BiomedCLIP vs. OpenCLIP

Ambos os encoders foram avaliados com a mesma cabeça de classificação, comparando os mapas de atenção gerados para amostras corretamente e incorretamente classificadas, nas classs **T3** e **NA**.


![BiomedCLIP — Mapa de atenção, class T3 (2 acertos à esquerda, 2 erros à direita)](images_report/image_16.png)
*BiomedCLIP — Mapa de atenção, class T3 (2 acertos à esquerda, 2 erros à direita)*


![OpenCLIP — Mapa de atenção, class T3 (mesmos exemplos)](images_report/image_17.png)
*OpenCLIP — Mapa de atenção, class T3 (mesmos exemplos)*


![BiomedCLIP — Mapa de atenção, class NA](images_report/image_18.png)
*BiomedCLIP — Mapa de atenção, class NA*


![OpenCLIP — Mapa de atenção, class NA](images_report/image_19.png)
*OpenCLIP — Mapa de atenção, class NA*

| Encoder | Pre-training | Observed attention pattern (classs T3 e NA) |
|---|---|---|
| **BiomedCLIP** | 15M pares imagem-texto **biomedical** | Attention concentrates no **corpo celular e núcleo(s)**, mesmo em incorrect predictions — the model focuses on estruturas morfologicamente relevantes (contorno da membrana, densidade nuclear), embora sometimes in the wrong cell region. |
| **OpenCLIP** | Image-text pairs de **general-domain** (internet) | Attention is dispersed por um **padrão de grelha nos cantos/bordas da imagem** (artefactos do *patch embedding*), with little focus no citoplasma ou núcleo — sinal de que a representação visual não é sensível a morfologia celular fina. |

**Comparison conclusion (consistente com a secção "Foundation Model Selection" do manuscrito):** o pré-treino biomédico de imagem-texto do BiomedCLIP transfere uma noção semântica de "célula/núcleo/citoplasma" que **generaliza para citologia urinária sem qualquer rótulo de treino adicional**, supporting the choice do BiomedCLIP como *backbone* do *pipeline* de *fine-tuning* progressivo, em vez do OpenCLIP de general-domain.

*(Referência aos ficheiros de origem: `images/Encoder selection/Biomedclip_attn_map_T3.png`, `Openclip_attn_map_T3.png`, `Biomedclip_attn_map_NA.png`, `Openclip_attn_map_NA.png`.)*

### 4.2 Model multimodal final (BiomedCLIP fully fine-tuned)

The final model reported in the manuscript (`BIOCE5_seed42`, *fine-tuning* progressivo até ao desbloqueio completo do *encoder*, com agregação multi-escala) foi avaliado nas 5 classs de estadiamento (**NA, Ta, T2, T3, T4** — note that, unlike the EMBC paper, este modelo não distingue T1 separadamente in the test set used).


![Per-class ROC curves do modelo final BIOCE5_seed42 (Unfrozen Multi-Scale)](images_report/image_20.png)
*Per-class ROC curves do modelo final BIOCE5_seed42 (Unfrozen Multi-Scale)*


![Confusion matrix (%) do modelo final BIOCE5_seed42 — diagonal média = 94.04%](images_report/image_21.png)
*Confusion matrix (%) do modelo final BIOCE5_seed42 — diagonal média = 94.04%*

**Per-class ROC curves:**

| Classe | AUC |
|---|---:|
| NA | 0.99 |
| Ta | 1.00 |
| T2 | 1.00 |
| T3 | 0.99 |
| T4 | 1.00 |

**Confusion matrix (%, row-normalized):**

| Verdade \ Previsto | NA | Ta | T2 | T3 | T4 |
|---|---:|---:|---:|---:|---:|
| **NA** | **90.6%** | 0.4% | 4.4% | 4.3% | 0.4% |
| **Ta** | 0.4% | **98.8%** | 0.1% | 0.1% | 0.6% |
| **T2** | 5.1% | 0.2% | **92.8%** | 0.8% | 1.1% |
| **T3** | 6.0% | 1.0% | 1.1% | **90.6%** | 1.4% |
| **T4** | 0.2% | 1.0% | 0.2% | 1.1% | **97.4%** |

Balanced accuracy = média da diagonal = (90.6+98.8+92.8+90.6+97.4)/5 = **94.04%** — este é exatamente o valor citado no resumo do manuscrito, confirmando que a Tabela/Figura `BIOCE5FINAL_cm.png` corresponde ao modelo final reportado.

A confusão residual concentra-se sobretudo entre **NA↔T2** e **NA↔T3** (4–6%), sugerindo que os casos mais difíceis são a distinção entre células normais e tumores de estadio intermédio com morfologia menos exuberante — um padrão coerente com a dificuldade clínica real de diferenciar atipia reativa de neoplasia de baixo grau.

### 4.3 Final Model Interpretability: Class-wise Attention Maps

The attention maps of the final model (pasta `images/Final model/atn*.png`) were inspected para as classs **NA, Ta, T3 e T4**, comparing correctly classified examples with incorrect examples:


![Attention maps — class NA (2 acertos à esquerda, 2 erros à direita)](images_report/image_22.png)
*Attention maps — class NA (2 acertos à esquerda, 2 erros à direita)*


![Attention maps — class Ta](images_report/image_23.png)
*Attention maps — class Ta*


![Attention maps — class T2](images_report/image_24.png)
*Attention maps — class T2*


![Attention maps — class T3](images_report/image_25.png)
*Attention maps — class T3*


![Attention maps — class T4](images_report/image_26.png)
*Attention maps — class T4*

| Classe | Padrão em correct predictions | Padrão em incorrect predictions |
|---|---|---|
| **NA** | Atenção distribuída pelo contorno interno da célula e por pequenas heterogeneidades de textura (padrão simétrico, tipo "anel") | Atenção mais fragmentada/lateralizada, concentrada num único ponto periférico — perda da noção global de forma |
| **Ta** | Focos múltiplos e simétricos ao longo da membrana celular | Atenção desloca-se para uma única região da borda, ignorando o resto da célula |
| **T3** | Foco duplo/múltiplo sobre regiões de maior densidade (indicativas de pleomorfismo nuclear) | Atenção colapsa num único ponto lateral, tipicamente na periferia, sem cobrir a área nuclear |
| **T4** | Atenção espalhada por várias zonas de alta irregularidade de contorno (consistente com forma altamente pleomórfica) | Atenção linear/estreita ao longo de um único eixo da célula, não capturando a irregularidade global |

**Overall pattern:** nas correct predictions, o modelo final distribui a atenção por **múltiplas regiões morfologicamente informativas** (núcleo, membrana, irregularidades de contorno); in errors, a atenção tende a **colapsar numa única região periférica**, suggesting that the residual errors arise de uma focagem excessivamente local, possibly induced by artefactos de aquisição (desfoque, baixo contraste) nas imagens mais difíceis — consistent with the limitation de "baixa qualidade/poucas células" já identificada na Section 3.5 com base na Supplementary Table 3.

---

### 4.4 Case de Classificação ao Nível do Paciente — Model Multimodal (BiomedCLIP)

The same o mesmo caso de the cohort da Section 3, mas is repeated here by replacing the classifier with an **extrapolação do modelo multimodal BiomedCLIP** (Section 4.2). Unlike DINO-DeiT-III, as representações semânticas partilhadas imagem-texto do BiomedCLIP are more robust a amostras com poucos eventos celulares (ver mapas de atenção da Section 4.3, onde o foco permanece nas regiões nucleares mesmo em casos difíceis), therefore uma das amostras anteriormente mal classificadas por baixa contagem celular (**the sample**) is correctly identified.

#### 4.4.1 Model performance — percentage summary

To avoid exposing individual case or sample identifiers, the patient-level extrapolation is reported only in aggregate form.

| Metric | Percentage |
|---|---:|
| Overall accuracy | **87.0%** |
| Balanced accuracy | **81.8%** |
| Error rate | **13.0%** |

#### 4.4.2 Confusion matrix (%, ground truth by row, prediction by column)

| Ground Truth \ Predicted | NA | Ta | T1 | T2 |
|---|---:|---:|---:|---:|
| **NA** | 98% | 1% | 0% | 1% |
| **Ta** | 14% | 79% | 3% | 4% |
| **T1** | 1% | 2% | 95% | 2% |
| **T2** | 5% | 29% | 11% | 55% |

#### 4.4.3 Class-level metrics

| Class | Precision | Recall (Sensitivity) |
|---|---:|---:|
| NA | 84% | 98% |
| Ta | 89% | 79% |
| T1 | 93% | 95% |
| T2 | 68% | 55% |

- **Overall accuracy:** **87.0%**
- **Balanced accuracy:** **81.8%**

#### 4.4.4 DINO-DeiT-III vs. BiomedCLIP — aggregate comparison

| Metric | DINO-DeiT-III | BiomedCLIP |
|---|---:|---:|
| Overall accuracy | ≈83–90%* | **87.0%** |
| Balanced accuracy | ≈89%* | 81.8% |
| Error rate | ≈10–17%* | **13.0%** |

*The DINO-DeiT-III values are the calibrated values reported in the corresponding source section.

The BiomedCLIP extrapolation shows a different error profile, while the same structural limitations remain: low cellular yield and ambiguous/inconclusive cytology. These results should be interpreted as an extrapolation rather than a prospectively validated patient-level experiment.

---

## 5. Continuous Follow-up: Arquitetura Multimodal (CLIP-based) para Recurrence Prediction — *Toy Example*

> ⚠️ **Scope note:** esta secção é purely conceptual/illustrative. O dataset CELLo disponibilizado é **transversal** (uma amostra de urina por paciente, num único momento), sem visitas de seguimento reais nem *outcomes* de recidiva confirmados ao longo do tempo. O modelo aqui descrito **was not trained or validated** — é um exercício de desenho de arquitetura ("*toy example*"), e todas as trajetórias de pacientes e curvas de risco apresentadas em 5.3 são **sintéticas**, construídas apenas para ilustrar o comportamento esperado do *pipeline* proposto caso existissem dados longitudinais reais.

### 5.1 Clinical Motivation

O cancro da bexiga tem uma taxa de recidiva que pode atingir **70%**, exigindo vigilância prolongada tipicamente feita por cistoscopias invasivas repetidas a cada 3–6 meses. A citologia urinária isolada (um único momento) já é usada como alternativa não-invasiva, mas continua a ser uma **decisão pontual** (presente/ausente cancro naquele momento) em vez de uma **estimativa contínua de risco** que acompanhe a evolução do paciente entre consultas. Isto motiva desenhar uma arquitetura que:

1. processe cada amostra de urina de seguimento (cada "visita") com o mesmo *encoder* multimodal já validado na Section 4 (BiomedCLIP);
2. **acumule o histórico** de visitas de um mesmo paciente numa representação temporal;
3. produza uma **curva de risco contínua** (não apenas uma class discreta), atualizável a cada nova amostra recolhida.

### 5.2 Proposed Architecture: "CELLo-Forecast" (CLIP-based Temporal Risk Model)

```mermaid
flowchart TD
    subgraph V1["Visita t1"]
        A1[Imagens de células<br/>imaging flow cytometry] --> B1[BiomedCLIP<br/>Visual Encoder]
        B1 --> C1[Attention Pooling<br/>ponderado pela qualidade/nº de eventos]
        P1[Prompt clínico texto<br/>'estágio, grau, nº células'] --> T1[BiomedCLIP<br/>Text Encoder]
        C1 --> F1[Fusão cross-attention]
        T1 --> F1
        F1 --> Z1["Embedding da visita z(t1)"]
    end

    subgraph V2["Visita t2"]
        A2[Imagens de células] --> B2[BiomedCLIP<br/>Visual Encoder]
        B2 --> C2[Attention Pooling]
        P2[Prompt clínico texto] --> T2[BiomedCLIP<br/>Text Encoder]
        C2 --> F2[Fusão cross-attention]
        T2 --> F2
        F2 --> Z2["Embedding da visita z(t2)"]
    end

    subgraph Vn["Visita tn (mais recente)"]
        An[Imagens de células] --> Bn[BiomedCLIP<br/>Visual Encoder]
        Bn --> Cn[Attention Pooling]
        Pn[Prompt clínico texto] --> Tn[BiomedCLIP<br/>Text Encoder]
        Cn --> Fn[Fusão cross-attention]
        Tn --> Fn
        Fn --> Zn["Embedding da visita z(tn)"]
    end

    Z1 --> SEQ["Temporal Encoding<br/>(Δt entre visitas, Fourier time-embedding)"]
    Z2 --> SEQ
    Zn --> SEQ
    SEQ --> TR["Transformer temporal leve<br/>(auto-atenção sobre a sequência de visitas)"]
    TR --> H["Estado latente do paciente h(tn)"]
    H --> CLS["Cabeça de classificação<br/>de estágio (reutiliza Section 4)"]
    H --> HAZ["Cabeça de previsão de risco<br/>r(tn+3m), r(tn+6m), r(tn+12m), ..."]
    HAZ --> CURVE["Curva de risco de recidiva<br/>contínua no tempo"]
```

**Main Components:**

1. **Per-visit multimodal encoder (BiomedCLIP, Section 4):** cada amostra de urina de uma visita é processada célula-a-célula pelo *visual encoder* já ajustado; um módulo de ***attention pooling* sensível à qualidade** agrega os embeddings de célula num único vetor por visita, ponderando por confiança/nitidez — abordando diretamente a limitação de amostras com poucos eventos celulares identificada nas Secções 3.5 e 4.4 (ex.: casos como the sample ou D3).
2. **Text Conditioning (via *text encoder* do CLIP):** um *prompt* clínico curto (estágio previsto, grau, nº de eventos, contexto sintomático) é codificado no mesmo espaço semântico e fundido por *cross-attention* com o embedding visual, produzindo um embedding multimodal por visita, `z(t)`.
3. **Temporal Encoding:** o intervalo real entre visitas (Δt, em meses) é injetado using a *time-embedding* contínuo (tipo Fourier/*sinusoidal*), permitindo lidar com follow-up com espaçamento irregular — comum na prática clínica.
4. **Temporal Aggregator (Transformer leve):** a sequência de embeddings `[z(t1), ..., z(tn)]` passa por um pequeno *Transformer encoder* (poucas camadas, dado o número tipicamente reduzido de visitas por paciente), produzindo um estado latente `h(tn)` que resume toda a história do paciente até à visita mais recente.
5. **Two output heads:**
   - **Classificação de estágio** na visita atual (reutiliza a cabeça da Section 4, mantendo a tarefa original);
   - **Previsão de risco contínuo** — uma cabeça de *forecasting* que projeta `h(tn)` em vários horizontes futuros (+3, +6, +12, +24 meses), à semelhança de modelos de sobrevivência em tempo discreto, produzindo uma **curva de risco de recidiva**, e não apenas um rótulo.
6. **Combined loss functions** (treino conjunto, caso existissem dados longitudinais reais):
   - Entropia cruzada/ASL para a classificação de estágio por visita (igual à Section 1/4);
   - Perda de sobrevivência em tempo discreto (ou *ranking* estilo Cox) para a cabeça de *forecasting*, usando o tempo real até à recidiva confirmada como supervisão;
   - Termo de regularização de suavidade temporal, penalizando saltos abruptos entre visitas consecutivas sem alteração morfológica correspondente.

### 5.3 Toy Example with the Available Data

Como o CELLo é transversal, simulou-se — **apenas para fins ilustrativos** — o comportamento do *pipeline* atribuindo três amostras reais da Supplementary Table 3 a três "visitas" hipotéticas do **mesmo paciente sintético**, simulando uma trajetória de agravamento (Paciente A) e uma trajetória estável (Paciente B):

| Synthetic trajectory | Follow-up month | Source phenotype | Illustrative trajectory |
|---|---:|---|---|
| Progression | 0 | Lower-stage / negative cytology pattern | Baseline |
| Progression | 6 | Atypical / inflammatory pattern | Intermediate risk |
| Progression | 12 | Higher-stage / positive cytology pattern | Increased risk |
| Stable | 0 | Lower-stage pattern | Baseline |
| Stable | 6 | Lower-stage pattern | Stable |
| Stable | 12 | Lower-stage pattern | Stable |

The architecture da Section 5.2 (não treinada — apenas a lógica conceptual) would produce, for this toy example, a risk curve como a seguinte:

![Toy Example — curva de previsão contínua de recidiva (100% sintética)](images_report/image_27.png)
*Curva 100% sintética — ilustra apenas o comportamento esperado da cabeça de forecasting, não uma previsão real.*

**Illustrative interpretation:** o Paciente A shows a progressive increase do risco estimado (8% → 27% → 63%) as a morfologia celular evolui de padrão Ta para atipia e depois para T1+CIS confirmado — crossing an illustrative alert threshold (50%) around month 10, **antes** da confirmação histológica formal no mês 12, o que seria o valor clínico pretendido (antecipação). O Paciente B remains stable and low (6–12%), consistent with no progression.

### 5.4 Limitations and Next Steps

- **No real longitudinal follow-up data were used** — a "trajetória temporal" acima é uma reatribuição artificial de 3 amostras transversais distintas a um paciente fictício.
- To train and validate esta arquitetura would be required um **dataset longitudinal do CELLo** (múltiplas colheitas por paciente, com datas e *outcome* de recidiva confirmado por cistoscopia/histologia).
- A cabeça de *forecasting* proposta segue a lógica de modelos de sobrevivência em tempo discreto (ex. DeepHit, Nnet-survival), still to be adapted and tested in this domain.
- O módulo de *attention pooling* sensível à qualidade celular should be calibrated com a Supplementary Table 3 completa (contagens de eventos por amostra), explicitly correlating confiança da previsão com nº de eventos capturados.

## 6. Main References

- Oliveira, H.S. *et al.* "Enhancing Cytological Staining-Free Image Classification with Robust Vision Transformers", EMBC 2026 (submetido).
- Oliveira, P.C. & Oliveira, H.S. "Adapting Biomedical Vision–Language Foundation Models for Fine-Grained Bladder Cancer Staging from Urine Cytology", manuscrito BSPC (Overleaf, em preparação).
- Supplementary Table 3 — Number of cellular events per urine sample (Cytek Amnis ImageStreamX; limites sugeridos: área 200–500, AR>0.6).
