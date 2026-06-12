---
description: Full pipeline with triage — idea to reviewed code via the right lane. Usage: /ship <idea>
---

Run the complete workflow for: $ARGUMENTS

0. Triage the lane (CLAUDE.md → The loop) and say which in one line:
   XS/mechanical → the /fix path; unambiguous S/M → start at the planner
   (mini-spec in plan.md, no pm); ambiguous or product-shaped → pm first.
1. `pm` (Opus) — Product lane only → spec. Material open questions →
   AskUserQuestion me before continuing; minor ones recorded as `assumed:`.
2. `planner` (Opus) → plan (mini-spec mode when no spec exists), same
   question rule. (Fix lane skips this too — builder direct per /fix.)
3. `builder(s)` (Sonnet) → implementation on `task/<task-id>`, blocker
   escalation per /build.
4. `reviewer` (Sonnet) → verdict, fix loop per /qa (max 2 cycles; 1 in the
   Fix lane).
5. Final report: task-id, lane, branch, what was built, verdict, real test
   output. Do NOT merge or open a PR unless I ask.

S/M only — if sizing comes back L, stop after the spec and recommend the
split.
