# Builders' Stack — the one-pager

**The idea:** expensive models think, cheap models type, files remember.
Specs and plans come from Opus/Fable agents, code is built and reviewed by
Sonnet agents, and every piece of state lives in committed files — so any
session, any person, any model picks up exactly where the last one stopped.

## Start (60 seconds)

- **New repo:** copy this template, fill the TODOs in `.claude/context/`
  (product, architecture, test/lint commands).
- **Existing repo:** run `/adopt` — Opus reads the codebase, drafts the
  context files, and asks you one batch of confirm questions. Nothing
  human-written gets overwritten; in-flight branches stay untracked until
  a lane touches them.

## Use (daily)

Describe the task in plain language, or pick the lane yourself:

| Task looks like | Type | What happens |
|---|---|---|
| "fix the dead link" — mechanical, no decisions | `/fix <desc>` | Sonnet builder goes straight to work; reviewer checks after |
| "add retries to the fetch client" — clear, S/M | `/plan <desc>` | Opus plans (mini-spec inline), Sonnet builds, Sonnet reviews — **the everyday default** |
| "rethink search ranking" — fuzzy, product-shaped, or large | `/pm <idea>` | Opus writes a spec (asks you the open questions), then plan → build → review |
| Any of the above, hands-off | `/ship <idea>` | The session triages the lane and runs it end-to-end |
| "Where is everything?" | `/status` | Index reconciled against git, one report |

No command memorized? Just say the task — the session names the lane in
one line and proceeds. Commands are accelerators, not gates.

## Across the life of a project

The same seven commands carry you from empty repo to maintenance — only
the *mix* shifts. What changes stage to stage is how much PM ceremony you
need, not the machinery.

### Stage 0 — Bootstrap (once)
Get the context pack real before any planning; agents plan badly against
TODOs.
```
# greenfield: edit .claude/context/{product,architecture,conventions}.md by hand
/adopt our Django + Postgres API, tests via pytest   # existing codebase: derive it, then confirm one batch
/status                                               # sanity-check the index is empty/clean
```

### Stage 1 — Zero to one (define what you're building)
The "what" is fuzzy, so you live in the **Product lane**. Spec first,
decisions captured as ADRs while they're cheap to change.
```
/pm a referral system that rewards both inviter and invitee
        → answer the open questions it surfaces; spec lands approved
/plan 2026-06-12-referral-system                      # Opus turns the spec into file-level steps
/build 2026-06-12-referral-system                     # Sonnet implements, pushing per step
/qa 2026-06-12-referral-system                        # reviewer verdict against the spec
# or collapse 1–4:  /ship a referral system that rewards both sides
```

### Stage 2 — Build-out (most of the project's life)
The product is understood; features are clear S/M asks. **Build lane is
your default** — skip the PM hop, the planner writes the mini-spec inline.
```
/plan add pagination to the offers endpoint           # clear ask → straight to a plan
/build 2026-06-12-paginate-offers
/qa 2026-06-12-paginate-offers
/fix offers endpoint returns 500 on empty cursor       # mechanical bug → builder direct, reviewer after
/status                                               # end of session: see what's in flight, what's stale
```

### Stage 3 — Scale & collaboration (more hands)
Multiple people/agents in parallel. Lanes don't change; the claim
protocol and `/status` do the coordinating.
```
/status                                               # who owns what, which claims are stale (>2h)
/plan migrate sessions table to partitioned storage    # planner declares Claimed paths → serializes collisions
/build 2026-06-13-partition-sessions                  # overlapping-path tasks wait; disjoint ones run concurrently
# stale claim? read progress.md, /build resumes from the last green step — takeover is protocol, not a meeting
```

### Stage 4 — Maintenance & hardening (mature)
Mostly small, mechanical work + the occasional reconsidered decision.
```
/fix bump axios past the advisory, rerun the lockfile  # the workhorse here
/fix correct the rate-limit header name
/pm reconsider whether we still need the v1 webhook     # a real product question resurfaces → back to the Product lane
/status                                               # archive done tasks, keep the live index small
```

### Stage 5 — Handoff / onboarding (anytime)
Onboarding a human or a fresh agent is two files, not a tour.
```
# point them at: README → docs/one-pager.md → CLAUDE.md
/status                                               # the live state of the world in one read
# everything else is in .claude/context (why) and .claude/state (what's happening)
```

**Rule of thumb:** early project → more `/pm`; mature project → more
`/fix` and `/plan`. If you're reaching for `/pm` on every small task, the
product's understood enough to drop to the Build lane. If `/fix` keeps
hitting design choices, you're under-specced — go up a lane.

## What you get

- **Continuity.** Every step is committed and pushed; a crash or context
  compaction costs at most one step. Resume = one index read.
- **Cost control.** ~70–80% of tokens (the typing) run on Sonnet — about
  3–5× cheaper than letting a frontier model do everything.
- **Parallelism.** Independent plan steps run as concurrent builders.
- **A second pair of eyes, always.** The reviewer gate is part of the
  loop, with a capped fix cycle so it can't spiral.
- **An audit trail.** Specs, ADRs, logged deviations, and a
  self-maintained learning log — "why is it like this" has a file, not a
  vibe.

## What it costs

Discipline: branch per task, per-step pushes, same-commit index updates.
For throwaway scripts and one-shot experiments, skip the stack — that's
what `/fix` (or freehand) is for. The system pays off the moment work
outlives one sitting or involves more than one pair of hands.

## The three rules that matter most

1. **Files over memory** — if the next session needs it, it's in a file.
2. **The index never lies** — status changes commit with the work.
3. **Ask before you assume** — material questions go to the human; trivia
   gets decided conservatively and recorded.
