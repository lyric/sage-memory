# Multi-Tenant: Upstream Issue Research

Researched: 2026-02-19
Source: openclaw/openclaw GitHub issues + branches

## Overview

Multi-tenancy in openclaw means running multiple agents on one gateway, each serving different users/orgs. The current architecture has significant isolation gaps. These issues document what needs to be addressed for a secure multi-tenant Mo deployment.

---

## Upstream Issues (by category)

### Platform Prerequisites

| Issue | Title | Summary |
|-------|-------|---------|
| #10004 | Platform prerequisites for multi-agent per-org isolation | Comprehensive checklist: session visibility scoping, agent-to-channel binding, cron ownership, workspace isolation, credential scoping. Each agent must only see/modify its own resources. |
| #7827 | Default safety posture: sandbox & session isolation | Sandbox is off by default; DM sessions collapsed not isolated; proposes safer defaults for new installs. |
| #7139 | Default config provides zero isolation | Unrestricted filesystem access and plaintext credentials by default. |

### Agent Scoping (tool-level isolation)

| Issue | Title | Summary |
|-------|-------|---------|
| #20305 | **SECURITY**: message tool cross-user sends | Any agent can message any user. Confirmed in live audit (~230 agents). Critical. |
| #16053 | Cron jobs should be agent-scoped | Cron jobs are gateway-level with no agentId. Agent A's crons can fire in Agent B's context. Proposes adding `agentId` field. |
| #15325 | memory-lancedb per-agent isolation | All memories in one shared table. Agent A recalls Agent B's memories. Fix: add `agentId` to `MemoryEntry` + scope queries. |
| #19330 | Inbound media shared across all agents | Media saved to single shared dir. No per-agent subdirectory. Fix: subdirectory by bot account ID at save time. |
| #18565 | Per-user context files | Single agent serving multiple users needs per-user USER.md (not per-agent). Proposes `users/` directory with peer ID mapping in frontmatter. |

### Access Control

| Issue | Title | Summary |
|-------|-------|---------|
| #8081 | RBAC — role-based access control | Admin/Standard/Guest roles. Credential visibility per role. Per-user config sections. Auth modes (token, OAuth). |
| #12530 | Missing RBAC enables full API access (molta) | Third-party UI has no permission model — full API access to anyone who can reach the gateway. |
| #13676 | First-class Projects in gateway dashboard | Workspace + skill scoping + runtime constraints per project. |

### Sandbox & Security

| Issue | Title | Summary |
|-------|-------|---------|
| #12505 | Unified sandbox with multi-platform support + tiered presets | Consolidate sandbox implementations across platforms. |
| #10890 | Skill security framework — permission manifests, signing, sandboxing | Skills need declared permissions, signatures for trust, and sandboxed execution. |
| #11829 | Protecting API keys from agent access | Agents can read env vars / config containing API keys. Needs isolation. |
| #10992 | Sub-agents bypass exec approvals for safeBins | Nested agent calls skip approval checks for "safe" binaries. |
| #17931 | Mount skill directories read-only in sandbox | Skills currently have write access in sandbox containers. |
| #8566 | Sandbox browser runs Chromium as root with --no-sandbox | Security issue in Docker sandbox. |
| #7575 | Sysbox Docker runtime for secure container isolation | Proposes Sysbox for nested container support without privileged mode. |
| #12512 | SKILL.md instruction isolation from agent context | Skill instructions can leak into/from agent context. |

---

## Upstream Branches (security/isolation)

| Branch | Latest Commit |
|--------|--------------|
| `client-side-security-initial` | feat: add client-side skill security enforcement |
| `security/council-recommendations` | security: apply Agents Council recommendations; require gateway auth by default; harden VPS install defaults |
| `security/gateway-exposure-check` | harden doctor gateway exposure warnings; deprecate query param hook token auth |
| `codex/cron-session-scoped-reminders` | Session-scoped cron reminders (addresses part of #16053) |
| `fix/3805-message-tool-sandbox-bypass` | Message tool sandbox bypass fix (addresses part of #20305) |
| `fix/elevated-ask-security` | Elevated exec security fix |
| `fix/security-audit-gateway-auth` | Gateway auth security audit |
| `fix/security-sanitize-env-vars` | Env var sanitization for sandbox |

---

## Priority Order for Mo Multi-Tenant Work

### Must-have (before any multi-tenant deployment)
1. **Message tool scoping** (#20305) — agents must only message their own users
2. **Cron job scoping** (#16053) — cron jobs must be agent-scoped
3. **Session visibility scoping** (#10004 item 1) — agents must not see each other's sessions
4. **Gateway auth by default** (security/council-recommendations branch)

### Should-have (for production multi-tenant)
5. **Memory isolation** (#15325) — per-agent memory tables/scoping
6. **Media isolation** (#19330) — per-agent inbound media directories
7. **Workspace isolation** (#10004 item 4) — per-agent workspace directories
8. **Credential scoping** (#10004 item 5) — per-agent OAuth/API key resolution
9. **Sandbox defaults** (#7827) — sandbox on by default for multi-tenant

### Nice-to-have (for enterprise/teams)
10. **RBAC** (#8081) — role-based access control
11. **Skill security framework** (#10890) — permission manifests + signing
12. **Per-user context** (#18565) — USER.md per user within an agent
13. **Agents Plane** (#17299) — full provisioning and K8s/VM isolation
