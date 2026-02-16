# Bolt Prompt — Mo Server

Copy everything below the line into Bolt.

---

## Project: Mo — Personal Productivity Dashboard & Editor

Build a full-stack web application called **Mo** — a personal productivity system that reads/writes markdown files with YAML frontmatter. Think Notion meets Obsidian, with a GTD (Getting Things Done) methodology baked into the dashboard, plus a chat interface to talk to an AI assistant.

### Stack

- **Frontend**: React 18+ with TypeScript, Tailwind CSS, shadcn/ui components
- **Backend**: Node.js with Express, TypeScript
- **Auth**: JWT access/refresh tokens + TOTP-based 2FA (authenticator app like Google Authenticator / Authy). Self-hosted — no third-party auth providers.
- **Database**: Start with a simple file-based storage layer (markdown files on disk), but use a **storage adapter pattern** so we can swap to Postgres or CouchDB later without changing business logic. Define a `StorageAdapter` interface that all data access goes through.
- **Real-time**: WebSocket for live updates between server and clients
- **Mobile**: The app should be PWA-ready (service worker, offline support, installable). We'll wrap it in Expo later, so keep the UI responsive and mobile-friendly.

### Core Features

#### 1. Authentication & User Management

- **Registration**: Username + password + TOTP setup (show QR code for authenticator app enrollment)
- **Login**: Username + password + TOTP code (6-digit)
- **JWT sessions**: Access token (short-lived, 15min) + refresh token (7 days, httpOnly cookie)
- **Multi-user**: Each user has their own isolated data directory. No shared/team features.
- **User settings**: Theme preference (light/dark/system), default view, timezone
- **Security**: Rate limiting on auth endpoints, CSRF protection, secure headers (helmet), password hashing (argon2)

#### 2. Dashboard (Home View)

The dashboard is the main view. It reads markdown files and their YAML frontmatter to build a GTD-aware overview. The dashboard should feel fast and actionable — not a wall of text.

**Sections:**

- **Inbox** — Count of items in the inbox folder, with a button to process. Show the first few items as cards.
- **Today** — Calendar items due today, any daily habits, overdue items flagged. Pull from frontmatter `due` field.
- **Next Actions** — Grouped by context tags (`@work`, `@home`, `@calls`, `@errands`, `@web`, `@anywhere`). Each context is a collapsible section showing action items. Items can be checked off (updates the markdown file's `status` field to `done`).
- **Active Projects** — Cards for each project folder with status, next action, and progress indicator.
- **Waiting For** — Items with `status: waiting` frontmatter, showing who/what you're waiting on.
- **Habits** — Visual habit tracker. Show streaks, today's habits as checkboxes. Data stored as markdown files with frontmatter tracking completion dates.
- **Quick Capture** — A persistent input bar (bottom of mobile, top-right of desktop) that creates a new file in the inbox folder with a single tap/enter. Support voice-to-text on mobile (browser API).

**Interactions:**
- Drag and drop to reorder items within sections (saves order to a metadata file)
- Click any item to open it in the editor
- Swipe actions on mobile (complete, defer, move to project)
- Keyboard shortcuts on desktop (j/k navigation, enter to open, x to complete)

#### 3. Markdown Editor

A WYSIWYG markdown editor that feels like Notion for basic use, with the option to switch to raw markdown for power users.

- **WYSIWYG mode** (default): Rich text editing — headings, bold, italic, lists, checkboxes, code blocks, tables, links, images. Use Tiptap or Milkdown as the editor engine.
- **Markdown mode**: Raw markdown with syntax highlighting and live preview (split pane).
- **Frontmatter panel**: A structured form at the top of each document showing frontmatter fields (type, status, context, project, due, tags, updated). Editing the form updates the YAML. Editing the YAML updates the form.
- **File browser sidebar**: Tree view of the file system, with folder collapse/expand. Current file highlighted. Create/rename/delete files and folders.
- **Auto-save**: Debounced save (1.5s after last edit). Show save indicator.
- **Version history**: Track versions of each file (store diffs or snapshots). Show a timeline sidebar to browse/restore previous versions.
- **Conflict detection**: If a file was modified on the server while the user was editing offline or in another tab, show a diff view and let the user choose which version to keep or merge manually. Never silently overwrite.

#### 4. Chat Interface

A chat panel (slide-out drawer on desktop, full screen on mobile) for talking to the Mo AI assistant.

- **Message input**: Text box with send button, support for markdown in messages
- **Message display**: Rendered markdown, code blocks with copy button, streaming responses
- **Chat connects to a WebSocket endpoint** (`/api/chat`) that we'll later wire to the Mo gateway. For now, stub it with echo responses or a simple OpenAI proxy.
- **Chat history**: Stored per-user, scrollable, searchable
- **Context-aware**: When a document is open, the chat knows which file you're looking at (send the file path as context)

#### 5. Calendar View

- Monthly/weekly/daily calendar showing items with `due` dates from frontmatter
- Click a day to see all items due that day
- Drag items to reschedule (updates `due` frontmatter)
- Color-coded by type (action, project milestone, calendar event, habit)

#### 6. File System & Storage

**Storage Adapter Interface** (critical for future flexibility):

```typescript
interface StorageAdapter {
  // Files
  readFile(userId: string, path: string): Promise<FileContent>
  writeFile(userId: string, path: string, content: string, expectedVersion?: string): Promise<FileVersion>
  deleteFile(userId: string, path: string): Promise<void>
  moveFile(userId: string, from: string, to: string): Promise<void>

  // Directories
  listDirectory(userId: string, path: string): Promise<DirectoryEntry[]>
  createDirectory(userId: string, path: string): Promise<void>

  // Versioning
  getVersions(userId: string, path: string): Promise<FileVersion[]>
  getVersion(userId: string, path: string, versionId: string): Promise<FileContent>

  // Search
  search(userId: string, query: string): Promise<SearchResult[]>
  searchByFrontmatter(userId: string, field: string, value: string): Promise<FileContent[]>

  // Sync
  getChangesSince(userId: string, since: Date): Promise<ChangeEvent[]>
}
```

**Initial implementation**: `FileSystemStorageAdapter` that reads/writes markdown files on disk under `data/<userId>/gtd/`.

**Default directory structure per user** (created on registration):

```
data/<userId>/gtd/
├── _inbox/
├── _next_actions/
│   ├── work.md
│   ├── home.md
│   ├── calls.md
│   ├── errands.md
│   └── web.md
├── projects/
│   ├── active/
│   ├── someday/
│   └── archive/
├── focus/
│   ├── goals/
│   └── areas/
├── habits/
├── agendas/
├── calendar/
└── reference/
```

#### 7. Sync & Offline Support

- **Optimistic updates**: UI updates immediately, syncs to server in background
- **Offline queue**: When offline, queue changes locally (IndexedDB). Sync when back online.
- **Conflict resolution**: Use file version IDs. If server version doesn't match expected version on write, return a conflict. Client shows a diff/merge UI.
- **WebSocket**: Real-time push of file changes from server to connected clients (for multi-device sync)

### UI/UX Design

- **Theme**: Clean, minimal, slightly warm. Light and dark modes. Similar to Linear or Notion's aesthetic.
- **Layout**:
  - Desktop: Sidebar (file tree + navigation) | Main content | Optional right panel (chat or properties)
  - Mobile: Bottom tab navigation (Dashboard, Notes, Chat, Settings). Swipe gestures.
- **Navigation tabs**: Dashboard, Notes (editor + file browser), Chat, Calendar, Settings
- **Typography**: Clean sans-serif, good readability. Monospace for code/raw markdown.
- **Animations**: Subtle, purposeful. Smooth transitions between views. No gratuitous motion.
- **Responsive breakpoints**: Mobile (<768px), Tablet (768-1024px), Desktop (>1024px)

### API Endpoints

```
POST   /api/auth/register          — Create account + TOTP setup
POST   /api/auth/login             — Login (returns JWT + sets refresh cookie)
POST   /api/auth/refresh           — Refresh access token
POST   /api/auth/verify-totp       — Verify TOTP during setup
POST   /api/auth/logout            — Clear refresh cookie

GET    /api/files/*path            — Read file content
PUT    /api/files/*path            — Write file (with version check)
DELETE /api/files/*path            — Delete file
POST   /api/files/move             — Move/rename file
GET    /api/files/tree             — Get directory tree
GET    /api/files/versions/*path   — Get version history
GET    /api/files/version/:id      — Get specific version

GET    /api/search?q=              — Full-text search
GET    /api/search/frontmatter     — Search by frontmatter fields

GET    /api/dashboard              — Aggregated dashboard data
GET    /api/habits                 — Habit tracking data
PUT    /api/habits/:id/check       — Check off habit for today

WS     /api/ws                     — WebSocket for real-time sync + chat
```

### Project Structure

```
mo-server/
├── apps/
│   ├── web/                       — React frontend
│   │   ├── src/
│   │   │   ├── components/        — Reusable UI components
│   │   │   ├── features/          — Feature modules (dashboard, editor, chat, calendar, auth)
│   │   │   ├── hooks/             — Custom React hooks
│   │   │   ├── lib/               — Utilities, storage client, API client
│   │   │   ├── stores/            — State management (zustand)
│   │   │   └── styles/            — Global styles, Tailwind config
│   │   └── public/
│   └── server/                    — Express backend
│       ├── src/
│       │   ├── routes/            — API route handlers
│       │   ├── middleware/        — Auth, rate limiting, error handling
│       │   ├── storage/           — StorageAdapter interface + FileSystemAdapter
│       │   ├── services/          — Business logic (auth, sync, search)
│       │   └── utils/             — Helpers
│       └── data/                  — User data directories
├── packages/
│   └── shared/                    — Shared types, frontmatter schema, constants
└── package.json                   — Monorepo root (npm workspaces or turborepo)
```

### What to Build First

Focus on getting these working in order:

1. **Auth flow** — Register, TOTP enrollment (QR code), login with 2FA, protected routes
2. **File browser + editor** — Tree view sidebar, open/edit/save markdown files, WYSIWYG editor with frontmatter panel
3. **Dashboard** — Read files, parse frontmatter, render the GTD dashboard sections
4. **Quick capture** — Input bar that creates inbox items
5. **Chat stub** — Chat UI with WebSocket connection (echo responses for now)
6. **Calendar view** — Pull due dates, render calendar
7. **Offline support** — Service worker, IndexedDB queue, conflict UI

### Important Notes

- **Don't over-engineer the storage layer** — The adapter interface is the important part. The filesystem implementation can be straightforward. We'll swap it later.
- **Frontmatter is the data model** — Every markdown file has YAML frontmatter with fields like `type`, `status`, `context`, `project`, `due`, `updated`, `tags`. The dashboard reads this to build views. Example:

```yaml
---
type: action
status: active
context: "@work"
project: mo-server
due: 2026-02-20
updated: 2026-02-15
tags: [coding, frontend]
---
# Design the dashboard layout

Research Notion and Linear for layout inspiration...
```

- **Security matters** — This will be exposed to the internet. Proper auth, CSRF, rate limiting, input sanitization, no path traversal in file API.
- **Mobile-friendly from the start** — Responsive layout, touch targets, swipe gestures. This will become an Expo app.
