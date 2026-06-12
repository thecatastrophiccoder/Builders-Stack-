# Builders' Stack

> **A note from Anmol (the human here):** this repo is my experiment in
> running a real product workflow on a team of AI agents — I bring the ideas
> and the taste, they bring the planning and the typing. Everything they do
> is written down in files you can read, including their own mistakes (see
> the learning log below — they're required to confess). Steal whatever's
> useful, and if you spot something dumb, odds are it's already logged.

Hey! This repo runs on a multi-agent Claude Code workflow. The short
version: **smart expensive models do the thinking, fast cheap models do the
typing, and everything lives in files** — so any person or agent can pick up
exactly where the last one stopped.

## How it works (30 seconds)

```
idea ──/pm──▶ spec ──/plan──▶ plan ──/build──▶ code ──/qa──▶ verdict
        Opus          Opus           Sonnet          Sonnet
```

- **Opus** (or Fable) writes the spec and the step-by-step plan — all the
  decisions get made here.
- **Sonnet** implements the plan literally and reviews the result — no
  improvising, no "creative" architecture at 2am.
- If anything's ambiguous, the workflow **asks you** instead of guessing.
  If you overrule a rule, it notes the trade-off and gets out of your way.

## Try it

```
/ship add fuzzy matching to merchant search   # the whole pipeline, one shot
/status                                       # what's going on right now
```

Or run the stages yourself: `/pm` → `/plan <task-id>` → `/build <task-id>`
→ `/qa <task-id>`.

## Where things live

| You want... | Look at... |
|---|---|
| The rules | `CLAUDE.md` (agents read this first, you should too) |
| What's in flight | `.claude/state/index.md` — every task, one line each |
| What the code does | `.claude/context/codemap.md` — map, not the territory |
| Why we did X | `.claude/context/decisions.md` — the ADR log |
| What went wrong & what we learned | `.claude/state/learnings.md` — agents log their own misses |
| A task's full story | `.claude/state/tasks/<id>/` — spec, plan, progress, review |

## Working with others (or just future-you)

Every task gets its own branch (`task/<id>`) and one owner at a time. Agents
push after every step, so if a laptop dies or a session ends, the next one
loses at most one step. Claims older than ~2 hours without a push are fair
game for takeover. Solo? Same flow, you can just skip the PR formality
(there's an ADR for that).

## Before your first real task

Fill in the TODOs in `.claude/context/product.md`, `architecture.md`, and
the test/lint commands in `conventions.md`. The planners are only as good
as what's in those files — garbage in, expensive garbage out.

More detail in [`docs/WORKFLOW.md`](docs/WORKFLOW.md).
