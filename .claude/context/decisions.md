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

## 2026-06-12 — Single index + claim protocol for multi-session collaboration
**Status:** accepted
**Context:** Multiple sessions and people work concurrently (local +
GitHub); a separate board and index would drift; full-tree reads waste
tokens in every subagent.
**Decision:** `.claude/state/index.md` is the only task map (no board),
carrying owner, branch head SHA, claimed paths, and a ≤15-word digest per
task — updated in the same commit as the change it describes. Git is ground
truth; the index is the cache, reconciled by `/status`. Ownership = claim in
index + progress, pushed; stale after ~2h without a pushed commit, then
takeover via the session log. `codemap.md` maps path → purpose and updates
in the same commit as structural changes.
**Consequences:** Agents read index/codemap first and open task files only
when digests fall short; per-step pushes are mandatory; reviewers reject
commits that skip index/codemap updates.
