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
