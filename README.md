# Build a RAG API with FastAPIThis project demonstrates how to build a Retrieval-Augmented Generation (RAG) API using FastAPI, ChromaDB, and Ollama. The API allows users to ask questions, and it provides grounded answers by retrieving relevant context from a personal knowledge base.## Features-   **FastAPI Backend**: A robust and modern Python web framework for building APIs.
-   **ChromaDB**: A vector database for storing and retrieving document embeddings.
-   **Ollama**: Used for running local Large Language Models (LLMs) and embedding models (nomic-embed-text, qwen2.5:0.5b).
-   **RAG Pipeline**: Implements the full RAG workflow: Retrieve, Augment, Generate.
-   **Interactive Documentation**: Built-in Swagger UI for API testing and exploration.## Project Structure```
rag-api/
├── main.py             # FastAPI application with the RAG endpoint
├── build_knowledge_base.py # Script to create and populate the ChromaDB knowledge base
├── profile.txt         # Your personal profile document
├── .gitignore          # Specifies intentionally untracked files to ignore
├── README.md           # This README file
└── venv/               # Python virtual environment (ignored by Git)
└── chroma_db/          # ChromaDB data directory (ignored by Git)
```## Getting Started### PrerequisitesBefore you begin, ensure you have the following installed:-   [Python 3.9+](https://www.python.org/downloads/)
-   [Ollama](https://ollama.com/download) (ensure it's running)### Installation1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    cd rag-api
    ```2.  **Create and activate a virtual environment:**
    ```bash
    python -m venv venv
    # On macOS/Linux
    source venv/bin/activate
    # On Windows
    .\venv\Scripts\activate
    ```3.  **Install dependencies:**
    ```bash
    pip install fastapi uvicorn chromadb ollama
    ```4.  **Pull Ollama models:**
    Ensure you have the necessary models downloaded via Ollama:
    ```bash
    ollama pull nomic-embed-text
    ollama pull qwen2.5:0.5b
    ```### Usage1.  **Prepare your personal profile:**
    Edit `profile.txt` with information about yourself (e.g., name, career goals, interests, skills, hobbies).2.  **Build your knowledge base:**
    Run the script to process your `profile.txt` and store its embeddings in ChromaDB:
    ```bash
    python build_knowledge_base.py
    ```3.  **Start the FastAPI application:**
    ```bash
    uvicorn main:app --reload
    ```
    The API will be running at `http://127.0.0.1:8000`.4.  **Test the API:**
    Open your browser and navigate to `http://127.0.0.1:8000/docs` to access the interactive Swagger UI. You can use the `/ask` endpoint to query your personal AI.## ContributingFeel free to fork this repository, make improvements, and submit pull requests.## LicenseThis project is licensed under the MIT License - see the LICENSE file for details.
