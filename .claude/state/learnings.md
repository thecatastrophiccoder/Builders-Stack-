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
