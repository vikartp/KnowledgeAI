# KnowledgeAI Backend

FastAPI backend for the KnowledgeAI RAG application.

## Features

- **PDF ingestion** — Upload PDFs, chunk text with `RecursiveCharacterTextSplitter`, and embed with OpenAI
- **Vector storage** — Per-user ChromaDB collections for data isolation
- **Two-stage retrieval** — Embedding similarity search (top-10) → FlashRank cross-encoder reranking (top-3)
- **RAG chain** — LangChain retrieval chain with a stuff documents prompt
- **JWT auth** — Validates NextAuth.js tokens for secure API access

## Quick Start

```bash
# Create env and install dependencies
cp .env.example .env
uv venv
uv sync

# Run the server
uv run python -m uvicorn main:app --host 0.0.0.0 --port 8080 --reload
```

## Docker

```bash
docker build -t knowledgeai-backend .
docker run -p 8080:8080 --env-file .env knowledgeai-backend
```

Or use `docker compose up` from the project root — see the root [README.md](../README.md) for details.

## Environment Variables

| Variable | Description | Default |
|---|---|---|
| `OPENAI_API_KEY` | OpenAI API key | *(required)* |
| `OPENAI_API_BASE` | OpenAI-compatible base URL | `https://api.openai.com/v1` |
| `NEXTAUTH_SECRET` | JWT secret (must match frontend) | *(required)* |
| `EMBEDDING_MODEL` | Embedding model name | `text-embedding-3-large` |
| `LLM_MODEL` | Chat model name | `gpt-4o` |

## Reranking

The `/api/ask` endpoint uses a two-stage retrieval pipeline:

1. **Stage 1 — Vector retrieval:** ChromaDB returns top-10 chunks by embedding cosine similarity
2. **Stage 2 — Cross-encoder reranking:** FlashRank (`ms-marco-MiniLM-L-12-v2`) scores each chunk against the query and keeps the top 3

This runs entirely locally — no additional API calls or keys required.

See the root [README.md](../README.md) for full documentation.
