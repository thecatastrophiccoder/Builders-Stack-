# ckSearch — Agent Workflow Rules

This repo uses a **plan-with-Opus, execute-with-Sonnet** multi-agent workflow.
Read this file fully before doing any work.

## Model routing (non-negotiable)

| Role | Agent | Model | Why |
|---|---|---|---|
| Product / PM specs | `pm` | opus (or fable if available) | Highest reasoning for requirements & trade-offs |
| Technical planning | `planner` | opus (or fable if available) | Architecture decisions, decomposition |
| Implementation | `builder` | sonnet | Fast, cheap, great at well-specified coding tasks |
| Review / verification | `reviewer` | sonnet | Diff review against an explicit plan is execution work |

Never let the builder make architectural decisions. If a builder hits a
decision the plan doesn't cover, it must stop and write the question into the
task's state file so the planner (Opus) resolves it.

## The loop

1. `/pm <idea>` — PM agent (Opus) turns an idea into a spec → `.claude/state/tasks/<id>/spec.md`
2. `/plan <id>` — Planner agent (Opus) turns the spec into a step plan → `.claude/state/tasks/<id>/plan.md`
3. `/build <id>` — Builder agents (Sonnet) execute plan steps, updating `progress.md` after every step
4. `/review <id>` — Reviewer (Sonnet) checks the diff against spec + plan → `review.md`
5. `/ship <idea>` — runs 1–4 end-to-end for small/medium tasks

## State & context files

- **Context (long-lived, read by every agent before working):**
  - `.claude/context/product.md` — what ckSearch is, users, goals
  - `.claude/context/architecture.md` — system design, stack, key modules
  - `.claude/context/conventions.md` — code style, testing, git rules
  - `.claude/context/decisions.md` — append-only ADR log
- **State (per-task, the single source of truth for a task):**
  - `.claude/state/tasks/<task-id>/spec.md` — what & why (owned by pm)
  - `.claude/state/tasks/<task-id>/plan.md` — how, step by step (owned by planner)
  - `.claude/state/tasks/<task-id>/progress.md` — live status, blockers, decisions made (owned by builder)
  - `.claude/state/tasks/<task-id>/review.md` — findings (owned by reviewer)
  - `.claude/state/board.md` — index of all tasks and their status

Rules:
- Task IDs are kebab-case: `2026-06-12-add-fuzzy-search`.
- Agents communicate **only** through these files, never by assuming chat memory.
- Every agent updates `board.md` when a task changes status:
  `backlog → spec → planned → building → review → done`.
- `decisions.md` is append-only. Never rewrite history.
- Update `progress.md` after **every** completed step, not at the end —
  a crashed/compacted session must be resumable from the files alone.

## Git

- One branch per task: `task/<task-id>`.
- Builder commits after each plan step with message `step N: <summary> [<task-id>]`.
- Never force-push. Never commit secrets. Run tests before every commit.
