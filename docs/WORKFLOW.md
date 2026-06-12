# Agent Workflow — How It Works & Best Practices

A **plan-with-Opus, execute-with-Sonnet** workflow for Claude Code.
Expensive high-reasoning models make the decisions; fast cheap models do the
typing. Files — not chat memory — carry all state, so any session, any
person, any agent can pick up where another left off.

## Quick start

```
/fix dead link in the README                  # XS: builder direct, reviewer after
/plan add retry to the fetch client           # S/M, clear ask: Opus plans (mini-spec inline)
/build 2026-06-12-add-retry-fetch-client      # Sonnet implements, pushing per step
/qa 2026-06-12-add-retry-fetch-client         # Sonnet verifies
/pm rethink merchant search ranking           # ambiguous/large: spec first, then the above
/status                                       # reconcile index with git, see everything

/ship add fuzzy matching to merchant search   # triaged + run end-to-end, S/M tasks only
```

Three lanes, picked by ambiguity. `/fix` for XS mechanical changes
(builder direct, steps live in progress.md, no spec/plan docs). `/plan
<description>` for clear S/M work — **the everyday default**; the planner
writes a mini-spec (goal + acceptance checks) into the plan, no PM hop.
`/pm` for ambiguous, product-shaped, or L work — the full pipeline.
Lighter lanes drop documents, never discipline: branch, index row,
per-step pushes, tests, and review apply in all three; the first material
question moves a task up a lane.

No command memorized? Describe the task in plain language — the session
triages it, names the lane in one line, and runs it (CLAUDE.md →
Plain-language asks). Commands are accelerators, not gates.

## Adopting mid-project

Most teams add this workflow to a codebase that already exists. `/adopt`
bootstraps it in one pass:

- **Derived, not typed.** planner + pm (Opus) read manifests, lockfiles,
  CI configs, entry points, and docs, then draft `architecture.md`,
  `codemap.md` (the real source tree), `conventions.md` (detected
  test/lint commands), `product.md`, and up to 3 "observed" ADRs.
  Anything inferred without hard evidence is `confirm:`-marked and comes
  back to you as one batched question list — you confirm, you don't type.
- **Merge, never clobber.** Existing CLAUDE.md/context content is merged
  around; replacing human-written text requires explicit confirmation.
- **Onboard-as-you-go.** Pre-existing branches and PRs are reported, not
  imported; they get index rows when a lane first touches them. The index
  never claims work it doesn't own.
- **History caveat.** The gitleaks CI guard scans full history — an older
  repo may surface historic secrets on its first run. That's signal:
  rotate the secret, then allowlist the commit.

## The pieces

```
CLAUDE.md              # operating manual — every agent reads this first
AGENTS.md              # bootstrap pointer for non-Claude tools
.claude/
├── agents/            # who does what, on which model
│   ├── pm.md          # opus   — idea → spec (asks before assuming)
│   ├── planner.md     # opus   — spec → step plan, interfaces, claims
│   ├── builder.md     # sonnet — plan → code, commit+push per step
│   └── reviewer.md    # sonnet — code → verdict + hygiene enforcement
├── commands/          # /adopt /fix /pm /plan /build /qa /ship /status
├── context/           # long-lived knowledge (read before working)
│   ├── product.md     # what & for whom            (FILL THE TODOs)
│   ├── architecture.md# stack & system design      (FILL THE TODOs)
│   ├── conventions.md # structure, style, git      (FILL THE TODOs)
│   ├── codemap.md     # path → purpose map of the tree
│   └── decisions.md   # append-only ADR log
└── state/
    ├── index.md       # THE task map: status/owner/head/claims/digest
    ├── templates/     # spec / plan / progress / review skeletons
    └── tasks/<id>/    # per-task working memory
```

To run planning tiers on Fable instead of Opus, change `model: opus` in
`agents/pm.md` and `agents/planner.md` to the Fable model id (requires org
access to Fable).

## Why this split works

- **Opus/Fable for planning:** decomposition, trade-offs, and "what breaks"
  thinking is where reasoning depth pays. A bad plan multiplied across many
  Sonnet steps is the most expensive failure mode in this system.
- **Sonnet for execution:** given an unambiguous step with a verification
  command, Sonnet is fast, cheap, and reliable. The plan's whole job is to
  make every step that unambiguous.
- **Files as the interface:** subagents share no context. Anything not in
  `spec/plan/progress/index` does not exist for the next agent — and a
  crashed or compacted session resumes purely from the files.

## Asking beats assuming

The workflow is built to question you rather than guess:

- The **pm** surfaces material ambiguities (scope, audience, success
  criteria) as Open Questions with a recommended answer each; the
  orchestrator brings them to you before planning starts.
- The **planner** refuses to plan over unresolved material questions.
- The **builder** never asks you — and never decides either. Design gaps
  become Blockers routed back to Opus; only spec-level problems reach you.
- Trivia gets decided conservatively and **recorded** (`assumed:`,
  Deviations, ADRs) so every guess is auditable later.

The thresholds: if the answer changes scope, architecture, data contracts,
dependencies, or anything user-visible → ask. Otherwise → decide and record.

## Collaboration: many sessions, many people

- **One task = one branch = one owner.** Claiming = writing yourself into
  the Owner fields (index + progress) and pushing. The pushed branch is the
  visible claim.
- **Stale after ~2h without a push** → anyone may take over: read
  `progress.md`, log the takeover, continue from the last green step.
- **Sync before, push after.** Fetch + rebase on default before building;
  push every step commit. A dead session loses at most one step.
- **Claimed paths serialize collisions.** Plans declare the globs they
  touch; overlapping tasks never run in parallel.
- **Merges go through PRs** after review approval. Agents never merge.

## The index: token-efficient shared memory

Think of `index.md` as the workflow's hash map: task-id → (status, owner,
branch, head SHA, claimed paths, digest). The rules that keep it honest:

1. **Same-commit rule.** The index row changes in the same commit as the
   thing it describes. A stale index is a lying index.
2. **Git is truth, the index is cache.** Head SHAs make staleness checkable
   (`git log -1 origin/task/<id>`); `/status` reconciles and repairs.
3. **Digests are ≤15 words.** Agents read the index first and open task
   files only when a digest falls short — that's the token budget's best
   friend.
4. **TL;DR lines everywhere.** Every state file opens with one; per-step
   updates keep it current, so cross-session pickup costs one line, not one
   file.
5. **codemap.md over globbing.** Path → purpose for the whole tree, updated
   with structural changes, enforced by the reviewer. New agents and new
   hires onboard by reading two files, not two hundred.

## Code quality bar

Set in `conventions.md`, designed by the planner, enforced by the reviewer:
small single-purpose functions; pure core with side effects at the edges;
modules with explicit interfaces; extraction on the second caller, not
speculatively; efficiency to the spec's stated budget, not vibes.

## Best practices

1. **Fill the context TODOs before the first real task.** Planning quality
   is capped by context quality; an Opus plan over an empty
   `architecture.md` is an expensive guess.
2. **Keep context short and current.** A wrong "fact" in `architecture.md`
   poisons every future plan. Prune ruthlessly.
3. **Recurring re-decisions mean a missing ADR.** Write it once in
   `decisions.md`, append-only, and stop paying for it.
4. **Problem before solution in specs.** Acceptance criteria must be
   checkable by someone who read nothing else; non-goals are what stop
   scope creep inside an autonomous loop.
5. **Every plan step ends green and committed.** Bisectable history, safe
   aborts, cheap takeovers.
6. **Pre-answer the builder's decisions.** Names, signatures, error
   handling, edge cases. Each omission is a coin-flip delegated to a
   cheaper model.
7. **Cap autonomous loops.** Two fix→review cycles, then a human. A loop
   that won't converge means the spec or plan is defective — more typing
   won't fix a thinking problem.
8. **Watch the failure signatures in `/status`.** Many `blocked` tasks →
   plans too thin (give Opus more context). Many `request-changes` →
   acceptance criteria too vague (fix the spec template usage, not the
   builder).
9. **Archive done tasks** to `state/archive/` and the index's Archive
   table once merged — the live table stays small enough to read in one
   gulp.
10. **Commit `.claude/` always.** Specs, ADRs, and the index are project
    history, not scratch space.
