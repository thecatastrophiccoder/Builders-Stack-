---
description: Fast lane — XS mechanical change, builder direct, no spec/plan docs. Usage: /fix <description>
---

Change: $ARGUMENTS

Gate (you, before launching anything): Fix lane is for mechanical work —
clear target, no design choice, ≤ ~30 min. Bigger or fuzzier → route to
`/plan` (clear S/M) or `/pm` (ambiguous/L) instead, and say why in one line.

1. Mint the task-id (`YYYY-MM-DD-<kebab-slug>`) and state ONE acceptance
   check — the observable change that proves it done.
2. Launch a `builder` (Sonnet) in fix mode with the description + that
   check. It claims the task (branch `task/<id>`, index row entering at
   `building`, progress.md), writes its own ≤3 steps into progress.md
   before any code, then follows normal builder discipline — tests green,
   codemap on structure changes, push per step.
3. If the builder hits a design choice or material question, it blocks per
   protocol; re-triage the task up a lane rather than pushing through.
4. Then `/qa <id>` — max 1 fix cycle in this lane. Docs-only diff: qa may
   be skipped; record the skip under Deviations in progress.md.
5. Relay: what changed, test output (real), verdict.
