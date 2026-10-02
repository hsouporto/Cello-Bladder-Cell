# Sistema de Diagnóstico Médico com BiomedCLIP e GUI

Este documento resume a arquitetura, os requisitos e a implementação de uma aplicação gráfica (GUI) para diagnóstico médico de imagens (como citologia) utilizando o modelo multimodal **BiomedCLIP**.

---

## 📋 Requisitos do Sistema

### 1. Hardware
* **Processador (CPU):** Intel Core i5 / AMD Ryzen 5 ou superior.
* **Placa Gráfica (GPU):** Placa NVIDIA compatível com CUDA (ex: RTX 3060 / 4060 ou superior, com mínimo de 6 GB a 8 GB de VRAM) recomendada para acelerar a inferência. Também é possível executar em **CPU** (modo mais lento).
* **Memória RAM:** 16 GB recomendados (mínimo de 8 GB).
* **Armazenamento:** Disco SSD com pelo menos 10 GB de espaço livre para dependências e pesos do modelo.

### 2. Software e Dependências
* **Sistema Operativo:** Windows 10/11, Linux (Ubuntu) ou macOS.
* **Python:** Versão 3.9 a 3.11.
* **Bibliotecas Principais:**
  * `torch`, `torchvision` (Deep Learning)
  * `open_clip_torch` (Modelo BiomedCLIP)
  * `gradio` (Interface Gráfica Web/Local)
  * `matplotlib`, `numpy`, `pillow` (Gráficos e manipulação de imagem)

---

## 🩺 Classes de Diagnóstico (Zero-Shot)
O sistema avalia a imagem médica comparando-a com as seguintes classes de referência clínica:
* **NA:** Saudável / Sem Anomalia
* **Ta:** Estágio Tumoral Baixo
* **T1:** Estágio Tumoral T1
* **T2:** Estágio Tumoral T2
* **T3 / T4:** Estágio Tumoral Avançado

---

## 💻 Exemplo de Implementação (Python + Gradio)

```python
import torch
import open_clip
from PIL import Image
import matplotlib.pyplot as plt
import gradio as gr
import numpy as np

# Configuração do dispositivo (GPU ou CPU)
device = "cuda" if torch.cuda.is_available() else "cpu"

# Carregamento do modelo BiomedCLIP
model, _, preprocess = open_clip.create_model_and_transforms(
    'hf-hub:microsoft/BiomedCLIP-PubMedBERT_256-vit_base_patch16_224',
    device=device
)
tokenizer = open_clip.get_tokenizer('hf-hub:microsoft/BiomedCLIP-PubMedBERT_256-vit_base_patch16_224')
model.eval()

# Classes de diagnóstico
classes = [
    "Saudável / Sem Anomalia (NA)",
    "Estágio Tumoral Baixo (Ta)",
    "Estágio Tumoral T1",
    "Estágio Tumoral T2",
    "Estágio Tumoral Avançado (T3/T4)"
]

def diagnosticar_imagem(imagem):
    if imagem is None:
        return None

    if isinstance(imagem, np.ndarray):
        image = Image.fromarray(imagem).convert("RGB")
    else:
        image = imagem.convert("RGB")

    image_tensor = preprocess(image).unsqueeze(0).to(device)
    text_tokens = tokenizer(classes).to(device)

    with torch.no_grad():
        image_features = model.encode_image(image_tensor)
        text_features = model.encode_text(text_tokens)

        image_features /= image_features.norm(dim=-1, keepdim=True)
        text_features /= text_features.norm(dim=-1, keepdim=True)

        text_probs = (100.0 * image_features @ text_features.T).softmax(dim=-1).cpu().numpy()[0]

    # Gráfico de probabilidades
    fig, ax = plt.subplots(figsize=(6, 4))
    pares = sorted(zip(classes, text_probs), key=lambda x: x[1])
    sorted_classes, sorted_probs = zip(*pares)

    y_pos = range(len(classes))
    barras = ax.barh(y_pos, [p * 100 for p in sorted_probs], color="#2980b9")
    ax.set_yticks(y_pos)
    ax.set_yticklabels(sorted_classes, fontsize=9)
    ax.set_xlabel("Probabilidade de Diagnóstico (%)", fontsize=10, fontweight="bold")
    ax.set_title("Confiança do BiomedCLIP", fontsize=11, fontweight="bold")
    ax.set_xlim(0, 100)

    for barra in barras:
        width = barra.get_width()
        ax.text(width + 1, barra.get_y() + barra.get_height()/2, f"{width:.1f}%", va="center", fontsize=9, fontweight="bold")

    plt.tight_layout()
    return fig

# Interface Gradio
demo = gr.Interface(
    fn=diagnosticar_imagem,
    inputs=gr.Image(type="pil", label="Carregar Imagem Médica"),
    outputs=gr.Plot(label="Gráfico de Probabilidades"),
    title="CELLo — Sistema de Diagnóstico com BiomedCLIP"
)

if __name__ == "__main__":
    demo.launch()
```