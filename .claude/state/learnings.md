# Learning Log

> **TL;DR:** Append-only confession booth. Agents log mistakes, shortcomings,
> rule gaps, and weird cases here — with enough context that a human (or a
> future planner) can act on it later. Reviewed periodically; good entries
> become ADRs or rule changes. Log it and move on — this is a record, not a
> support ticket.

Entry format (newest at the bottom):

```
### YYYY-MM-DD — <one-line title>
- **By:** <agent / session / person> · **Task:** <task-id or n/a> · **Type:** mistake | shortcoming | edge-case | idea
- **What happened:** the facts, no blame
- **Context:** enough to understand or reproduce this cold
- **Impact:** what it cost (time, tokens, quality, a confused human)
- **Suggested improvement:** concrete — a rule change, a template field, a check
- **Status:** open | adopted → <ADR/rule> | rejected (why)
```

---

### 2026-06-12 — Workflow built blind against GitHub permissions
- **By:** orchestrating session (workflow bootstrap) · **Task:** n/a · **Type:** shortcoming
- **What happened:** Built and committed the whole workflow, then discovered
  nothing could be pushed: git push 403, API file-push 403, repo creation
  denied (org and personal). Burned several retry rounds and two fallback
  paths before concluding the integration simply had no write grant.
- **Context:** Remote sandbox session; GitHub app token had read-only access
  to an empty repo. The push failure mode ("denied to <org>") doesn't
  distinguish missing-grant from transient proxy errors, so backoff retries
  looked reasonable but were wasted.
- **Impact:** ~10 minutes of retries/fallbacks; deliverable reduced to a
  bundle + tarball handoff instead of a pushed branch.
- **Suggested improvement:** Verify write access FIRST in any new
  environment (`git push --dry-run` or check app installation) before
  building work that assumes pushing; surface a permissions checklist to the
  user at session start instead of at delivery time.
- **Status:** open

### 2026-06-12 — /review collided with a built-in skill and never registered
- **By:** orchestrating session · **Task:** n/a · **Type:** mistake
- **What happened:** `.claude/commands/review.md` never registered as a
  slash command — Claude Code ships a built-in `review` skill, and name
  collisions don't error, they silently shadow. The session's skill registry
  showed every command registering except `/review`.
- **Context:** Built-in skill names observed in-session: review, verify,
  code-review, security-review, init, run, loop, simplify. None may be used
  as project command names.
- **Impact:** The verification stage of the loop was unreachable by its
  documented name; docs pointed at a command that didn't exist.
- **Suggested improvement:** Check the session's skill list before naming a
  command; prefer collision-proof names. Renamed to `/qa` — it registered
  immediately, confirming the diagnosis.
- **Status:** adopted → ADR 2026-06-12 (Builders' Stack generalization)

### 2026-06-12 — Broke our own same-commit codemap rule
- **By:** orchestrating session · **Task:** n/a · **Type:** mistake
- **What happened:** README.md was committed with no codemap.md row — in the
  same session that wrote the rule requiring exactly that.
- **Context:** The rule lives in conventions.md, but the orchestrator wasn't
  re-checking its own conventions before committing structural changes.
  Caught only during a later explicit review pass.
- **Impact:** Stale code map; mildly embarrassing precedent — if the
  rule-writer skips the rule, agents will too.
- **Suggested improvement:** Any commit adding/renaming top-level or source
  files should be grepped against codemap.md before committing (a cheap
  reviewer/builder checklist line — already in reviewer.md step 5; the
  orchestrator must hold itself to it too).
- **Status:** adopted (rows fixed in the same commit as this entry)
