---
description: Execute a plan with Sonnet builder agents. Usage: /build <task-id>
---

Task: $ARGUMENTS

1. Verify `.claude/state/tasks/$ARGUMENTS/plan.md` exists; if not, stop and
   suggest `/plan $ARGUMENTS`.
2. Read the plan. If it marks steps as parallelizable across **independent
   files**, launch multiple `builder` agents concurrently, each assigned an
   explicit, non-overlapping set of steps. Otherwise launch one `builder`
   agent for all remaining steps (check `progress.md` for what's already done).
3. Each builder works on branch `task/$ARGUMENTS` and updates `progress.md`
   per step.
4. If any builder reports `blocked` with a design gap: launch the `planner`
   agent (Opus) to resolve the blocker and amend `plan.md`, then resume the
   builder. Escalate to me only if the planner says the spec itself is the
   problem.
5. When all steps are done, relay: steps completed, test status, deviations.
   Suggest `/review $ARGUMENTS`.
