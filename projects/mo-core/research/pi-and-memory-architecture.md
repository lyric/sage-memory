# Pi Agent Framework & Memory Architecture

*Researched: 2026-02-17*

## Pi — The Agent Execution Engine

**Pi** is an external agent framework SDK (`@mariozechner/pi-agent-core` + `@mariozechner/pi-coding-agent`) that abstracts away differences between LLM providers. Mo-core runs it in **"Embedded Pi"** mode — directly in-process, not as a separate service.

### High-level flow

```
Incoming message (Telegram, Discord, etc.)
  → auto-reply / agent-runner
    → runEmbeddedPiAgent()
      → subscribeEmbeddedPiSession()  (streaming subscription)
        → SessionManager.run()        (pi-coding-agent)
          → agent.run()               (pi-agent-core)
            → model.call()            (vendor API)
```

### Key files (`src/agents/`)

| Files | Purpose |
|---|---|
| `pi-embedded.ts` | Public API — run, abort, subscribe |
| `pi-embedded-runner/run.ts` | Core async entry point |
| `pi-embedded-runner/attempt.ts` | Per-attempt execution with retry logic |
| `pi-embedded-subscribe.ts` | Stream event handling (text blocks, tool calls, reasoning) |
| `pi-tools.ts` | Tool definitions (bash, file I/O, messaging, sandbox) |
| `pi-tools.policy.ts` | Tool allowlisting per agent/group/sender |
| `pi-embedded-runner/google.ts` | Gemini-specific schema sanitization & turn ordering |
| `pi-embedded-runner/compact.ts` | Context window compaction when history overflows |

### What Pi provides

- **Model-agnostic streaming** — one interface for Claude, Gemini, OpenAI, Ollama, Bedrock
- **Session management** — persisted as `.jsonl` files under `~/.mo/agents/<agentId>/sessions/`
- **Tool execution** — framework handles tool call/result loops
- **Context management** — auto-compaction when context window fills up

### What Mo adds on top of Pi

- **Agent runner** (`src/auto-reply/reply/agent-runner.ts`) — orchestrates model selection, auth rotation, system prompt building, and block-streaming replies back to channels
- **Tool policy gating** — per-agent, per-group, per-sender allowlists
- **Error recovery** — auth rotation, rate-limit backoff, fallback models, reasoning downgrade
- **Diagnostics** — usage events piped to Langfuse/OTEL (including reasoning capture)
- **Multi-channel messaging tools** — Telegram, WhatsApp, Discord, Slack, Signal, etc.

---

## Memory Subsystem

Memory is **file-backed + SQLite-indexed** with hybrid search. No external vector DB — everything lives locally.

### Storage

- **`MEMORY.md`** — curated long-term memory (loaded into main sessions only)
- **`memory/YYYY-MM-DD.md`** — daily logs (append-only; reads today + yesterday at session start)
- **`memory/*.md`** — additional topic files
- **SQLite** at `~/.mo/memory/{agentId}.sqlite` — embedding cache, chunks, FTS5 full-text index, optional `sqlite-vec` vector table

### Core components (`src/memory/`)

| File | Role |
|---|---|
| `manager.ts` | `MemoryIndexManager` — syncs files, manages embeddings, orchestrates search |
| `manager-search.ts` | Vector (cosine similarity) + keyword (BM25 via FTS5) search |
| `hybrid.ts` | Merges results — default 0.7 vector / 0.3 keyword weighting |
| `internal.ts` | Chunks markdown into ~400-token pieces with 80-token overlap |
| `embeddings.ts` | Provider abstraction (OpenAI `text-embedding-3-small`, Gemini, or local llama) |
| `memory-schema.ts` | SQLite schema — `files`, `chunks`, `embedding_cache`, `chunks_fts`, `chunks_vec` |

### Agent-facing tools (`src/agents/tools/memory-tool.ts`)

- **`memory_search`** — semantic/hybrid search over indexed chunks (returns snippets, paths, scores)
- **`memory_get`** — read a specific memory file by path (scoped to `MEMORY.md`, `memory/`, and configured extra paths)

### Memory flush (`src/auto-reply/reply/agent-runner-memory.ts`)

Before context-window compaction, the agent runner silently injects a "flush" turn that reminds the model to write important context to `MEMORY.md` or daily logs. Prevents knowledge loss during compaction.

### Plugin layer

Memory tools are managed through **Mo's own plugin system**, not Pi's. The `memory-core` extension registers tools via `api.registerTool()`. Pi has no awareness of memory — it just sees `memory_search` and `memory_get` as opaque tools.

Two plugin implementations:
- **`memory-core`** (default) — SQLite-backed, hybrid vector+keyword search
- **`memory-lancedb`** (experimental) — LanceDB vector backend with OpenAI embeddings

### Key design insight

The agent is both the writer and reader of memory. It writes memories to disk as markdown files; the memory system indexes them and makes them searchable via embeddings. It's a self-managed RAG loop.
