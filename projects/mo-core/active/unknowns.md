# Unknowns: GTD Skills

Gaps and questions that need resolving before planning.

---

## Technical Unknowns

*(all resolved)*

## Resolved

- [x] Per-user GTD root → `~/.mo/gtd/` — each user has their own `~/`, same as sessions/config/credentials. No multi-tenant path routing needed.
- [x] Mo-core integration → Private repo, no npm publish. Same extension structure as existing plugins (package.json + mo.plugin.json + index.ts + skills/). Clone alongside, reference via Mo config (plugin path). No submodules, no workspace coupling.
- [x] Phase 2 sync → Mo IS the interface. Users interact via channels (Telegram/web/etc). Mo reads/writes files on server. Local machine users can browse `~/.mo/gtd/` directly (Obsidian, VS Code, etc). Daily notes and direct editing supported now; Mo UI integration later.
- [x] Daily notes → Part of `~/.mo/gtd/` file structure. Inspectable/editable directly for now. Mo UI integration deferred.

## Resolved

- [x] Repo structure → Hybrid plugin: skills/ (SKILL.md) + TypeScript (file ops, cron, queries)
- [x] Cron → Yes. Plugin registers crons that fire skills. Skills also invocable manually.
- [x] Standalone → SKILL.md files work as Claude Code commands without Mo runtime
- [x] Onboarding → TypeScript creates dirs deterministically; skill guides the conversation
- [x] Son → Same server, own Mo system, separate GTD root, same base skills

## Resolved

- [x] Which GTD stages → All 5 (capture, clarify, organize, reflect, engage) + onboarding
- [x] Obsidian vault structure → Already designed, well-documented with INDEX files
- [x] mind-sweep → Moves into gtd-mo-core
- [x] Storage → Start with markdown files (not DB), swap later. Transparency > convenience.
- [x] Reference info → NotebookLM docs (10 files), purpose-and-values, obsidian-livesync
- [x] Obsidian interaction → Direct file read/write (no API needed now)
- [x] Multi-user → Yes, separate GTD roots per user
- [x] Server → Yes, Mo gateway, accessed via channels

---

*Updated during exploration. Items resolved are moved to exploration.md decisions.*
