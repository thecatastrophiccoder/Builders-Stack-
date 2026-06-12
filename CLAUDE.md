# Builders' Stack — Agent Operating Manual

Written and maintained as the staff engineer owning this workflow. Every rule
here paid for itself in a past failure. Follow them. If one is wrong, change
it via PR with an ADR entry — don't quietly ignore it.

## Bootstrap — fresh agent or fresh session starts here

Read, in order, and nothing else first:

1. **This file** — the rules.
2. **`.claude/state/index.md`** — the live map: every task, its status, owner,
   branch, head SHA, one-line digest. One read tells you the state of the
   world. Open deeper files only when a digest isn't enough — that is how we
   stay token-cheap.
3. **`.claude/context/`** — `product.md`, `architecture.md`, `conventions.md`,
   `codemap.md`, `decisions.md`. Long-lived truth. `codemap.md` replaces
   exploratory globbing: read the map, not the tree.
4. **Your task's dir** `.claude/state/tasks/<id>/` — spec, plan, progress,
   review.
5. **Capability check** — probe what the session's work will assume, now,
   not at delivery: can you push (`git push --dry-run`)? Open PRs? Reach
   the network? Surface any gap to the user as a one-line checklist before
   building on top of it.

All working state lives in these files, in the repo, committed. Chat memory
is not storage: sessions crash, compact, and end. If the next session needs
something you learned, write it to the right file before you stop. If you
cannot push, the files still serve local resume — commit anyway.

## Model routing (non-negotiable)

| Role | Agent | Model | Why |
|---|---|---|---|
| Product / PM specs | `pm` | opus (or fable if available) | Requirements & trade-offs need the deepest reasoning |
| Technical planning | `planner` | opus (or fable if available) | Architecture, decomposition, risk |
| Implementation | `builder` | sonnet | Fast, cheap, excellent at well-specified steps |
| Review / verification | `reviewer` | sonnet | Diff-vs-plan checking is execution work |

The builder never makes architectural decisions. A plan that forces it to is
a defective plan — route the gap back to the planner.

## Ask before you execute

A wrong assumption costs more than a question. Before acting:

- **Ask the user** when the answer changes scope, architecture, data
  contracts, dependencies, or anything user-visible — and the spec/context
  files don't already answer it.
- **Decide and record** when it's trivia: make the conservative call and
  write it down (`assumed:` in the spec, `Deviations` in progress, or an ADR).
- Subagents can't reach the user. They write questions into their state file
  (with a recommended answer each) and return; the orchestrating session asks
  the user and routes answers back through the files.
- Never plan on top of an unresolved material question. Never bury a question
  inside a requirement.

## Enable, don't gate

The defaults here are guardrails, not handcuffs. If the user overrides a
rule — merge without review, skip the spec, commit straight to default —
state the trade-off in one or two lines, record it (`Deviations`, or an ADR
if durable), and **proceed**. Don't refuse, re-litigate, or stall their
workflow.

- Hard stops are reserved for the genuinely destructive or irreversible:
  force-pushing shared branches, rewriting pushed history, deleting
  branches/data, committing secrets.
- Everything safe and reversible on your side, do without waiting. Never
  leave work uncommitted because a question is pending — commit, note the
  open question in the state file, move on. Blocked on one front? Advance
  every other front you can.

## The loop

1. `/pm <idea>` — PM (Opus) → `spec.md`; asks the user through the
   orchestrator if the idea is ambiguous.
2. `/plan <id>` — Planner (Opus) → `plan.md`, file-level steps.
3. `/build <id>` — Builder(s) (Sonnet) execute steps; progress + push after
   every step.
4. `/qa <id>` — Reviewer (Sonnet) → `review.md`, verdict. (Named `/qa`
   because `/review` collides with a built-in skill and gets shadowed.)
5. `/ship <idea>` — 1–4 end-to-end, small/medium tasks only.
6. `/status` — reconcile index against git and report.

## State, context, and the index

- **Context (long-lived):** `.claude/context/{product,architecture,
  conventions,codemap,decisions}.md`. `decisions.md` is append-only.
- **State (per-task):** `.claude/state/tasks/<id>/{spec,plan,progress,
  review}.md`, created from `.claude/state/templates/`.
- **Index:** `.claude/state/index.md` — the single task map. There is no
  separate board; one source of truth or none.

Index discipline — this is what keeps collaboration and token spend sane:

- Any commit that changes a task's status, owner, or claimed paths updates
  `index.md` **in the same commit**. A stale index is a lying index.
- Git is the ground truth; the index is the cheap cache. Each row carries the
  task branch's head SHA — if `git log -1 origin/task/<id>` disagrees, trust
  git and fix the row.
- `codemap.md` gets its row added/updated in the same commit that creates,
  moves, or repurposes a source file. Reviewers reject structure changes
  that skip this.
- Every state file opens with a one-line **TL;DR**. Keep it current; most
  readers must be able to stop there.

## Learning log — the workflow improves itself

`.claude/state/learnings.md` is the append-only log of mistakes,
shortcomings, edge cases, and ideas. It exists because the same failure paid
for twice is a process bug, not bad luck.

- **When to log:** before you finish a work session — and whenever you
  reconnect to git after a stretch of work — ask: did anything fight me? A
  rule that got in the way, a gap the plan/spec/templates didn't cover, an
  assumption that turned out wrong, wasted retries, a case nobody
  anticipated. If yes, append an entry (format is at the top of the file).
- **How to log:** facts and context, no blame. Enough detail that a human
  reading it cold, weeks later, can act on it. Include the concrete
  improvement you'd suggest — an entry without a suggestion is half an entry.
- **Logging is not asking.** Don't wait for a response; record it, push it,
  move on. Material questions still go through the ask-before-execute
  protocol — the log is for what's already happened.
- **The loop closes:** humans and the pm/planner review the log
  periodically; adopted entries become ADRs or rule changes via PR, and the
  entry's Status is updated to point at them.

## Collaboration — many sessions, many people

Several humans and agents work this repo concurrently, locally and on GitHub:

- **One task = one branch `task/<id>` = one owner at a time.** Claiming =
  writing your identity (email or session id) into the Owner fields of
  `index.md` and the task's `progress.md`, committing, and pushing the task
  branch. A pushed branch with recent commits IS the visible claim signal.
- **Claims go stale after ~2h without a pushed commit.** Anyone may take
  over: read `progress.md`, append a takeover line to its Session log,
  re-claim, continue from the last green step. This is why progress is
  written per-step, never at the end.
- **Sync before work, push after every step.** `git fetch origin`, rebase the
  task branch on the default branch, then build. Step commits are pushed
  immediately so other sessions see real state, and a dead session loses at
  most one step.
- **File claims prevent collisions.** A plan declares the paths it touches;
  the index records them. Two tasks claiming overlapping paths never run in
  parallel — the planner serializes them (`waits-on:<id>` in the digest).
- Merging to the default branch happens via PR after the reviewer approves.
  Nobody merges their own unreviewed work.
- **Solo mode.** One human + agents is still multi-session: keep the claims,
  per-step pushes, and the index — they're what make tomorrow's session
  cheap. You may relax PR-only merging (merge your own task branch after a
  green review) — record that choice once as an ADR and the workflow honors
  it without nagging.
- Restricted sessions (e.g. remote env that can only push one branch): keep
  commits on the permitted branch, record the intended `task/<id>` in
  `progress.md`, and reconcile on merge.

## Git

- Branch per task: `task/<task-id>`. IDs are kebab-case with date:
  `2026-06-12-add-fuzzy-search`.
- Builder commits per plan step: `step N: <summary> [<task-id>]`. State-only
  commits: `spec|plan|review: <summary> [<task-id>]`.
- Run tests before every commit. Never force-push shared branches. Never
  commit secrets.
