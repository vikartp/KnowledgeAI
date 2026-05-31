# ---------- Build stage ----------
FROM python:3.12-slim AS builder

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /app

# Install dependencies first (cached layer)
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev --no-install-project

# Copy application code
COPY . .
RUN uv sync --frozen --no-dev

# Pre-download the FlashRank reranker model so it's baked into the image
RUN uv run python -c "from flashrank import Ranker; Ranker(model_name='ms-marco-MiniLM-L-12-v2')"

# ---------- Runtime stage ----------
FROM python:3.12-slim

# curl is needed for the docker-compose healthcheck
RUN apt-get update && apt-get install -y --no-install-recommends curl && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the virtual environment and app from builder
COPY --from=builder /app /app

ENV PATH="/app/.venv/bin:$PATH"

EXPOSE 8080

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
