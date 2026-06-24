# InterviewX: RAG-Powered System Design Interview Learning Platform

> Learn GenAI concepts hands-on by building a retrieval-augmented generation system over Alex Xu's "System Design Interview - Volume 1"

## 🎯 Project Overview

**InterviewX** is an AI-powered Q&A platform that lets you ask questions about system design concepts from Alex Xu's book using Claude as the LLM backbone. Unlike simple keyword search, it uses **semantic search with embeddings** to find relevant content and **Claude's reasoning** to synthesize intelligent answers.

### Why This Project?
- **Learn GenAI**: Build a real-world RAG system, understand embeddings, vector databases, prompt engineering, agents
- **Master System Design**: Deep dive into Alex Xu's content through interactive Q&A
- **Production-Grade**: Not a toy—learn patterns used in real GenAI products

### Key Features
- 📖 PDF ingestion & semantic chunking
- 🔍 Vector similarity search with Claude embeddings
- 🤖 Multi-turn conversations with context awareness
- 🧠 Agentic retrieval for complex multi-step questions
- ⚡ Real-time streaming responses
- 🎓 Learn GenAI concepts hands-on at every layer

---

## 🏗️ Architecture & Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    PHASE 1: INGESTION                        │
├─────────────────────────────────────────────────────────────┤
│  PDF → Extract Text → Chunk (500 tokens) → Add Metadata     │
│  Output: List of chunks with chapter/page/section info      │
└─────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────┐
│                   PHASE 2: EMBED & INDEX                     │
├─────────────────────────────────────────────────────────────┤
│  For each chunk: Generate embedding via Claude API          │
│  Store in Vector DB (ChromaDB) with metadata                │
│  Build similarity index for fast retrieval                  │
└─────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────┐
│                  PHASE 3: RETRIEVAL & RANKING                │
├─────────────────────────────────────────────────────────────┤
│  User Query → Embed query → Semantic search top-k chunks    │
│  Optional: Rerank results for relevance                     │
│  Build context window: system prompt + retrieved chunks     │
└─────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────┐
│                  PHASE 4: GENERATION & UI                    │
├─────────────────────────────────────────────────────────────┤
│  Claude API (with tool_use for agentic retrieval)           │
│  Stream response tokens in real-time                        │
│  Store conversation history for multi-turn Q&A             │
│  Return citations: chunks used + page references           │
└─────────────────────────────────────────────────────────────┘
```

### System Design Principles
- **Modular**: Pluggable components (embeddings, vector DB, LLM)
- **Observable**: Log queries, retrievals, generations for learning
- **Cost-aware**: Track API costs per query
- **Streaming-first**: Real-time token output for UX
- **Agentic**: Claude decides when to retrieve multiple times

---

## 🛠️ Tech Stack

### Backend
| Component | Technology | Why |
|-----------|-----------|-----|
| **Language** | Python 3.10+ | Rich GenAI libraries, fast iteration |
| **Framework** | FastAPI | Modern async, built-in OpenAPI, streaming support |
| **Orchestration** | LangChain | Unify embeddings, retrieval, agents |
| **LLM** | Claude (Anthropic SDK) | State-of-the-art reasoning, tool use, streaming |
| **Embeddings** | Claude embeddings API | Production-quality, learn at scale |
| **Vector DB** | ChromaDB (Phase 1), PostgreSQL + pgvector (Phase 2) | Simple → production |
| **PDF Processing** | PyPDF2 + pdfplumber | Robust text extraction |
| **Async** | asyncio, aiohttp | Concurrent embeddings/API calls |

### Frontend
| Component | Technology | Why |
|-----------|-----------|-----|
| **Framework** | React 18 | Modern, component reuse, hooks |
| **Styling** | TailwindCSS | Rapid UI development |
| **State** | TanStack Query + Zustand | Server cache + local state |
| **Streaming** | Server-Sent Events (SSE) | Real-time token output |
| **Code Quality** | TypeScript, ESLint, Prettier | Type safety, consistency |

### Infrastructure
| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Package Manager** | Poetry | Reproducible dependencies |
| **Testing** | pytest, pytest-asyncio | Backend testing |
| **Code Quality** | black, flake8, mypy | Linting & type checking |
| **Monitoring** | Custom logging + LangSmith (optional) | Debug & cost tracking |
| **Deployment** | Docker + GitHub Actions | CI/CD ready |

---

## 📚 GenAI Concepts You'll Learn (Hands-on)

### Core Concepts

| Concept | Implementation | Tools & Frameworks | Learning Depth |
|---------|-----------------|-------------------|-----------------|
| **Embeddings** | Upload PDF → chunks → Claude embeddings API | Claude embeddings, `anthropic` SDK, LangChain | Understand dimensionality, similarity metrics, cost |
| **Vector Similarity** | Implement cosine/L2 distance, understand ranking | NumPy, scikit-learn, ChromaDB | How vector search works under the hood |
| **Semantic Search** | Query → embed → similarity search | ChromaDB query interface, LangChain `Retriever` | Why semantic > keyword search |
| **Chunking Strategy** | Experiment chunk size, overlap, boundaries | LangChain `RecursiveCharacterTextSplitter`, custom splitters | Impact on retrieval quality |
| **Prompt Engineering** | System prompts, context templates, few-shot | Claude API, LangChain `PromptTemplate` | Prompt design patterns for RAG |
| **Token Counting** | Track context usage, manage window limits | `anthropic.tokenize()`, Anthropic SDK | Context window management |
| **Streaming** | Real-time token output in UI | Anthropic SDK `stream=True`, SSE, WebSockets | Building responsive AI UIs |
| **Conversation Memory** | Store history, handle context overflow | LangChain `ConversationBufferMemory`, PostgreSQL | Stateful AI interactions |

### Advanced Concepts

| Concept | Implementation | Tools & Frameworks | Learning Depth |
|---------|-----------------|-------------------|-----------------|
| **Tool Use / Function Calling** | Claude calls retrieval functions autonomously | Anthropic SDK `tools` parameter, LangChain `AgentExecutor` | How agents make decisions |
| **Multi-step Reasoning** | Complex queries split into retrieval + synthesis | Claude API + custom orchestration | Planning & decomposition |
| **Retrieval Ranking** | Rerank results by relevance, diversity | Cohere reranker API, LangChain `ContextualCompressionRetriever` | Improving retrieval quality |
| **Hallucination Mitigation** | RAG grounds answers in document chunks | Citation system, constraint prompts | Why RAG reduces confabulation |
| **Cost Optimization** | Track embeddings, generations, API calls | Token counting, logging middleware, billing APIs | GenAI economics |
| **Evaluation & Metrics** | Measure retrieval quality, generation coherence | `ragas` library, custom evaluation functions | How to evaluate RAG systems |

### Production Patterns

| Pattern | When to Use | Tools |
|---------|------------|-------|
| **Query Rewriting** | User query doesn't match document terms | Claude API with few-shot |
| **Query Expansion** | Single query might be ambiguous | ChromaDB multi-query retrieval |
| **Re-ranking** | Retrieve-then-rank improves relevance | Cohere API |
| **Summary Indexing** | Long documents, extractive summaries | Claude API |
| **Hypothetical Document Embedding (HyDE)** | Improve retrieval by asking Claude what answer looks like | Claude API |

---

## 🚀 Implementation Phases

### Phase 1: Foundation (Week 1)
**Goal**: Working RAG system, understand embeddings & retrieval

#### Backend
- [ ] Project scaffold with Poetry
- [ ] PDF upload & text extraction (PyPDF2)
- [ ] Chunk strategy (configurable: recursive, markdown-aware)
- [ ] Generate embeddings via Claude API
- [ ] Store in ChromaDB with metadata
- [ ] Basic retrieval: query → embed → similarity search
- [ ] Prompt template for Claude generation
- [ ] FastAPI endpoints: `/upload`, `/query`

#### Frontend
- [ ] React app scaffold with Vite
- [ ] PDF upload component
- [ ] Q&A chat interface
- [ ] SSE streaming for real-time responses
- [ ] Metadata display (chunk source, page ref)

#### Learning Outcomes
- How embeddings capture semantic meaning
- Vector similarity & distance metrics
- Chunking impact on retrieval quality
- Basic prompt engineering for RAG

---

### Phase 2: Conversation & Agents (Week 2)
**Goal**: Multi-turn Q&A, agentic retrieval

#### Backend
- [ ] Conversation history storage (in-memory → PostgreSQL)
- [ ] Claude tool_use for multi-step retrieval
- [ ] Token counting & context window management
- [ ] Query rewriting when no good matches found
- [ ] Re-ranking with alternative metrics
- [ ] Follow-up question handling

#### Frontend
- [ ] Chat history persistence
- [ ] Message threading
- [ ] Show reasoning steps (what Claude looked up)
- [ ] Follow-up suggestions

#### Learning Outcomes
- How agents make decisions via tool_use
- Multi-turn conversation state management
- Token budgeting in context window
- Agentic vs. simple RAG tradeoffs

---

### Phase 3: Production Polish (Week 3+)
**Goal**: Scalable, observable, cost-efficient

#### Backend
- [ ] Migrate to PostgreSQL + pgvector
- [ ] Implement caching (query → answer)
- [ ] Rate limiting & auth
- [ ] LangSmith integration for observability
- [ ] Cost tracking & analytics
- [ ] Evaluation framework for retrieval quality
- [ ] A/B testing framework for prompt variants

#### Frontend
- [ ] Analytics dashboard (queries, latency, cost)
- [ ] Admin console for chunk management
- [ ] Feedback collection (thumbs up/down)
- [ ] Dark mode, accessibility

#### Deployment
- [ ] Docker containerization
- [ ] GitHub Actions CI/CD
- [ ] Deploy to cloud (GCP / AWS / Fly.io)

#### Learning Outcomes
- Production GenAI patterns
- Observability & cost management
- Evaluation of RAG quality
- Scaling from prototype → product

---

## 💾 Database Schema

### Phase 1: ChromaDB (In-memory)
```
collections:
  - name: "system_design_book"
    metadata:
      - chapter: str
      - page: int
      - section: str
    documents: [text chunks]
    embeddings: [vector]
    ids: [chunk_id]
```

### Phase 2: PostgreSQL
```sql
-- Chunks table
CREATE TABLE chunks (
  id UUID PRIMARY KEY,
  content TEXT NOT NULL,
  chapter VARCHAR,
  page INT,
  section VARCHAR,
  embedding vector(1536),  -- Claude embedding dimension
  created_at TIMESTAMP,
  CONSTRAINT fk_chapter FOREIGN KEY (chapter) REFERENCES chapters(name)
);

-- Conversations table
CREATE TABLE conversations (
  id UUID PRIMARY KEY,
  user_id VARCHAR,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

-- Messages table
CREATE TABLE messages (
  id UUID PRIMARY KEY,
  conversation_id UUID REFERENCES conversations(id),
  role VARCHAR(10),  -- 'user' or 'assistant'
  content TEXT,
  retrieved_chunks JSONB,  -- chunks used for this message
  tokens_used INT,
  cost DECIMAL,
  created_at TIMESTAMP
);

-- Metadata for analysis
CREATE TABLE query_log (
  id UUID PRIMARY KEY,
  query TEXT,
  embedding_model VARCHAR,
  top_k_retrieved INT,
  generation_time_ms INT,
  tokens_in INT,
  tokens_out INT,
  cost DECIMAL,
  user_feedback VARCHAR,  -- thumbs up/down
  created_at TIMESTAMP
);
```

---

## 🔌 API Endpoints

### Backend (FastAPI)

#### Document Management
```
POST /api/documents/upload
  - Accept: PDF file
  - Response: {document_id, chunks_count, embedding_status}

GET /api/documents
  - List all uploaded documents

DELETE /api/documents/{doc_id}
  - Remove document + embeddings
```

#### Query & Chat
```
POST /api/query
  - Body: {query: str, conversation_id?: str}
  - Response: Server-Sent Event stream of tokens
  - Footer: {citations: [{chunk_id, chapter, page}], tokens_used, cost}

GET /api/conversations/{conv_id}
  - Retrieve conversation history

POST /api/conversations/{conv_id}/feedback
  - Body: {message_id, rating: 'good'|'bad', comment?}
```

#### Admin / Analytics
```
GET /api/analytics/queries
  - Query volume, latency distribution, cost

GET /api/analytics/retrieval
  - Chunk popularity, retrieval quality metrics

POST /api/admin/reindex
  - Rebuild all embeddings (e.g., after switching models)
```

---

## 🧪 Testing Strategy

### Unit Tests
- Chunking logic (edge cases: long sentences, tables)
- Prompt template rendering
- Token counting accuracy

### Integration Tests
- End-to-end: PDF upload → query → Claude response
- Embedding generation & similarity search
- Conversation history persistence

### Evaluation Tests
- Retrieval quality: Does top-k include relevant chunks?
- Generation quality: Does Claude cite sources accurately?
- Hallucination check: Does answer stay grounded in chunks?

### Tools
```bash
pytest tests/ -v
pytest --cov=src/ --cov-report=html  # Coverage
```

---

## 📦 Getting Started

### Prerequisites
- Python 3.10+
- Node.js 18+
- Anthropic API key (get at https://console.anthropic.com)
- Postgres 14+ (Phase 2)

### Local Development Setup

#### Backend
```bash
cd backend
poetry install
poetry run python -m pip install --upgrade pip

# Create .env
cat > .env << EOF
ANTHROPIC_API_KEY=your-key-here
DATABASE_URL=postgresql://user:pass@localhost/interviewx
ENVIRONMENT=development
EOF

# Run server
poetry run uvicorn app.main:app --reload --port 8000
```

#### Frontend
```bash
cd frontend
npm install
npm run dev  # Dev server at localhost:5173
```

#### Upload Sample PDF
```bash
curl -X POST -F "file=@path/to/SystemDesignInterview.pdf" \
  http://localhost:8000/api/documents/upload
```

#### Test Query
```bash
curl -X POST http://localhost:8000/api/query \
  -H "Content-Type: application/json" \
  -d '{"query": "How do I design a cache?"}'
```

---

## 📊 Key Metrics & Monitoring

### Performance Metrics
- **Embedding latency**: Time to embed a query
- **Retrieval latency**: Time to find top-k chunks
- **Generation latency**: Time for Claude to respond
- **Total latency**: End-to-end response time

### Quality Metrics
- **Retrieval recall**: % of relevant chunks in top-k
- **Citation accuracy**: % of cited chunks actually used
- **Hallucination rate**: % of claims not grounded in chunks
- **User satisfaction**: Thumbs up/down ratio

### Cost Metrics
- **Cost per query**: Embeddings + generation
- **Cost per conversation**: Multi-turn total
- **Model cost breakdown**: Which phase costs most?

---

## 🎓 Learning Outcomes by Phase

### After Phase 1
- [ ] Understand embeddings: how they work, why they're better than keywords
- [ ] Build a retrieval system from scratch
- [ ] Prompt engineering for RAG (system prompts, context templates)
- [ ] Real-time streaming in web apps
- [ ] PDF processing pipelines

### After Phase 2
- [ ] Design agentic systems with Claude's tool_use
- [ ] Multi-turn conversation management
- [ ] Token budgeting & context window strategies
- [ ] Complex prompt patterns (reasoning, multi-step)
- [ ] Trade-offs: agentic vs. simple RAG

### After Phase 3
- [ ] Production patterns for GenAI systems
- [ ] Cost optimization & economics of LLMs
- [ ] Evaluation frameworks for RAG quality
- [ ] Scaling from prototype to product
- [ ] Observability & debugging GenAI apps

---

## 🔮 Future Enhancements

### Retrieval Improvements
- [ ] Hypothetical document embeddings (HyDE)
- [ ] Query expansion with multi-hop retrieval
- [ ] Recursive summarization for long chapters
- [ ] Semantic clustering of chunks

### Agentic Features
- [ ] Web search fallback for out-of-domain questions
- [ ] Cross-book retrieval (combine multiple books)
- [ ] Quiz generation mode (generate questions from content)
- [ ] Debate mode (show multiple perspectives)

### Multi-Modal
- [ ] Include diagrams/images from PDF
- [ ] Embed images alongside text chunks
- [ ] Return visual explanations with text

### Community
- [ ] Public question-answer library
- [ ] Shared conversation histories
- [ ] Leaderboard of popular questions
- [ ] Annotation tool for improving chunks

---

## 📖 Resources & References

### GenAI & RAG
- [Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook) — Claude best practices
- [LangChain Docs](https://docs.langchain.com/) — RAG orchestration
- [Pinecone RAG Guide](https://www.pinecone.io/learn/retrieval-augmented-generation/) — Concepts
- [RAGAS](https://docs.ragas.io/) — RAG evaluation framework

### System Design
- [Alex Xu's System Design Interview - Volume 1](https://www.amazon.com/System-Design-Interview-Alex-Xu/dp/B08CMF2CQF) — Primary source
- [Web Architecture 101](https://engineering.hashicorp.com/posts/web-architecture-101) — Complementary

### Tools & Libraries
- [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python)
- [LangChain](https://github.com/langchain-ai/langchain)
- [ChromaDB](https://docs.trychroma.com/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [pgvector](https://github.com/pgvector/pgvector)

---

## 📝 Project Structure

```
InterviewX/
├── backend/
│   ├── app/
│   │   ├── main.py              # FastAPI app
│   │   ├── config.py            # Settings
│   │   ├── database/            # DB models & migrations
│   │   ├── api/
│   │   │   ├── documents.py     # Upload, list, delete
│   │   │   ├── query.py         # Q&A endpoints
│   │   │   └── admin.py         # Admin endpoints
│   │   ├── services/
│   │   │   ├── embedding.py     # Claude embeddings
│   │   │   ├── retrieval.py     # Vector search
│   │   │   ├── generation.py    # Claude generation
│   │   │   └── chunking.py      # Text processing
│   │   └── utils/
│   │       ├── logging.py       # Logging config
│   │       └── metrics.py       # Cost tracking
│   ├── tests/
│   ├── pyproject.toml
│   └── .env.example
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── ChatInterface.tsx
│   │   │   ├── DocumentUpload.tsx
│   │   │   └── SourceCitation.tsx
│   │   ├── hooks/
│   │   │   ├── useQuery.ts
│   │   │   └── useConversation.ts
│   │   ├── pages/
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── package.json
│   └── vite.config.ts
│
├── docs/
│   ├── architecture.md
│   ├── api.md
│   └── deployment.md
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── docker-compose.yml
├── Dockerfile
└── README.md
```

---

## 🤝 Contributing & Feedback

This is a learning project. Share what you discover:
- [ ] What chunking strategy works best?
- [ ] How many retrievals before hallucinations appear?
- [ ] Cost optimizations you find
- [ ] Prompt engineering tricks

---

## 📄 License

MIT (feel free to fork, modify, extend for learning)

---

## 🚀 Ready to Build?

Start with **Phase 1** to get a working RAG system in 1 week.  
Then move to **Phase 2** for agentic intelligence.  
Graduate to **Phase 3** for production patterns.

Each phase teaches real GenAI concepts you'll see in production systems.

**Happy learning! 🎓**
