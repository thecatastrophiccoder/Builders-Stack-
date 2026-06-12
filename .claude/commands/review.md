---
description: Verify a build against its spec and plan (Sonnet reviewer agent). Usage: /review <task-id>
---

Task: $ARGUMENTS

1. Launch the `reviewer` agent for this task.
2. If the verdict is `request-changes`: launch a `builder` agent to fix the
   blocking findings (they're in `progress.md` Blockers), then re-run the
   reviewer. Maximum 2 fix-review cycles before escalating to me with the
   sticking points.
3. Relay the final verdict and the criterion pass/fail table.
