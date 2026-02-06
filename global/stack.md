# Stack Decisions

Canonical technology choices and rationale.

---

| Choice | Decision | Rationale |
|--------|----------|-----------|
| Language | TypeScript | Type safety, ecosystem, team familiarity |
| Package manager | bun | Fast, built-in bundler, TS-first |
| Runtime | Node | Broad compatibility, Heroku support |
| Backend | Heroku | Simple deploys, managed Postgres |
| Database | PostgreSQL | ACID, JSON support, extensions |
| Frontend | React + Tailwind | Component model, utility-first CSS |
| Testing | Playwright (E2E), Jest/Vitest (unit) | Browser automation, fast unit tests |
| Docs | Markdown | Universal, greppable, version-friendly |
