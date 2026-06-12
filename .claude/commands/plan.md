---
description: Turn a spec into a step-by-step implementation plan (Opus planner agent). Usage: /plan <task-id>
---

Task: $ARGUMENTS

1. Check `.claude/state/index.md` for the task. No `spec` row → list
   available tasks and stop.
2. Launch the `planner` agent. It explores the codebase (codemap first),
   writes `plan.md`, fills Claimed paths, and moves the index row to
   `planned`.
3. If the planner surfaces questions (spec ambiguity, claimed-path collision
   with an active task), ask me with AskUserQuestion — planner's
   recommendation first — and send my answers back to the same planner
   agent to finish.
4. Relay: step count, parallel groups, claimed paths, risks. Suggest
   `/build $ARGUMENTS`.
