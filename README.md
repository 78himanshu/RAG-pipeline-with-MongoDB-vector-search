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
- `main.py` : optional/placeholder entry
- `pyproject.toml`, `uv.lock` : `uv` project configuration
- `requirements.txt` : optional reference list
- `.env.example` : environment variable template (do not commit `.env`)

## Setup

### 1) Create and activate environment (uv)

```bash
uv venv
source .venv/bin/activate
uv sync
```

### 2) Configure environment variables

Create a `.env` file in the project root (this file is gitignored):

```bash
MONGODB_URI="mongodb+srv://<username>:<password>@<cluster>.mongodb.net/?retryWrites=true&w=majority"
```

Tip: Copy `.env.example` and fill in your values.

### 3) Atlas prerequisites

In MongoDB Atlas:
1. Create a new Project + free M0 cluster
2. Create a DB user with readWrite access
3. Add your IP to Network Access (IP Access List)
4. Ensure Vector Search is available for your cluster
5. Copy the connection string from **Connect -> Drivers** and paste it into `.env`

### 4) Run the notebook

Open `rag.ipynb` and run cells top-to-bottom.

The notebook will:
- download the PDF
- chunk + embed
- insert into MongoDB
- create the Atlas Vector Search index
- run retrieval queries

## Notes / Common Issues

- Vector index dimensions must match your embedding model dimension.
  - `BAAI/bge-small-en-v1.5` -> 384 dims
- `numCandidates` in `$vectorSearch` is NOT the embedding dimension.
  - Typical values: 50–200 (start with 100)
- If MongoDB connection fails:
  - check `.env` is loaded
  - confirm IP allowlist and DB user/password in Atlas
  - verify your `MONGODB_URI` starts with `mongodb+srv://`

## Next Step: add Generation 

This repo currently builds retrieval context. To complete full RAG, add a local LLM (free) such as Ollama and pass the retrieved `context_string` into the model prompt.

## License

MIT (or replace with your preferred license)


