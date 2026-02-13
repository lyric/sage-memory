# Exploration: GTD Skills for Document Management

**Feature:** GTD skills for managing documents (currently in Obsidian)
**Repo:** https://github.com/lyric/gtd-mo-core (empty, freshly created)
**Started:** 2026-02-13

---

## 2026-02-13 — Initial Research & Context Gathering

### What We're Building

A set of GTD (Getting Things Done) skills for Mo that manage documents currently living in Obsidian. Key design constraint: skills should be **usable without mo-core** but **bundled with mo-core**. The skills will live in a separate repo (`gtd-mo-core`) and be distributed as a skill pack.

### Research Completed

#### Existing GTD in Mo-Core
- **mind-sweep** skill already exists in `skills/mind-sweep/SKILL.md` — handles open-loop capture
- mind-sweep references future skills: `inbox-process`, `weekly-review`, `planning-coach`, `daily-dashboard`
- mind-sweep outputs to `areas/GTD/mindsweep-YYYY-MM-DD/mind-sweep-master.md` in Obsidian vault

#### Mo-Core Skills Architecture
- 53 bundled skills in `skills/` directory
- Skills are **documentation-first** (SKILL.md with frontmatter + markdown body, no code)
- Loaded by pi-coding-agent framework, injected into system prompt
- Metadata declares: requirements (bins, env, config), install steps, platform filters
- Precedence: workspace > managed > bundled > extra
- Skills can also ship via plugins (`mo.plugin.json` with `"skills": ["./skills"]`)
- Live-reloaded via file watching

#### Mo-Core Extensions/Plugin Architecture
- Plugins are TypeScript packages with `mo.plugin.json` manifest
- Have full lifecycle (start/stop), config schema, register tools/services/hooks
- Hybrid approach possible: plugin + skills (plugin provides runtime, skills provide docs)
- `open-prose` extension is an example of a pure skill pack via plugin

#### Reference GTD Projects Reviewed
1. **gtd-coach-plugin** — Goal → SOP framework, quarterly phases, memory-driven state, multi-level reviews
2. **cc-gtd** — David Allen GTD with Todoist/Calendar integration, context-based task organization, energy-aware time blocking
3. **gtd-cc/obsidian** — PARA methodology, vault structure, knowledge + task integration

### Key Decisions

*(none yet — exploration just started)*

### Obsidian Vault GTD Structure (RESOLVED)

User's vault at `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/docs/gtd/`:

```
gtd/
├── INDEX.md                  → System navigation + processing rules + frontmatter standard
├── _inbox/                   → Capture: everything lands here first
├── _next_actions/            → Actions by context (@work, @home, @calls, @errands, @web)
│   ├── _overview.md          → Context summary + usage guide
│   ├── _waiting_for.md       → Delegated items
│   ├── work.md / home.md / calls.md / errands.md / web_shopping.md
├── projects/                 → Outcomes requiring multiple actions
│   ├── INDEX.md              → Project template + review process
│   ├── active/ / someday/ / archive/
├── focus/                    → Higher horizons (purpose → goals → areas)
│   ├── INDEX.md              → Horizons guide + decision filter + review cadence
│   ├── life_purpose.md / core_values_principles.md
│   ├── goals/                → 2026.md, 2026-Q1.md, weekly/2026-W07.md
│   └── areas/                → home.md, finances.md, GTD/principles/, GTD/reference/
├── agendas/                  → People/meeting agendas
├── calendar/                 → Date-specific items
└── reference/                → Non-actionable reference material
```

Key conventions:
- Frontmatter: type, status, context, project, due, updated
- Context tags: @work, @home, @calls, @errands, @web, @anywhere
- Processing: inbox → 2-min rule → project or next-action or reference/someday
- Horizons: Ground (actions) → 10k (projects) → 20k (areas) → 30k (goals) → 40k (vision) → 50k (purpose)
- Review cadence: daily, weekly (Sunday/Monday), monthly, quarterly, annual
- Recent mind sweep: `focus/areas/GTD/mindsweep-feb-8-2026/`

### Reference GTD Examples Found

Located at `~/Library/Mobile Documents/com~apple~CloudDocs/src/other/examples/gtd/`:
1. **gtd-coach-plugin** — Goal framework, quarterly phases, memory-driven state, review templates
2. **cc-gtd** — David Allen GTD with Todoist/Calendar, context-based, energy-aware time blocking
3. **gtd-cc/obsidian** — PARA + GTD integration, vault structure, knowledge + task linking

### NotebookLM Reference Docs (10 files)

Located at `~/Library/Mobile Documents/com~apple~CloudDocs/src/docs/gtd/focus/areas/GTD/reference/gtd from notebooklm/`:

These are comprehensive GTD-for-Claude-skills guides generated from NotebookLM. Key takeaways:

1. **"Architecting GTD Workflow Skills"** — Recommends 5 skills: Capture (mind sweep), Clarify & Organize (decision engine), Weekly Review (facilitator), Engage (context filter), Natural Planning (project launcher). Emphasizes "habits over features" and avoiding overcomplication.

2. **"Claude Skills Implementation Guide"** — Deep technical blueprint. Three integration stages: understanding → implementation → behavior change. Detailed prompt-level logic for each skill. Mandates physical verb library for next actions (Call, Draft, Research, Buy, Email, Clean, Deliver, Review). Forbids ABC priority codes. Includes 2-minute rule "hard stop" pattern.

3. **"Mind Sweep and Base System Guide"** — Professional + personal trigger categories. Physical sweep prompts. Base system setup: clarify actionability → 2-min rule → organize into lists (Projects, Calendar, Next Actions by context, Waiting For).

4. **"Weekly Review"** — Three phases: Get Clear (empty inboxes), Get Current (review all lists, calendar, waiting-for, ensure every project has a next action), Get Creative (someday/maybe, new ideas).

5. **"Five Stages of Getting Things Done"** — Canonical: Capture → Clarify → Organize → Reflect → Engage. Four-criteria engagement model: Context → Time → Energy → Priority.

6. **"Overcoming GTD Implementation Pitfalls"** — 10 failure modes: piecemeal implementation, incomplete capture, decisiveness deficit, review neglect, tech obsession, overcomplication, arbitrary due dates, life-siloing, preemptive modification, motivational decay.

7. **"Architecture of Action — Task Contexts"** — Starter set: @Calls, @Computer, @Office, @Home, @Errands, @Anywhere, @Agendas, @Waiting For.

8. **"Incompletion Trigger List"** — Comprehensive professional + personal trigger categories for mind sweeps.

9. **"Natural Planning Model"** — 5 steps: Purpose/Principles → Vision → Brainstorming → Organizing → Next Actions.

10. **"Six Horizons of Focus"** — Ground (actions) → 10k (projects) → 20k (areas) → 30k (goals) → 40k (vision) → 50k (purpose).

### Additional Reference Material

- **purpose-and-values.md** — User's personal 50k ft doc: 9 vision dimensions, decision filter, core loves (music, spirituality, building, nature), Portugal move rationale.
- **gtd-references/** — Official David Allen Company setup guide PDFs (Todoist, Trello, M365, Google Apps, Paper), GTD book reference PDF, LifeDev cheatsheet.
- **obsidian-livesync** (cloned to `src/other/examples/`) — Obsidian sync plugin using CouchDB/PouchDB/WebRTC. Relevant for understanding Obsidian vault internals. Key finding: **no exposed REST API** — vault interaction is direct filesystem (markdown files) or CouchDB if LiveSync is configured. For GTD skills, direct file read/write is the simplest path.

### Open Questions

- What GTD stages need skills? (capture/inbox, clarify/process, organize, reflect/review, engage — all?)
- Should mind-sweep move into gtd-mo-core or remain bundled in mo-core?
- What makes this "usable without mo-core"? (Claude Code skill? standalone CLI? plain markdown docs anyone can use?)
- Should this be pure SKILL.md files or hybrid plugin (skills + TypeScript runtime)?
- How should skills interact with the vault? (direct file read/write seems natural since it's local markdown)
- How will mo-core bundle these? (git submodule? npm package? config extraDirs? workspace package?)
- User mentioned "reference info" to include — what is it?
