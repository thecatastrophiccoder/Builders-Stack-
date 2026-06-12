# Agent Workflow — How It Works & Best Practices

This repo runs a **plan-with-Opus, execute-with-Sonnet** workflow inside
Claude Code. Expensive high-reasoning models make the decisions; fast cheap
models do the typing. Files — not chat history — carry all state.

## Quick start

```
/pm add fuzzy matching to merchant search     # Opus writes a spec
/plan 2026-06-12-fuzzy-merchant-search        # Opus writes a step plan
/build 2026-06-12-fuzzy-merchant-search       # Sonnet implements it
/review 2026-06-12-fuzzy-merchant-search      # Sonnet verifies it
/status                                       # where is everything?

/ship add fuzzy matching to merchant search   # all of the above, S/M tasks only
```

## The pieces

```
.claude/
├── agents/            # who does what, on which model
│   ├── pm.md          # opus  — idea → spec
│   ├── planner.md     # opus  — spec → step plan
│   ├── builder.md     # sonnet — plan → code (commits per step)
│   └── reviewer.md    # sonnet — code → verdict
├── commands/          # /pm /plan /build /review /ship /status
├── context/           # long-lived knowledge, read by every agent
│   ├── product.md     # what & for whom        (FILL THE TODOs)
│   ├── architecture.md# stack & system design  (FILL THE TODOs)
│   ├── conventions.md # style, testing, git    (FILL THE TODOs)
│   └── decisions.md   # append-only ADR log
└── state/             # per-task working memory
    ├── board.md       # index: every task + status
    ├── templates/     # spec / plan / progress / review skeletons
    └── tasks/<id>/    # spec.md, plan.md, progress.md, review.md
```

To use Fable instead of Opus for planning tiers, change `model: opus` to the
Fable model id in `agents/pm.md` and `agents/planner.md` (requires access to
Fable in your org).

## Why this split works

- **Opus/Fable for planning:** decomposition, trade-offs, and "what could go
  wrong" thinking are where reasoning depth pays for itself. A bad plan
  multiplied across many Sonnet steps is the most expensive failure mode.
- **Sonnet for execution:** given an unambiguous step with a verification
  command, Sonnet is fast, cheap, and reliable. The plan's job is to make
  every step that unambiguous.
- **Files as the interface:** subagents don't share chat context. Anything
  not written to `spec/plan/progress` does not exist for the next agent —
  and a compacted or crashed session can resume purely from the files.

## Best practices

### Writing context (the highest-leverage thing you can do)
1. Fill the TODOs in `.claude/context/*.md` before the first real task.
   Planning quality is capped by context quality.
2. Keep context files short and current. Prune ruthlessly; a wrong "fact"
   in `architecture.md` poisons every future plan.
3. Decisions go in `decisions.md`, append-only. When agents keep re-deciding
   something, that's a missing ADR.

### Specs (PM work)
4. Problem before solution. A spec that opens with the solution hides the
   requirement.
5. Acceptance criteria must be checkable by someone who read nothing else.
   "Search feels faster" is not a criterion; "p95 < 200ms on the offers
   index" is.
6. Non-goals are as important as goals — they're what stops scope creep
   inside an autonomous loop.

### Plans
7. Every step ends green (build + tests pass) and gets its own commit.
   That makes any failure bisectable and any abort safe.
8. Pre-answer the builder's decisions: names, error handling, edge cases.
   Each decision the plan omits is a coin-flip delegated to a cheaper model.
9. Mark independent steps as parallel groups — `/build` will fan out
   multiple Sonnet builders, but only across non-overlapping files.

### Building
10. Builders never improvise design. Mechanical gap → fix and log under
    Deviations. Design gap → write a Blocker, flip status to `blocked`,
    stop. The orchestrator routes blockers back to Opus, not to chat.
11. Never mark a step done with failing tests, and never "fix" a test by
    weakening it.

### Reviewing
12. Review against the spec's criteria, not vibes — explicit pass/fail per
    criterion with evidence (real command output).
13. Cap the fix→re-review loop at 2 cycles, then escalate to a human.
    Endless autonomous loops burn money and hide a bad spec or plan.

### Hygiene
14. One task = one ID = one branch = one state directory. Tasks sized L get
    split at spec time, not discovered to be L at step 14.
15. Commit `.claude/` to git. The board, specs, and ADRs are project
    history, not scratch space. (Done task dirs can be archived to
    `state/archive/` once merged.)
16. Watch model spend with `/status`: lots of `blocked` tasks means your
    plans are too thin (give Opus more context); lots of review
    `request-changes` means your specs' criteria are too vague.
