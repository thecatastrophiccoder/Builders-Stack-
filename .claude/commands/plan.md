---
description: Turn a spec into a step-by-step implementation plan (Opus planner agent). Usage: /plan <task-id>
---

Task: $ARGUMENTS

1. Verify `.claude/state/tasks/$ARGUMENTS/spec.md` exists; if not, list
   available tasks from `.claude/state/board.md` and stop.
2. Launch the `planner` agent for this task. It must explore the codebase,
   write `plan.md`, and update the board to `planned`.
3. Relay the plan summary: step count, parallelizable steps, risks.
   Suggest `/build $ARGUMENTS` as the next move.
