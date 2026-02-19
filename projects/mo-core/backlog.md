# Mo-Core Backlog

## Queued

- **per-skill-reasoning-level** — Allow skills to declare a `reasoningLevel` in their metadata, automatically enabling extended thinking for skills that need deeper reasoning (e.g. mind-sweep, complex analysis)
  - Tags: skills, reasoning, langfuse
  - Added: 2026-02-10 (during: langfuse-input-output)
  - Notes: Add `reasoningLevel` to `MoSkillMetadata` in `src/agents/skills/types.ts`. Override session reasoning level during skill invocation in `src/auto-reply/reply/directive-handling.impl.ts`. Same pattern could later support per-skill `contextTokens`, `modelOverride`, `verboseLevel`, etc.

- **otel-register-log-transport-jiti-fix** — Fix jiti dual-instance bug for `registerLogTransport` in the OTEL diagnostics plugin (same pattern as the `onDiagnosticEvent` fix)
  - Tags: otel, diagnostics, jiti
  - Added: 2026-02-10 (during: langfuse-input-output)
  - Notes: OTEL plugin imports `registerLogTransport` from `mo/plugin-sdk` which resolves to a separate module instance under jiti. Fix: pass through `MoPluginServiceContext` like we did for `onDiagnosticEvent`.

- **hosted-server-ready** — Make mo-core ready to run as a hosted server (multi-tenant, cloud deployment, not just local/self-hosted gateway)
  - Tags: infra, hosting, deployment
  - Added: 2026-02-10
  - Notes: Review upstream openclaw/openclaw issues for headless/server-related bugs and feature requests — selectively pull relevant ones. Known upstream issues to review: #11805 (gateway install/status fails on headless servers due to systemctl --user requiring D-Bus), and any others tagged with server/headless/VPS/EC2/systemd.
  - Upstream review process: `upstream` and `openclaw` remotes removed from mo-core (2026-02-19) to prevent accidental pulls overwriting Mo rebrand. To review upstream: browse GitHub (openclaw/openclaw), use `gh` CLI, or read the local clone at `~/src/other/upstream/openclaw`. Never re-add upstream as a remote — cherry-pick by reading diffs and manually applying.

- **review-plugins-extensions** — Review Mo plugins and extensions — remove unused ones, review Pi marketplace for useful additions
  - Tags: plugins, extensions, cleanup
  - Added: 2026-02-13 (during: gtd-mo-core)

- **telegram-polling-auto-recover** — Telegram polling should auto-recover from getUpdates timeout errors instead of stopping permanently
  - Tags: telegram, reliability, polling
  - Added: 2026-02-17
  - Notes: The grammyjs runner has a `maxRetryTime` of 5 minutes. When it gives up, the resulting timeout error ("Request to 'getUpdates' timed out after 500 seconds") isn't recognized as recoverable by `isRecoverableTelegramNetworkError` / `isNetworkRelatedError` in `src/telegram/network-errors.ts`, so `monitorTelegramProvider` in `src/telegram/monitor.ts` treats it as a permanent failure and stops the channel. Fix: (1) classify timeout-class errors from the runner as recoverable so the outer retry loop retries, and (2) consider increasing or removing the runner's `maxRetryTime` ceiling.

## Parked

## Promoted
