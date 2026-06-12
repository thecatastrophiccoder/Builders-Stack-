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

## 2026-06-12 — Three lanes: bias the loop toward building
**Status:** accepted
**Context:** Every ask, however mechanical, paid idea → pm spec → plan —
two Opus hops and two documents before any code. A workflow heavier than
freehand coding gets bypassed, and a bypassed workflow protects nothing.
**Decision:** Triage by ambiguity into three lanes. **Fix** (`/fix`, XS
mechanical): builder direct; its ≤3 steps live in progress.md; no
spec/plan docs; qa capped at one cycle (docs-only may skip, recorded).
**Build** (`/plan <description>`, unambiguous S/M): the coding default —
the planner mints the task and writes a mini-spec (goal + acceptance
checks) into plan.md; no pm hop. **Product** (`/pm`): ambiguous "what",
product-shaped, or L — the full pipeline, unchanged. `/ship` triages
instead of always starting at pm. Escalation is one-way up: a design
choice mid-fix or a material "what" question mid-plan stops the line and
re-routes; ask-before-execute holds in every lane.
**Consequences:** pm specs only what is genuinely ambiguous; most coding
starts at the planner or the builder. Branch, claim, index row, per-step
pushes, tests, codemap, and review are identical in all lanes — lanes
drop documents, not discipline. Index rows may enter mid-chain (Build at
`planned`, Fix at `building`).

## 2026-06-12 — Mid-project adoption: /adopt derives the context pack
**Status:** accepted
**Context:** The stack assumed greenfield — context files shipped as
hand-fill TODOs and the codemap mapped only workflow files. Real adoption
usually happens mid-project, where hand-typing the context pack is
exactly the ceremony this workflow exists to kill — and clobbering
existing CLAUDE.md/context content, or importing a backlog nobody asked
for, would poison trust on day one.
**Decision:** `/adopt` bootstraps into an existing codebase: planner and
pm (Opus, adopt modes, parallel on disjoint files) derive architecture,
codemap (the real tree), conventions (detected test/lint commands),
product, and up to 3 "observed" ADRs — from manifests, lockfiles, CI
configs, entry points, and docs, never from "typical project" memory.
Lines inferred without hard evidence carry a `confirm:` prefix and return
to the user as one batched question list. Merge, never clobber.
Pre-existing branches/PRs join the index onboard-as-you-go — when a lane
first touches them. The gitleaks full-history caveat is relayed at
adoption.
**Consequences:** Day-one adoption cost is one command plus one confirm
batch; context starts from evidence, humans confirm instead of typing;
the index never claims work it doesn't own.

## 2026-06-12 — Name the lane on plain-language asks (suggest, never block)
**Status:** accepted
**Context:** Operators forget command names or don't know them yet; a
workflow only reachable by memorized syntax gets bypassed, and a session
that quizzes the user before working becomes a gate.
**Decision:** When a task arrives in plain language, the orchestrating
session triages it against the lane table, names the routing in one line
("Build-lane work; running it as `/plan <desc>`"), and proceeds. Unsure
between lanes → one short question with a recommendation. Operator
declines the workflow → honor it and record the deviation. Never refuse,
never require command syntax.
**Consequences:** Commands are discoverable through use; plain language
is a first-class entry point; the suggestion is informational, the work
proceeds either way.
