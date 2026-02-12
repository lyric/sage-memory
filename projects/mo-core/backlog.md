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

## Parked

## Promoted
