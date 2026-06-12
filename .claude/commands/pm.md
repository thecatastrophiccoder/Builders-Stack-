---
description: Turn an idea into a spec (Opus pm agent). Usage: /pm <idea>
---

Launch the `pm` agent with this idea: $ARGUMENTS

When it returns:
1. If it surfaced **open questions**, ask me with AskUserQuestion (use the
   pm's recommended answers as the first options), then send the answers
   back to the same pm agent to finalize the spec as `approved`. Do not
   proceed to planning around unresolved material questions.
2. Relay: task-id, spec TL;DR, size, and what was assumed vs. asked.
3. Suggest `/plan <task-id>` only once the spec is `approved`.
4. Product lane only: if the ask turns out to be an unambiguous S/M coding
   task, don't spec it — say so and point at `/plan <desc>` instead.
