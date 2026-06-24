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
| **Orchestration** | LangChain (Phase 1) → LangGraph (Phase 2+) | Simple chains → stateful agent graphs |
| **Agent Framework** | LangGraph | Graph-based agent orchestration, branching logic, loops |
| **External Tools** | MCP (Model Context Protocol) | Standardized integration for Claude with external systems |
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

## 🤖 Agentic AI with LangGraph & MCP

### What is LangGraph?

**LangGraph** is a framework for building stateful, multi-actor applications with Claude. It represents agent logic as a **directed graph** where:
- **Nodes** = discrete steps (think, retrieve, synthesize, validate)
- **Edges** = conditional routing (if no good results, try X; else continue)
- **State** = persistent context passed through steps
- **Loops** = agents can recursively call tools (retrieve, then reason, then retrieve more)

**Why LangGraph over plain LangChain agents?**
- Better control flow (explicit state machines)
- Easier to debug (see exact path through graph)
- Streaming-friendly (per-node outputs)
- Production-grade (used in real systems)

### What is MCP (Model Context Protocol)?

**MCP** is a standardized protocol for connecting Claude to external tools/data sources. Instead of writing tool definitions, you:
1. Run an MCP server (e.g., for filesystem, web search, databases)
2. Claude automatically discovers available tools
3. Claude calls them naturally via tool_use

**Why MCP?**
- Separation of concerns (Claude core ≠ tool integrations)
- Reusable servers (share MCP implementations)
- Standardization (Claude + other models can use same servers)
- Learn production patterns (MCP is becoming standard)

---

## 🧠 Agentic Use Cases for InterviewX

### Use Case 1: Multi-Step Query Resolution (LangGraph)
**Problem**: User asks "Compare rate limiting with load balancing" — requires synthesizing from multiple chapters.

**Agentic Flow**:
```
┌────────────────────────────────────────┐
│  USER: "Compare rate limiting & load   │
│  balancing for video streaming"        │
└────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────┐
│ [NODE 1: PLAN]                         │
│ Claude decides: Need rate limiting     │
│ concepts + load balancing concepts     │
│ + video streaming context              │
└────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────┐
│ [NODE 2: RETRIEVE] (Multi-retrieve)    │
│ • Search: "rate limiting algorithms"   │
│ • Search: "load balancing strategies"  │
│ • Search: "video streaming requirements"
│ Collect all relevant chunks            │
└────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────┐
│ [NODE 3: EVALUATE]                     │
│ Claude checks: "Do I have enough       │
│ info to compare?" → Yes/No             │
│ If No → Retrieve more (loop)           │
└────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────┐
│ [NODE 4: SYNTHESIZE]                   │
│ Claude generates comparison with       │
│ citations from all retrieved chunks    │
└────────────────────────────────────────┘
```

**What you learn:**
- Graph-based reasoning (explicit decision points)
- Conditional routing (if/else in agent logic)
- Handling uncertainty (when to retrieve more vs. answer)
- Multi-tool orchestration

---

### Use Case 2: Query Rewriting with Fallback (LangGraph + Tool Use)
**Problem**: User query "caching strategies" doesn't match well—document uses "caching layer", "in-memory cache", etc.

**Agentic Flow**:
```
[Initial Search] → No good results?
     ↓
[Rewrite Query] → Claude suggests: 
  • "caching layer database"
  • "distributed cache design"
  • "cache invalidation patterns"
     ↓
[Multi-Query Search] → Search each variant
     ↓
[Rank & Synthesize] → Pick best + combine
```

**What you learn:**
- Query optimization with LLM
- Handling retrieval failures gracefully
- Multi-query search patterns

---

### Use Case 3: External Tool Integration with MCP
**Problem**: User asks "What cloud providers discuss this?" or "Generate a quiz from this chapter"—not in document.

**MCP Servers to Build/Use**:
```
┌─────────────────┐
│  MCP: Filesystem│ → List chapters, read files
└─────────────────┘
         ↓
┌─────────────────┐
│  MCP: Web Search│ → Search web for complementary info
└─────────────────┘
         ↓
┌─────────────────┐
│  MCP: Quiz Gen  │ → Generate questions from text
└─────────────────┘
         ↓
┌─────────────────┐
│ Claude Decides: │ → When to call which tool
└─────────────────┘
```

**Example Flow**:
```
User: "What else should I know about this design pattern?"
  ↓
Claude decides: I can search the web via MCP
  ↓
[MCP: Web Search] → Fetch latest industry practices
  ↓
Claude synthesizes: Combine book knowledge + web research
  ↓
User gets: Grounded answer with both sources cited
```

**What you learn:**
- MCP server architecture
- Tool discovery & dynamic invocation
- Integration patterns for Claude

---

### Use Case 4: Verification Agent (LangGraph Loop)
**Problem**: Claude generated answer—is it actually grounded in the document?

**Agentic Flow**:
```
[Generate Answer] 
     ↓
[Extract Claims] (Claude lists key claims)
     ↓
[Verify Each Claim]
  • Retrieve document chunk for each claim
  • Check if claim exists in document
  • Flag unsupported claims
     ↓
[Refine Answer]
  • Remove unsupported claims
  • Add caveats where needed
  • Return verified answer + confidence
```

**What you learn:**
- Self-correction loops
- Verification patterns
- Confidence scoring

---

### Use Case 5: Conversation Memory with Agent State (LangGraph State)
**Problem**: Multi-turn Q&A needs memory of previous context.

**Graph State**:
```python
{
  "conversation_id": "uuid",
  "messages": [...],           # Full history
  "current_query": "string",
  "retrieved_chunks": [...],   # From this turn
  "context_window_tokens": int,
  "agent_reasoning": "string", # Claude's thinking
  "citations": [...]           # Tracked automatically
}
```

**What you learn:**
- Stateful agents (persist data across nodes)
- Context management in conversations
- Token budgeting

---

## 📋 GenAI Concepts Table (Updated with Agentic Patterns)

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
| **Tool Use / Function Calling** | Claude calls retrieval functions autonomously | Anthropic SDK `tools` parameter, LangChain `AgentExecutor`, LangGraph `ToolNode` | How agents make decisions |
| **Multi-step Reasoning** | Complex queries split into retrieval + synthesis | Claude API + LangGraph node composition | Planning & decomposition |
| **Agentic Workflows** | Build graph-based agent logic with nodes & edges | LangGraph: nodes, edges, state, conditional routing | Production agent patterns |
| **State Management** | Persistent context across agent steps | LangGraph `StateGraph`, state dictionaries | Managing complex workflows |
| **Conditional Routing** | Agent decides next step based on intermediate results | LangGraph conditional edges, decision functions | Intelligent agent control flow |
| **Loops & Iteration** | Agent can recursively retrieve/refine until satisfied | LangGraph cycle detection, max iterations | When agents should stop |
| **MCP Integration** | Connect Claude to external tools via standardized protocol | MCP servers, `mcp_invoke_tool` | Integration patterns |
| **Multi-Query Search** | Try multiple search variants for better coverage | LangGraph parallel nodes, MCP for query generation | Improving retrieval coverage |
| **Retrieval Ranking** | Rerank results by relevance, diversity | Cohere reranker API, LangGraph reranking node | Improving retrieval quality |
| **Verification Agent** | Self-check answers against source documents | LangGraph verification loop, claim extraction | Hallucination mitigation |
| **Cost Optimization** | Track embeddings, generations, API calls | Token counting, LangGraph state introspection | GenAI economics |
| **Evaluation & Metrics** | Measure retrieval quality, generation coherence, agent efficiency | `ragas` library, LangSmith tracing | How to evaluate RAG + agentic systems |

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

### Phase 2: Agentic Workflows with LangGraph & MCP (Week 2-3)
**Goal**: Graph-based agent orchestration, external tool integration, multi-step reasoning

#### Backend: LangGraph Implementation
- [ ] Build `RetrievalGraph` — nodes for: plan → retrieve → evaluate → synthesize
- [ ] Implement conditional routing (if low confidence, retrieve more)
- [ ] Multi-query search node (Claude suggests search variants)
- [ ] Verification loop (fact-check answers against chunks)
- [ ] State management: conversation memory, retrieved chunks, agent reasoning
- [ ] Token counting per node (understand where tokens go)
- [ ] Error handling & fallbacks (when retrieval fails)

#### Backend: MCP Integration
- [ ] Design first MCP server: `filesystem_mcp` (list chapters, read text files)
- [ ] Build `quiz_generator_mcp` (Claude generates questions from text)
- [ ] Optional: `web_search_mcp` (fallback for out-of-domain questions)
- [ ] MCP tool discovery & dynamic invocation
- [ ] Claude decides when to call which MCP server

#### Database
- [ ] Conversation history table with full state snapshots
- [ ] Agent reasoning logs (what Claude thought at each node)
- [ ] Tool call logs (which tools called, when, with what args)
- [ ] Evaluation metrics (query satisfaction, hallucination rate)

#### Frontend
- [ ] Chat history with reasoning visualization
- [ ] Show agent path through graph (nodes visited)
- [ ] Display MCP tool calls & results
- [ ] Follow-up suggestions from agent reasoning
- [ ] Side panel: Retrieved chunks + citations

#### Learning Outcomes
- [ ] Graph-based agent design (nodes, edges, state)
- [ ] Conditional routing & decision-making in agents
- [ ] State management across multi-step workflows
- [ ] MCP server architecture & integration
- [ ] How production agentic systems work (LangGraph patterns)
- [ ] Cost per agent path (which nodes are expensive?)
- [ ] When to loop vs. when to answer (agent stopping conditions)

#### Example: Multi-Step Query Agent
```
User: "Compare rate limiting approaches for video streaming with 1M concurrent users"
  ↓
[Plan Node] → Claude: "Need rate limiting concepts + load balancing + video streaming"
  ↓
[Multi-Retrieve Node] → Search 3 variants in parallel
  ├─ "rate limiting algorithms video"
  ├─ "distributed load balancing"
  └─ "handling concurrent users scale"
  ↓
[Evaluate Node] → Claude: "Do I have enough info?" → Yes
  ↓
[Synthesize Node] → Claude generates comparison with citations
  ↓
[Optional Verify Node] → Check each claim against chunks
  ↓
[Return] → Answer + agent path + citations
```

#### Example: MCP Integration
```
User: "Generate a quiz from the caching chapter"
  ↓
Claude (via tool_use): "I need to call quiz_generator_mcp"
  ↓
[MCP: Quiz Generator]
  • Reads chapter from filesystem
  • Returns structured Q&A
  ↓
Claude synthesizes: "Based on the chapter, here are 5 questions..."
```

---

### Phase 3: Production Polish & Scaling (Week 3+)
**Goal**: Scalable, observable, cost-efficient agentic system

#### Backend: LangGraph at Scale
- [ ] Graph checkpointing (persist agent state for resumability)
- [ ] Parallel node execution (search multiple queries simultaneously)
- [ ] Timeout & max-iteration limits (prevent infinite loops)
- [ ] Performance profiling per node (understand bottlenecks)
- [ ] Graph versioning (A/B test different agent flows)
- [ ] LangSmith integration (trace every agent step)

#### Backend: MCP Server Scaling
- [ ] Deploy MCP servers independently (microservice pattern)
- [ ] MCP server registry (discovery & health checks)
- [ ] Rate limiting per MCP tool
- [ ] Fallback MCP servers (if one fails, use backup)
- [ ] Monitor MCP server performance & costs

#### Database & Observability
- [ ] Migrate to PostgreSQL + pgvector
- [ ] Agent path analytics (which graph paths are most common?)
- [ ] Cost tracking per agent step (which nodes cost most?)
- [ ] Evaluation framework: compare agent paths → results
- [ ] A/B testing framework for graph variants

#### Frontend
- [ ] Analytics dashboard (agent paths, node latency, cost)
- [ ] Admin console for graph management
- [ ] MCP tool availability status
- [ ] Agent performance heatmaps
- [ ] Feedback & quality metrics

#### Deployment
- [ ] Docker compose for full stack (FastAPI + agents + MCP servers)
- [ ] GitHub Actions CI/CD with agent graph testing
- [ ] Deploy to cloud with agent observability (LangSmith)
- [ ] Load testing for concurrent agent runs

#### Learning Outcomes
- LangGraph production patterns (checkpointing, parallelization)
- MCP server deployment & scaling
- Observability of agentic systems
- Cost optimization across graph nodes
- Testing & evaluation of complex agents

---

## 🏛️ LangGraph Architecture Deep Dive

### Graph Structure

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Literal

# Define the state shape
class AgentState(TypedDict):
    query: str
    retrieved_chunks: list[str]
    search_variants: list[str]
    agent_reasoning: str
    final_answer: str
    confidence: float
    citations: list[dict]
    tokens_used: int

# Create graph
graph = StateGraph(AgentState)

# Add nodes (each node is a function: state → state)
graph.add_node("plan", plan_node)          # Claude plans retrieval strategy
graph.add_node("retrieve", retrieve_node)  # Semantic search
graph.add_node("evaluate", evaluate_node)  # Claude checks if done
graph.add_node("synthesize", synthesize_node)  # Generate answer
graph.add_node("verify", verify_node)      # Optional: fact-check

# Add conditional edges (routing logic)
graph.add_edge("plan", "retrieve")
graph.add_conditional_edges(
    "retrieve",
    should_evaluate,  # Function: state → next node name
    {
        "retrieve_more": "retrieve",  # Loop back for more retrieval
        "evaluate": "evaluate"        # Move to next node
    }
)
graph.add_edge("evaluate", "synthesize")
graph.add_edge("synthesize", "verify")
graph.add_edge("verify", END)

# Compile into runnable
agent = graph.compile()

# Use it
result = agent.invoke({
    "query": "How do I design a cache?",
    "retrieved_chunks": [],
    ...
})
```

### Node Types in InterviewX

| Node | Input | Process | Output | Cost |
|------|-------|---------|--------|------|
| **Plan** | User query | Claude decides: what to retrieve? | `search_variants`, `agent_reasoning` | 1 LLM call |
| **Retrieve** | search_variants | Embed query, similarity search (can parallelize) | `retrieved_chunks` | N embeddings |
| **Evaluate** | retrieved_chunks | Claude: "Enough info?" | `confidence`, routing decision | 1 LLM call |
| **Synthesize** | retrieved_chunks | Claude generates answer | `final_answer`, `citations` | 1 LLM call |
| **Verify** (optional) | final_answer, chunks | Claude checks claims against chunks | `verified_answer`, flags | 1 LLM call |

### Conditional Routing Patterns

```python
def should_continue(state: AgentState) -> Literal["retrieve_more", "synthesize"]:
    """Decide: retrieve more or move to synthesis?"""
    if state["confidence"] < 0.7:
        return "retrieve_more"
    return "synthesize"

def should_verify(state: AgentState) -> Literal["verify", END]:
    """Decide: verify answer or return?"""
    if "hallucination_risk" in state.get("flags", []):
        return "verify"
    return END
```

### State Persistence & Resumability

```python
# LangGraph supports checkpointing
config = {"configurable": {"thread_id": "user_123"}}
agent.invoke(input_state, config=config)

# Later, resume from same state
result = agent.invoke(
    {"query": "What about load balancing?"},  # Follow-up
    config=config  # Same thread → shares history
)
```

---

## 🔌 MCP (Model Context Protocol) Architecture

### MCP Server Anatomy

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server

# Create MCP server
server = Server("quiz-generator")

@server.list_tools()
async def list_tools():
    """Tell Claude what tools are available"""
    return [
        {
            "name": "generate_quiz",
            "description": "Generate 5 quiz questions from text",
            "inputSchema": {
                "type": "object",
                "properties": {
                    "text": {"type": "string", "description": "Chapter text"},
                    "difficulty": {"type": "string", "enum": ["easy", "hard"]}
                },
                "required": ["text"]
            }
        }
    ]

@server.call_tool()
async def call_tool(name: str, arguments: dict):
    """Execute tool when Claude calls it"""
    if name == "generate_quiz":
        text = arguments["text"]
        difficulty = arguments.get("difficulty", "medium")
        quiz = generate_questions(text, difficulty)
        return {"type": "text", "text": json.dumps(quiz)}
```

### MCP Servers in InterviewX

| Server | Tools | Purpose | When Claude Uses It |
|--------|-------|---------|-------------------|
| **filesystem_mcp** | `list_chapters`, `read_section`, `search_index` | Direct file access | User: "Show me the caching chapter" |
| **quiz_generator_mcp** | `generate_quiz`, `generate_summary` | Generate content from text | User: "Quiz me on this topic" |
| **web_search_mcp** | `search`, `fetch_url` | External knowledge | User: "What's the latest in X?" (out of PDF scope) |
| **evaluation_mcp** | `evaluate_answer`, `check_correctness` | Verify quiz answers | Interactive quiz mode |

### Claude Tool Use Flow

```
Claude thinks: "User wants a quiz, I should call quiz_generator_mcp"
    ↓
Claude generates tool call:
{
  "type": "use_tool",
  "name": "quiz_generator_mcp/generate_quiz",
  "arguments": {
    "text": "...",
    "difficulty": "medium"
  }
}
    ↓
Backend invokes MCP server:
MCP_QUIZ_GENERATOR.call_tool("generate_quiz", {...})
    ↓
MCP returns: {"type": "text", "text": "[{\"q\": ..., \"a\": ...}]"}
    ↓
Claude continues: "Based on the chapter, here are 5 questions..."
```

### Combining LangGraph + MCP

```python
# In a LangGraph node, call MCP tools dynamically
async def retrieve_with_mcp(state: AgentState):
    query = state["query"]
    
    # Claude decides which MCP to call
    tool_decision = await claude.invoke([
        {"role": "user", "content": f"What MCP tool should I use for: {query}?"}
    ])
    
    if "quiz" in tool_decision:
        result = await quiz_generator_mcp.call_tool("generate_quiz", {...})
    elif "search" in tool_decision:
        result = await web_search_mcp.call_tool("search", {...})
    
    return {
        **state,
        "mcp_results": result,
        "agent_reasoning": f"Used {tool_decision}"
    }
```

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

### After Phase 2: LangGraph & MCP Mastery
- [ ] **LangGraph Fundamentals**: Build stateful agent graphs with nodes, edges, conditional routing
- [ ] **State Management**: Persist & pass context across multi-step agent workflows
- [ ] **Conditional Routing**: Agent decides next step based on intermediate results
- [ ] **Loops & Cycles**: Handle recursive retrieval (when to stop?)
- [ ] **MCP Server Design**: Create MCP servers with tool definitions
- [ ] **MCP Integration**: Claude autonomously calls MCP tools via tool_use
- [ ] **Tool Discovery**: Dynamic tool availability & fallbacks
- [ ] **Multi-step Reasoning**: Complex queries decomposed across graph nodes
- [ ] **Token Budgeting**: Track cost per node, optimize critical paths
- [ ] **Agent Tracing**: Understand what agent did at each step (LangSmith)
- [ ] **Verification Patterns**: Self-checking agents that validate their answers
- [ ] **Cost Analysis**: Which agent paths are expensive? Can we optimize?

### After Phase 3: Production-Grade Agentic Systems
- [ ] **Graph Checkpointing**: Resume agent execution from any state (multi-turn)
- [ ] **Parallel Execution**: Run independent retrieval tasks simultaneously
- [ ] **MCP Scaling**: Deploy MCP servers as microservices with health checks
- [ ] **Performance Tuning**: Profiling, parallelization, caching strategies
- [ ] **Advanced Patterns**: Multi-agent orchestration, hierarchical graphs
- [ ] **Observability**: Full tracing of agent paths with LangSmith
- [ ] **Cost Optimization**: Per-node cost analysis, route optimization
- [ ] **Evaluation & Testing**: Compare agent graphs, measure improvement
- [ ] **Production Deployment**: Docker + orchestration for complex agent systems
- [ ] **Debugging**: Step through agent execution, inspect state at each node

---

## 🔮 Future Enhancements

### Retrieval Improvements
- [ ] Hypothetical document embeddings (HyDE)
- [ ] Query expansion with multi-hop retrieval
- [ ] Recursive summarization for long chapters
- [ ] Semantic clustering of chunks

### Agentic Features (LangGraph + MCP)
- [ ] Web search MCP fallback for out-of-domain questions
- [ ] Cross-book retrieval (graph node that searches multiple knowledge bases)
- [ ] Quiz generation MCP (Claude calls it autonomously)
- [ ] Debate mode (parallel graph branches for different perspectives)
- [ ] Code snippet executor MCP (run example code from chapters)
- [ ] Citation verification MCP (fact-check answers automatically)
- [ ] Advanced routing (ensemble of agents voting on answer)
- [ ] Multi-agent graph (agents discuss before answering user)

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

### Agentic Systems & LangGraph
- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/) — Graph-based agents
- [LangGraph Examples](https://github.com/langchain-ai/langgraph/tree/main/examples) — Reference implementations
- [LangSmith Tracing](https://smith.langchain.com/) — Agent observability
- [Anthropic Tool Use Guide](https://docs.anthropic.com/claude/reference/tool-use) — How Claude calls tools

### Model Context Protocol (MCP)
- [MCP Specification](https://modelcontextprotocol.io/) — Official spec
- [MCP Python SDK](https://github.com/anthropics/python-sdk-mcp) — Build servers
- [MCP Example Servers](https://github.com/anthropics/mcp-servers) — Reference implementations
- [Claude MCP Integration](https://docs.anthropic.com/claude/reference/claude-with-mcp) — How Claude uses MCP

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

## 💻 Code Examples

### Example 1: Simple LangGraph Agent

```python
# backend/app/agents/retrieval_graph.py
from langgraph.graph import StateGraph, START, END
from typing import TypedDict

class State(TypedDict):
    query: str
    chunks: list[str]
    answer: str

async def plan_node(state: State):
    """Claude decides retrieval strategy"""
    result = await claude.messages.create(
        model="claude-opus-4-1",
        messages=[{"role": "user", "content": f"Plan: {state['query']}"}]
    )
    return {"query": state["query"]}

async def retrieve_node(state: State):
    """Search for relevant chunks"""
    # Embed query, search vector DB
    chunks = await vector_db.search(state["query"], top_k=5)
    return {"chunks": chunks}

async def generate_node(state: State):
    """Claude generates answer"""
    context = "\n".join(state["chunks"])
    result = await claude.messages.create(
        model="claude-opus-4-1",
        messages=[
            {"role": "user", "content": f"Answer: {state['query']}\n\nContext: {context}"}
        ]
    )
    return {"answer": result.content[0].text}

# Build graph
builder = StateGraph(State)
builder.add_node("plan", plan_node)
builder.add_node("retrieve", retrieve_node)
builder.add_node("generate", generate_node)

builder.add_edge(START, "plan")
builder.add_edge("plan", "retrieve")
builder.add_edge("retrieve", "generate")
builder.add_edge("generate", END)

graph = builder.compile()

# Use it
result = await graph.ainvoke({"query": "How do I design a cache?"})
print(result["answer"])
```

### Example 2: MCP Server (Quiz Generator)

```python
# backend/mcp_servers/quiz_generator.py
from mcp.server import Server
import json

server = Server("quiz-generator")

@server.list_tools()
async def list_tools():
    return [
        {
            "name": "generate_quiz",
            "description": "Generate 5 multiple-choice questions from text",
            "inputSchema": {
                "type": "object",
                "properties": {
                    "text": {"type": "string"},
                    "topic": {"type": "string"},
                    "difficulty": {"enum": ["easy", "medium", "hard"]}
                }
            }
        }
    ]

@server.call_tool()
async def call_tool(name: str, arguments: dict):
    if name == "generate_quiz":
        text = arguments["text"]
        topic = arguments.get("topic", "System Design")
        difficulty = arguments.get("difficulty", "medium")
        
        # Use Claude to generate questions
        prompt = f"""Generate 5 {difficulty} multiple-choice questions about {topic} based on:

{text}

Return as JSON: [{{"question": "...", "options": ["a", "b", "c", "d"], "answer": "a"}}]"""
        
        response = await claude.messages.create(
            model="claude-opus-4-1",
            messages=[{"role": "user", "content": prompt}]
        )
        
        questions = json.loads(response.content[0].text)
        return {
            "type": "text",
            "text": json.dumps({"questions": questions, "count": len(questions)})
        }

if __name__ == "__main__":
    import asyncio
    asyncio.run(server.run_stdio())
```

### Example 3: Conditional Routing in Graph

```python
# backend/app/agents/advanced_graph.py
from langgraph.graph import StateGraph

async def evaluate_node(state: State):
    """Check if we have enough info"""
    result = await claude.messages.create(
        model="claude-opus-4-1",
        messages=[
            {"role": "user", "content": f"Do we have enough info to answer: {state['query']}? Answer: YES or NO"}
        ]
    )
    has_enough = "YES" in result.content[0].text
    return {
        "confidence": 0.9 if has_enough else 0.3,
        "chunks": state["chunks"]
    }

def should_retrieve_more(state: State) -> str:
    """Decide: retrieve more or synthesize?"""
    if state["confidence"] < 0.7:
        return "retrieve_more"  # Go back to retrieve node
    return "synthesize"

builder = StateGraph(State)
# ... add nodes ...

builder.add_conditional_edges(
    "evaluate",
    should_retrieve_more,
    {
        "retrieve_more": "retrieve",  # Loop back
        "synthesize": "synthesize"     # Move forward
    }
)
```

### Example 4: Calling MCP from Graph Node

```python
# backend/app/agents/mcp_node.py
async def quiz_generation_node(state: State):
    """Generate quiz using MCP server"""
    # Claude decides to call MCP
    response = await claude.messages.create(
        model="claude-opus-4-1",
        tools=[
            {
                "name": "call_mcp",
                "description": "Call external MCP server",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "server": {"type": "string"},
                        "tool": {"type": "string"},
                        "args": {"type": "object"}
                    }
                }
            }
        ],
        messages=[
            {"role": "user", "content": f"Generate a quiz for: {state['query']}"}
        ]
    )
    
    # Extract tool call
    tool_call = response.content[0]
    if tool_call.type == "tool_use":
        # Invoke MCP server
        mcp_result = await mcp_registry.invoke(
            tool_call.input["server"],
            tool_call.input["tool"],
            tool_call.input["args"]
        )
        
        # Claude uses result to continue
        return {"quiz": mcp_result, "chunks": state["chunks"]}
```

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
