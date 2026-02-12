# Reference Repositories

Repos with patterns worth reusing. Each entry has tags for automatic matching during feature work, plus key files/directories to look at.

To add a reference: "add ~/src/xyz as a reference for [what it's good for]"

---

## clawdbot

- **Path**: `~/src/other/examples/clawdbot`
- **Tags**: agents, chat, messaging, multi-channel, voice, canvas, gateway, bot, typescript, whatsapp, telegram, slack, discord, signal, imessage, skills, plugins
- **Stack**: TypeScript / Node.js / pnpm
- **What**: Pre-rebrand snapshot of Mo (then "Clawdbot") — same architecture, same codebase lineage

**Key Files & Dirs**:
- `src/agents/` — Pi embedded runner, tools, skills, system prompt (same structure as mo-core)
- `src/auto-reply/` — Inbound message handling, triggers, directives
- `src/gateway/` — WebSocket + HTTP server, channel orchestration
- `extensions/` — Channel plugins, diagnostics
- `skills/` — Agent skills collection

**Good for**: Diffing against mo-core to understand what changed during rebrand; historical reference for architecture decisions

---

## upstream-openclaw

- **Path**: `~/src/other/upstream/openclaw`
- **Tags**: agents, gateway, legacy, reference, typescript
- **Stack**: TypeScript / Node.js
- **What**: Upstream OpenClaw repo (pre-Mo fork point) with `openclaw.mjs` entrypoint

**Key Files & Dirs**:
- `openclaw.mjs` — Original entrypoint (now `mo.mjs` in mo-core)
- Same directory structure as mo-core but older

**Good for**: Understanding upstream changes, comparing divergence from fork, rebrand reference

---

## pi-messenger

- **Path**: `~/src/other/examples/pi-messenger`
- **Tags**: agents, multi-agent, coordination, pi, skills, file-based, crew, planning, task-management
- **Stack**: TypeScript / npm
- **What**: File-based multi-agent coordination layer for Pi agents — join, reserve files, send messages, plan/work/review crews

**Key Files & Dirs**:
- `ARCHITECTURE.md` — Crew orchestration flow (PRD → plan → tasks → work → done)
- `crew/` — Crew agents, handlers, state management, ID allocation
- `crew/agents/` — Agent definitions (planner, worker, reviewer)
- `skills/pi-messenger-crew/` — Skill for crew orchestration
- `config.ts` — Configuration system
- `handlers.ts` — Message and action handlers
- `store.ts` — File-based state persistence

**Good for**: Multi-agent coordination patterns, file-based messaging, crew/task orchestration, Pi extension system, skill structure

---

## mcp-agent-mail

- **Path**: `~/src/other/examples/mcp_agent_mail`
- **Tags**: mcp, multi-agent, coordination, fastmcp, git, sqlite, leases, identity, python
- **Stack**: Python / FastMCP / SQLite / Git
- **What**: Mail-like coordination for coding agents — identities, inbox/outbox, file reservations, searchable history

**Key Files & Dirs**:
- `src/mcp_agent_mail/` — Core server implementation
- `project_idea_and_guide.md` — Detailed design doc (idea → plan → swarm workflow)
- `AGENTS.md` — Multi-agent coordination rules
- `scripts/` — Install, run, deployment helpers
- `tests/` — Test suite
- `web/` — Web UI

**Good for**: MCP server patterns, multi-agent coordination, file lease/reservation system, agent identity management, Git-backed audit trails

---

## swift-scribe

- **Path**: `~/src/other/examples/ios/swift-scribe`
- **Tags**: ios, macos, voice, transcription, speech, diarization, swiftui, on-device-ai, foundation-models
- **Stack**: Swift 6.2+ / SwiftUI / SpeechTranscriber / FluidAudio / Foundation Model Framework
- **What**: Real-time voice transcription with speaker diarization and on-device AI for iOS 26 / macOS 26+

**Key Files & Dirs**:
- `Scribe/ScribeApp.swift` — App entry point
- `Scribe/Audio/` — Audio capture and processing
- `Scribe/Transcription/` — Speech-to-text pipeline, SpeechTranscriber integration
- `Scribe/Models/` — Data models
- `Scribe/Views/` — SwiftUI views
- `Configuration/` — Build configuration
- `CLAUDE.md` — AI context for the project

**Good for**: iOS 26/macOS 26 voice APIs (SpeechTranscriber, FluidAudio), on-device Foundation Models, speaker diarization, SwiftUI audio app patterns

---

## anthropics-skills

- **Path**: `~/src/other/examples/skills/anthropics-skils`
- **Tags**: skills, templates, reference, claude, spec, pdf, xlsx, pptx, mcp, webapp, canvas
- **Stack**: Mixed (skill definitions)
- **What**: Anthropic's official skill templates and spec for Claude agent skills

**Key Files & Dirs**:
- `spec/agent-skills-spec.md` — Official skill specification
- `skills/` — Reference skill implementations (pdf, xlsx, pptx, mcp-builder, skill-creator, webapp-testing, canvas-design, frontend-design, etc.)
- `template/` — Skill template scaffolding

**Good for**: Skill specification reference, skill structure patterns, official Anthropic skill templates

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

## ~/src/other — Additional Repos (scan on demand)

Other repos available for reference when relevant features come up. These may change; scan fresh when needed.

| Repo | What | Tags |
|------|------|------|
| `examples/Fabric` | Go AI framework with CLI + prompt patterns | ai, cli, go, prompts |
| `examples/LibreChat` | Self-hosted multi-model chat interface (React/Node) | chat, ui, multi-model, self-hosted |
| `examples/smolagents` | HuggingFace lightweight agent framework, sandboxed execution | agents, python, sandbox, code-agents |
| `examples/open_deep_research` | LangGraph deep research agent | agents, research, langgraph, python |
| `examples/local-agent` | Fully local AI agents (Ollama/LangChain/ChromaDB) | agents, local, rag, python |
| `examples/nanobot` | Ultra-lightweight Python bot (~4K lines, multi-provider) | bot, python, lightweight |
| `examples/outline` | Collaborative knowledge base (React/Node/Postgres) | docs, knowledge-base, collaboration |
| `examples/vibe-kanban` | Kanban board (Rust backend, React frontend) | productivity, kanban, rust |
| `examples/gtd` | GTD methodology with Claude Code integration | productivity, gtd, agents |
| `examples/basic guard bot` | Python Telegram parental-monitoring bot | bot, telegram, python |
| `examples/ios/ios-memsieve-app` | iOS dictation with Whisper/GPT-4o | ios, voice, whisper |
| `examples/ios/LiquidGlassReference` | iOS 26 Liquid Glass SwiftUI reference | ios, swiftui, design-system |
| `examples/notes-app/Habit` | iOS habit tracker (SwiftUI/CoreData) | ios, productivity |
| `examples/notes-app/Hyprnote` | AI meeting notepad (TS/Rust, macOS, local LLMs) | notes, voice, macos |
| `examples/notes-app/meeting-minutes` | Meeting assistant (Rust + Whisper) | voice, transcription, rust |
| `examples/notes-app/Whisperboard` | iOS Whisper transcription app | ios, voice, whisper |
| `examples/skills/awesome-claude-skills` | Curated Claude skills collection | skills, reference |
| `examples/skills/claude-code-showcase` | Claude Code + MCP demos | showcase, mcp |

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
