---
description: Bootstrap the workflow into an existing codebase — derive the context pack from reality. Usage: /adopt [notes about the project]
---

Adopting mid-project. Notes: $ARGUMENTS

Greenfield repo? Skip this — fill the context TODOs or just start at /pm.

Rules before anything runs:
- Merge, never clobber: context files holding real (non-TODO) human
  content get appended/refined only; replacing human-written text needs my
  explicit OK first.
- Everything inferred ships marked: lines without hard evidence start
  `confirm:` and come back to me batched, not one-by-one.

1. Launch `planner` (Opus, adopt mode) and `pm` (Opus, adopt mode) in
   parallel — disjoint files, no collision:
   - planner → `architecture.md` (stack from manifests/lockfiles, system
     overview from entry points), `codemap.md` (the real source tree, top
     two levels minimum), `conventions.md` (test/lint/format commands
     detected from package.json/Makefile/CI), and up to 3 "observed" ADRs
     for load-bearing structures already in the code.
   - pm → `product.md` draft from README/docs/app surface; gaps become
     open questions with recommended answers.
2. Collect every `confirm:` from both into ONE AskUserQuestion batch; send
   answers back; agents finalize and commit per the same-commit
   discipline, push.
3. In-flight work stays in-flight: report existing non-default branches
   and open PRs, but the index gains rows onboard-as-you-go — only when a
   lane first touches that work. Don't backfill a board nobody asked for.
4. If the CI guards are installed, relay the history caveat: gitleaks
   scans full history, so an older repo may surface historic secrets on
   the first run — that's signal, not noise (rotate, then allowlist).
5. Report: derived vs confirmed vs still-TODO, in-flight work found, and
   the suggested first task through a lane.
