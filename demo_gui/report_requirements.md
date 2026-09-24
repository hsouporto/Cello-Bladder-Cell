# Functional Specification and Requirements: Decentralized Cytological Diagnostics System (CELLo)

This document focuses exclusively on the **conceptual architecture, functional/non-functional requirements, and technical specificities** to be developed for the AI-assisted cytology diagnostic system.

---

## 1. Overview and Decentralized Architecture (Client-Server)

The system adopts a decoupled architecture to separate intensive AI computation from the clinician/researcher interface:

*   **Client Layer (Frontend / User Interface):**
    *   **Role:** Runs locally on the user's device (via a lightweight web application).
    *   **Responsibilities:** Management of high-resolution cytological image uploads, interactive AI model selection, API request submission, and visual rendering of results (charts, reports, and overlays).
*   **Server Layer (Backend / Computational Node):**
    *   **Role:** Hosts the GPU infrastructure (e.g., cloud or institutional server).
    *   **Responsibilities:** Management of the deep learning model lifecycle, execution of the cell segmentation engine, calculation of multimodal embeddings (BiomedCLIP), and parallel inference processing of classes and probabilities.

---

## 2. Functional Requirements

### RF01: Dynamic AI Model Selection
*   The system must provide a dynamic catalog of pre-trained models hosted on the server (e.g., *BiomedCLIP-PubMedBERT*, *DINO-DeiT-III*, *ClipBase*, etc.).
*   The user must be able to switch between different backbones directly in the interface before submitting the image for analysis, adapting the inference engine to the specific clinical objective.

### RF02: Automated Cellular Segmentation
*   The segmentation subsystem must process the cytological smear image to isolate individual cellular entities (avoiding a purely global analysis of the slide).
*   It must support both the automated segmentation of all detected cells and interactive selection by the user (if applicable).

### RF03: Individual Cell Classification (Zero-Shot & Supervised)
*   Each segmented cell crop must be submitted to an individual classification pipeline.
*   The system must map the cell's visual representation against the clinical text space, evaluating the probability of belonging to the following reference classes:
    *   **NA:** Healthy / No Anomaly
    *   **Ta:** Low Tumor Stage
    *   **T1:** Tumor Stage T1
    *   **T2:** Tumor Stage T2
    *   **T3 / T4:** Advanced Tumor Stage

### RF04: Generation of Visual Overlays and Reports
*   The interface must project a system of overlays (bounding boxes or colored masks) associated with each segmented cell onto the original image.
*   Each overlay must visually make explicit the predicted class and the associated confidence level.
*   A global statistical panel with the probability distribution of the entire slide must be generated simultaneously.

---

## 3. Non-Functional Requirements and Technical Specificities

### RNF01: Performance and Scalability (Server)
*   The server must ensure support for hardware acceleration (NVIDIA GPUs with CUDA architecture) to ensure that segmentation and multimodal inference occur in a timely manner (target under seconds per slide).
*   The system must implement model caching in VRAM to prevent excessive latency during the dynamic switching of architectures.

### RNF02: Decoupling and Communication Security
*   Communication between the Client and the Server must be managed through a robust RESTful API (e.g., FastAPI), ensuring the validation of image formats (PNG, JPEG, high-resolution TIFF) and the protection of sensitive anatomical pathology data.

### RNF03: Interpretability and Explainability (XAI)
*   In addition to class predictions, the pipeline must provide for the future integration of attention maps and spatial interpretability metrics, allowing the specialist to audit why the model assigned a given stage (Ta–T4) to a specific cell.