---
description: Full pipeline — idea to reviewed code (pm → plan → build → review). Usage: /ship <idea>
---

Run the complete workflow for: $ARGUMENTS

1. `pm` agent (Opus) → spec. If it returns open questions that materially
   change scope, stop and ask me; for minor ones, make the conservative call
   and record it in the spec's Open Questions section as "assumed: ...".
2. `planner` agent (Opus) → plan.
3. `builder` agent(s) (Sonnet) → implementation on `task/<task-id>`,
   following the blocker-escalation rule from /build.
4. `reviewer` agent (Sonnet) → review, with the fix loop from /review.
5. Final report to me: task-id, branch, what was built, review verdict,
   test output. Do NOT merge or open a PR unless I ask.

Use this only for S/M tasks. If the pm sizes it L, stop after the spec and
recommend splitting.
