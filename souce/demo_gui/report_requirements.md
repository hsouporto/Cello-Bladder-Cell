# Especificação Funcional e Requisitos: Sistema Descentralizado de Diagnóstico Citológico (CELLo)

Este documento foca-se exclusivamente na **arquitetura conceptual, nos requisitos funcionais/não funcionais e nas especificidades técnicas** a desenvolver para o sistema de diagnóstico de citologia assistido por IA.

---

## 1. Visão Geral e Arquitetura Descentralizada (Cliente-Servidor)

O sistema adota uma arquitetura desacoplada para separar a computação intensiva de IA da interface de interceção do clínico/investigador:

*   **Camada Cliente (Frontend / User Interface):**
    *   **Papel:** Corre localmente no dispositivo do utilizador (via aplicação web leve).
    *   **Responsabilidades:** Gestão do upload de imagens citológicas em alta resolução, seleção interativa de modelos de IA, envio de pedidos via API e renderização visual dos resultados (gráficos, relatórios e *overlays*).
*   **Camada Servidor (Backend / Computational Node):**
    *   **Papel:** Aloja a infraestrutura de GPU (ex: nuvem ou servidor institucional).
    *   **Responsabilidades:** Gestão do ciclo de vida dos modelos de Deep Learning, execução do motor de segmentação celular, cálculo dos embeddings multimodais (BiomedCLIP) e processamento de inferência em paralelo das classes e probabilidades.

---

## 2. Requisitos Funcionais

### RF01: Seleção Dinâmica de Modelos de IA
*   O sistema deve disponibilizar um catálogo dinâmico de modelos pré-treinados alojados no servidor (ex: *BiomedCLIP-PubMedBERT*, *DINO-DeiT-III*, *ClipBase* , etc).
*   O utilizador deve poder alternar entre diferentes *backbones* diretamente na interface antes de submeter a imagem para análise, adaptando o motor de inferência ao objetivo clínico específico.

### RF02: Segmentação Celular Automatizada
*   O subsistema de segmentação deve processar a imagem do esfregaço citológico para isolar entidades celulares individuais (evitando a análise puramente global da lâmina).
*   Deve suportar tanto a segmentação automática de todas as células detetadas como a seleção interativa por parte do utilizador (se aplicável).

### RF03: Classificação Individual por Célula (Zero-Shot & Supervised)
*   Cada recorte celular segmentado deve ser submetido a uma pipeline de classificação individual.
*   O sistema deve mapear a representação visual da célula face ao espaço textual clínico, avaliando a probabilidade de pertença às seguintes classes de referência:
    *   **NA:** Saudável / Sem Anomalia
    *   **Ta:** Estágio Tumoral Baixo
    *   **T1:** Estágio Tumoral T1
    *   **T2:** Estágio Tumoral T2
    *   **T3 / T4:** Estágio Tumoral Avançado

### RF04: Geração de Overlays Visuais e Relatórios
*   A interface deve projetar sobre a imagem original um sistema de *overlays* (caixas delimitadoras ou máscaras coloridas) associados a cada célula segmentada.
*   Cada overlay deve explicitar visualmente a classe prevista e o grau de confiança associado.
*   Deve ser gerado em simultâneo um painel estatístico global com a distribuição de probabilidades de toda a lâmina.

---

## 3. Requisitos Não Funcionais e Especificidades Técnicas

### RNF01: Desempenho e Escalabilidade (Servidor)
*   O servidor deve garantir suporte a aceleração por hardware (GPUs NVIDIA com arquitetura CUDA) para assegurar que a segmentação e a inferência multimodal ocorram em tempo útil (alvo inferior a segundos por lâmina).
*   O sistema deve implementar caching de modelos na memória VRAM para evitar latências excessivas na troca dinâmica de arquiteturas.

### RNF02: Desacoplamento e Segurança de Comunicação
*   A comunicação entre o Cliente e o Servidor deve ser gerida através de uma API RESTful robusta (ex: FastAPI), garantindo a validação de formatos de imagem (PNG, JPEG, TIFF de alta resolução) e a proteção de dados sensíveis de anatomopatologia.

### RNF03: Interpretabilidade e Explicabilidade (XAI)
*   Além das previsões de classes, o pipeline deve prever a integração futura de mapas de atenção (*attention maps*) e métricas de interpretabilidade espacial, permitindo ao especialista auditar o motivo pelo qual o modelo atribuiu determinado estágio (Ta–T4) a uma célula específica.