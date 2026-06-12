---
description: Full pipeline — idea to reviewed code (pm → plan → build → review). Usage: /ship <idea>
---

Run the complete workflow for: $ARGUMENTS

1. `pm` (Opus) → spec. Material open questions → AskUserQuestion me before
   continuing; minor ones the pm records as `assumed:`.
2. `planner` (Opus) → plan, same question rule.
3. `builder(s)` (Sonnet) → implementation on `task/<task-id>`, blocker
   escalation per /build.
4. `reviewer` (Sonnet) → verdict, fix loop per /qa (max 2 cycles).
5. Final report: task-id, branch, what was built, verdict, real test
   output. Do NOT merge or open a PR unless I ask.

S/M tasks only — if the pm sizes it L, stop after the spec and recommend
the split.
