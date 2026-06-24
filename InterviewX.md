# InterviewX: RAG-Powered System Design Interview Learning Platform

> Learn GenAI concepts hands-on by building a retrieval-augmented generation system over Alex Xu's "System Design Interview - Volume 1"

---

## 🎯 Project Overview

**InterviewX** is an AI-powered learning platform where users ask questions about system design and get intelligent, cited answers powered by Claude and embeddings.

### Vision
- **Phase 1**: Basic Q&A (Week 1) — Simple, working, valuable
- **Phase 2**: Smart Learning (Week 2-3) — Enhanced with quizzes & analytics
- **Phase 3**: Complete Platform (Week 3+) — Mock interviews, peer learning, certification

Each phase delivers a **complete working product** users can start using immediately.

### Why Build This?
- Learn modern GenAI: RAG, embeddings, agentic workflows (LangGraph + MCP)
- Build something real users want
- Practice vertical slicing: shipping working features every 2-3 weeks
- Understand production AI systems

---

## 📱 Phase 1: InterviewX Basic — Q&A Platform

**Timeline**: 1 week  
**Goal**: Minimal viable product—answer questions about the book  

### 🎯 What Users Can Do

```
Student uploads: "System Design Interview.pdf"
Student asks: "How do I design a cache?"
System responds: "Based on Chapter 5 (pages 120-145)..."
Student asks: "What about cache invalidation?"
System responds with more context
Student gives feedback: 👍 (helpful) or 👎 (not helpful)
```

#### Available Features
- ✅ **PDF Upload**: Upload System Design Interview book
- ✅ **Ask Questions**: Unlimited Q&A on the content
- ✅ **Get Answers**: Real-time streaming responses
- ✅ **See Citations**: Know which chapter/page the answer came from
- ✅ **Feedback**: Like/dislike answers
- ✅ **History**: View past conversations
- ✅ **Accounts**: Save conversations with login

### 💻 Technical Implementation

#### Backend
```
Stack: FastAPI + Python + Anthropic SDK + ChromaDB

Modules:
├── PDF Processing
│   ├── Upload & text extraction (PyPDF2)
│   ├── Smart chunking (500 tokens, with overlap)
│   └── Add metadata (chapter, page, section)
│
├── Embeddings
│   ├── Generate embeddings via Claude API
│   ├── Store in ChromaDB (in-memory for Phase 1)
│   └── Build search index
│
├── Query & Retrieval
│   ├── Embed user query
│   ├── Similarity search (top-5 chunks)
│   └── Rank by relevance
│
└── Generation
    ├── Build prompt with chunks
    ├── Call Claude API with streaming
    └── Stream tokens to frontend
```

#### Frontend
```
Stack: React 18 + TypeScript + TailwindCSS

Components:
├── PDFUpload.tsx
│   ├── Drag-drop upload
│   └── Progress bar
│
├── ChatInterface.tsx
│   ├── Message history
│   ├── Input field
│   ├── Real-time streaming
│   └── Copy-to-clipboard
│
├── CitationDisplay.tsx
│   ├── Source references
│   ├── Link to chapter
│   └── Page numbers
│
└── Dashboard.tsx
    ├── Query history
    ├── Quick stats
    └── Feedback summary
```

#### Database (Phase 1)
```
ChromaDB (in-memory SQLite)
├── Collections: chunks
│   ├── content: string
│   ├── chapter: string
│   ├── page: int
│   ├── section: string
│   ├── embedding: vector
│   └── metadata: json
```

### 🎓 What You Learn (GenAI Concepts)

| Concept | How You Learn It |
|---------|-----------------|
| **Embeddings** | Generate embeddings for each chunk via Claude API |
| **Vector Similarity** | Implement cosine distance search in Python |
| **Chunking Strategy** | Experiment with chunk size, overlap impact on quality |
| **Prompt Engineering** | Build effective system prompts for Q&A |
| **Token Management** | Track token usage, understand costs |
| **Streaming** | Real-time token output to UI |
| **RAG (Retrieval-Augmented Generation)** | See how retrieval + generation works together |

### 🚀 Implementation Checklist

#### Week 1: Core (Day 1-3)
- [ ] FastAPI project scaffold with Poetry
- [ ] PDF upload endpoint
- [ ] Text extraction & chunking
- [ ] Claude embeddings API integration
- [ ] ChromaDB setup & storage
- [ ] Similarity search implementation
- [ ] Basic Q&A endpoint with streaming

#### Week 1: Polish (Day 4-5)
- [ ] React frontend scaffold
- [ ] Upload UI
- [ ] Chat interface with real-time streaming
- [ ] Citations display
- [ ] Error handling & edge cases
- [ ] Deploy to cloud (Vercel + Railway/Fly.io)

#### Week 1: Launch (Day 6-7)
- [ ] Test with real users
- [ ] Gather feedback
- [ ] Fix critical bugs
- [ ] Launch! 🚀

### 📊 Success Criteria
- ✅ Users can upload PDF and ask 10+ questions
- ✅ Responses are accurate and cited
- ✅ <2 second response time
- ✅ Deploy to production
- ✅ Get first 10 users

---

## 📊 Phase 2: InterviewX Pro — Smart Learning Assistant

**Timeline**: 2-3 weeks (build on Phase 1)  
**Goal**: Add learning features—quizzes, tracking, smart search  

### 🎯 What Users Can Do

#### New Capabilities (on top of Phase 1)
```
Student: "Quiz me on load balancing"
System: Generates 5 multiple-choice questions
Student answers → Gets feedback + explanations

Student: "Search for topics about caching"
System: Automatically searches 3+ chapter variants
Returns best results from all chapters

Dashboard shows:
- Topics studied: [Caching 80%, Load Balancing 60%]
- Weak areas: [Rate Limiting 30%]
- Suggestion: "Review Chapter 7"
```

#### Available Features
- ✅ All Phase 1 features, PLUS:
- ✅ **AI Quiz Generation**: Custom quizzes from any topic
- ✅ **Smart Search**: Multi-chapter auto-search with query rewriting
- ✅ **Study Dashboard**: Track mastery levels per topic
- ✅ **Multi-turn Context**: Conversations remember context
- ✅ **Performance Tracking**: Quiz scores over time
- ✅ **Suggestions**: "You should review..." recommendations
- ✅ **Study Goals**: Set targets and track streaks

### 💻 Technical Implementation

#### Backend
```
Stack: FastAPI + LangChain + LangGraph + Claude + PostgreSQL + ChromaDB

Key Changes:
├── LangGraph Implementation
│   ├── Nodes: plan, retrieve, evaluate, synthesize
│   ├── Conditional routing (retrieve more or answer?)
│   ├── State management (conversation context)
│   └── Multi-query search node
│
├── Quiz Generation
│   ├── Template-based quiz creation
│   ├── Claude generates varied questions
│   ├── Answer tracking & grading
│   └── Performance analytics
│
├── Database Upgrade
│   ├── PostgreSQL for persistence
│   ├── Users table
│   ├── Quizzes table
│   ├── Quiz attempts table
│   ├── Topic mastery tracking
│   └── Conversation history
│
└── Smart Search
    ├── Query rewriting (Claude suggests variants)
    ├── Multi-query parallel search
    ├── Result ranking & deduplication
    └── Confidence scoring
```

#### Frontend
```
New Components:
├── QuizInterface.tsx
│   ├── Question display
│   ├── Multiple choice options
│   ├── Answer submission
│   ├── Instant feedback
│   └── Score display
│
├── StudyDashboard.tsx
│   ├── Topics mastered (progress bars)
│   ├── Weak areas (highlighted)
│   ├── Quiz scores over time (chart)
│   ├── Suggestions
│   └── Study goals
│
├── PerformanceChart.tsx
│   ├── Line chart: progress over time
│   └── Heatmap: topics by difficulty
│
└── SearchResults.tsx
    ├── Multi-source results
    ├── Relevance scoring
    └── Format options (text, code, diagram)
```

#### Database (Phase 2)
```
PostgreSQL (Production-ready)

Tables:
├── users
│   ├── id, email, created_at
│   └── settings (preferences)
│
├── topics
│   ├── id, name, chapter
│   └── difficulty
│
├── user_topic_mastery
│   ├── user_id, topic_id
│   ├── mastery_level (0-100%)
│   └── last_reviewed
│
├── quizzes
│   ├── id, user_id, topic_id
│   ├── questions (JSON)
│   ├── user_answers (JSON)
│   ├── score, created_at
│   └── time_taken
│
└── conversations
    ├── id, user_id
    ├── messages (with context)
    └── created_at
```

### 🎓 What You Learn (GenAI Concepts)

| Concept | How You Learn It |
|---------|-----------------|
| **LangGraph** | Build multi-node graph for quiz generation & search |
| **State Management** | Persist conversation context across turns |
| **Conditional Routing** | Agent decides: "retrieve more or answer?" |
| **Query Rewriting** | Claude rewrites queries for better search |
| **Tool Use** | Claude calls quiz generation function autonomously |
| **Multi-step Reasoning** | Complex queries decomposed across nodes |
| **Token Budgeting** | Track cost per graph node |
| **Streaming Optimization** | Stream quiz questions as they're generated |

### 🚀 Implementation Checklist

#### Week 2: Core (Day 1-4)
- [ ] Migrate to PostgreSQL
- [ ] Build LangGraph for quiz generation
- [ ] Implement quiz creation endpoint
- [ ] Quiz storage & retrieval
- [ ] Quiz scoring logic
- [ ] Performance tracking database

#### Week 2: Smart Search (Day 5-6)
- [ ] Implement query rewriting (Claude)
- [ ] Multi-query parallel search
- [ ] Result ranking algorithm
- [ ] Test search quality

#### Week 2-3: Frontend (Day 7-9)
- [ ] Quiz UI components
- [ ] Study dashboard
- [ ] Performance charts
- [ ] Smart search UI
- [ ] Integrate with backend

#### Week 3: Launch (Day 10-11)
- [ ] Load testing
- [ ] Bug fixes
- [ ] Deploy to production
- [ ] Announce new features

### 📊 Success Criteria
- ✅ Users can take custom quizzes
- ✅ Dashboard shows progress accurately
- ✅ Smart search finds relevant content across chapters
- ✅ Quiz generation takes <3 seconds
- ✅ 10x more user engagement (session duration)
- ✅ Users report better learning outcomes

---

## 🎓 Phase 3: InterviewX Enterprise — Complete Learning Platform

**Timeline**: 3-4 weeks (build on Phase 1 & 2)  
**Goal**: Mock interviews, collaboration, certification, advanced analytics  

### 🎯 What Users Can Do

#### New Capabilities (on top of Phase 1 & 2)
```
User: "Generate a mock interview on system design"
System asks: "What's the scale?" → User answers
         "How would you design caching?" → User answers
         "What about load balancing?" → User answers
System evaluates and provides:
- Score: 72/100
- Feedback: "Good DB schema, missed load balancing"
- Suggestions: Review Chapter 8

User in bootcamp:
- Sees: "You're in top 15 of 50 students"
- Joins study group discussion
- Gets feedback from instructor
- Downloads certificate
- Shares on LinkedIn
```

#### Available Features
- ✅ All Phase 1 & 2 features, PLUS:
- ✅ **Mock Interviews**: AI-driven interview simulation
- ✅ **Peer Comparison**: See how you compare (in groups)
- ✅ **Study Groups**: Collaborate with classmates
- ✅ **Code & Diagrams**: Architecture diagrams embedded
- ✅ **Video Explanations**: AI-generated or human videos
- ✅ **Learning Paths**: AI-recommended curriculum
- ✅ **Instructor Tools**: For bootcamp/university use
- ✅ **Certification**: Downloadable certificates
- ✅ **Advanced Analytics**: Learning velocity, readiness prediction
- ✅ **Calendar & Reminders**: Schedule study sessions
- ✅ **Export**: Certificate, progress reports, badges

### 💻 Technical Implementation

#### Backend
```
Stack: FastAPI + LangGraph + MCP Servers + Claude + PostgreSQL

Key Components:
├── LangGraph Advanced
│   ├── Graph checkpointing (resumability)
│   ├── Parallel node execution
│   ├── Timeout & max-iteration limits
│   ├── Performance profiling per node
│   └── Graph versioning for A/B testing
│
├── MCP Servers (External Tools)
│   ├── filesystem_mcp: Read chapters, content management
│   ├── quiz_generator_mcp: Advanced quiz generation
│   ├── evaluation_mcp: Mock interview evaluation
│   └── video_generator_mcp: AI video synthesis
│
├── Mock Interview Engine
│   ├── Question bank (by difficulty)
│   ├── Progressive question selection
│   ├── Real-time evaluation
│   ├── Detailed feedback generation
│   └── Score calculation
│
├── Collaboration Features
│   ├── Study group management
│   ├── Group discussions (forum)
│   ├── Peer comparison analytics
│   └── Instructor dashboards
│
├── Advanced Analytics
│   ├── Learning velocity metrics
│   ├── Time-to-mastery tracking
│   ├── Readiness prediction
│   ├── Cohort benchmarking
│   └── Churn prediction
│
└── Certification System
    ├── Certificate generation
    ├── Verification system
    ├── LinkedIn badge integration
    └── Progress reports
```

#### Frontend
```
New Components:
├── MockInterview.tsx
│   ├── Progressive question UI
│   ├── Text/voice input
│   ├── Real-time evaluation feedback
│   ├── Score display
│   └── Detailed feedback report
│
├── PeerComparison.tsx
│   ├── Rank in cohort
│   ├── Topic mastery comparison
│   ├── Leaderboards
│   └── Performance insights
│
├── StudyGroup.tsx
│   ├── Group discussion forum
│   ├── Member list & progress
│   ├── Instructor posts
│   └── Resource sharing
│
├── AdvancedDashboard.tsx
│   ├── Learning velocity chart
│   ├── Time-to-mastery gauge
│   ├── Readiness prediction
│   ├── Cohort comparison
│   └── Recommendations
│
├── CertificateView.tsx
│   ├── Download certificate
│   ├── Share to LinkedIn
│   ├── Verification link
│   └── Progress summary
│
└── LearningPath.tsx
    ├── Recommended curriculum
    ├── Prerequisites display
    ├── Progress tracker
    └── Next steps
```

#### Database (Phase 3)
```
PostgreSQL (Enterprise-grade)

New Tables:
├── mock_interviews
│   ├── id, user_id
│   ├── questions (JSON)
│   ├── user_answers (JSON)
│   ├── evaluation (Claude feedback)
│   ├── score, time_taken
│   └── created_at
│
├── study_groups
│   ├── id, name, instructor_id
│   ├── members (array of user_ids)
│   ├── created_at
│   └── settings
│
├── group_discussions
│   ├── id, group_id
│   ├── topic, posts (JSON)
│   └── created_at
│
├── peer_comparison
│   ├── user_id, group_id
│   ├── rank, score_percentile
│   └── mastered_topics (array)
│
├── certificates
│   ├── id, user_id
│   ├── issued_date, verification_token
│   ├── score, topics_mastered
│   └── certificate_url
│
├── learning_paths
│   ├── user_id
│   ├── recommended_topics (array, ordered)
│   ├── current_topic
│   ├── completion_percentage
│   └── predicted_completion_date
│
└── analytics_events
    ├── user_id, event_type
    ├── metadata (JSON)
    └── timestamp
```

### 🧠 What You Learn (GenAI Concepts)

| Concept | How You Learn It |
|---------|-----------------|
| **LangGraph Advanced** | Checkpointing, parallel execution, graph versioning |
| **MCP Servers** | Design and deploy 3+ MCP servers for Claude integration |
| **Tool Use at Scale** | Claude autonomously chooses which MCP tool to call |
| **Multi-agent Orchestration** | Multiple agents evaluating mock interview answers |
| **Advanced Prompting** | Few-shot examples for consistent interview feedback |
| **Evaluation Metrics** | Design rubrics for evaluating learning outcomes |
| **Observability** | Full tracing of agent paths with LangSmith |
| **Performance at Scale** | Handle 100K+ users, optimize query latency |

### 🚀 Implementation Checklist

#### Week 3: Mock Interviews (Day 1-4)
- [ ] Design interview question bank (by difficulty)
- [ ] Implement progressive question selection
- [ ] Build real-time answer evaluation (Claude)
- [ ] Generate detailed feedback
- [ ] Mock interview scoring algorithm

#### Week 3: Collaboration (Day 5-6)
- [ ] Study group creation & management
- [ ] Discussion forum backend
- [ ] Peer comparison analytics
- [ ] Instructor dashboard

#### Week 3-4: Advanced Features (Day 7-9)
- [ ] MCP server integration
- [ ] Learning path recommendation engine
- [ ] Advanced analytics dashboard
- [ ] Certification system
- [ ] Calendar & reminders

#### Week 4: Polish & Launch (Day 10-11)
- [ ] Load testing (100K+ users)
- [ ] Performance optimization
- [ ] Security audit
- [ ] Deploy to production
- [ ] White-label option testing

### 📊 Success Criteria
- ✅ Mock interviews feel realistic and valuable
- ✅ Users improve significantly after using (measure via quiz scores)
- ✅ Peer comparison drives engagement
- ✅ Certificates are shareable and verifiable
- ✅ System handles 50K+ users reliably
- ✅ Users report 70%+ job placement success after bootcamp

---

## 🛠️ Tech Stack (All Phases)

### Backend
| Layer | Technology | Why |
|-------|-----------|-----|
| **Language** | Python 3.10+ | Rich GenAI ecosystem |
| **Framework** | FastAPI | Async, streaming-first |
| **LLM** | Claude (Anthropic SDK) | Best reasoning for learning |
| **Embeddings** | Claude embeddings | Production quality |
| **Orchestration** | LangChain → LangGraph | Simple to advanced graphs |
| **Vector DB** | ChromaDB (P1) → PostgreSQL + pgvector (P2/3) | Simple to enterprise |
| **PDF Processing** | PyPDF2 + pdfplumber | Robust extraction |
| **MCP** | Anthropic MCP SDK | Tool integration |

### Frontend
| Layer | Technology | Why |
|-------|-----------|-----|
| **Framework** | React 18 | Modern, component-based |
| **Language** | TypeScript | Type safety |
| **Styling** | TailwindCSS | Rapid development |
| **Streaming** | Server-Sent Events (SSE) | Real-time updates |
| **State** | TanStack Query + Zustand | Server + client state |
| **Charts** | Recharts | Simple, effective |

### Infrastructure
| Component | Technology |
|-----------|-----------|
| **Package Manager** | Poetry |
| **Testing** | pytest, pytest-asyncio |
| **Database** | PostgreSQL 14+ |
| **Deployment** | Docker + GitHub Actions |
| **Hosting** | Vercel (frontend), Fly.io/Railway (backend) |
| **Monitoring** | LangSmith (agents), DataDog (ops) |

---

## 📚 GenAI Concepts Covered

### Phase 1: Foundations
- Embeddings (generation, dimensionality)
- Vector similarity (cosine distance)
- Semantic search vs keyword search
- Chunking strategies
- Prompt engineering basics
- Token counting & budgeting
- Streaming

### Phase 2: Agentic
- LangGraph (nodes, edges, state)
- Conditional routing
- Multi-step reasoning
- Query rewriting
- Tool use (function calling)
- State persistence
- Cost tracking

### Phase 3: Production
- Graph checkpointing
- MCP server design
- Advanced prompting (evaluation rubrics)
- Observability (LangSmith)
- Performance optimization
- Multi-agent systems
- Evaluation frameworks

---

## 📖 Resources

### Documentation
- [Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook)
- [LangChain Docs](https://docs.langchain.com/)
- [LangGraph Guide](https://langchain-ai.github.io/langgraph/)
- [LangSmith Tracing](https://smith.langchain.com/)
- [MCP Spec](https://modelcontextprotocol.io/)

### Learning
- [Alex Xu's System Design Interview - Volume 1](https://www.amazon.com/System-Design-Interview-Alex-Xu/dp/B08CMF2CQF)
- [Claude API Docs](https://docs.anthropic.com/claude/)
- [RAG Best Practices](https://www.pinecone.io/learn/retrieval-augmented-generation/)

---

## 📁 Project Structure

```
InterviewX/
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── api/
│   │   │   ├── documents.py (Phase 1)
│   │   │   ├── query.py (Phase 1)
│   │   │   ├── quizzes.py (Phase 2)
│   │   │   ├── analytics.py (Phase 2)
│   │   │   ├── interviews.py (Phase 3)
│   │   │   ├── groups.py (Phase 3)
│   │   │   └── admin.py (Phase 3)
│   │   ├── services/
│   │   │   ├── embedding.py
│   │   │   ├── retrieval.py
│   │   │   ├── generation.py
│   │   │   ├── quiz_gen.py (Phase 2)
│   │   │   ├── interview_eval.py (Phase 3)
│   │   │   └── analytics.py (Phase 3)
│   │   ├── agents/
│   │   │   ├── retrieval_graph.py (Phase 2)
│   │   │   ├── quiz_graph.py (Phase 2)
│   │   │   └── interview_graph.py (Phase 3)
│   │   └── mcp/
│   │       ├── quiz_generator.py (Phase 2)
│   │       ├── evaluator.py (Phase 3)
│   │       └── video_gen.py (Phase 3)
│   ├── tests/
│   ├── pyproject.toml
│   └── .env.example
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── ChatInterface.tsx (P1)
│   │   │   ├── QuizInterface.tsx (P2)
│   │   │   ├── MockInterview.tsx (P3)
│   │   │   ├── StudyDashboard.tsx (P2)
│   │   │   ├── PeerComparison.tsx (P3)
│   │   │   └── ...
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── App.tsx
│   ├── package.json
│   └── vite.config.ts
│
├── docs/
│   ├── architecture.md
│   ├── phase1.md
│   ├── phase2.md
│   └── phase3.md
│
├── docker-compose.yml
├── Dockerfile
├── InterviewX.md
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
- Python 3.10+, Node.js 18+
- Anthropic API key
- PostgreSQL 14+ (Phase 2+)

### Phase 1 Setup (5 minutes)
```bash
# Backend
cd backend
poetry install
cp .env.example .env  # Add ANTHROPIC_API_KEY
poetry run uvicorn app.main:app --reload

# Frontend (new terminal)
cd frontend
npm install
npm run dev
```

### Deploy
- **Frontend**: Vercel (automatic from GitHub)
- **Backend**: Fly.io or Railway
- **Database**: Cloud SQL or Railway PostgreSQL

---

## ✨ Key Takeaways

**Phase 1**: Simple but powerful—RAG Q&A is production-ready  
**Phase 2**: Real engagement—learning tools drive usage  
**Phase 3**: Full platform—everything a learner needs  

Each phase teaches GenAI concepts hands-on while delivering working features users love.

---

## 🤝 What's Next?

Start with Phase 1 and build upward. Each phase stands alone and adds real value.

Questions? Reach out in discussions.

Happy building! 🚀
