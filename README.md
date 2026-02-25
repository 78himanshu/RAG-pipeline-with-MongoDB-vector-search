# RAG with MongoDB Atlas Vector Search (Local Embeddings)

This project is a simple end-to-end RAG (Retrieval-Augmented Generation) foundation using:
- MongoDB Atlas Vector Search for vector indexing + retrieval
- Local (free) embeddings using `sentence-transformers`
- A Jupyter notebook workflow for loading a PDF, chunking, embedding, and querying

Right now, this repo implements the Retrieval + Context part of RAG. The final Generation step (LLM answering) can be added later (for example with Ollama).

## What this does

1. Downloads a PDF
2. Splits it into small overlapping text chunks
3. Generates an embedding vector for each chunk (locally)
4. Stores `{ text, embedding, metadata }` in MongoDB
5. Creates an Atlas Vector Search index on the `embedding` field
6. Runs `$vectorSearch` to retrieve top-k relevant chunks for a query
7. Builds a `context_string` from the retrieved chunks (ready to be fed into an LLM)

## Tech Stack

- Python 3.13 (via `uv`)
- MongoDB Atlas + Vector Search
- `pymongo`
- `langchain-community`, `langchain-text-splitters`, `pypdf`
- `sentence-transformers` (default model: `BAAI/bge-small-en-v1.5`, 384 dims)

## Project Structure

- `rag.ipynb` : main notebook (ingestion + indexing + retrieval)
- `main.py` : placeholder entry (optional)
- `pyproject.toml`, `uv.lock` : `uv` project configuration
- `requirements.txt` : optional reference list
- `.env.example` : environment variable template (do not commit `.env`)

## Setup

### 1) Create and activate environment (uv)

```bash
uv venv
source .venv/bin/activate
uv sync
