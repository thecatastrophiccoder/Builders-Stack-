---
description: Verify a build against its spec and plan (Sonnet reviewer agent). Usage: /review <task-id>
---

Task: $ARGUMENTS

1. Launch the `reviewer` agent for this task.
2. On `request-changes`: launch a `builder` to fix the blocking findings
   (they're in `progress.md → Blockers`), then re-run the reviewer.
   Maximum 2 fix→review cycles, then stop and bring me the sticking points —
   a loop that won't converge means the spec or plan is defective, and that
   is a planning problem, not a typing problem.
3. Relay the verdict and criterion pass/fail table. On approve, remind me
   the merge is mine to make (PR) — agents don't merge.
