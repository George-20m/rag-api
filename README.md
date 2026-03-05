# RAG API · FastAPI × ChromaDB × Ollama

> *Ask questions. Get answers grounded in your own knowledge.*

A production-ready **Retrieval-Augmented Generation** API that transforms a plain text profile into a queryable personal intelligence layer — no cloud, no data leaving your machine.

---

## Why This Exists

Most AI chatbots hallucinate. They confidently answer questions about *you* with information they've made up.

This project solves that by grounding every response in **your actual data** — chunked, embedded, and retrieved at query time before the LLM ever generates a word.

---

## How It Works

```
Your Document  →  Embeddings  →  ChromaDB
                                    ↓
User Question  →  Retrieve Context  →  Augment Prompt  →  Ollama LLM  →  Answer
```

1. **Ingest** — `build_knowledge_base.py` chunks your `profile.txt` and stores vector embeddings in ChromaDB
2. **Retrieve** — At query time, the API fetches the most semantically relevant chunks
3. **Generate** — Those chunks are injected into the prompt context before the LLM responds

No guessing. No hallucination. Just answers that trace back to your source.

---

## Stack

| Layer | Technology |
|---|---|
| API Framework | [FastAPI](https://fastapi.tiangolo.com/) |
| Vector Store | [ChromaDB](https://www.trychroma.com/) |
| LLM Runtime | [Ollama](https://ollama.com/) (fully local) |
| Embedding Model | `nomic-embed-text` |
| Language Model | `qwen2.5:0.5b` |

---

## Features

- **Fully local** — no API keys, no cloud services, no data exposure
- **Interactive docs** — Swagger UI included at `/docs` out of the box
- **Swappable models** — drop in any Ollama-compatible model with one config change
- **Minimal footprint** — no Docker, no orchestration overhead; just Python

---

## Project Structure

```
rag-api/
├── main.py                   # FastAPI app + RAG endpoint
├── build_knowledge_base.py   # Ingestion pipeline → ChromaDB
├── profile.txt               # Your source document
├── .gitignore
├── README.md
├── venv/                     # Virtual environment (git-ignored)
└── chroma_db/                # Vector store data (git-ignored)
```

---

## Getting Started

### Prerequisites

- [Python 3.9+](https://www.python.org/downloads/)
- [Ollama](https://ollama.com/download) installed and running

### 1 — Clone

```bash
git clone https://github.com/your-username/your-repo-name.git
cd rag-api
```

### 2 — Environment

```bash
python -m venv venv

# macOS / Linux
source venv/bin/activate

# Windows
.\venv\Scripts\activate
```

### 3 — Install Dependencies

```bash
pip install fastapi uvicorn chromadb ollama
```

### 4 — Pull Models

```bash
ollama pull nomic-embed-text
ollama pull qwen2.5:0.5b
```

---

## Usage

### Step 1 — Add Your Data

Edit `profile.txt` with information about yourself: background, skills, career goals, interests, projects — anything you'd want an AI to know.

### Step 2 — Build the Knowledge Base

```bash
python build_knowledge_base.py
```

This processes your document and writes embeddings to ChromaDB. Run this whenever `profile.txt` changes.

### Step 3 — Start the API

```bash
uvicorn main:app --reload
```

API live at: `http://127.0.0.1:8000`

### Step 4 — Query It

Navigate to `http://127.0.0.1:8000/docs` and use the `/ask` endpoint.

```json
POST /ask
{
  "question": "What are this person's strongest technical skills?"
}
```

---

## Extending This

Some directions worth exploring:

- **Multiple documents** — ingest an entire folder of notes, CVs, or research papers
- **Reranking** — add a cross-encoder reranker between retrieval and generation for higher precision
- **Streaming** — swap the response model for SSE streaming with FastAPI's `StreamingResponse`
- **Persistent sessions** — add conversation memory to support multi-turn dialogue
- **Different models** — try `llama3`, `mistral`, or `phi3` in place of `qwen2.5:0.5b`

---

## Contributing

Fork it, improve it, open a PR. Issues and feature requests welcome.

---

## License

MIT — see `LICENSE` for details.