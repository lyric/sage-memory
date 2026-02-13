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

### Open Questions

- What GTD stages need skills? (capture/inbox, clarify/process, organize, reflect/review, engage — all?)
- Should mind-sweep move into gtd-mo-core or remain bundled in mo-core?
- What makes this "usable without mo-core"? (Claude Code skill? standalone CLI? plain markdown docs anyone can use?)
- Should this be pure SKILL.md files or hybrid plugin (skills + TypeScript runtime)?
- How should skills interact with the vault? (direct file read/write seems natural since it's local markdown)
- How will mo-core bundle these? (git submodule? npm package? config extraDirs? workspace package?)
- User mentioned "reference info" to include — what is it?
