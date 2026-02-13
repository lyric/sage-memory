# Unknowns: GTD Skills

Gaps and questions that need resolving before planning.

---

## Technical Unknowns

- [ ] How does mo-core pull in gtd-mo-core? (extraDirs config, git submodule, npm package, workspace link?)
- [ ] Phase 2 sync — how users view/edit from phone/web when files live on server

## Resolved

- [x] Per-user GTD root → `~/.mo/gtd/` — each user has their own `~/`, same as sessions/config/credentials. No multi-tenant path routing needed.

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
