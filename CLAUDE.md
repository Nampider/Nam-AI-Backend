# Nam AI Backend — instructions for coding agents

This repo, `Nam-AI-Backend`, is the **Python agent backend** for Nam AI, a chat application for accountants who ask tax questions and get cited answers from a separate tax RAG service (an MCP server). The frontend is the sibling repo `Nam-AI`; the real RAG server is `Nam-AI-RAG-MCP` (do not touch it until the RAG steps).

## Read first, every session

The design and plan live in the frontend repo:

1. `../Nam-AI/CLAUDE.md`
2. `../Nam-AI/docs/ARCHITECTURE.md`: what we are building and why (source of truth for design).
3. `../Nam-AI/docs/IMPLEMENTATION_PLAN.md`: ordered build steps, contracts (appendices), the "Repositories" section and the progress log. Follow its "Rules for the coding agent".

Work on the **first unchecked step** in Part 1 of the plan; do backend steps here. Do one step per turn unless the owner says "keep going".

## Layout (this repo)

- Repo root = Python 3.12 + FastAPI + LangChain/LangGraph app (`pyproject.toml`, `src/nam_agent/`, `migrations/`, `tests/`, `evals/`). Owns the database, the agents, the MCP registry, HITL and guardrails.
- `infra/`: Docker Compose (Postgres, stub RAG MCP server, optional app containers).

## Models

Local development uses **Ollama** models running natively on the Mac (`COORDINATOR_MODEL=ollama:…`, `HELPER_MODEL=ollama:…`). Cloud models (Claude) replace them at plan Step 4.10 by changing env vars only. Only `agents/models.py` may know about providers. Always set Ollama's context window (`OLLAMA_NUM_CTX`), and never stream or save model reasoning text.

## Commands (once the relevant step has landed)

```bash
# local models (Ollama app running natively, not in Docker)
ollama list
ollama ps

# infrastructure
docker compose -f infra/docker-compose.yml up -d postgres stub-rag-mcp

# agent API
uv run alembic upgrade head
uv run python -m nam_agent.db.seed
uv run uvicorn nam_agent.main:app --reload --port 8000
uv run pytest            # add -m "not llm and not integration" for fast runs
uv run ruff check . && uv run mypy src
```

## Non-negotiables

- **Never commit or push without the owner's explicit permission.** Leave changes uncommitted and suggest the commit message.
- Access tokens and secrets stay server-side and out of git.
- Every agent API route checks the token and permission, and scopes data to the current user (404 for other users' chats).
- Risk tiers decide what pauses for approval (`tool_policies`), never the LLM. Unknown tools default to Tier 2.
- Tax answers come only from retrieved evidence, cited with `[n]` markers.
- Identifiers (SSN, ITIN, EIN, account numbers) are masked before saving or sending to any model.
- Check current library docs before coding against LangChain/LangGraph or MCP; record differences from the plan in the progress log.

## The owner

Chris is an experienced Java/Spring developer, newer to Python, LangChain and agent systems. When reporting back, explain in plain language, define new terms briefly, and give exact commands to try things.
