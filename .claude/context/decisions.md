# Decision Log (ADR) — ckSearch

> Append-only. Newest at the bottom. Written by `pm` and `planner` agents and
> humans. Format:
>
> ```
> ## YYYY-MM-DD — <title>
> **Status:** accepted | superseded by <entry>
> **Context:** why this came up
> **Decision:** what we chose
> **Consequences:** what this commits us to
> ```

## 2026-06-12 — Adopt plan-with-Opus / execute-with-Sonnet agent workflow
**Status:** accepted
**Context:** PM and coding work was ad-hoc; model spend and quality were
inconsistent. Architectural decisions were being made implicitly during
implementation.
**Decision:** All specs and plans are produced by Opus-tier agents (`pm`,
`planner`); all implementation and review by Sonnet-tier agents (`builder`,
`reviewer`). Agents communicate exclusively through files in
`.claude/state/` and `.claude/context/`.
**Consequences:** Plans must be detailed enough for literal execution;
builders must escalate design gaps instead of improvising; task state must
survive session loss.
