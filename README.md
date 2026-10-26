# NexoraAI — Production-Grade Full-Stack AI E-Commerce Platform

[![Frontend Deploy](https://img.shields.io/badge/Vercel-Deployed-success?style=flat&logo=vercel)](https://day23-jaya-sujitha.vercel.app/)
[![Backend API](https://img.shields.io/badge/Render-Online-blue?style=flat&logo=render)](https://nexora-backend-b0fh.onrender.com/health)
[![API Docs](https://img.shields.io/badge/FastAPI-Swagger_Docs-green?style=flat&logo=fastapi)](https://nexora-backend-b0fh.onrender.com/docs)
[![Unit Tests](https://img.shields.io/badge/Vitest-49%2F49%20Passed-brightgreen?style=flat&logo=vitest)](https://github.com/sujitha611/NexoraAI-Ecommerce)
[![Docker Ready](https://img.shields.io/badge/Docker_Compose-6_Services-2496ED?style=flat&logo=docker)](https://github.com/sujitha611/NexoraAI-Ecommerce)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?style=flat&logo=typescript)](https://www.typescriptlang.org/)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=flat&logo=python)](https://python.org)

**NexoraAI** is an enterprise-grade, full-stack E-Commerce ecosystem engineering high throughput, sub-millisecond caching, background worker queues, full-duplex WebSockets, and generative AI shopping assistants with zero-cost fallback hierarchies.

---

## 🚀 Live Production Deployments

| Component | Provider | Live URL | Description |
| :--- | :--- | :--- | :--- |
| **Storefront Web App** | **Vercel** | [day23-jaya-sujitha.vercel.app](https://day23-jaya-sujitha.vercel.app/) | Responsive SPA built with React 19, TypeScript, Tailwind CSS, Zustand, and TanStack Query |
| **Backend REST & WS API** | **Render** | [nexora-backend-b0fh.onrender.com](https://nexora-backend-b0fh.onrender.com/) | Production FastAPI gateway with PostgreSQL, Redis fallback, and WebSocket endpoints |
| **Interactive API Docs** | **Swagger UI** | [nexora-backend-b0fh.onrender.com/docs](https://nexora-backend-b0fh.onrender.com/docs) | OpenAPI 3.0 interactive specification & sandbox testing |
| **Health Telemetry** | **Render API** | [nexora-backend-b0fh.onrender.com/health](https://nexora-backend-b0fh.onrender.com/health) | Real-time database, Redis cache, and security middleware health status |

---

## 🏛️ System Architecture

```mermaid
flowchart TD
    subgraph Client["Frontend Client (React 19 + TypeScript + Vite)"]
        UI["Tailwind UI & Zustand Store"]
        SSE["SSE AI Streaming Client"]
        WSClient["WebSocket Client (Chat & Orders)"]
    end

    subgraph Gateway["API Gateway & Web Server (FastAPI + Nginx)"]
        Nginx["Nginx Reverse Proxy (:3000 / :80)"]
        FastAPI["FastAPI Application (:8000)"]
        SlowAPI["SlowAPI Rate Limiter"]
        OWASP["OWASP Headers & GZip Middleware"]
    end

    subgraph DataCache["Data & State Tier"]
        PG[("PostgreSQL 16: day10_db\n(Full-Text & pg_trgm GIN Indexes)")]
        Redis[("Redis 7 (AOF)\nCart (24h TTL) + AI Cache + Broker")]
        Chroma[("ChromaDB Vector Store\nSemantic Catalog Embeddings")]
    end

    subgraph Workers["Asynchronous Task Tier (Celery)"]
        Worker["Celery Worker (Concurrency: 2)"]
        Beat["Celery Beat Periodic Scheduler"]
    end

    subgraph AIEngine["AI Shopping Engine"]
        Chaining["LangChain Prompt Chaining"]
        Guardrails["Moderation & Anti-Hallucination"]
        Providers["Multi-Provider Hierarchy\n(Groq / Gemini / Mistral / Free Local)"]
    end

    UI -->|HTTP / REST| Nginx
    WSClient -->|WebSocket /ws/| Nginx
    SSE -->|SSE /ai/stream| Nginx
    Nginx --> FastAPI

    FastAPI --> SlowAPI
    FastAPI --> OWASP
    FastAPI --> PG
    FastAPI --> Redis
    FastAPI --> Chroma
    FastAPI --> AIEngine

    FastAPI -->|Enqueue Task| Redis
    Redis --> Worker
    Beat -->|Scheduled Cron| Redis
    Worker --> PG
```

---

## 🌟 Core Features & Technical Highlights

### 1. Generative AI Shopping Engine
- **Server-Sent Events (SSE) Streaming**: Real-time token streaming (`/ai/stream`) for conversational product recommendations.
- **Semantic Product Search**: ChromaDB vector store matching user intents beyond exact keywords with category and price metadata filtering.
- **Deterministic Response Caching**: Redis cache-aside layer hashing normalized queries to eliminate redundant LLM API calls and keep costs at $0.
- **Prompt Injection & Abuse Guardrails**: Pre-generation content moderation screening out SQLi, XSS, and jailbreak prompts.
- **Multi-Provider Fallback Hierarchy**: Resilient fallback pipeline traversing Groq (Llama 3.3 70B), Gemini 2.5 Flash, Mistral, and local rule-based responses.

### 2. High-Performance E-Commerce Engine
- **Atomic Checkout & Inventory Locks**: PostgreSQL database transactions with row-level locking to prevent race conditions and overselling.
- **High-Speed Shopping Cart**: Redis ephemeral cart storage with 24-hour TTL, saving database write amplification.
- **SQLAlchemy Optimization**: Query optimization eliminating \(N+1\) performance bottlenecks using `selectinload` and `joinedload`.
- **Advanced Catalog Search**: PostgreSQL Full-Text Search (`tsvector` + GIN index) and trigram fuzzy matching (`pg_trgm`) handling user typos.

### 3. Asynchronous Celery Processing
- **Decoupled Task Execution**: Celery workers powered by Redis as broker and results backend.
- **Automated PDF Invoices**: Asynchronous generation of branded PDF purchase receipts without blocking the checkout HTTP thread.
- **Bulk CSV Product Ingestion**: Batch processing of thousands of catalog entries with validation reporting and failed image recovery.
- **Task Lifecycle Telemetry**: Frontend polling mechanism tracking background tasks from `PENDING` $\to$ `STARTED` $\to$ `COMPLETED` / `FAILED`.

### 4. Real-Time WebSockets
- **Full-Duplex Customer Support**: Multi-room real-time chat with support agents (`/ws/chat/{room_id}`).
- **Live Order Status Broadcasting**: Real-time order progress updates (`PENDING` $\to$ `PROCESSING` $\to$ `SHIPPED` $\to$ `DELIVERED`) dispatched via WebSocket events.

### 5. Enterprise Security & Hardening
- **OWASP API Top 10 Protections**: `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, and `Strict-Transport-Security`.
- **Rate Limiting**: Tiered endpoint throttling via SlowAPI (`3/min` auth, `180/min` catalog, `60/min` AI).
- **GZip Response Compression**: Automated compression on HTTP payloads $\ge 500$ bytes reducing egress bandwidth by up to 70%.
- **Role-Based Access Control**: JWT Bearer token authentication safeguarding customer profiles and administrator control dashboards.

---

## 📁 Repository Structure

```text
NexoraAI-Ecommerce/
├── backend/
│   ├── app/
│   │   ├── ai/            # ChromaDB vector store, LangChain prompts, AI moderation
│   │   ├── models/        # SQLAlchemy ORM models (User, Product, Order, OrderItem)
│   │   ├── routers/       # FastAPI route handlers (auth, products, orders, cart, ai, ws)
│   │   ├── schemas/       # Pydantic data validation and serialization contracts
│   │   ├── services/      # Business logic, caching strategies, and PDF generator
│   │   ├── tasks/         # Celery background jobs (bulk import, email notifications)
│   │   └── utils/         # JWT security utilities, password hashing, and logging
│   ├── tests/             # Backend Pytest test suite
│   ├── Dockerfile         # Multi-stage minimal Python 3.12-slim production container
│   ├── requirements.txt   # Backend production dependencies
│   └── README.md          # Backend API architecture guide
├── frontend/
│   ├── src/
│   │   ├── components/    # Reusable UI controls, drawers, modals, and chat widgets
│   │   ├── context/       # Auth, Cart, Theme, and Realtime WebSocket contexts
│   │   ├── pages/         # Storefront, Admin, AI Assistant, and Order History pages
│   │   ├── services/      # Axios API clients, MSW mock handlers, and SSE stream hooks
│   │   └── test/          # Vitest suite covering 7 test specifications (49 tests)
│   ├── Dockerfile         # Multi-stage Node 20 builder + Nginx Alpine runner
│   ├── nginx.conf         # Production Nginx reverse proxy configuration
│   ├── package.json       # Frontend scripts and dependencies
│   └── vite.config.ts     # Vite bundler, chunking strategies, and test configuration
├── docker-compose.yml     # Orchestration of 6 microservices
├── DOCKER_ARCHITECTURE.md # Deep-dive into networking, persistent volumes, and secrets
├── vercel.json            # Vercel SPA routing and cache headers
└── render.yaml            # Render Blueprint deployment definition
```

---

## 🛠️ Local Development & Quick Start

### Option A: Docker Compose (Recommended)

Run the full stack with all 6 microservices (Postgres, Redis, Backend, Celery Worker, Celery Beat, Frontend) with a single command:

```bash
# Clone the repository
git clone https://github.com/sujitha611/NexoraAI-Ecommerce.git
cd NexoraAI-Ecommerce

# Launch all microservices in detached mode
docker compose up --build -d

# Check running status of services
docker compose ps
```

- **Frontend App**: `http://localhost:3000`
- **Backend API & Swagger**: `http://localhost:8000/docs`
- **PostgreSQL Database**: `localhost:5432` (`postgres:postgres`)
- **Redis Cache & Broker**: `localhost:6379`

### Option B: Manual Setup

#### 1. Backend Setup
```bash
cd backend
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

pip install -r requirements.txt
cp .env.example .env

# Run development server
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

#### 2. Frontend Setup
```bash
cd frontend
npm install
npm run dev
```

---

## 🧪 Automated Testing & Verification

The frontend test suite is powered by **Vitest**, **React Testing Library**, and **Mock Service Worker (MSW)**.

```bash
cd frontend
npm test -- --run
```

### Verified Test Results (All 7 Suites Passing):
```text
 ✓ src/test/day19.test.tsx (3 tests)
     ✓ renders priority LCP product card and lazy loads below-fold images
     ✓ loads live Security, OWASP Headers, and GZip telemetry
     ✓ triggers SlowAPI Rate Limiter probe (5 allowed, 6th HTTP 429)
 ✓ src/test/day21.test.tsx (4 tests)
     ✓ renders Redis Cache, Rate Limit, Description Generator, and Tool Agent
     ✓ generates AI product description and displays Redis Cache HIT
     ✓ executes Tool-Calling AI Agent with validated schemas
     ✓ screens prompt injection via Content Moderation Guardrail
 ✓ src/test/day18.test.tsx (5 tests)
     ✓ polls Celery PDF invoice task lifecycle and verifies download
     ✓ handles Celery task FAILED state gracefully
     ✓ executes Bulk CSV Product Import with batch telemetry
     ✓ executes PostgreSQL Fuzzy (pg_trgm) search for typo query
 ✓ src/test/day20.test.tsx (6 tests)
     ✓ renders AiChatFloatingButton and AiChatDrawer
     ✓ dispatches SSE streaming chat via useAiChatStream
     ✓ queries ChromaDB vector catalog playground
 ✓ src/test/day17.test.tsx (7 tests)
     ✓ updates live order status via WebSocket broadcasts
     ✓ manages realtime notification panel dismissals
     ✓ establishes WebSocket chat connection and broadcasts messages
 ✓ src/test/components.test.tsx (9 tests)
     ✓ renders ProductCard formatted prices and stock states
     ✓ manages cart mutations via MSW mock handlers
     ✓ validates login credentials with error boundary alerts
 ✓ src/test/day16.test.tsx (15 tests)
     ✓ enforces Zod schema validation on shipping and phone formats
     ✓ completes atomic checkout and verifies order confirmation
     ✓ validates Admin CRUD operations (POST, PUT, DELETE /products)

 Test Files  7 passed (7)
      Tests  49 passed (49)
   Duration  18.48s
```

---

## 🔐 Environment Variables Reference

A `.env.docker.example` file is included in the root directory. Copy it before running the project:

```bash
# Core Application Settings
ENVIRONMENT=production
SECRET_KEY=your-random-64-character-jwt-secret-key
DATABASE_URL=postgresql+psycopg2://postgres:postgres@postgres:5432/day10_db
REDIS_URL=redis://redis:6379/0

# Optional Generative AI Keys (Zero-cost local fallbacks operate when omitted)
GEMINI_API_KEY=your-gemini-api-key
GROQ_API_KEY=your-groq-api-key
MISTRAL_API_KEY=your-mistral-api-key
```

---

## 👤 Author

**Battu Jaya Sujitha**
- GitHub: [@sujitha611](https://github.com/sujitha611)
- Portfolio Project: [NexoraAI-Ecommerce](https://github.com/sujitha611/NexoraAI-Ecommerce)
- Live Storefront: [day23-jaya-sujitha.vercel.app](https://day23-jaya-sujitha.vercel.app/)
