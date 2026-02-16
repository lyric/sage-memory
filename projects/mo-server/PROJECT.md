# Mo Server (mo-server)

## Overview

**Mo Server** is the web/mobile frontend and API server for the Mo GTD system. It provides a dashboard-driven UI for viewing and editing markdown files, chatting with Mo agents, and managing GTD workflows — all backed by markdown files with a path to database storage (Postgres/CouchDB). Designed for multi-user self-hosted use with TOTP-based 2FA.

- **Repo path**: `~/src/mo-server`
- **iCloud path**: `~/Library/Mobile Documents/com~apple~CloudDocs/src/mo-server`
- **Status**: Pre-code — architecture and UI prototyping phase

## Relationship to Mo Ecosystem

- **mo-core**: The agent runtime, gateway, channels, skills, plugins. Mo-server is a *client* of mo-core's gateway API.
- **gtd-mo-core**: GTD skills and plugin that manage the markdown files. Mo-server reads/writes the same files (or goes through an API layer).
- Mo-server gives users a web/mobile frontend to the system that Mo agents also read/write.

## Planned Architecture

```
┌──────────────────────────────────────────────┐
│              Mo Server                        │
├──────────────┬───────────────────────────────┤
│  Frontend    │  Backend API                  │
│  (Web/Expo)  │  (Node/Express or similar)    │
│              │                               │
│  Dashboard   │  Auth (JWT + TOTP)            │
│  Notes/Editor│  File API (md read/write)     │
│  Chat        │  Sync (conflict resolution)   │
│  Calendar    │  WebSocket (real-time)         │
│  Habits      │  Storage adapter (fs → db)    │
└──────┬───────┴───────────┬───────────────────┘
       │                   │
       ▼                   ▼
  Mobile (Expo/PWA)   Mo Gateway (mo-core)
                      GTD files (markdown)
```

## Planned Stack

| Component | Technology | Notes |
|-----------|------------|-------|
| Frontend | React (or framework Bolt chooses) | Dashboard + editor + chat |
| Mobile | Expo + PWA fallback | Offline-first capture, mini dashboard |
| Backend | Node.js | API server |
| Auth | JWT + TOTP (self-hosted) | No 3rd party dependency |
| Editor | WYSIWYG markdown | Notion-like with full md capability |
| Storage | Markdown files (phase 1) → Postgres/CouchDB (phase 2) | Adapter pattern for swappable storage |
| Sync | Conflict-aware, version tracking | Similar to Syncthing/LiveSync approach |
| Real-time | WebSocket | Live updates |

## Key Requirements

### Users & Auth
- Multi-user (Lyric + Kohan initially, then family/testers)
- Separate Mo systems per user (not team/shared)
- TOTP 2FA (authenticator app)
- JWT sessions
- Web security best practices

### Dashboard
- Driven by markdown files and frontmatter tags
- GTD-aware: inbox, next actions, projects, contexts, horizons
- Easy input flow for quick capture
- Drag-and-drop for priority changes
- Habit tracking view
- Calendar/date view
- Daily dashboard summary

### Editor
- WYSIWYG markdown (Notion-like for general use)
- Full markdown editing for notes/advanced use
- Frontmatter-aware (type, status, context, project, due, etc.)

### Chat
- Talk to Mo system for reviews, queries, processing
- Connected to Mo gateway

### Sync & Conflict Handling
- Offline-first (especially mobile)
- Version history
- Merge handling (single user per file typical, but edits can be delayed)
- Surface conflicts when they arise, don't silently lose data
- NOT messing things up is a key priority

### Storage Flexibility
- Start with markdown files on filesystem
- Adapter/abstraction layer so storage can switch to Postgres or CouchDB later
- Schema changes should be easy

### Mobile
- Expo app with offline support
- PWA fallback
- Mobile-first for capture + daily dashboard
- Desktop-first for full editing/management

## GTD File Structure (from mo-core exploration)

```
gtd/
├── INDEX.md                  → System navigation + processing rules
├── _inbox/                   → Capture: everything lands here first
├── _next_actions/            → Actions by context (@work, @home, @calls, etc.)
├── projects/                 → Active / someday / archive
├── focus/                    → Horizons: purpose → goals → areas
├── agendas/                  → People/meeting agendas
├── calendar/                 → Date-specific items
└── reference/                → Non-actionable reference
```

Frontmatter conventions: type, status, context, project, due, updated

## Features (Active)

_(none yet — prototyping phase)_

## Decisions

_(tracked in `decisions/` as they arise)_

## Learnings

_(tracked in `learnings/` as they arise)_
