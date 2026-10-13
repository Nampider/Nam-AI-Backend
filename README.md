# Nam AI Backend

Python agent API for Nam AI: FastAPI + LangChain/LangGraph. It owns the database, the agents, the MCP registry, human-in-the-loop approvals and guardrails. The web app lives in the separate `Nam-AI` repo; the real tax RAG server is `Nam-AI-RAG-MCP`.

## Layout

- repo root: the agent API (`pyproject.toml`, `src/nam_agent`, `migrations`, `tests`, `evals`), added from plan Step 1.3
- `infra/`: Docker Compose (Postgres, stub RAG MCP server)

## Prerequisites

Python 3.12, [uv](https://docs.astral.sh/uv/), Docker Desktop, [Ollama](https://ollama.com) with `qwen3:14b` and `qwen3:8b`.

## Run locally

Filled in as build steps land. Copy `.env.example` to `.env` (and `infra/.env`) and fill in real values. Never commit them.
