# 🧠 KnowledgeAI

> A full-stack **Retrieval-Augmented Generation** application that lets users upload PDF documents and ask natural language questions about their content — powered by LangChain, ChromaDB, OpenAI, and **FlashRank reranking**.

![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat-square&logo=next.js)
![FastAPI](https://img.shields.io/badge/FastAPI-0.135-009688?style=flat-square&logo=fastapi)
![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python)
![ChromaDB](https://img.shields.io/badge/ChromaDB-1.5-FF6B6B?style=flat-square)
![LangChain](https://img.shields.io/badge/LangChain-1.2-1C3C3C?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker)

---

## ✨ Features

- 🔐 **Google OAuth Authentication** — Secure login via NextAuth.js with Google provider
- 📄 **PDF Upload & Processing** — Upload PDFs that get chunked and embedded into a vector store
- 💬 **Conversational Q&A** — Ask questions about uploaded documents and get AI-generated answers
- 🧩 **Per-User Data Isolation** — Each user's documents are stored in separate vector collections
- 🔀 **Cross-Encoder Reranking** — Retrieved chunks are reranked using FlashRank for higher relevance
- ⚡ **Real-time Chat UI** — Beautiful, responsive chat interface with typing indicators
- 🐳 **Docker Compose** — One-command setup for the entire stack

---

## 🏗️ Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────┐
│    Frontend      │────▶│    Backend        │────▶│  ChromaDB   │
│  (Next.js 16)    │     │   (FastAPI)       │     │ (Vector DB) │
│   Port: 3000     │     │   Port: 8080      │     │             │
└─────────────────┘     └────────┬─────────┘     └─────────────┘
                                 │
                                 ▼
                        ┌──────────────────┐
                        │   OpenAI API      │
                        │  (Embeddings +    │
                        │   Chat Completion)│
                        └──────────────────┘
```

### How It Works

1. **User logs in** via Google OAuth (NextAuth.js)
2. **User uploads a PDF** → Backend extracts text using PyPDF, splits it into chunks
3. **Chunks are embedded** using OpenAI's `text-embedding-3-large` model and stored in ChromaDB
4. **User asks a question** → 10 candidate chunks are retrieved from ChromaDB via similarity search
5. **Chunks are reranked** using a cross-encoder model (FlashRank `ms-marco-MiniLM-L-12-v2`) to keep only the top 3 most relevant
6. **LLM generates an answer** using the reranked context via a RAG chain (LangChain)

---

## 📁 Project Structure

```
KnowledgeAI/
├── docker-compose.yml           # One-command full-stack deployment
├── frontend/                    # Next.js 16 application
│   ├── Dockerfile               # Multi-stage Docker build
│   ├── src/
│   │   ├── app/
│   │   │   ├── page.tsx         # Landing page with Google sign-in
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx     # Main chat & PDF upload interface
│   │   │   ├── api/auth/
│   │   │   │   └── [...nextauth]/
│   │   │   │       └── route.ts # NextAuth API route
│   │   │   ├── layout.tsx       # Root layout with session provider
│   │   │   └── globals.css      # Global styles (Tailwind)
│   │   ├── components/
│   │   │   └── SessionWrapper.tsx
│   │   └── lib/
│   │       └── config.ts        # Centralized API URL config
│   ├── .env.example             # Environment variables template
│   ├── package.json
│   └── next.config.ts
│
├── backend/                     # FastAPI application
│   ├── Dockerfile               # Multi-stage Docker build (uv)
│   ├── main.py                  # API endpoints (upload, ask, health)
│   ├── pyproject.toml           # Python dependencies (uv)
│   ├── .env.example             # Environment variables template
│   ├── .python-version          # Python version pinning
│   └── uv.lock                  # Lock file for reproducible installs
│
└── README.md                    # You are here
```

---

## 🚀 Getting Started

### Option A: Docker Compose (Recommended)

The fastest way to run the full stack:

```bash
# 1. Clone the repository
git clone https://github.com/your-username/KnowledgeAI.git
cd KnowledgeAI

# 2. Create environment files
cp backend/.env.example backend/.env
# Edit backend/.env with your OpenAI API key and NEXTAUTH_SECRET

# Create frontend environment file
cp frontend/.env.example frontend/.env.local
# Edit frontend/.env.local with your Google OAuth credentials and NEXTAUTH_SECRET

# 3. Start everything
docker compose up --build
```

The app will be available at:
- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:8080
- **API Docs:** http://localhost:8080/docs

### Option B: Manual Setup

#### Prerequisites

- [Node.js](https://nodejs.org/) v18+
- [Python](https://www.python.org/) 3.12+
- [uv](https://docs.astral.sh/uv/) (Python package manager)
- [Google Cloud Console](https://console.cloud.google.com/) project with OAuth credentials

#### 1. Clone the Repository

```bash
git clone https://github.com/your-username/KnowledgeAI.git
cd KnowledgeAI
```

#### 2. Setup Backend

```bash
cd backend

# Create environment file
cp .env.example .env
# Edit .env with your actual API keys

# Create virtual environment and install dependencies
uv venv
uv sync

# Start the backend server
uv run python -m uvicorn main:app --host 0.0.0.0 --port 8080 --reload
```

The API will be available at `http://localhost:8080`. Visit `http://localhost:8080/docs` for interactive API documentation.

#### 3. Setup Frontend

```bash
cd frontend

# Create environment file
cp .env.example .env.local
# Edit .env.local with your Google OAuth credentials

# Install dependencies
npm install

# Start the development server
npm run dev
```

The app will be available at `http://localhost:3000`.

#### 4. Setup Google OAuth

1. Go to [Google Cloud Console → Credentials](https://console.cloud.google.com/apis/credentials)
2. Create a new **OAuth 2.0 Client ID** (Web application)
3. Add `http://localhost:3000/api/auth/callback/google` as an **Authorized redirect URI**
4. Copy the **Client ID** and **Client Secret** into `frontend/.env.local`

---

## ⚙️ Environment Variables

### Backend (`backend/.env`)

| Variable | Description | Example |
|---|---|---|
| `OPENAI_API_KEY` | OpenAI API key (or compatible provider) | `sk-...` |
| `OPENAI_API_BASE` | Base URL for the OpenAI-compatible API | `https://api.openai.com/v1` |
| `NEXTAUTH_SECRET` | Secret for JWT validation (must match frontend) | `openssl rand -base64 32` |
| `EMBEDDING_MODEL` | Embedding model name | `text-embedding-3-large` |
| `LLM_MODEL` | Chat completion model name | `gpt-4o` |

### Frontend (`frontend/.env.local`)

| Variable | Description | Example |
|---|---|---|
| `GOOGLE_CLIENT_ID` | Google OAuth client ID | `xxxx.apps.googleusercontent.com` |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret | `GOCSPX-...` |
| `NEXTAUTH_URL` | Canonical URL of the app | `http://localhost:3000` |
| `NEXTAUTH_SECRET` | Secret for JWT signing | `openssl rand -base64 32` |
| `NEXT_PUBLIC_API_URL` | Backend API base URL | `http://localhost:8080` |

---

## 🔌 API Endpoints

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `GET` | `/` | ❌ | Health check |
| `POST` | `/api/upload` | ✅ JWT | Upload and process a PDF (multipart form) |
| `POST` | `/api/ask` | ✅ JWT | Ask a question about uploaded documents |

### Upload PDF
```bash
curl -X POST http://localhost:8080/api/upload \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -F "file=@document.pdf"
```

### Ask a Question
```bash
curl -X POST http://localhost:8080/api/ask \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"question": "What is this document about?"}'
```

---

## 🔀 Reranking Pipeline

The RAG pipeline uses a **two-stage retrieval** strategy for better answer quality:

```
User Query
    │
    ▼
┌──────────────────────────────┐
│  Stage 1: Vector Retrieval   │  ChromaDB returns top-10 chunks
│  (Embedding similarity)      │  by cosine similarity
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│  Stage 2: Cross-Encoder      │  FlashRank (ms-marco-MiniLM-L-12-v2)
│  Reranking                   │  scores each chunk against the query
│  → Keep top-3 most relevant  │  and returns the 3 best matches
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│  LLM Generation (GPT-4o)     │  Generates a concise answer
│  with reranked context       │  from the most relevant chunks
└──────────────────────────────┘
```

**Why reranking?** Embedding similarity (Stage 1) is fast but approximate. Cross-encoders (Stage 2) jointly encode the query and each document for more accurate relevance scoring, filtering out false positives before the LLM sees the context.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 16, React 19, Tailwind CSS 4, NextAuth.js |
| **Backend** | FastAPI, Uvicorn, Python 3.12 |
| **AI/ML** | LangChain, OpenAI (GPT-4o + text-embedding-3-large) |
| **Reranking** | FlashRank (ms-marco-MiniLM-L-12-v2 cross-encoder) |
| **Vector DB** | ChromaDB |
| **PDF Parsing** | PyPDF |
| **Package Mgmt** | npm (frontend), uv (backend) |
| **Deployment** | Docker, Docker Compose |

---

## 📝 License

This project is for educational and personal use.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
