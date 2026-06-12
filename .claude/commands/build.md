---
description: Execute a plan with Sonnet builder agents. Usage: /build <task-id>
---

Task: $ARGUMENTS

Pre-flight:
1. `git fetch origin`. Verify `plan.md` exists (else suggest
   `/plan $ARGUMENTS`) and check the claim in `.claude/state/index.md`:
   another owner with a push in the last ~2h → report and stop; stale or
   free → proceed (the builder claims it).
2. Check `progress.md` for already-done steps — builders resume, never redo.

Execution:
3. If the plan marks parallel groups across **non-overlapping files**,
   launch multiple `builder` agents concurrently, each with an explicit,
   disjoint step set. Otherwise one builder for all remaining steps.
4. Builders push after every step. If one reports `blocked` with a design
   gap: launch the `planner` agent (Opus) to resolve the blocker and amend
   `plan.md`, then resume the builder. If the planner says the **spec** is
   the problem, ask me with AskUserQuestion before anything else moves.

5. When all steps are done relay: steps completed, test status, deviations.
   Suggest `/qa $ARGUMENTS`.
