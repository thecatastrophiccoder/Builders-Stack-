# Conventions — ckSearch

> Read by every agent. The builder follows these literally.

## Code style
- Match the style of the surrounding file over any global rule.
- No dead code, no commented-out blocks, no TODOs without a task-id.
- Comments only for non-obvious constraints, never to narrate the code.

## Testing
- TODO: test command (e.g. `npm test`, `pytest`).
- Every behavior change ships with a test. Bug fixes ship with a regression
  test that fails before the fix.
- Never weaken or delete a failing test to get green — that's a blocker,
  not a fix.

## Lint / format
- TODO: lint command. Run before every commit.

## Git
- Branch per task: `task/<task-id>`.
- Commit per plan step: `step N: <summary> [<task-id>]`.
- Never force-push. Never commit secrets, `.env`, or credentials.

## Dependencies
- Adding a dependency is a **planning** decision (Opus), never a builder
  improvisation.
