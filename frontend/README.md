# KnowledgeAI Frontend

Next.js 16 frontend for the KnowledgeAI RAG application.

## Features

- **Google OAuth** — Secure authentication via NextAuth.js
- **PDF upload** — Drag-and-drop PDF upload interface
- **Chat UI** — Real-time conversational Q&A with typing indicators
- **Responsive design** — Tailwind CSS 4 with modern glassmorphism aesthetics

## Quick Start

```bash
# Create environment file
cp .env.example .env.local
# Edit .env.local with your Google OAuth credentials

# Install dependencies
npm install

# Start the development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser.

## Docker

```bash
docker build -t knowledgeai-frontend \
  --build-arg NEXT_PUBLIC_API_URL=http://localhost:8080 .
docker run -p 3000:3000 --env-file .env.local knowledgeai-frontend
```

Or use `docker compose up` from the project root — see the root [README.md](../README.md) for details.

## Environment Variables

| Variable | Description | Example |
|---|---|---|
| `GOOGLE_CLIENT_ID` | Google OAuth client ID | `xxxx.apps.googleusercontent.com` |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret | `GOCSPX-...` |
| `NEXTAUTH_URL` | Canonical app URL | `http://localhost:3000` |
| `NEXTAUTH_SECRET` | JWT signing secret (must match backend) | `openssl rand -base64 32` |
| `NEXT_PUBLIC_API_URL` | Backend API base URL | `http://localhost:8080` |

## Tech Stack

- [Next.js 16](https://nextjs.org/) with App Router
- [React 19](https://react.dev/)
- [Tailwind CSS 4](https://tailwindcss.com/)
- [NextAuth.js](https://next-auth.js.org/) (Google OAuth)
- [Framer Motion](https://www.framer.com/motion/) for animations
- [Lucide React](https://lucide.dev/) for icons

See the root [README.md](../README.md) for full documentation.
