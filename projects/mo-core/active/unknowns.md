# Unknowns: GTD Skills

Gaps and questions that need resolving before planning.

---

## Technical Unknowns

- [ ] Repo structure — pure skills (SKILL.md only) or hybrid plugin (skills + TypeScript for onboarding/cron)?
- [ ] How does mo-core pull in gtd-mo-core? (extraDirs config, git submodule, npm package, workspace link?)
- [ ] Per-user GTD root path convention — `~/.mo/gtd/<userId>/`? configurable?
- [ ] Cron wiring — mo-core has `src/cron/`. Can skills declare cron schedules, or does the plugin need to register them?

## Design Unknowns

- [ ] How "usable without mo-core" works in practice — same SKILL.md files as Claude Code commands? Just the methodology docs?
- [ ] Phase 2 sync — how do users view/edit GTD files from phone/web when files live on server?
- [ ] How does onboarding work for a new user on the server? (agent creates dirs, or CLI command, or both?)

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
