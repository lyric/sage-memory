# Server Deployment: Upstream Issue Research

Researched: 2026-02-19
Source: openclaw/openclaw GitHub issues + local upstream clone + pi-mono

## What Mo-Core Already Has
- `Dockerfile`, `Dockerfile.sandbox`, `Dockerfile.sandbox-browser`
- `docker-compose.yml`
- `scripts/bundle-deploy.sh` — bundles repo + `~/.mo` state into a tarball with a `deploy.sh` helper
- `scripts/docker/` — test/smoke scripts
- `src/daemon/` — systemd + launchd service management code

## pi-mono
- Shares same upstream history as mo-core/openclaw. No unique server deployment content.

---

## Upstream Issues to Review (by priority)

### Critical — Security

| Issue | Title | Notes |
|-------|-------|-------|
| #20305 | `message` tool allows cross-user sends in multi-tenant | Confirmed in live security audit (~230 agents). Any agent can message any user. `cron list` exposes all user IDs. No clean workaround. |

### High — systemd / Linux Service Management

| Issue | Title | Notes |
|-------|-------|-------|
| #11805 | `gateway status/install` fails on headless EC2/VPS | `systemctl --user` requires D-Bus session bus + `XDG_RUNTIME_DIR`. Affects all headless Linux. Proposed: `--system` flag or auto-detect. |
| #17309 | systemd ExecStart path breaks after pnpm upgrade | pnpm content-addressed store path changes on upgrade. `--force` flag silently ignored. Service reached 412 restart attempts. |
| #19410 | Gateway token baked into systemd unit, goes stale | Token hardcoded in unit file; config changes don't update it. Causes persistent token mismatch. |
| #4832 | `node install` enables wrong systemd service name | `installSystemdService()` hardcodes gateway service name regardless of context. |
| #15316 | `doctor --fix` writes nonexistent nvm PATH into unit | No existence check on PATH dirs before writing to systemd unit. |

### High — Headless exec-approvals

| Issue | Title | Notes |
|-------|-------|-------|
| #17322 | `system.run` hangs on headless gateway | exec.approval.request hangs 120s — no companion approval socket on headless. |
| #6874 | `exec-approvals.sock` never created on headless Linux | All `nodes run` commands timeout after 35s. Config allowlist has no effect. |

### High — macOS LaunchAgent

| Issue | Title | Notes |
|-------|-------|-------|
| #14161 | SIGUSR1 restart triggers launchd crash-loop throttle | Rapid restarts cause launchd to silently unload the service. |
| #13340 | Plist gets `__REDACTED__` env vars on install | `collectConfigEnvVars()` reads already-redacted config object. All env-dependent tools fail. |
| #13258 | LaunchAgent plist not updated during `update` | Stale plist after upgrade (path drift). |

### Medium — Docker / Container

| Issue | Title | Notes |
|-------|-------|-------|
| #5559 | Docker CLI can't reach gateway (loopback networking) | Multiple compounding issues: loopback bind, token mismatch, file permissions. |
| #11131 | Docker CLI container defaults to 127.0.0.1 | Needs `--url` and `--token` flags; docs don't explain. |
| #14439 | Docker deployment guide needed (with browser automation) | Working architecture contributed; key pitfalls documented. |
| #18625 | chromium snap stub fails in Docker containers | Ubuntu 22.04 specific. Workaround: playwright install chromium. |
| #17504 | Docker: unknown command `dist/index.js` | Entrypoint path issues. |
| #17584 | Browser port 18791 never binds in Docker | Browser automation port binding issue. |
| #8273 | Sandbox browser unreachable from agent container | Browser binds to container localhost only. |

### Medium — macOS LaunchAgent (additional)

| Issue | Title | Notes |
|-------|-------|-------|
| #17460 | LaunchAgent NECP blocks LAN access | `spawn type = daemon (3)` restricts LAN TCP. Fix: set `ProcessType` in plist. Also notes SSRF guard blocks private IPs with no user config. |
| #20030 | Restart fails after unload — wrong launchctl sequence | bootstrap/bootout sequence bug. |
| #3780 | `gateway stop` uses `bootout`, breaking `gateway start` | `bootout` vs `unload` prevents re-loading. |

### Feature — Multi-tenant Architecture

| Issue | Title | Notes |
|-------|-------|-------|
| #17299 | "Agents Plane" — multi-tenant provisioning and isolation | VM-per-agent or K8s namespace. Per-agent GCP service accounts, Secret Manager, VPC/firewall, IAP tunnel. |
| #11973 | systemd WatchdogSec for gateway hang recovery | Integrate `WatchdogSec` + `Type=notify`. Existing 30s tick could carry heartbeat. |

---

## Upstream Branches (server-related)

- `ci/build-docker-image`
- `docs/fly-private-deployment`
- `docs/northflank-deploy-guide`
- `fix-sandbox-docker-error-handling-*`
- `fix/dockerfile-install-bun`
- `fix/node-daemon-run`
- `fix/synology-docker`
- `fix/systemd-execstart-whitespace`

---

## Upstream Commits (server-related, recent 500)

Key commits in upstream not yet in mo-core (pulled 2026-02-19):

**Security (high priority for server):**
- `638853c6d` — sanitize sandbox env vars before docker launch
- `5487c9ade` — sandbox env sanitization helpers + tests
- `d51929ecb` — block ISATAP SSRF bypass via shared host/ip guard
- `d05c8eb91` — unify SSRF hostname/ip precheck and add policy regression
- `35016a380` / `cc29be8c9` — serialize sandbox registry mutations (race condition fixes)

**Daemon/service refactoring:**
- `1b46f7d0b` — simplify gateway service backend delegates
- `70900feaa` — share service arg types across backends
- `9fd810e3a` — share systemd service action flow
- `111a24d55` — scope token drift warnings
- `11fcbadec` — guard preferred node selection

**Docker:**
- `466a1e1cd` — include `docker-compose.extra.yml` in helper commands
- `7255c20dd` — harden docker-setup mount validation
- `ca43efa96` — force npm install path in smoke docker tests

**Older (pre-Feb 2):**
- Docker fixes: gateway CMD, `--bind lan` removal, cloud-compatible flags, typing fixes
- systemd: legacy service migration, ExecStart whitespace fix
- Docs: Fly private deployment, Northflank guide, Oracle Cloud guide, Docker setup clarification
- Security: PATH injection prevention in docker sandbox, host exec env validation

---

## Next Steps (when we start this work)

1. **Security first**: Review #20305 (cross-user message sends) — critical for any multi-tenant deployment
2. **Headless Linux**: Fix systemd user bus detection (#11805) and exec-approvals on headless (#17322, #6874)
3. **Docker hardening**: Fix CLI-gateway networking (#5559, #11131), entrypoint paths (#17504)
4. **Service stability**: Token drift (#19410), pnpm path breakage (#17309), launchd crash-loop (#14161)
5. **Existing assets**: We already have `bundle-deploy.sh`, Dockerfiles, and docker-compose — review and update for Mo branding
