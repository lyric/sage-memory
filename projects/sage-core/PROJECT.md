# Project: sage-core
Updated: 2026-02-06

## What Is This

The core PAI/Sage personal AI infrastructure system. Skills, workflows, tools, templates, and rules that power the dev assistant. Formerly PAI-config, becoming sage-core as the foundational building block system.

## Architecture

Skill-based architecture where each capability is a self-contained skill with:
- `SKILL.md` — definition, triggers, routing, examples
- `Workflows/` — markdown workflow files executed by the AI agent
- `Tools/` — TypeScript CLI tools run via bun
- `Templates/` — Handlebars templates for structured document generation

Skills are registered in `skill-index.json` and loaded on-demand based on trigger phrases. The CORE skill is always loaded (identity, preferences, contacts).

## Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| Language | TypeScript | All tools and scripts |
| Package Manager | bun | Fast, TS-first |
| Runtime | Node | Via bun |
| Templates | Handlebars | .hbs files for structured docs |
| Rules | Markdown | .cursor/rules/ for IDE context |

## Key Directories

- `Skills/` — All skill definitions (CORE, DevLifecycle, SageMemory, Art, Prompting, etc.)
- `Skills/<Name>/Workflows/` — Workflow markdown files for each skill
- `Skills/<Name>/Tools/` — TypeScript CLI tools for each skill
- `Skills/<Name>/Templates/` — Handlebars templates
- `Tools/` — Global CLI tools (SkillSearch, GenerateSkillIndex, PaiArchitecture)
- `.cursor/rules/` — Cursor IDE rules organized by category (Base, Stack, Workflows, Projects)
- `.cursor/commands/` — Slash commands for Cursor (0-summarize through 8-ralph)
- `tmp/` — Gitignored scratch/planning area for active feature work

## Data Flows

```
User trigger phrase → SKILL.md routing table → Workflow.md execution
                                             → Tools/*.ts for deterministic ops
                                             → Templates/*.hbs for doc generation
```

Skill discovery: trigger phrase → skill-index.json → load SKILL.md → route to workflow.

## Current State

Building the SageMemory skill — persistent project memory system.

## Feature History

| Feature | Status | Date | Path |
|---------|--------|------|------|
| devlifecycle-skill | Complete | 2026-01 | archive/2026-01-devlifecycle-skill/ |
| sage-memory | Building | 2026-02 | active/ |

## Gotchas

- Skills are loaded on-demand via trigger phrases in the `description` field — if triggers are wrong, the skill won't activate
- `skill-index.json` must be regenerated after adding/modifying skills: `bun ~/.config/pai/Tools/GenerateSkillIndex.ts`
- `tmp/` is gitignored — feature artifacts there are ephemeral (that's why sage-memory exists)
- Templates use custom Handlebars helpers (`now`, `default`, `join`, `padStart`, `eq`, `add`) defined in the Prompting skill's RenderTemplate.ts

## References

- **humanlayer** — ~/src/lyric/humanlayer: Context engineering patterns (thoughts system, agent routing, handoffs)
- **PAI** — ~/src/PAI: Upstream framework repo with pack definitions (kai-history-system for memory patterns)
