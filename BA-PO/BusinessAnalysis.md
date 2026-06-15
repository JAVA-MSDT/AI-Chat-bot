# Business Analysis: Generic AI ChatBot Engine

---

## 1. Business Plan

A reusable, domain-agnostic AI chatbot engine built on **Java 21 + Spring Boot + Spring AI**. The engine lets any team
plug in their own LLM (Ollama, Claude, OpenAI, or others), feed it domain-specific knowledge from any data source (DB,
documents, APIs), and deploy a production-ready chatbot without rebuilding core AI infrastructure. The initial plan is
captured in `Plan.md` at the project root.

---

## 2. Business Requirements

| #     | Requirement                                                                         | Priority    |
|-------|-------------------------------------------------------------------------------------|-------------|
| BR-01 | Pluggable LLM adapter — swap providers via configuration only, zero code changes    | Must Have   |
| BR-02 | Configurable data ingestion pipeline supporting DB, REST APIs, and document uploads | Must Have   |
| BR-03 | RAG (Retrieval-Augmented Generation) pipeline with vector store semantic search     | Must Have   |
| BR-04 | Persistent chat session & history per user across sessions                          | Must Have   |
| BR-05 | Domain restriction guardrails — responses scoped to configured knowledge base only  | Must Have   |
| BR-06 | REST API + WebSocket interface for query submission and streaming response          | Must Have   |
| BR-07 | Self-serve configuration endpoint (first-run setup + ongoing reconfiguration)       | Must Have   |
| BR-08 | Response format flexibility: plain text, Markdown, JSON                             | Should Have |
| BR-09 | No credential or sensitive data leakage in any response                             | Must Have   |
| BR-10 | Zero hardcoded values — all config externalized                                     | Must Have   |
| BR-11 | Embedded UI for standalone deployment (optional lightweight frontend)               | Could Have  |
| BR-12 | Audit logging for queries and responses                                             | Should Have |

---

## 3. Business Goals

- **Enable Java teams to adopt AI** without switching technology stacks or vendor lock-in.
- **Reduce time-to-chatbot** from weeks of custom AI work to days of configuration.
- **Democratize domain expertise** by allowing any team to create a knowledgeable chatbot from their existing data.
- **Preserve data sovereignty** by supporting fully on-premises LLM deployments (Ollama).
- **Create a reusable asset** within EPAM / the organization that can be licensed or productized.

---

## 4. Business Objectives

| Objective            | Metric                                                                  | Target                        |
|----------------------|-------------------------------------------------------------------------|-------------------------------|
| Faster deployment    | Time from integration to first working chatbot                          | ≤ 2 business days             |
| LLM portability      | Number of LLM providers supported out of the box                        | ≥ 3 (Ollama, OpenAI, Claude)  |
| Data source coverage | Number of ingestion source types supported                              | ≥ 3 (DB, REST API, documents) |
| Developer adoption   | Teams able to integrate with zero AI expertise                          | 100% (config-only onboarding) |
| Cost reduction       | Ability to run entirely on local/free LLMs                              | Full support via Ollama       |
| Response quality     | Domain-scoped answer accuracy (no hallucination outside knowledge base) | ≥ 90% in UAT                  |

---

## 5. Business Value Proposition

**For Java development teams:**
> "Deploy a production-grade AI chatbot in days — not months — using the stack you already know, with the data you
> already own."

| Stakeholder         | Value Delivered                                                                      |
|---------------------|--------------------------------------------------------------------------------------|
| Engineering Teams   | No new language/framework to learn; pure Java/Spring                                 |
| Product Owners      | Rapid prototyping of domain chatbots (healthcare, finance, e-commerce, etc.)         |
| Architects          | Pluggable, future-proof design that adapts as AI landscape evolves                   |
| Security/Compliance | On-premises LLM option keeps sensitive data within org boundaries                    |
| Business Leaders    | Cost-controlled AI: start free with local models, scale to cloud only when justified |

---

## 6. Business Risks

| Risk                                            | Likelihood | Impact   | Mitigation                                                                          |
|-------------------------------------------------|------------|----------|-------------------------------------------------------------------------------------|
| Spring AI API instability (early-stage library) | High       | High     | Pin minor versions; abstract Spring AI behind internal ports/adapters               |
| LLM provider API breaking changes               | Medium     | High     | Versioned adapter layer; integration test suite per provider                        |
| Vector store choice lock-in                     | Medium     | Medium   | Abstract vector store behind a repository interface (swap Chroma/Pinecone/pgvector) |
| Domain guardrail bypass (prompt injection)      | Medium     | High     | Input sanitization, system prompt hardening, output filtering layer                 |
| Performance degradation at scale                | Medium     | Medium   | Async processing, connection pooling, horizontal scaling design from day one        |
| Sensitive data ingested into vector store       | Low        | Critical | Data classification step in ingestion pipeline; field-level redaction hooks         |
| Adoption friction (configuration complexity)    | Medium     | Medium   | Opinionated defaults, starter configs per domain type, comprehensive docs           |

---

## 7. Business Constraints

- **Technology**: Java 21 + Spring Boot + Spring AI only — no Python, no Node.js backend
- **Budget**: POC/prototype phase — minimal external infrastructure costs; prefer open-source components
- **LLM Costs**: Must support zero-cost local LLM path (Ollama) for sensitive or budget-constrained domains
- **Regulatory**: Any healthcare or finance domain deployment must support on-premises data processing (HIPAA, GDPR
  consideration)
- **Team**: Small team (POC context); architecture must be simple enough for one engineer to understand end-to-end
- **Timeline**: POC working demo expected within short iteration; no hard deadline stated yet

---

## 8. Business Assumptions

- Teams integrating this engine have basic Spring Boot knowledge
- The primary deployment model is as a **library/starter** embedded in existing Spring Boot apps, not a standalone SaaS
- Vector store will initially be in-memory or file-based (e.g., pgvector / Chroma) — no managed cloud vector DB required
  for POC
- LLM inference for the POC will be tested primarily with **Ollama (local)** and **OpenAI**
- Domain training data is owned by the integrating team and provided in standard formats (SQL, PDF, JSON, REST
  endpoints)
- A simple embedded UI (Vaadin or Thymeleaf) is a stretch goal, not a core deliverable
- No multi-tenant architecture needed for POC; single-tenant with user session isolation is enough

---

## 9. Estimated Timeline

```
Phase 0 – Foundation (Week 1–2)
  ├── Spring Boot project scaffold + Maven/Gradle setup
  ├── Spring AI dependency configuration
  ├── LLM adapter abstraction (port + first implementation: Ollama)
  └── Basic REST API skeleton

Phase 1 – RAG Core (Week 3–4)
  ├── Document ingestion pipeline (PDF, TXT, JSON)
  ├── Vector store integration (pgvector or Chroma)
  ├── Embedding + semantic search
  └── First end-to-end query → retrieval → LLM → response

Phase 2 – Multi-Source Ingestion (Week 5–6)
  ├── DB ingestion connector
  ├── REST API ingestion connector
  ├── Configurable data source registry
  └── Domain restriction guardrail

Phase 3 – Session & History (Week 7)
  ├── Persistent chat session per user
  ├── In-conversation context window management
  └── WebSocket streaming support

Phase 4 – Configuration Endpoint & Polish (Week 8)
  ├── Self-serve configuration API
  ├── OpenAI + Claude LLM adapters
  ├── Response format options (text/Markdown/JSON)
  └── Security hardening (no credential leakage, input sanitization)

Phase 5 – POC Demo & Docs (Week 9–10)
  ├── Integration guide + sample domain (e.g., Diving knowledge base)
  ├── Embedded UI (stretch goal)
  └── Performance baseline + load test
```

---

## 10. Assessments & Recommendations

### Feasibility

**High.** The Java/Spring ecosystem (Spring AI) now has mature enough abstractions for RAG pipelines. Ollama makes local
LLM testing completely free. This is a realistic 8–10-week POC for a small team.

### Market Value

**Strong within enterprise Java shops.** The main differentiation is the Java-native approach — most RAG frameworks (LangChain, LlamaIndex) are Python-first, creating adoption friction in Java-heavy organizations (banking, insurance,
telecom). This fills a real gap.

### Recommendations

1. **Start with Ollama + pgvector** for the POC — zero external dependencies, fully reproducible
2. **Abstract everything behind interfaces on day one** — `LlmPort`, `VectorStorePort`, `IngestionPort` — this is the
   core architectural bet
3. **Resist feature creep in Phase 0–2** — the pluggable adapter pattern is the product; get it right before adding
   sources
4. **Build a demo domain immediately** (e.g., Diving or Sports) to validate the full pipeline end-to-end with real data
5. **Document the integration guide as you build**, not after — it is the primary adoption artifact
6. **Consider packaging as a Spring Boot Starter** (`spring-boot-starter-chatbot`) — lowers integration friction to a
   single Maven dependency

---

## 11. Implementation Plan

### Step 1 – Project Scaffold

- Initialize Spring Boot 3.x project (Maven or Gradle)
- Add Spring AI BOM + Ollama dependency
- Set up multi-module structure: `core`, `adapters`, `api`, `ingestion`

### Step 2 – LLM Adapter Layer

- Define `LlmPort` interface: `chat(prompt, context) → response`
- Implement `OllamaLlmAdapter` (Spring AI `OllamaChatClient`)
- Implement `OpenAiLlmAdapter` (Spring AI `OpenAiChatClient`)
- Implement `ClaudeAdapter` (Spring AI or direct Anthropic SDK)
- Configuration: `chatbot.llm.provider=ollama|openai|claude`

### Step 3 – Ingestion Pipeline

- Define `IngestionPort` interface: `ingest(DataSource) → VectorizedChunks`
- Implement: `DocumentIngester` (PDF/TXT/Markdown via Spring AI ETL)
- Implement: `DatabaseIngester` (JDBC → chunked rows)
- Implement: `ApiIngester` (REST endpoint → paginated pull)
- Chunking strategy: configurable chunk size and overlap

### Step 4 – Vector Store + RAG

- Integrate pgvector (PostgreSQL extension) via Spring AI `VectorStore`
- Embedding: use LLM provider's embedding API or local model
- Query pipeline: embed user query → cosine similarity search → top-K chunks → prompt injection

### Step 5 – API Layer

- `POST /api/chat` — synchronous query
- `WS /ws/chat` — streaming WebSocket
- `POST /api/admin/config` — configuration endpoint
- `POST /api/admin/ingest` — trigger ingestion job

### Step 6 – Session & History

- `ChatSession` entity (JPA) — sessionId, userId, messages[]
- Spring AI `MessageHistory` or custom implementation
- Context window: last N messages injected into prompt

### Step 7 – Domain Guardrails

- System prompt includes domain scope definition
- Post-response filter: flag/reject out-of-domain responses
- Configurable: `chatbot.domain.scope=...`

### Step 8 – Security & Config Hardening

- No credentials in responses (output filter regex patterns)
- All configs via `application.yml` + env vars
- Spring Security for admin endpoints

---

## 12. Proposed Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                         │
│   REST Client / WebSocket Client / Embedded UI (optional)   │
└──────────────────────┬──────────────────────────────────────┘
                       │ HTTP / WebSocket
┌──────────────────────▼──────────────────────────────────────┐
│                     API LAYER (Spring MVC)                  │
│   ChatController  │  AdminController  │  IngestionController │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                   CORE ENGINE (Domain)                       │
│                                                              │
│  ┌──────────────┐   ┌───────────────┐   ┌────────────────┐  │
│  │ Chat Service │──▶│  RAG Pipeline │──▶│  LLM Port      │  │
│  │ (Session Mgr)│   │  (Retrieval + │   │  (Interface)   │  │
│  └──────────────┘   │   Prompt Enr.)│   └───────┬────────┘  │
│                     └───────┬───────┘           │           │
│                             │                   │           │
│  ┌──────────────────────────▼───┐    ┌──────────▼────────┐  │
│  │     Vector Store Port        │    │   LLM Adapters    │  │
│  │     (Interface)              │    │  ┌─────────────┐  │  │
│  └──────────────────────────────┘    │  │ Ollama      │  │  │
│                                      │  │ OpenAI      │  │  │
│  ┌───────────────────────────────┐   │  │ Claude      │  │  │
│  │     Ingestion Pipeline        │   │  └─────────────┘  │  │
│  │  Doc │ DB │ API Ingestors     │   └───────────────────┘  │
│  └──────────────┬────────────────┘                          │
└─────────────────┼───────────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────────┐
│                   INFRASTRUCTURE LAYER                       │
│                                                              │
│   pgvector (Vector Store)   │   PostgreSQL (Session/History) │
│   Ollama (local LLM)        │   External LLM APIs            │
│   File System (documents)   │   External Data APIs           │
└─────────────────────────────────────────────────────────────┘
```

**Key Design Patterns:**

- **Hexagonal Architecture** (Ports & Adapters) — core is pure domain logic, all I/O via interfaces
- **Strategy Pattern** — LLM provider and vector store are interchangeable strategies
- **Pipeline Pattern** — ingestion and RAG retrieval are composable pipeline steps
- **Spring AI Abstractions** — `ChatClient`, `VectorStore`, `EmbeddingClient`, `DocumentReader`

---

## 13. JIRA Tickets Outline

### Epic 1: Project Foundation

- `CHAT-001` Set up Spring Boot 3.x multi-module project structure
- `CHAT-002` Configure Spring AI BOM and Ollama dependency
- `CHAT-003` Define core interfaces: `LlmPort`, `VectorStorePort`, `IngestionPort`
- `CHAT-004` Set up PostgreSQL and pgvector Docker Compose for local dev

### Epic 2: LLM Adapter Layer

- `CHAT-010` Implement `OllamaLlmAdapter` with Spring AI OllamaChatClient
- `CHAT-011` Implement `OpenAiLlmAdapter` with Spring AI OpenAiChatClient
- `CHAT-012` Implement `ClaudeAdapter` (Anthropic SDK integration)
- `CHAT-013` LLM provider configuration via `application.yml`
- `CHAT-014` Integration tests per LLM adapter

### Epic 3: Ingestion Pipeline

- `CHAT-020` Document ingestion (PDF, TXT, Markdown) using Spring AI DocumentReader
- `CHAT-021` Database ingestion connector (JDBC-based)
- `CHAT-022` REST API ingestion connector (paginated pull)
- `CHAT-023` Chunking strategy: configurable size and overlap
- `CHAT-024` Ingestion trigger API endpoint (`POST /api/admin/ingest`)

### Epic 4: RAG Core

- `CHAT-030` Integrate pgvector as Spring AI VectorStore
- `CHAT-031` Embedding pipeline (text → vector)
- `CHAT-032` Semantic search (query embedding → cosine similarity → top-K)
- `CHAT-033` Prompt enrichment with retrieved context
- `CHAT-034` End-to-end RAG integration test with sample domain data

### Epic 5: Chat API & Session

- `CHAT-040` `POST /api/chat` synchronous REST endpoint
- `CHAT-041` WebSocket `/ws/chat` with streaming response
- `CHAT-042` `ChatSession` entity and JPA repository
- `CHAT-043` Persistent message history (last N messages in context)
- `CHAT-044` Session isolation per user

### Epic 6: Configuration & Admin

- `CHAT-050` Self-serve configuration endpoint (`POST /api/admin/config`)
- `CHAT-051` First-run bootstrap: autoload config and seed vector store
- `CHAT-052` Domain scope guardrail (system prompt and response filter)
- `CHAT-053` Response format selector (text / Markdown / JSON)

### Epic 7: Security & Hardening

- `CHAT-060` Output filter: prevent credential/PII leakage in responses
- `CHAT-061` Input sanitization against prompt injection
- `CHAT-062` Spring Security for admin endpoints (API key or OAuth2)
- `CHAT-063` Zero hardcoded values audit and externalized config review

### Epic 8: Documentation & Demo

- `CHAT-070` Integration guide (README-level, config-first walkthrough)
- `CHAT-071` Sample domain dataset and demo chatbot (Diving or Sports domain)
- `CHAT-072` Architecture diagram (export from this plan)
- `CHAT-073` Performance baseline: latency and throughput under a simulated load

---

## 14. Open Questions

| #     | Question                                                                                             | Owner           | Priority |
|-------|------------------------------------------------------------------------------------------------------|-----------------|----------|
| OQ-01 | What is the primary deployment model — embedded Spring Boot Starter, standalone service, or both?    | PO/Architect    | High     |
| OQ-02 | Which vector store is preferred for POC: pgvector (PostgreSQL), Chroma (local), or Pinecone (cloud)? | Architect       | High     |
| OQ-03 | Is there a specific first domain to build the demo on? (Diving is mentioned — confirmed?)            | PO              | Medium   |
| OQ-04 | What is the expected user scale for POC — single user, small team, or multi-tenant?                  | PO              | High     |
| OQ-05 | Should the embedded UI be in scope for the POC or deferred?                                          | PO              | Medium   |
| OQ-06 | Is there a preference for sync vs. streaming responses as the default API mode?                      | PO              | Medium   |
| OQ-07 | Will the ingestion pipeline run on-demand (API trigger) or on a schedule (cron)?                     | PO              | Medium   |
| OQ-08 | Are there specific compliance requirements (GDPR, HIPAA) that affect the POC design?                 | Legal/Architect | Medium   |
| OQ-09 | What monitoring/observability tooling is expected (Spring Actuator + Prometheus sufficient)?         | Architect       | Low      |
| OQ-10 | Should chat history be stored indefinitely or with a retention policy?                               | PO/Legal        | Low      |

---

## 15. Additional Information

### Relevant Technologies & Libraries

- **Spring AI** (https://spring.io/projects/spring-ai) — core AI abstraction library; actively developed
- **Ollama** (https://ollama.com) — run LLMs locally; supports Llama, Mistral, Gemma, etc.
- **pgvector** (https://github.com/pgvector/pgvector) — open-source vector extension for PostgreSQL
- **LangChain4j** — an alternative Java AI framework worth evaluating alongside Spring AI
- **Chroma** (https://www.trychroma.com) — lightweight local vector DB if pgvector is too heavy for POC

### Similar Projects / Reference Implementations

- **Spring AI Pet Clinic RAG demo** — official Spring AI RAG example
- **LangChain4j examples** — alternative Java RAG patterns
- Other projects in this monorepo: `AI-DevWorkflow`, `Jira-Analysis-Agent` — both use Java + Claude API, good reference
  for patterns already established in this codebase

### Architectural Considerations Not in Original Plan

1. **Embedding model choice**: The embedding model should be independent of the chat LLM — consider using a dedicated
   embedding model (e.g., `nomic-embed-text` via Ollama) for vector store operations
2. **Chunking strategy matters**: Naive fixed-size chunking degrades RAG quality; consider sentence-aware or
   paragraph-aware chunking from the start
3. **Hybrid search**: Combine vector similarity search with keyword search (BM25) for better retrieval quality — Spring
   AI supports this via `VectorStore` with metadata filters
4. **Streaming is non-trivial**: WebSocket streaming with Spring AI requires a reactive pipeline; plan for Flux/reactive
   types in the response path
5. **Observability**: Spring AI has built-in observability via Micrometer — enabled from day one to track token usage,
   latency, and cache hit rates
