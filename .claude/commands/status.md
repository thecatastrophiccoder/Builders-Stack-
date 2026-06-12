---
description: Reconcile the index with git and report. Usage: /status [task-id]
---

1. `git fetch --all --prune`, then read `.claude/state/index.md` — one read,
   don't open task dirs unless a digest is insufficient or asked.
2. Reconcile: compare each row's Head against `git log -1 origin/task/<id>`
   and list `task/*` branches missing from the index. Report drift (git is
   truth) and fix the index rows.
3. If a task-id was given ($ARGUMENTS): summarize its spec TL;DR, plan steps
   vs. progress, owner freshness, and the next command to run.
4. Otherwise: tasks by status, active/stale claims, blockers, and the single
   most useful next command.
