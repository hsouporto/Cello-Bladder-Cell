# Sistema Avançado de Diagnóstico Médico com BiomedCLIP e Arquitetura Descentralizada

Este documento descreve a arquitetura, os requisitos e a implementação de uma plataforma avançada para diagnóstico médico de imagens citológicas. O sistema suporta **arquitetura descentralizada (Cliente-Servidor)**, **seleção dinâmica de modelos de IA**, **segmentação de células individuais** e **classificação individual com overlays visuais**.

---

## 🏗️ 1. Arquitetura do Sistema (Descentralizada)

Para otimizar os recursos computacionais, a aplicação adota um modelo **Cliente-Servidor**:
* **Lado do Utilizador (Cliente GUI):** Corre na máquina local do clínico/investigador (via Gradio/Streamlit), servindo apenas para interceção visual, upload de imagens, seleção de modelos e visualização de resultados e *overlays*.
* **Lado do Servidor (Backend / Cloud):** Um servidor remoto ou GPU dedicada (ex: Colab, AWS, servidor institucional) que aloja e executa os modelos pesados de segmentação e inferência multimodal (BiomedCLIP, DINO, etc.). A comunicação é feita via API (ex: FastAPI).

---

## 📋 Requisitos de Hardware e Software

### 1. Hardware
* **Servidor (Backend):** Placa NVIDIA com suporte a CUDA (mín. 12GB–16GB VRAM para correr em simultâneo o modelo de segmentação e o BiomedCLIP).
* **Cliente (Frontend):** Computador standard ou portátil com 8 GB de RAM e browser moderno.

### 2. Software e Dependências
* **Backend:** Python 3.9+, PyTorch, Open_CLIP, FastAPI, OpenCV / Segment Anything (SAM) ou YOLO para segmentação celular.
* **Frontend:** Gradio / Streamlit, Requests (para comunicação com a API).

---

## 🎛️ Funcionalidades Avançadas

### 1. Gestão e Seleção de Modelos
O utilizador pode visualizar dinamicamente os modelos disponíveis no servidor através da interface gráfica e alternar entre eles antes de iniciar a análise:
* `BiomedCLIP-PubMedBERT` (Ideal para zero-shot clínico geral)
* `DINO-DeiT-III` (Focado em representações visuais/citologia fina)
* `ST-GAE Hybrid` (Para redes de objetos e dinâmica espacial)

### 2. Segmentação de Células Individuais e Classificação
Em vez de analisar apenas a imagem global:
1. **Segmentação:** Um modelo auxiliar (ex: YOLO / SAM / Thresholding avançado) deteta e isola cada célula individual presente no esfregaço citológico.
2. **Classificação Individual:** Cada recorte celular é submetido individualmente ao modelo selecionado para prever o seu estágio específico (**NA, Ta, T1, T2, T3/T4**).
3. **Overlay Visual:** A interface desenha caixas delimitadoras (*bounding boxes*) ou máscaras coloridas sobre cada célula na imagem original, indicando a classe prevista e a respetiva percentagem de confiança.

---

## 💻 Exemplo de Implementação Base (Estrutura Cliente-Servidor com Gradio)

```python
import torch
import open_clip
from PIL import Image, ImageDraw, ImageFont
import matplotlib.pyplot as plt
import gradio as gr
import numpy as np

# Configuração do dispositivo no Servidor
device = "cuda" if torch.cuda.is_available() else "cpu"

# Dicionário de Modelos Disponíveis no Servidor
MODELOS_DISPONIVEIS = {
    "BiomedCLIP (Padrão Clínico)": "hf-hub:microsoft/BiomedCLIP-PubMedBERT_256-vit_base_patch16_224",
    # Outros modelos podem ser adicionados aqui dinamicamente
}

# Classes de diagnóstico
classes = [
    "Saudável / Sem Anomalia (NA)",
    "Estágio Tumoral Baixo (Ta)",
    "Estágio Tumoral T1",
    "Estágio Tumoral T2",
    "Estágio Tumoral Avançado (T3/T4)"
]

def carregar_modelo_selecionado(nome_modelo):
    path_hf = MODELOS_DISPONIVEIS.get(nome_modelo, list(MODELOS_DISPONIVEIS.values())[0])
    model, _, preprocess = open_clip.create_model_and_transforms(path_hf, device=device)
    tokenizer = open_clip.get_tokenizer(path_hf)
    model.eval()
    return model, preprocess, tokenizer

def pipeline_analise_celular(imagem, modelo_escolhido):
    if imagem is None:
        return None, "Nenhuma imagem fornecida."

    if isinstance(imagem, np.ndarray):
        image = Image.fromarray(imagem).convert("RGB")
    else:
        image = imagem.convert("RGB")

    # 1. Carregar modelo selecionado pelo utilizador
    model, preprocess, tokenizer = carregar_modelo_selecionado(modelo_escolhido)

    # 2. Simulação de segmentação celular (em produção, aplicaria-se YOLO/SAM para extração de bounding boxes)
    # Para efeitos deste protótipo, analisamos a imagem global e simulamos overlays por célula detetada
    image_tensor = preprocess(image).unsqueeze(0).to(device)
    text_tokens = tokenizer(classes).to(device)

    with torch.no_grad():
        image_features = model.encode_image(image_tensor)
        text_features = model.encode_text(text_tokens)
        image_features /= image_features.norm(dim=-1, keepdim=True)
        text_features /= text_features.norm(dim=-1, keepdim=True)
        text_probs = (100.0 * image_features @ text_features.T).softmax(dim=-1).cpu().numpy()[0]

    # 3. Gerar Overlay Visual na Imagem (Exemplo de marcação de células detetadas)
    draw_img = image.copy()
    draw = ImageDraw.Draw(draw_img)
    
    # Exemplo simulado de coordenadas de células detetadas pelo modelo de segmentação
    # [ymin, xmin, ymax, xmax]
    largura, altura = image.size
    celulas_simuladas = [
        {"box": [int(altura*0.2), int(largura*0.2), int(altura*0.5), int(largura*0.5)], "classe": classes[np.argmax(text_probs)]}
    ]

    for cel in celulas_simuladas:
        box = cel["box"]
        draw.rectangle(box, outline="red", width=4)
        draw.text((box[0], box[1] - 15), cel["classe"], fill="red")

    # Gráfico de probabilidades globais
    fig, ax = plt.subplots(figsize=(6, 4))
    pares = sorted(zip(classes, text_probs), key=lambda x: x[1])
    sorted_classes, sorted_probs = zip(*pares)
    y_pos = range(len(classes))
    ax.barh(y_pos, [p * 100 for p in sorted_probs], color="#2980b9")
    ax.set_yticks(y_pos)
    ax.set_yticklabels(sorted_classes, fontsize=9)
    ax.set_xlabel("Probabilidade (%)", fontsize=10, fontweight="bold")
    ax.set_title(f"Inferência via: {modelo_escolhido}", fontsize=11, fontweight="bold")
    ax.set_xlim(0, 100)
    plt.tight_layout()

    return draw_img, fig

# Interface Gradio Descentralizada
with gr.Blocks(theme=gr.themes.Default()) as demo:
    gr.Markdown("# 🩺 Sistema CELLo — Diagnóstico Celular Descentralizado")
    gr.Markdown("Selecione o modelo de IA no servidor, carregue a imagem citológica e obtenha a segmentação de células com overlays de diagnóstico.")
    
    with gr.Row():
        dropdown_modelos = gr.Dropdown(
            choices=list(MODELOS_DISPONIVEIS.keys()), 
            value=list(MODELOS_DISPONIVEIS.keys())[0], 
            label="Selecione o Modelo de IA no Servidor"
        )
    
    with gr.Row():
        img_input = gr.Image(type="pil", label="Imagem Citológica (Cliente)")
        img_output = gr.Image(type="pil", label="Células Segmentadas com Overlay")
    
    btn_executar = gr.Button("Executar Análise Celular", variant="primary")
    plot_output = gr.Plot(label="Distribuição de Probabilidade Global")

    btn_executar.click(
        fn=pipeline_analise_celular,
        inputs=[img_input, dropdown_modelos],
        outputs=[img_output, plot_output]
    )

if __name__ == "__main__":
    demo.launch(share=True)
```