---
description: Plan a task — from its spec, or directly from a description (Build lane, Opus planner). Usage: /plan <task-id | description>
---

Input: $ARGUMENTS

1. If $ARGUMENTS matches an index row with a spec → spec mode. Otherwise
   treat it as a Build-lane description: the planner mints the task-id and
   writes the mini-spec (goal, acceptance checks, non-goals) at the top of
   plan.md — no pm hop. If the "what" is materially ambiguous or it sizes
   L, the planner stops and says so → route to `/pm`.
2. Launch the `planner` agent. It explores the codebase (codemap first),
   writes `plan.md`, fills Claimed paths, and moves (or creates) the index
   row at `planned`.
3. If the planner surfaces questions (ambiguity, claimed-path collision
   with an active task), ask me with AskUserQuestion — planner's
   recommendation first — and send my answers back to the same planner
   agent to finish.
4. Relay: step count, parallel groups, claimed paths, risks. Suggest
   `/build <task-id>`.
