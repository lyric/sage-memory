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

### Open Questions

- What specific documents/workflows does the user manage in Obsidian today?
- What GTD stages need skills? (capture/inbox, clarify/process, organize, reflect/review, engage)
- How should skills interact with Obsidian vault? (direct file ops vs obsidian-cli vs API)
- Should mind-sweep be moved into this package or remain bundled?
- What makes this "usable without mo-core"? (standalone CLI? Claude Code skill? plain docs?)
- Should this be a skill pack (pure SKILL.md files) or hybrid plugin (skills + runtime)?
- What's the Obsidian vault structure? (PARA? GTD-specific? Custom?)
- User mentioned "reference info" to include — what is it?
