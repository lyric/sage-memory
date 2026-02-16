# Bolt Prompt — Mo Server

Copy everything below the line into Bolt.

---

## Project: Mo — Personal Productivity Dashboard & Editor

Build a full-stack web application called **Mo** — a personal productivity system that reads/writes markdown files with YAML frontmatter. Think Notion meets Obsidian, with a GTD (Getting Things Done) methodology baked into the dashboard, plus a chat interface to talk to an AI assistant.

### Stack

- **Frontend**: React 18+ with TypeScript, Tailwind CSS, shadcn/ui components
- **Editor**: **Tiptap 2** (ProseMirror-based, same engine Outline and Notion use). Tiptap gives us a Notion-like feel out of the box with extensions for everything we need. Do NOT use CodeMirror, Milkdown, or raw ProseMirror — Tiptap has the best DX and extension ecosystem.
- **Backend**: Node.js with Express, TypeScript
- **Auth**: JWT access/refresh tokens + TOTP-based 2FA (authenticator app like Google Authenticator / Authy). Self-hosted — no third-party auth providers.
- **Database**: Start with a simple file-based storage layer (markdown files on disk), but use a **storage adapter pattern** so we can swap to Postgres or CouchDB later without changing business logic. Define a `StorageAdapter` interface that all data access goes through.
- **Real-time**: WebSocket (Socket.io) for live updates between server and clients
- **State management**: Zustand for client state
- **Mobile**: The app should be PWA-ready (service worker, offline support, installable). We'll wrap it in Expo later for native iOS/Android, so keep the UI responsive and mobile-friendly. A clean REST API layer is critical — the API will be consumed by the web app, the Expo mobile app, AND an AI agent (Mo) that reads/writes the same data programmatically. Every UI action should go through the API, not talk to storage directly.

### UI Layout — Sidebar + Main Content

The app uses a **collapsible left sidebar** with two zones, plus a main content area. The sidebar collapses to icon-only mode (40px wide) and expands to full width (260px) with a toggle button. On mobile, the sidebar is a slide-out drawer.

#### Sidebar Structure

```
┌──────────────────────┐
│ Mo                 ‹ │  ← App name + collapse toggle
├──────────────────────┤
│ ☀ Today              │  ← Focus views (always visible)
│ 📅 This Week         │
│ 📆 Calendar          │
│ 💬 AI Chat           │
├──────────────────────┤
│ NOTES              + │  ← File tree section header + new note button
│ ▸ 📁 Work            │
│   ▸ 📁 Projects      │
│     📄 Q1 Planning   │
│     📄 Meeting Notes  │
│ ▸ 📁 Personal        │
│   📄 Shopping List    │
│ 📄 Quick Ideas       │
│                      │
│                      │
├──────────────────────┤
│ ⚙ Settings           │  ← Pinned to bottom
└──────────────────────┘
```

**When collapsed to icons:**
```
┌────┐
│ › │  ← Expand toggle
├────┤
│ ☀ │
│ 📅 │
│ 📆 │
│ 💬 │
├────┤
│ 📁 │  ← Opens file tree in popover
├────┤
│ ⚙ │
└────┘
```

**Sidebar behavior:**
- Top section: **Focus views** — Today, This Week, Calendar, AI Chat. These are fixed navigation items, always visible. Active item has a subtle highlight background.
- Middle section: **Notes** — Collapsible file tree browser. Folders expand/collapse with chevrons. Files show document icon. A "+" button in the section header creates a new note. Drag and drop to move files between folders.
- Bottom section: **Settings** — Pinned to the bottom of the sidebar.
- The sidebar remembers its collapsed/expanded state in localStorage.
- Transition: smooth 200ms width animation when collapsing/expanding.
- On mobile (<768px): sidebar is hidden by default, opened via hamburger menu, slides in as an overlay with backdrop.

#### Main Content Area

The main content area renders the selected view:
- **Today** → Today dashboard
- **This Week** → Week overview dashboard
- **Calendar** → Full calendar view
- **AI Chat** → Chat interface
- **Any note** → Tiptap editor with frontmatter panel
- **Settings** → User settings page

### Core Features

#### 1. Authentication & User Management

- **Registration**: Username + password + TOTP setup (show QR code for authenticator app enrollment)
- **Login**: Username + password + TOTP code (6-digit)
- **JWT sessions**: Access token (short-lived, 15min) + refresh token (7 days, httpOnly cookie)
- **Multi-user**: Each user has their own isolated data directory. No shared/team features.
- **User settings**: Theme preference (light/dark/system), default view, timezone, sidebar collapsed state
- **Security**: Rate limiting on auth endpoints, CSRF protection, secure headers (helmet), password hashing (argon2)

#### 2. Today Dashboard

The default view when you open the app. Reads markdown files and their YAML frontmatter to build a GTD-aware daily overview. Should feel fast and actionable — cards and lists, not a wall of text.

**Sections (rendered as cards/panels in a responsive grid):**

- **Inbox** — Count badge + first few items as compact cards. "Process Inbox" button opens the inbox folder. Each item shows title and age (e.g., "2 days ago").
- **Due Today** — Items with `due: today's date` in frontmatter. Overdue items flagged with red accent. Checkboxes to mark complete (updates `status: done` in frontmatter).
- **Next Actions** — Grouped by context tags (`@work`, `@home`, `@calls`, `@errands`, `@web`, `@anywhere`). Each context is a collapsible section. Items have checkboxes. Show only first 5 per context with "show more" link.
- **Habits** — Visual habit tracker row. Today's habits as checkboxes with streak counts. Small sparkline or dot grid showing recent completion.
- **Waiting For** — Items with `status: waiting`, showing who/what you're waiting on and how long.
- **Quick Capture** — A persistent input bar at the top of the dashboard (and floating button on mobile) that creates a new file in the inbox folder with a single tap/enter.

**Interactions:**
- Drag and drop to reorder items within sections (saves order to a metadata file)
- Click any item title to open it in the editor
- Checkbox to complete items inline
- Keyboard shortcuts: `n` for new capture, `j/k` navigation, `x` to complete

#### 3. This Week Dashboard

A broader view for weekly planning and review.

**Sections:**
- **Week at a Glance** — 7-day strip showing items due each day, color-coded by type
- **Active Projects** — Cards for each project with status, next action, and progress (% of actions completed)
- **Upcoming** — Items due this week grouped by day
- **Review Prompts** — GTD weekly review checklist (if it's the configured review day)
- **Completed This Week** — Recently completed items for satisfaction/review

#### 4. Tiptap Editor

The editor is the heart of the app. It must feel like Notion for people who don't know markdown, while supporting full markdown for power users.

**Editor engine: Tiptap 2** with these extensions:

- **StarterKit** — Paragraphs, headings (H1-H6), bold, italic, strike, code, blockquote, lists (bullet, ordered), hard break, horizontal rule
- **TaskList + TaskItem** — Interactive checkboxes/todo lists that sync to frontmatter `status`
- **CollapsibleHeading** (custom extension) — Headings that collapse/expand their content when clicked. Show a chevron toggle on hover. This is critical for organizing long documents — users can collapse sections they're not working on. Collapsed state persists.
- **Link** — Inline links with paste-detection (auto-link URLs)
- **PageLink** (custom extension) — `[[page name]]` syntax that creates internal links to other notes. Show as styled chips/pills. Autocomplete dropdown when typing `[[`. Clicking navigates to the linked page. Backlinks panel shows which pages link to the current one.
- **Tag** (custom extension) — `#tag` syntax rendered as colored pills/chips. Tags are searchable and clickable (clicking a tag shows all files with that tag). Tags also populate frontmatter `tags` field.
- **Image** — Drag-and-drop image upload, paste from clipboard
- **Table** — Basic tables with add/remove row/column
- **CodeBlock** — Syntax-highlighted code blocks with language selector
- **Placeholder** — "Type '/' for commands..." placeholder text
- **SlashCommand** (custom extension) — Notion-style "/" menu for inserting blocks: heading, todo list, bulleted list, numbered list, code block, table, image, divider, quote, callout, page link
- **Typography** — Smart quotes, em dashes
- **CharacterCount** — Word/character count in status bar

**Frontmatter panel:**
- Displayed as a compact, styled form above the editor content (not raw YAML)
- Fields: type (dropdown), status (dropdown), context (tag chips), project (dropdown), due (date picker), tags (tag input), updated (auto-set on save)
- Editing the form updates the YAML frontmatter. Toggle to show raw YAML if needed.
- The panel is collapsible — users who don't care about metadata can hide it.

**Editor toolbar:**
- Floating bubble toolbar on text selection (bold, italic, strike, code, link, highlight, comment)
- Fixed top toolbar with: text style dropdown (paragraph, H1-H3), list buttons, checkbox, table, image, code block
- Slash command menu as alternative to toolbar

**Other editor features:**
- **Auto-save**: Debounced save (1.5s after last edit). Show "Saving..." / "Saved" indicator in top bar.
- **Markdown toggle**: Button to switch between WYSIWYG and raw markdown view (split pane with preview). Most users will never use this.
- **Version history**: Track versions on each save. Show a history panel (right drawer) to browse/restore previous versions with diff highlighting.
- **Conflict detection**: If a file was modified on the server while the user was editing, show a diff view and let the user choose which version to keep or merge. Never silently overwrite.
- **Breadcrumb**: Show file path as breadcrumb above the editor (e.g., Work > Projects > Q1 Planning)

#### 5. Chat Interface

When "AI Chat" is selected in the sidebar, the main content area becomes a full chat interface.

- **Message input**: Text box with send button at bottom, support for markdown in messages
- **Message display**: Rendered markdown, code blocks with copy button, streaming token-by-token display
- **Chat connects to a WebSocket endpoint** (`/api/chat`) that we'll later wire to the Mo AI gateway. For now, stub it with echo responses or a simple OpenAI-compatible proxy.
- **Chat history**: Stored per-user, scrollable, searchable. Conversations listed in sidebar when Chat view is active.
- **Context-aware**: When you navigate to Chat from a document, automatically include the document path as context. Show a "Chatting about: filename" indicator.
- **Quick actions**: Buttons for common requests — "Review my inbox", "What's due today?", "Weekly review"

#### 6. Calendar View

When "Calendar" is selected in the sidebar:

- Monthly/weekly/daily toggle views
- Items pulled from frontmatter `due` dates
- Click a day to see all items due that day in a side panel
- Drag items to reschedule (updates `due` frontmatter)
- Color-coded by type (action, project milestone, calendar event, habit)
- "Add item" button on each day cell

#### 7. File System & Storage

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
  getVersion(userId: string, path: string, versionId: string): Promise<FileVersion>

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

#### 8. Sync & Offline Support

- **Optimistic updates**: UI updates immediately, syncs to server in background
- **Offline queue**: When offline, queue changes locally (IndexedDB). Sync when back online. Show offline indicator in top bar.
- **Conflict resolution**: Use file version IDs (content hash or incrementing version). If server version doesn't match expected version on write, return a conflict. Client shows a diff/merge UI with "keep mine", "keep theirs", or "merge" options. Never silently overwrite. Reference pattern: Syncthing uses vector clocks + modification time as tiebreaker, with non-destructive conflict copies.
- **WebSocket (Socket.io)**: Real-time push of file changes from server to connected clients (for multi-device sync). Auto-reconnect on disconnect.

### UI/UX Design

- **Theme**: Clean, minimal, slightly warm. Light and dark modes. Similar to Linear or Notion's aesthetic — lots of white space, subtle borders, rounded corners (8px), soft shadows.
- **Colors**: Primary accent (blue-indigo), success (green), warning (amber), danger (red). Context tags get unique muted colors (@work = blue, @home = green, @calls = purple, etc.)
- **Layout**:
  - Desktop: Collapsible sidebar (260px / 40px) | Main content (flex-1) | Optional right panel for properties/history (320px, toggle)
  - Mobile: Hidden sidebar (hamburger), bottom floating action button for quick capture, swipe gestures
- **Typography**: Inter or system sans-serif for UI, slightly larger base size (16px). Monospace (JetBrains Mono or similar) for code blocks only.
- **Animations**: Subtle, purposeful. Sidebar collapse 200ms ease. View transitions 150ms fade. Card hover lift. No gratuitous motion.
- **Responsive breakpoints**: Mobile (<768px), Tablet (768-1024px), Desktop (>1024px)
- **Empty states**: Friendly illustrations or messages when sections are empty ("No items due today — nice!" or "Inbox zero achieved")

### API-First Architecture

**This is critical**: The API is the single source of truth for all data access. The web frontend, the future Expo mobile app, and the Mo AI agent all consume the same REST API. No client should talk to storage directly.

- Every UI action maps to an API call
- The API must be complete enough that you could build the entire app with curl
- Every endpoint returns consistent JSON (`{ data: ... }` on success, `{ error: "message", code: "ERROR_CODE" }` on failure)
- All write endpoints return the updated resource
- Pagination on list endpoints (`?limit=50&offset=0`)
- The Mo AI agent will use these same endpoints to read/write files, check dashboard state, and manage tasks — so the API must be clean, well-documented, and self-sufficient

### API Endpoints

```
POST   /api/auth/register          — Create account + TOTP setup
POST   /api/auth/login             — Login (returns JWT + sets refresh cookie)
POST   /api/auth/refresh           — Refresh access token
POST   /api/auth/verify-totp       — Verify TOTP during setup
POST   /api/auth/logout            — Clear refresh cookie

GET    /api/files/*path            — Read file content
PUT    /api/files/*path            — Write file (with version check header)
DELETE /api/files/*path            — Delete file
POST   /api/files/move             — Move/rename file
GET    /api/files/tree             — Get full directory tree
GET    /api/files/versions/*path   — Get version history for a file
GET    /api/files/version/:id      — Get specific version content
GET    /api/files/backlinks/*path  — Get pages that link to this file

GET    /api/search?q=              — Full-text search across all files
GET    /api/search/tag/:tag        — Get all files with a specific tag
GET    /api/search/frontmatter     — Search by frontmatter field/value

GET    /api/dashboard/today        — Aggregated today dashboard data
GET    /api/dashboard/week         — Aggregated week dashboard data
GET    /api/habits                 — Habit tracking data
PUT    /api/habits/:id/check       — Check off habit for today

WS     /api/ws                     — Socket.io for real-time sync + chat
```

### Project Structure

```
mo-server/
├── apps/
│   ├── web/                       — React frontend (Vite)
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── sidebar/       — Sidebar, nav items, file tree
│   │   │   │   ├── editor/        — Tiptap editor, extensions, toolbar
│   │   │   │   ├── dashboard/     — Dashboard cards, sections
│   │   │   │   ├── chat/          — Chat messages, input, history
│   │   │   │   ├── calendar/      — Calendar views
│   │   │   │   └── ui/            — shadcn/ui base components
│   │   │   ├── features/          — Feature modules (auth, settings)
│   │   │   ├── hooks/             — Custom React hooks
│   │   │   ├── lib/               — API client, storage client, utils
│   │   │   ├── stores/            — Zustand stores (auth, files, ui, chat)
│   │   │   └── styles/            — Global styles, Tailwind config
│   │   └── public/
│   └── server/                    — Express backend
│       ├── src/
│       │   ├── routes/            — API route handlers
│       │   ├── middleware/        — Auth, rate limiting, validation, error handling
│       │   ├── storage/           — StorageAdapter interface + FileSystemAdapter
│       │   ├── services/          — Business logic (auth, sync, search, dashboard)
│       │   └── utils/             — Helpers, frontmatter parser, markdown utils
│       └── data/                  — User data directories (gitignored)
├── packages/
│   └── shared/                    — Shared types, frontmatter schema, constants
└── package.json                   — Monorepo root (npm workspaces)
```

### What to Build First

Focus on getting these working in order:

1. **Sidebar + layout shell** — Collapsible sidebar with Focus views and Notes tree, main content area, responsive mobile layout. Get the navigation working with placeholder content.
2. **Auth flow** — Register, TOTP enrollment (QR code), login with 2FA, protected routes, JWT refresh
3. **File tree + editor** — Tree view in sidebar, open/edit/save markdown files, Tiptap WYSIWYG editor with collapsible headings, tags, todos, page links, frontmatter panel
4. **Today dashboard** — Read files, parse frontmatter, render inbox/due/actions/habits sections
5. **Quick capture** — Input bar that creates inbox items instantly
6. **This Week dashboard** — Weekly view with projects, upcoming, review prompts
7. **Chat stub** — Chat UI with Socket.io connection (echo responses for now)
8. **Calendar view** — Pull due dates, render monthly/weekly calendar
9. **Offline support** — Service worker, IndexedDB queue, conflict UI

### Tiptap Extensions to Install

```bash
npm install @tiptap/react @tiptap/starter-kit @tiptap/extension-task-list @tiptap/extension-task-item @tiptap/extension-link @tiptap/extension-image @tiptap/extension-table @tiptap/extension-table-row @tiptap/extension-table-cell @tiptap/extension-table-header @tiptap/extension-code-block-lowlight @tiptap/extension-placeholder @tiptap/extension-typography @tiptap/extension-character-count @tiptap/extension-highlight @tiptap/extension-underline lowlight
```

Custom extensions to build:
- **CollapsibleHeading** — Extends Heading node: adds collapse toggle on hover, hides content between this heading and the next same-or-higher-level heading, persists collapsed state
- **PageLink** — Node for `[[page name]]` internal links with autocomplete and navigation
- **Tag** — Inline node for `#tag` with colored pill rendering and click-to-filter
- **SlashCommand** — Suggestion plugin showing a command palette on "/" keystroke

### Important Notes

- **Don't over-engineer the storage layer** — The adapter interface is the important part. The filesystem implementation can be straightforward. We'll swap it later.
- **Frontmatter is the data model** — Every markdown file has YAML frontmatter. The dashboard reads this to build views. Example:

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

- **API-first, always** — The frontend is just one consumer of the API. The Expo mobile app and the Mo AI agent are the other two. If a feature can't be done through the API alone, the API is incomplete. All business logic lives in the server, not in React components.
- **Security matters** — This will be exposed to the internet. Proper auth, CSRF, rate limiting, input sanitization, no path traversal in file API.
- **Mobile-friendly from the start** — Responsive layout, touch targets (min 44px), swipe gestures. This will become an Expo app for iOS/Android.
- **The editor should feel like Notion** — People who don't know markdown should be able to use it without knowing they're writing markdown. The slash command menu, floating toolbar, and WYSIWYG rendering are key. Power users can toggle to raw markdown view.
- **Page links are important** — The `[[page name]]` linking creates a knowledge graph. The backlinks panel shows connections. This is how GTD projects reference their next actions and how areas reference their goals.
- **Tags drive the dashboard** — Tags like `#habit`, `@work`, `@home` are how the dashboard groups and filters items. Make tag rendering consistent between editor and dashboard.
