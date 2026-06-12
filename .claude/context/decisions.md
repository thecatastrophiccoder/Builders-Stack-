# Decision Log (ADR) — Builders' Stack

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

## 2026-06-12 — Generalize into Builders' Stack; rename /review to /qa
**Status:** accepted
**Context:** The workflow outgrew its first host (ckSearch) and now lives at
thecatastrophiccoder/Builders-Stack- as a reusable template. Separately, the
`/review` command name collides with Claude Code's built-in `review` skill
and was silently shadowed — the project command never registered.
**Decision:** Brand context/docs as Builders' Stack with product-agnostic
placeholders; verification command is `/qa`. The task artifact keeps the
name `review.md` and the `reviewer` agent is unchanged — only the slash
command renamed.
**Consequences:** Each host product fills product/architecture TODOs fresh.
New command names must avoid built-in skill names (`review`, `verify`,
`code-review`, `security-review`, `init`, `run`, `loop`, `simplify`) —
check the skill list before adding one.

## 2026-06-12 — MIT license, CI guards, session capability check
**Status:** accepted
**Context:** The template is public and meant for reuse but shipped no
license — default copyright means nobody may legally reuse it. The learning
log already shows rules that rely on voluntary compliance get broken
(same-commit codemap miss; /review shadowing), and holds an open entry
asking for a session-start permissions check after a push was denied at
delivery time.
**Decision:** License the template MIT. Add `.github/workflows/checks.yml`
enforcing in CI: (a) command names must not match built-in skill names —
list extended beyond the previous ADR's eight with the other bare-named
bundled skills observed in-session (`schedule`, `deep-research`,
`update-config`, `keybindings-help`, `fewer-permission-prompts`,
`claude-api`); (b) every backticked codemap row path must exist in the
tree; (c) gitleaks secret scan over full history. Harden `.gitignore`
(keys, credentials, IDE/OS junk, build dirs). Add Bootstrap step 5 to
CLAUDE.md: probe assumed capabilities (push, PRs, network) at session
start. Solo mode is in effect for this repo: the orchestrating session
commits workflow meta-changes directly to main (recorded here once, per
CLAUDE.md → Collaboration → Solo mode).
**Consequences:** Collision and stale-codemap mistakes fail in CI instead
of silently recurring; reusers get real license terms; the open
permissions learning is adopted and closed. The CI built-ins list needs
extending when sessions observe new bundled skill names.
