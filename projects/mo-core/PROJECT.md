# Mo (mo-core)

## Overview

**Mo** is a personal AI assistant you run on your own devices. It answers on channels you already use (WhatsApp, Telegram, Slack, Discord, Google Chat, Signal, iMessage, Microsoft Teams, WebChat) plus extension channels (BlueBubbles, Matrix, Zalo, Nostr, Mattermost, LINE, Twitch, Nextcloud Talk, Tlon). It speaks and listens on macOS/iOS/Android, renders a live Canvas, and provides a full TUI. The Gateway is the control plane; the product is the assistant.

- **Repo**: https://github.com/mo/mo
- **Docs**: https://docs.mo.ai (Mintlify)
- **License**: MIT
- **Rebrand**: OpenClaw → Mo (in progress; Swift source dirs not renamed yet)

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      Gateway Server                      │
│  (WebSocket + HTTP, manages channels, agents, sessions) │
├──────────┬──────────┬──────────┬──────────┬─────────────┤
│ Channels │  Agents  │ Sessions │ Plugins  │   Config    │
│ (in/out) │ (Pi/LLM) │ (JSONL)  │ (jiti)   │  (mo.json)  │
└────┬─────┴────┬─────┴────┬─────┴────┬─────┴──────┬──────┘
     │          │          │          │            │
┌────▼────┐ ┌──▼───┐ ┌───▼───┐ ┌───▼────┐ ┌─────▼─────┐
│WhatsApp │ │Claude│ │ JSONL │ │Langfuse│ │  mo.json   │
│Telegram │ │OpenAI│ │ Store │ │  OTEL  │ │ ~/.mo/     │
│Slack    │ │Gemini│ │       │ │ Custom │ │            │
│Discord  │ │Ollama│ └───────┘ └────────┘ └───────────┘
│Signal   │ │Bedrock│
│iMessage │ └──────┘
│MS Teams │
│WebChat  │
│LINE     │
│+ exts   │
└─────────┘
```

### Core Flow

1. **Inbound**: Message arrives on a channel → routing/allowlist → session lookup → agent runner
2. **Agent Runner** (`pi-embedded-runner`): Builds system prompt, loads tools/skills/bootstrap files, runs Pi agent (Claude/OpenAI/etc.), streams responses
3. **Outbound**: Agent reply → chunking/splitting → delivery back to originating channel (or cross-channel)
4. **Gateway**: WebSocket server orchestrating channels, agents, sessions, plugins, cron, hooks, and native app communication

## Stack

| Component | Technology |
|-----------|------------|
| Language | TypeScript (ESM, strict) |
| Runtime | Node 22+ (Bun for dev/scripts) |
| Package Manager | pnpm (bun also supported) |
| Build | tsc → `dist/` (noEmit in tsconfig; use `--noEmit false` to emit) |
| Linting | oxlint (type-aware) |
| Formatting | oxfmt |
| Testing | Vitest (V8 coverage, 70% thresholds) |
| AI Agent | @mariozechner/pi-agent-core (embedded Pi runner) |
| Web Framework | Express 5 (gateway HTTP) |
| WebSocket | ws |
| WhatsApp | @whiskeysockets/baileys |
| Telegram | grammy |
| Discord | @buape/carbon + discord-api-types |
| Slack | @slack/bolt |
| CLI | Commander |
| macOS App | SwiftUI (Observation framework) |
| iOS App | SwiftUI + xcodegen |
| Android App | Kotlin + Gradle |
| UI (control) | Lit (web components, rolldown bundled) |
| Media | sharp (images), pdfjs-dist, file-type |
| TTS | node-edge-tts, sherpa-onnx |
| Database | SQLite (sqlite-vec for vector search) |
| Config | JSON5 (mo.json), Zod + TypeBox schemas |

## Directory Map

### Top-Level

| Directory | Purpose |
|-----------|---------|
| `src/` | TypeScript source (core) |
| `dist/` | Compiled output (tsc) |
| `apps/` | Native apps (macos, ios, android, shared MoKit) |
| `extensions/` | Plugin packages (channel extensions, diagnostics, memory, LLM tasks) |
| `skills/` | Agent skills (1password, github, coding-agent, mind-sweep, voice-call, etc.) |
| `scripts/` | Build, release, test, packaging scripts |
| `docs/` | Mintlify documentation site |
| `ui/` | Lit web-component control UI |
| `packages/` | Shared workspace packages |
| `patches/` | pnpm patches |
| `test/` | Test fixtures/docker |
| `vendor/` | Vendored dependencies |

### `src/` — Core Source

| Directory | Purpose |
|-----------|---------|
| `src/agents/` | Agent runner: Pi embedded runner, tools, skills, sandbox, model selection, auth profiles, system prompt, subagents |
| `src/auto-reply/` | Inbound message handling: triggers, directives, reply queue, block streaming, skill commands |
| `src/gateway/` | Gateway server: WebSocket, HTTP (OpenAI-compat + Open Responses), channels, chat, plugins, cron, hooks, discovery (mDNS) |
| `src/cli/` | CLI (Commander): all `mo <command>` subcommands |
| `src/commands/` | Command implementations: onboard, doctor, status, agent, channels, models, config, health, sessions, sandbox |
| `src/config/` | Config system: schema (Zod + TypeBox), paths, env vars, validation, legacy migration, sessions config |
| `src/channels/` | Channel abstraction: registry, allowlists, typing, ack reactions, command gating, mention gating |
| `src/routing/` | Message routing: session key resolution, route bindings |
| `src/plugins/` | Plugin system: loader (jiti), registry, hooks, slots, discovery, manifest, install, tools |
| `src/media/` | Media pipeline: fetch, parse, store, MIME detection, image ops, audio tags, server |
| `src/sessions/` | Session management: transcript events, level overrides, model overrides, send policy |
| `src/telegram/` | Telegram channel implementation (grammy) |
| `src/discord/` | Discord channel implementation (Carbon) |
| `src/slack/` | Slack channel implementation (@slack/bolt) |
| `src/signal/` | Signal channel implementation |
| `src/imessage/` | iMessage channel implementation |
| `src/web/` | WhatsApp Web channel (Baileys) |
| `src/line/` | LINE channel implementation |
| `src/providers/` | Auth providers: GitHub Copilot, Google, Qwen portal |
| `src/infra/` | Infrastructure utilities |
| `src/security/` | Security checks and policies |
| `src/tui/` | Terminal UI |
| `src/tts/` | Text-to-speech |
| `src/memory/` | Agent memory (vector search) |
| `src/browser/` | Browser automation (Playwright) |
| `src/canvas-host/` | Canvas rendering host (A2UI) |
| `src/hooks/` | Lifecycle hooks system |
| `src/cron/` | Scheduled tasks |
| `src/daemon/` | Gateway daemon (launchd/systemd) |
| `src/macos/` | macOS-specific integration |
| `src/terminal/` | Terminal utilities: palette, table formatting |
| `src/logging/` | Structured logging (tslog) |
| `src/shared/` | Shared utilities |
| `src/utils/` | General utilities |
| `src/markdown/` | Markdown processing |
| `src/acp/` | Agent Client Protocol |
| `src/plugin-sdk/` | Plugin SDK (exported as `mo/plugin-sdk`) |

### `extensions/` — Plugin Packages

Channel extensions: `bluebubbles`, `discord`, `googlechat`, `imessage`, `line`, `matrix`, `mattermost`, `msteams`, `nostr`, `nextcloud-talk`, `signal`, `slack`, `telegram`, `tlon`, `twitch`, `voice-call`, `whatsapp`, `zalo`, `zalouser`

Diagnostics: `diagnostics-langfuse`, `diagnostics-otel`

AI/LLM: `llm-task`, `google-antigravity-auth`, `google-gemini-cli-auth`, `minimax-portal-auth`, `qwen-portal-auth`, `copilot-proxy`

Other: `lobster` (CLI palette), `memory-core`, `memory-lancedb`, `open-prose`

### `apps/` — Native Applications

| Directory | Purpose |
|-----------|---------|
| `apps/macos/` | SwiftUI macOS menubar app (gateway host, voice wake) |
| `apps/ios/` | SwiftUI iOS app (xcodegen) |
| `apps/android/` | Kotlin Android app (Gradle) |
| `apps/shared/` | Shared MoKit (Swift package, cross-platform) |

## Key Entry Points

| File | Purpose |
|------|---------|
| `mo.mjs` | Node entrypoint (loads `dist/entry.js`) |
| `src/entry.ts` | Main entry: CLI bootstrap |
| `src/cli/program.ts` | Commander program definition |
| `src/gateway/server.ts` | Gateway server entry |
| `src/agents/pi-embedded-runner.ts` | Core agent runner |
| `src/agents/system-prompt.ts` | System prompt builder |
| `src/auto-reply/reply.ts` | Reply orchestration |
| `src/config/config.ts` | Config loader/validator |
| `src/config/schema.ts` | Config schema (Zod + TypeBox) |
| `src/plugins/loader.ts` | Plugin loader (jiti) |
| `src/channels/registry.ts` | Channel registry |

## Key Patterns

- **Config**: `~/.mo/mo.json` (JSON5), validated by Zod schemas, with legacy migration support
- **Sessions**: JSONL transcript files under `~/.mo/agents/<agentId>/sessions/`
- **Plugin loading**: jiti-based dynamic loading; plugins get `MoPluginServiceContext` (critical: jiti creates separate module instances, so context must be passed explicitly, not imported)
- **Tools**: Pi SDK tool definitions adapted via `pi-tool-definition-adapter`; skills loaded from `skills/` directories
- **Multi-agent**: Subagent spawning with announce queue, per-agent config, sandbox isolation
- **Auth profiles**: Rotating OAuth/API-key profiles with cooldown and failover
- **Streaming**: Block-based reply streaming with paragraph-aware chunking
- **Diagnostics**: Event-based (`emitDiagnosticEvent`), consumed by Langfuse/OTEL plugins via `ctx.onDiagnosticEvent`

## Build & Dev Commands

```bash
pnpm install                    # Install deps
pnpm build                      # Type-check + compile + bundle A2UI
pnpm exec tsc -p tsconfig.json --noEmit false  # Compile TS only (fast)
pnpm lint                       # oxlint (type-aware)
pnpm format                     # oxfmt
pnpm test                       # Vitest (parallel)
pnpm test:coverage              # With V8 coverage
pnpm mo <command>               # Run CLI in dev
node mo.mjs gateway run ...     # Run gateway from dist/
```

## Gotchas

- **noEmit default**: `tsconfig.json` has `noEmit: true`. Must pass `--noEmit false` to actually emit to `dist/`.
- **A2UI bundle**: `pnpm build` fails without A2UI assets. Use `pnpm exec tsc` for TS-only compilation.
- **jiti dual-instance**: Plugins loaded via jiti get separate module instances. Never import shared singletons (like diagnostic event listeners) directly — pass through plugin context instead.
- **iCloud paths**: Paths with `~` or spaces cause Node/tsx issues. Use the `~/src/mo-core` symlink or `run-node.mjs`.
- **Rebrand in progress**: OpenClaw → Mo. Swift source dirs (`apps/macos/Sources/OpenClaw*`) not renamed yet.
- **Carbon**: Never update the `@buape/carbon` dependency (pinned at 0.14.0).
- **Patched deps**: Dependencies in `pnpm.patchedDependencies` must use exact versions (no `^`/`~`).
- **Gateway dev**: Must recompile (`--noEmit false`) before restarting gateway. The gateway runs from `dist/`.

## Features (Active)

_(none currently — ready for new feature work)_

## Decisions

_(tracked in `decisions/` as they arise)_

## Learnings

_(tracked in `learnings/` as they arise)_
