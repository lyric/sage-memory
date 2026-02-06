# Feature: sage-memory

**Project**: sage-core
**Started**: 2026-02-06
**Phase**: Building

---

## 2026-02-06 — Initial Exploration

### What We're Building

Persistent project memory system for dev workflows. A standalone git repo that stores project context, feature explorations, research, decisions, and learnings across sessions and projects. Wraps around the existing DevLifecycle skill to add memory persistence, a conversational feature lifecycle (explore → plan → build → verify), built-in help, and reference repo management.

### Key Decisions

- **Standalone git repo**: ~/sage-memory/ is its own repo, not inside any project — projects come and go, memory outlasts them
- **Markdown-first, no database**: All memory is markdown files — greppable, editor-friendly, no infrastructure
- **Intentional saves over passive capture**: Agent/developer explicitly saves via workflows, not automatic hooks — signal over volume
- **Living documents over versioned copies**: One exploration.md per feature with timestamped sections — git handles diff history
- **Router as core memory**: router.md is compact, always loaded first, actively maintained — fastest cold-start path
- **Symlinks into projects**: .sage/ symlink in each project root — model can access memory from within any project
- **Wraps DevLifecycle, doesn't replace it**: sage-memory adds memory layer; DevLifecycle keeps plan/build/review execution
- **Letta concepts, not Letta code**: Two-tier memory model (core + archival) and active management tools, but implemented as files not a server
- **Name: sage-memory**: Part of the broader rename from PAI to Sage

### Constraints

- Must not require any running services — files and git only
- Must not check memory into project repos — symlinks + global gitignore
- Must work with existing DevLifecycle workflows, not replace them
- Built-in help must be accessible via natural language ("sage help")

### Rejected Approaches

- **Letta/MemGPT server**: Too heavy — Python server + Postgres for something that should be files
- **Passive hook-based capture** (PAI history system): Produces volume not signal — intentional saves better for "where did we leave off"
- **Day-stamped files**: Multiple files per feature forces synthesis — single living doc with sections is cleaner
- **In-repo storage**: tmp/ is ephemeral and per-project — memory should outlast features and span projects

### Relevant Code & References

- `Skills/DevLifecycle/` — Existing workflow patterns to integrate with
- `Skills/DevLifecycle/Templates/context.hbs` — Handlebars template pattern to follow
- `Skills/CreateSkill/SKILL.md` — Minimal skill structure reference
- `~/src/lyric/humanlayer/hlyr/THOUGHTS.md` — Thoughts system architecture
- `~/src/PAI/Packs/kai-history-system.md` — History capture patterns (1600 lines of detail)

### Open Questions

- None remaining — all resolved during exploration

---

## 2026-02-06 — Build Planning

### Plan Created

Full spec, tasks, and context at `tmp/plan/sage-memory/`:
- 31 tasks across 8 phases
- MVP: Phases 1-3 (store, templates, core lifecycle)
- 5 user stories covering the full system

### Build Started

Building all phases in sequence. Using the feature itself as its own first test subject.
