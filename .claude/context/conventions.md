# Conventions — Builders' Stack

> Read by every agent and human. The builder follows these literally; the
> reviewer enforces them.

## Code structure (non-negotiable)
- **Small, single-purpose functions.** A function does one thing; if the
  name needs "and", split it.
- **Pure core, effectful edges.** Business logic takes data in, returns
  data out; I/O, network, and datastore writes live in thin outer layers.
  This is what makes the core unit-testable.
- **Modules own one concern** with an explicit interface; cross-module
  reach-ins are review failures.
- **Duplication over wrong abstraction, but not three times.** Extract on
  the second real caller, never speculatively.
- **Efficiency is specified, not vibes.** Budgets (latency, memory,
  storage size) come from the spec; meet them, and don't micro-optimize
  where no budget exists.

## Code style
- Match the surrounding file over any global rule.
- No dead code, no commented-out blocks, no TODOs without a task-id.
- Comments state non-obvious constraints only — never narrate the code.

## Documentation discipline
- New/moved/repurposed file → its `.claude/context/codemap.md` row updates
  in the **same commit**.
- Every state file keeps its TL;DR line current — that line is what other
  sessions read instead of the file.
- Public interfaces get a contract docstring (inputs, outputs, errors).

## Testing
- TODO: test command (e.g. `npm test`, `pytest`).
- Every behavior change ships with a test; bug fixes ship with a regression
  test that fails before the fix.
- Never weaken or delete a failing test to get green — that's a blocker,
  not a fix.

## Lint / format
- TODO: lint command. Run before every commit.

## Git & collaboration
- Branch per task `task/<task-id>`; claim it in `index.md` before building.
- Commit per plan step `step N: <summary> [<task-id>]`; **push every step**.
- Sync before work: `git fetch origin` + rebase task branch on default.
- Merge via PR after review approval. Never merge your own unreviewed work.
- Never force-push shared branches. Never commit secrets or `.env`.

## Dependencies
- Adding a dependency is a **planning** decision (Opus), never a builder
  improvisation.
