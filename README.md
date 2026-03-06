# RAG API · FastAPI × ChromaDB × Ollama

> *Ask questions. Get answers grounded in real data — for anyone.*

A production-ready **Retrieval-Augmented Generation** API that transforms raw text profiles into a queryable personal intelligence layer. Submit documents via API, query by user, and get grounded answers — no cloud, no data leaving your machine.

---

## Why This Exists

Most AI chatbots hallucinate. They confidently answer questions about *you* with information they've made up.

This project solves that by grounding every response in **your actual data** — chunked, embedded, and retrieved at query time before the LLM ever generates a word. And now with multi-user support, one API can hold and query knowledge for many people at once.

---

## How It Works

```
POST /documents  →  Chunk by paragraph  →  Embed  →  ChromaDB (tagged by user)
                                                              ↓
GET /ask?question=...&user=...  →  Retrieve  →  Augment Prompt  →  Ollama LLM  →  Answer
```

1. **Submit** — `POST /documents` accepts a user name and profile text, chunks it by paragraph, and stores each chunk in ChromaDB with user metadata attached
2. **Retrieve** — At query time, the API fetches the most semantically relevant chunks — optionally filtered to a specific user
3. **Generate** — Those chunks are injected into the prompt context before the LLM ever generates a word

No guessing. No hallucination. Every answer traces back to submitted source data.

---

## Stack

| Layer | Technology |
|---|---|
| API Framework | [FastAPI](https://fastapi.tiangolo.com/) |
| Data Validation | [Pydantic](https://docs.pydantic.dev/) |
| Vector Store | [ChromaDB](https://www.trychroma.com/) |
| LLM Runtime | [Ollama](https://ollama.com/) (fully local) |
| Embedding Model | `nomic-embed-text` |
| Language Model | `qwen2.5:0.5b` |

---

## Features

- **Multi-user support** — submit profiles for multiple people; query all at once or filter by user
- **API-driven ingestion** — no scripts to run; add documents dynamically via `POST /documents`
- **Scoped retrieval** — the `/ask` endpoint accepts an optional `?user=` param to search only that person's data
- **Fully local** — no API keys, no cloud services, no data exposure
- **Interactive docs** — Swagger UI included at `/docs` out of the box
- **Swappable models** — drop in any Ollama-compatible model with one config change

---

## API Reference

### `POST /documents`

Submit a profile to the knowledge base. Content is automatically chunked by paragraph and stored with user metadata for scoped retrieval.

**Request body:**
```json
{
  "user_name": "alice",
  "content": "Alice is a senior backend engineer with 8 years of experience...\n\nShe specialises in distributed systems and has led teams of up to 12 engineers..."
}
```

**Response:**
```json
{
  "message": "Added 2 chunks for user 'alice'.",
  "user_name": "alice",
  "chunks_added": 2
}
```

---

### `GET /ask`

Query the knowledge base. Optionally scope the search to a single user.

**Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `question` | string | yes | The question to answer |
| `user` | string | no | Filter retrieval to a specific user's data |

**Example — scoped query:**
```
GET /ask?question=What are Alice's strongest skills?&user=alice
```

**Example — search all profiles:**
```
GET /ask?question=Who has experience with distributed systems?
```

**Response:**
```json
{
  "question": "What are Alice's strongest skills?",
  "answer": "Based on the provided context, Alice's strongest skills are...",
  "context_used": ["Alice is a senior backend engineer...", "She specialises in distributed systems..."],
  "filtered_by_user": "alice"
}
```

---

## Project Structure

```
rag-api/
├── main.py                   # FastAPI app — POST /documents + GET /ask
├── build_knowledge_base.py   # One-time script to seed ChromaDB from profile.txt
├── profile.txt               # Your personal profile document
├── .gitignore
├── README.md
├── venv/                     # Virtual environment (git-ignored)
└── chroma_db/                # Vector store data (git-ignored)
```

> `build_knowledge_base.py` is a legacy ingestion script for seeding from a local file. The API-driven `POST /documents` endpoint is now the preferred way to add profiles.

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
pip install fastapi uvicorn chromadb ollama pydantic
```

### 4 — Pull Models

```bash
ollama pull nomic-embed-text
ollama pull qwen2.5:0.5b
```

---

## Usage

### Step 1 — Start the API

```bash
uvicorn main:app --reload
```

API live at: `http://127.0.0.1:8000`

### Step 2 — Submit a Profile

```bash
curl -X POST http://127.0.0.1:8000/documents \
  -H "Content-Type: application/json" \
  -d '{
    "user_name": "alice",
    "content": "Alice is a backend engineer with expertise in Python and distributed systems.\n\nShe has led multiple cloud migration projects and mentors junior developers."
  }'
```

### Step 3 — Query It

```bash
# Scoped to one user
curl "http://127.0.0.1:8000/ask?question=What+is+Alice+good+at&user=alice"

# Across all profiles
curl "http://127.0.0.1:8000/ask?question=Who+has+cloud+experience"
```

Or navigate to `http://127.0.0.1:8000/docs` for the interactive Swagger UI.

---

## Contributing

Fork it, improve it, open a PR. Issues and feature requests welcome.

---

## License

MIT — see `LICENSE` for details.
