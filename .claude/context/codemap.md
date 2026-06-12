# Code Map — Builders' Stack

> **TL;DR:** path → purpose for the whole tree. Read this instead of
> globbing. Updated in the same commit that creates, moves, or repurposes a
> file — the reviewer rejects structure changes that skip it.

| Path | Purpose | Entry points / notes |
|---|---|---|
| `README.md` | Human landing page — note from Anmol, quick start, file map | |
| `LICENSE` | MIT — reuse terms for the template | |
| `CLAUDE.md` | Agent operating manual — rules, routing, protocols | Start here |
| `AGENTS.md` | Bootstrap pointer for non-Claude agents/tools | Points to CLAUDE.md |
| `.claude/agents/` | Agent definitions: pm, planner (opus) / builder, reviewer (sonnet) | Frontmatter pins model + tools |
| `.claude/commands/` | Slash commands: /adopt /fix /pm /plan /build /qa /ship /status | /qa not /review — built-in name collision |
| `.claude/context/` | Long-lived knowledge: product, architecture, conventions, codemap, ADRs | Humans curate; agents append |
| `.claude/state/index.md` | Live task map (the only board) | Same-commit update rule |
| `.claude/state/learnings.md` | Append-only mistake/learning/improvement log | Agents log before final push; reviewed → ADRs |
| `.claude/state/templates/` | Skeletons for spec/plan/progress/review | Copy, don't invent formats |
| `.claude/state/tasks/<id>/` | Per-task working memory | Travels with `task/<id>` branch |
| `.claude/state/archive/` | Dirs of merged/abandoned tasks | Index keeps a one-line Archive row |
| `.github/workflows/checks.yml` | CI guards: command-name shadowing, codemap path check, gitleaks | Extend builtins list per ADR |
| `docs/WORKFLOW.md` | Human-facing guide + best practices | |
| _source tree: TODO — `/adopt` writes it on existing codebases; else added as code lands_ | | |
