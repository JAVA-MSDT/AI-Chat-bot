# AI ChatBot Engine

## About

A reusable, domain-agnostic AI chatbot engine built on Java 21 + Spring Boot + Spring AI. Drop it into any project,
point it at your data, configure your LLM of choice, and have a production-ready chatbot without rebuilding AI
infrastructure from scratch.

## Features

- **Pluggable LLM support** — swap between Ollama, OpenAI, and Claude via config only
- **RAG pipeline** — semantic search over your domain knowledge using a vector store
- **Flexible data ingestion** — ingest from databases, REST APIs, or documents (PDF, TXT, Markdown)
- **Persistent chat history** — user sessions and conversation context survive restarts
- **Domain guardrails** — restrict responses to your configured knowledge base
- **Streaming responses** — real-time output via WebSocket
- **Self-serve configuration** — configure and re-train through an admin API endpoint
- **Multiple response formats** — plain text, Markdown, or JSON

## Tech Stack

| Layer          | Technology               |
|----------------|--------------------------|
| Language       | Java 21                  |
| Framework      | Spring Boot 3.x          |
| AI Abstraction | Spring AI                |
| Vector Store   | pgvector (PostgreSQL)    |
| Local LLM      | Ollama                   |
| Cloud LLMs     | OpenAI, Anthropic Claude |
| Persistence    | PostgreSQL + JPA         |
| API            | REST + WebSocket         |

## Project Structure

```
ai-chatbot/
├── core/               # Domain logic: chat service, RAG pipeline, ports (interfaces)
├── adapters/           # LLM adapters: Ollama, OpenAI, Claude
├── ingestion/          # Data ingestors: documents, database, REST API
├── api/                # REST controllers, WebSocket handlers
└── src/main/resources/
    └── application.yml # All configuration
```

## Getting Started

**Prerequisites:** Java 21, Docker (for PostgreSQL + pgvector + Ollama)

```bash
# 1. Start infrastructure
docker compose up -d

# 2. Build and run
./mvnw spring-boot:run

# 3. Trigger initial data ingestion
curl -X POST http://localhost:8080/api/admin/ingest

# 4. Chat
curl -X POST http://localhost:8080/api/chat \
  -H "Content-Type: application/json" \
  -d '{"sessionId": "user-1", "message": "Your question here"}'
```

## Configuration

All configuration is externalized in `application.yml`. No hardcoded values.

```yaml
chatbot:
  llm:
    provider: ollama          # ollama | openai | claude
    model: llama3.2
  domain:
    scope: "Answer only questions related to ..."
  ingestion:
    chunk-size: 512
    chunk-overlap: 64
```

LLM API keys are supplied via environment variables:

```bash
OPENAI_API_KEY=...
ANTHROPIC_API_KEY=...
```

## License

MIT

## Author

Ahmed Samy
