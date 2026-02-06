# Reference Repositories

Repos with patterns worth reusing. Each entry has tags for automatic matching during feature work, plus key files/directories to look at.

To add a reference: "add ~/src/xyz as a reference for [what it's good for]"

---

## bridge-cli

- **Path**: `~/src/crunchy/bridge-cli`
- **Tags**: cli, command-line, shell, terminal, completions
- **Stack**: Crystal
- **What**: CLI tool (`cb`) for managing Crunchy Bridge Postgres clusters

**Key Files & Dirs**:
- `src/cb/` — Command-per-file pattern, each command is a standalone module
- `src/cb/completion/` — Fish shell completion generation
- `shard.yml` — Crystal dependency manifest
- `flake.nix` — Nix build definition

**Good for**: CLI architecture, command routing pattern, shell completions, Nix packaging

---

## bridge-platform

- **Path**: `~/src/crunchy/bridge-platform`
- **Tags**: api, http, rest, go, backend, sqlc, heroku
- **Stack**: Go
- **What**: Core HTTP API powering Crunchy Bridge

**Key Files & Dirs**:
- `go.mod` — Go module definition with ~66 packages
- Top-level packages follow Go convention: one package per concern
- Uses sqlc for type-safe SQL generation
- Heroku deployment via `Procfile`
- direnv for environment management

**Good for**: Go API structure, sqlc patterns, Heroku deployment, large Go codebase organization

---

## bridge-raven

- **Path**: `~/src/crunchy/bridge-raven`
- **Tags**: dashboard, admin, remix, react, typescript, tailwind, charts, frontend
- **Stack**: TypeScript / Remix / React / Tailwind
- **What**: Internal admin dashboard for Postgres cluster monitoring

**Key Files & Dirs**:
- `app/routes/` — Remix file-based routing
- `app/components/` — React components with Tailwind
- `app/utils/` — API client (Axios), auth, helpers
- Uses Recharts for data visualization
- Redis sessions, Node 22

**Good for**: Remix app structure, TypeScript dashboard, Tailwind components, data visualization with Recharts

---

## humanlayer

- **Path**: `~/src/lyric/humanlayer`
- **Tags**: ai, agents, context-engineering, memory, handoffs, mcp, orchestration
- **Stack**: TypeScript (hlyr) / Go (hld) / React (wui)
- **What**: AI coding agent orchestration platform (CodeLayer)

**Key Files & Dirs**:
- `hlyr/THOUGHTS.md` — Full docs on the Thoughts persistence system
- `hlyr/src/thoughtsConfig.ts` — Thoughts config, profiles, symlink management
- `hlyr/src/commands/thoughts/` — CLI commands for thought management
- `.claude/agents/` — Specialized sub-agent definitions (codebase-locator, analyzer, pattern-finder, thoughts-locator, web-researcher)
- `.claude/commands/` — Workflow commands (research_codebase, create_plan, implement_plan, create_handoff, resume_handoff)
- `CLAUDE.md` — Top-level project context file

**Good for**: Context persistence (thoughts system), agent orchestration, research/plan/handoff workflows, sub-agent patterns, MCP integration

---

## mcp-snowflake-service

- **Path**: `~/src/mcp/mcp-snowflake-service`
- **Tags**: mcp, model-context-protocol, snowflake, python, server
- **Stack**: Python
- **What**: MCP server giving Claude access to Snowflake databases

**Key Files & Dirs**:
- Single-file server pattern
- MCP protocol implementation
- SQL execution, connection management

**Good for**: MCP server implementation in Python, database integration via MCP

---

## zendesk-ticket-analysis

- **Path**: `~/src/zendesk-ticket-analysis`
- **Tags**: ai, llm, analysis, streamlit, snowflake, pipeline, python
- **Stack**: Python / Streamlit / Snowflake
- **What**: AI-powered Zendesk ticket categorization and analysis platform

**Key Files & Dirs**:
- Streamlit dashboard components
- LLM analysis pipelines (categorization, team routing, automation detection)
- Bulk processing workflows
- Snowflake-native app deployment

**Good for**: AI/LLM analysis pipelines, Streamlit dashboards, Snowflake deployment, bulk data processing

---

<!--
To add a new reference, append a section following this format:

## name

- **Path**: `~/src/path/to/repo`
- **Tags**: tag1, tag2, tag3
- **Stack**: Language / Framework
- **What**: One-line description

**Key Files & Dirs**:
- `path/to/important/file` — Why it matters
- `path/to/key/dir/` — What's in here

**Good for**: When to look at this repo
-->
