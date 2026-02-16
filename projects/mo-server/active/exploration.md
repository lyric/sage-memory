# Exploration: Mo Server — Web/Mobile UI

**Feature:** Web and mobile frontend for the Mo GTD system
**Started:** 2026-02-15

---

## 2026-02-15 — Initial Exploration

### What We're Building

A web + mobile frontend for the Mo personal AI assistant system. Users (starting with Lyric + son Kohan, then family/testers) need a way to:
- View and edit their GTD markdown files from web and iOS
- Chat with their Mo agent for reviews, processing, queries
- See a dashboard driven by md file frontmatter/tags
- Quick capture on mobile, full editing on desktop
- All with proper security (TOTP 2FA, JWT, multi-user)

### Key Design Decisions

1. **Storage abstraction** — Start with markdown files on filesystem, but use an adapter pattern so storage can switch to Postgres or CouchDB later without rearchitecting
2. **Offline-first** — Especially on mobile. Edits can be delayed (phone/laptop), so sync must handle this gracefully
3. **Conflict handling** — Single user per file typical, but delayed syncs can cause conflicts. Must version, merge, and surface conflicts — never silently lose data
4. **Per-user isolation** — Separate Mo systems, not shared/team features
5. **TOTP 2FA** — Self-hosted, no 3rd party auth dependency
6. **Mo reads/writes same files** — The agent and the UI both interact with the same underlying markdown files
7. **Prototyping with Bolt/Replit** — Use credits to rapidly prototype the UI, then iterate

### UI Requirements

- **Dashboard**: GTD-aware (inbox count, next actions by context, projects, habits, calendar/dates), drag-and-drop for priorities
- **Editor**: WYSIWYG markdown (Notion-like for general use, raw markdown for advanced), frontmatter-aware
- **Chat**: Talk to Mo system behind the UI for reviews, queries, processing
- **Habits**: Visual habit tracking
- **Calendar**: Date-specific items, due dates
- **Quick capture**: Fast input flow, especially on mobile

### Platform Strategy

- Desktop: Full-featured web app
- Mobile: Expo app with offline support + PWA fallback
- Both share the same backend API

### Prototyping Approach

Using Bolt.new with credits to generate initial UI prototype. Need a comprehensive prompt that captures all requirements so Bolt can scaffold a working starting point.

### Example Repos to Study (not yet cloned)

- **huley** — (need to locate/clone)
- **obsidian-livesync** — CouchDB/PouchDB sync for Obsidian. Key insight: no REST API, vault interaction is direct filesystem or CouchDB
- **obsidian-remote** — (need to locate/clone)
- **syncthing** — Decentralized file sync with conflict handling

### Relevant Existing References

- **LibreChat** (`~/src/other/examples/LibreChat`) — Self-hosted multi-model chat interface (React/Node), relevant for chat UI patterns
- **outline** (`~/src/other/examples/outline`) — Collaborative knowledge base (React/Node/Postgres), relevant for editor and document management patterns
