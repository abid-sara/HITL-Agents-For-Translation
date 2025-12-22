# Human-in-the-Loop Translation System

## Overview

This repository implements a **human-in-the-loop translation pipeline** that combines multiple LLMs, human corrections, and automated agents to continuously improve translation quality. The system ensures high-quality translations by leveraging user edits, deduplication, few-shot prompting, and periodic fine-tuning.

---

## Pipeline

1. **User Upload**: The user uploads a PDF or DOCX file. Text is extracted and split into sentences.
2. **LLM Translation**: Multiple LLMs translate each sentence in parallel and send outputs to the UI.
3. **Human Correction**: The user edits translations and submits corrections.
4. **Agent Processing**:

   * **Preprocessor Agent**: Normalizes text and creates a JSON object of the source, LLM outputs, and human correction.
   * **Acceptance Agent**: Validates corrections using metrics or a mini-LLM judge. Alerts the user if the edit is worse.
   * **Deduplicator Agent**: Checks uniqueness with FAISS embeddings; rejects duplicates.
   * **Executor Agent**: Saves accepted corrections to `master.jsonl` and adds embeddings to FAISS.
   * **Monitor Agent**: Watches JSONL file size, quality, or time; triggers fine-tuning when thresholds are reached.
5. **Few-Shot Prompting**: While waiting for fine-tuning, selected high-quality past corrections are used as few-shot examples to guide the LLM.
6. **Fine-Tuning**: Triggered automatically when enough corrections are collected. The model is updated at endpoints, and JSONL and FAISS are cleared for the next cycle.

---

## Storage

* **JSONL (`master.jsonl`)**: Stores full triplets (source, LLM outputs, human correction, scores, timestamps) for fine-tuning.
* **FAISS**: Stores embeddings of human-corrected sentences for deduplication and few-shot retrieval.

---

## Installation

```bash
git clone <repo-url>
cd human-in-the-loop-translation
pip install -r requirements.txt
```

**Dependencies:** `transformers`, `torch`, `sentence-transformers` (or LaBSE), `faiss-cpu`/`faiss-gpu`, `fastapi` or `flask`, `redis` (optional).

---

If you want, I can **also add a simple ASCII flowchart** at the top showing UI → Agents → FAISS/JSONL → Few-Shot → Fine-Tuning → LLM Endpoints to make it more visual. Do you want me to do that?
