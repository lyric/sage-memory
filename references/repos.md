# Reference Repositories

Repos with patterns worth reusing. Linked, not copied -- read the notes file for key takeaways.

---

## CLI Patterns

- **bridge-cli** (`~/src/crunchy/bridge-cli`) -- Crystal CLI for Crunchy Bridge
  - Command-per-file pattern in `src/cb/`
  - Fish shell completions, Nix flake
  - Good reference for: CLI structure, command routing, shell integration

## API Patterns

- **bridge-platform** (`~/src/crunchy/bridge-platform`) -- Go HTTP API for Crunchy Bridge
  - sqlc for type-safe SQL, direnv for env management
  - Heroku deployment, ~66 packages
  - Good reference for: Go API structure, sqlc patterns, Heroku deploys

## Dashboard Patterns

- **bridge-raven** (`~/src/crunchy/bridge-raven`) -- TypeScript/Remix internal admin dashboard
  - Remix SSR, Tailwind, Recharts, Axios API client, Redis sessions
  - Good reference for: Remix app structure, TypeScript dashboard, data visualization

## Context Engineering

- **humanlayer** (`~/src/lyric/humanlayer`) -- AI coding agent orchestration
  - Thoughts system: separate git repo, symlinks, searchable dirs, profiles
  - Agent system: specialized sub-agents with context routing
  - Handoff/resume commands for session persistence
  - Good reference for: context persistence patterns, agent orchestration, research workflows

## MCP Patterns

- **mcp-snowflake-service** (`~/src/mcp/mcp-snowflake-service`) -- Python MCP server for Snowflake
  - Clean single-file server pattern
  - Good reference for: MCP server implementation

## AI Analysis

- **zendesk-ticket-analysis** (`~/src/zendesk-ticket-analysis`) -- AI ticket categorization
  - Streamlit dashboards, LLM pipelines, Snowflake deployment
  - Good reference for: AI/LLM analysis pipelines, Streamlit apps
