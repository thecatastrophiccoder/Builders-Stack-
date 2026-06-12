---
description: Show the task board and any blockers. Usage: /status [task-id]
---

If a task-id was given ($ARGUMENTS), read that task's `spec.md` size line,
`plan.md` step list, and `progress.md`, and summarize where it stands and
what's next.

Otherwise read `.claude/state/board.md` and every `blocked`/`building` task's
`progress.md`, and give me: a table of tasks by status, active blockers, and
the single most useful next command to run.
