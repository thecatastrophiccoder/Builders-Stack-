---
description: Turn an idea into a spec (Opus pm agent). Usage: /pm <idea>
---

Launch the `pm` agent with this idea: $ARGUMENTS

The agent must produce `.claude/state/tasks/<task-id>/spec.md` and update
`.claude/state/board.md`. When it returns, relay to me: the task-id, the spec
summary, and any open questions. If there are open questions, ask me to
resolve them before suggesting `/plan <task-id>`.
