# Reference Repositories

Repos with patterns worth reusing. Each entry has tags for automatic matching during feature work, plus key files/directories to look at.

To add a reference: "add ~/src/xyz as a reference for [what it's good for]"

---

## pi-mono

- **Path**: `~/src/other/upstream/pi-mono`
- **Tags**: agents, ai, llm, runtime, tools, tui, coding-agent, multi-provider, pi, upstream, typescript
- **Stack**: TypeScript / npm workspaces / biome
- **What**: Pi monorepo — unified multi-provider LLM API, agent runtime, coding agent CLI, TUI library. Mo's core `@mariozechner/pi-*` dependencies come from here.

**Key Files & Dirs**:
- `packages/ai/` — `@mariozechner/pi-ai`: Unified LLM API (OpenAI, Anthropic, Google, etc.)
- `packages/agent/` — `@mariozechner/pi-agent-core`: Agent runtime with tool calling and state management
- `packages/coding-agent/` — `@mariozechner/pi-coding-agent`: Interactive coding agent CLI (Pi itself)
- `packages/tui/` — `@mariozechner/pi-tui`: Terminal UI with differential rendering
- `packages/web-ui/` — `@mariozechner/pi-web-ui`: Web components for AI chat interfaces
- `packages/mom/` — `@mariozechner/pi-mom`: Slack bot delegating to Pi coding agent
- `packages/pods/` — `@mariozechner/pi-pods`: vLLM GPU pod management CLI
- `AGENTS.md` — Project rules for humans and agents
- `packages/coding-agent/examples/extensions/` — Extension examples (custom providers)

**Online Resources**:
- [Pi docs + package directory](https://shittycodingagent.ai/packages) — browse extensions, skills, themes; install from npm or git
- [Extensions docs](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/docs/extensions.md) — extension API, lifecycle events, custom tools, TUI rendering
- [Extension examples](https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent/examples/extensions) — todo, sandbox, plan-mode, permission-gate, doom-overlay, custom providers
- [awesome-pi-agent](https://github.com/qualisero/awesome-pi-agent) — community curated list of extensions, skills, hooks, tools
- [DeepWiki: pi-mono](https://deepwiki.com/badlogic/pi-mono) — AI-generated deep documentation of the codebase
- [Pi: The Minimal Agent Within OpenClaw](https://lucumr.pocoo.org/2026/1/31/pi/) — Armin Ronacher's writeup on Pi's architecture
- [Anatomy of Pi coding agent](https://medium.com/@shivam.agarwal.in/agentic-ai-pi-anatomy-of-a-minimal-coding-agent-powering-openclaw-5ecd4dd6b440) — deep dive on how Pi powers Mo/OpenClaw
- [hjanuschka/shitty-extensions](https://github.com/hjanuschka/shitty-extensions) — community extensions collection
- [Discord](https://discord.com/invite/3cU7Bz4UPx) — Pi community, package discovery

**Good for**: Understanding Mo's upstream agent runtime, LLM provider abstraction, tool-calling internals, TUI rendering, Pi extension system. Essential reference when debugging or extending pi-embedded-runner behavior in mo-core.

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

## siyuan

- **Path**: `~/src/other/examples/siyuan`
- **Tags**: notes, editor, markdown, wysiwyg, sync, self-hosted, sqlite, electron, go, typescript, plugins, offline-first
- **Stack**: Go 1.25 (backend/kernel) + TypeScript (frontend) / Electron / SQLite3 / Gin / Webpack / pnpm
- **What**: Open-source note-taking app — Markdown WYSIWYG, block-based, self-hosted, plugin system, encrypted sync

**Key Files & Dirs**:
- `kernel/` — Go backend: HTTP server (Gin), 47 API handler files, 65+ model files, SQLite storage
- `kernel/api/router.go` — 300+ API endpoint registrations
- `kernel/model/` — Core business logic including sync, repo, conflict handling
- `kernel/server/serve.go` — HTTP server with middleware
- `app/src/protyle/` — WYSIWYG editor (Lute engine + custom Protyle UI)
- `app/src/plugin/` — Plugin system (Petal API, JS/TS runtime loading)
- `app/src/mobile/` — Mobile-specific UI
- `app/electron/main.js` — Electron main process

**Architecture Notes**:
- Desktop-first: Electron wraps local Go HTTP server (port 6806)
- Frontend is a web UI connecting to local REST API
- Mobile apps (separate repos) embed same Go kernel via CGO/gomobile
- 3 SQLite databases: main (blocks/notebooks), history (versions/undo), asset content (FTS)
- Sync via Dejavu library: encrypted, supports official cloud, S3/MinIO, WebDAV, local dir
- Single primary user + admin role (not true multi-user — all sessions share one workspace)
- Docker self-hosting available but browser-only access

**Good for**: Understanding a mature Go+TypeScript note app architecture, SQLite-based storage patterns, Markdown WYSIWYG editor implementation (Lute/Protyle), plugin system design, encrypted sync protocol, self-hosted deployment

---

## affine

- **Path**: `~/src/other/examples/affine`
- **Tags**: notes, editor, blocks, notion, collaboration, crdt, yjs, react, nestjs, graphql, postgres, self-hosted, mobile, capacitor, offline-first, typescript, rust
- **Stack**: TypeScript + Rust / React 19 / NestJS / GraphQL / PostgreSQL / Prisma / Yjs (CRDT) / Vite / Electron / Capacitor / Yarn 4
- **What**: Open-source Notion+Obsidian hybrid — block editor, canvas, real-time collaboration, CRDT-based, self-hosted

**Key Files & Dirs**:
- `blocksuite/` — Custom collaborative editor framework (CRDT-based, Lit web components)
- `blocksuite/framework/store/` — Document storage & CRDT
- `blocksuite/framework/sync/` — Synchronization protocol
- `packages/backend/server/` — NestJS GraphQL API server
- `packages/backend/server/schema.prisma` — Full database schema (~2789 lines)
- `packages/backend/server/src/core/auth/` — Auth (JWT, OAuth, magic links)
- `packages/backend/server/src/core/sync/gateway.ts` — WebSocket real-time sync
- `packages/backend/server/src/core/storage/` — S3-compatible blob storage
- `packages/frontend/core/` — Main React application (modules, components)
- `packages/frontend/apps/web/` — Web app entry
- `packages/frontend/apps/electron/` — Desktop app
- `packages/frontend/apps/ios/` — iOS (Capacitor)
- `packages/frontend/apps/android/` — Android (Capacitor)
- `packages/common/nbstore/` — Local storage abstraction (IndexedDB/SQLite)
- `packages/common/y-octo/` — Rust-native CRDT implementation

**Architecture Notes**:
- True multi-user with workspace-level roles (Owner, Manager, Member, Guest)
- CRDT (Yjs) for conflict-free real-time collaboration
- Local-first: IndexedDB (web) or SQLite (desktop) with cloud sync
- Capacitor for iOS/Android (same React codebase)
- PostgreSQL + Prisma for server, Redis for sessions/queue, S3 for blobs
- Docker self-hosting with compose
- Jotai for client state management
- Plugin system in development (BlockSuite extension loader)

**Good for**: Full-stack collaborative editor architecture, CRDT/Yjs patterns, Capacitor mobile deployment, NestJS+GraphQL server patterns, Prisma schema design, multi-user workspace permissions, self-hosted deployment, local-first with cloud sync

---

## obsidian-livesync

- **Path**: `~/src/other/examples/obsidian-livesync`
- **Tags**: sync, couchdb, pouchdb, webrtc, p2p, conflict-resolution, obsidian, typescript, svelte, offline-first, replication
- **Stack**: TypeScript / Svelte 5 / PouchDB / CouchDB / esbuild
- **What**: Obsidian plugin for real-time vault sync via CouchDB, S3/R2, or WebRTC P2P

**Key Files & Dirs**:
- `src/main.ts` — Plugin entry point
- `src/modules/core/ModuleReplicator.ts` — Replication orchestration
- `src/modules/core/ModuleReplicatorCouchDB.ts` — CouchDB sync
- `src/modules/core/ModuleReplicatorMinIO.ts` — S3/R2 object storage sync
- `src/modules/core/ModuleReplicatorP2P.ts` — WebRTC peer-to-peer sync
- `src/modules/core/ReplicateResultProcessor.ts` — Incoming change processing
- `src/modules/coreFeatures/ModuleConflictResolver.ts` — Conflict handling
- `src/modules/coreObsidian/storageLib/StorageEventManager.ts` — Vault file watching
- `docs/setup_own_server.md` — Self-hosted CouchDB setup

**Architecture Notes**:
- PouchDB ↔ Obsidian vault synchronizer
- Three sync backends: CouchDB (primary), S3/MinIO/R2, WebRTC P2P
- Conflict resolution: three-way merge via diff-match-patch for text, timestamp-based for binary
- No REST API exposed — interacts via PouchDB replication protocol or S3 API
- Chunk-based storage for large files
- Path obfuscation support for privacy
- Self-hosting: CouchDB in Docker, or serverless via object storage

**Good for**: PouchDB/CouchDB replication patterns, conflict resolution algorithms (diff-match-patch three-way merge), multiple sync backend strategies, offline-first sync architecture, P2P WebRTC sync

---

## obsidian-remote

- **Path**: `~/src/other/examples/obsidian-remote`
- **Tags**: obsidian, docker, remote-desktop, vnc, self-hosted, kasmvnc
- **Stack**: Docker / KasmVNC / Debian / Openbox / Bash
- **What**: Runs Obsidian desktop app in Docker, accessible via web browser (VNC-based remote desktop)

**Key Files & Dirs**:
- `Dockerfile` — Multi-stage build, downloads Obsidian AppImage
- `root/defaults/startwm.sh` — Starts Openbox + PulseAudio
- `root/defaults/autostart` — Launches Obsidian with --no-sandbox
- `root/etc/cont-init.d/50-config` — Password, timezone, permissions setup

**Architecture Notes**:
- Not a sync solution — literally runs Obsidian in a container and streams the UI via VNC
- KasmVNC provides browser-based access (port 8080/8443)
- Vault files mounted as Docker volumes at /vaults
- Optional HTTP Basic Auth
- Multi-arch (amd64 + arm64)
- Good for accessing Obsidian from devices that can't run it natively

**Good for**: Docker-based desktop app hosting pattern, KasmVNC setup, remote access to local-first apps. Limited relevance for mo-server (different approach to the problem)

---

## syncthing

- **Path**: `~/src/other/examples/syncthing`
- **Tags**: sync, p2p, decentralized, conflict-resolution, versioning, vector-clocks, go, protocol-buffers, file-sync
- **Stack**: Go 1.25 / Protocol Buffers / LZ4 / SQLite
- **What**: Decentralized P2P file sync with vector-clock conflict detection and block-level transfer

**Key Files & Dirs**:
- `lib/protocol/protocol.go` — BEP connection handling, message I/O
- `lib/protocol/vector.go` — Vector clock logic for conflict detection
- `lib/model/model.go` — Main sync state machine (~114KB)
- `lib/model/folder_sendrecv.go` — Bidirectional sync logic (71KB, handles conflicts)
- `lib/scanner/walk.go` — Filesystem scanning, block hashing (SHA256)
- `lib/versioner/` — File versioning strategies (simple, trashcan, staggered)
- `proto/bep/bep.proto` — Protocol Buffer definitions (BEP v1)
- `lib/connections/` — Network connection management
- `lib/discover/` — Peer discovery

**Architecture Notes**:
- BEP (Block Exchange Protocol): Index + Request/Response, block-level granularity
- Vector clocks for causality tracking — each device has ShortID + counter
- Conflict detection: if neither version dominates → conflict
- Conflict resolution: (1) modification time (newest wins), (2) device ID as tiebreaker
- Conflicts preserved as `.sync-conflict-YYYYMMDD-HHMMSS-deviceid.ext` — never destructive
- Folder types: send-receive, send-only, receive-only, receive-encrypted
- Versioning: pluggable strategies archive old versions before overwrite
- Continuous sync via filesystem watcher + incremental IndexUpdate messages

**Good for**: Vector clock conflict detection/resolution patterns, block-level sync protocol design, versioning strategies, decentralized P2P architecture, non-destructive conflict handling. Key reference for mo-server's sync/conflict approach.

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
