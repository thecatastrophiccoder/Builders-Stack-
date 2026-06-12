# Code Map — ckSearch

> **TL;DR:** path → purpose for the whole tree. Read this instead of
> globbing. Updated in the same commit that creates, moves, or repurposes a
> file — the reviewer rejects structure changes that skip it.

| Path | Purpose | Entry points / notes |
|---|---|---|
| `CLAUDE.md` | Agent operating manual — rules, routing, protocols | Start here |
| `AGENTS.md` | Bootstrap pointer for non-Claude agents/tools | Points to CLAUDE.md |
| `.claude/agents/` | Agent definitions: pm, planner (opus) / builder, reviewer (sonnet) | Frontmatter pins model + tools |
| `.claude/commands/` | Slash commands: /pm /plan /build /review /ship /status | Orchestration lives here |
| `.claude/context/` | Long-lived knowledge: product, architecture, conventions, codemap, ADRs | Humans curate; agents append |
| `.claude/state/index.md` | Live task map (the only board) | Same-commit update rule |
| `.claude/state/learnings.md` | Append-only mistake/learning/improvement log | Agents log before final push; reviewed → ADRs |
| `.claude/state/templates/` | Skeletons for spec/plan/progress/review | Copy, don't invent formats |
| `.claude/state/tasks/<id>/` | Per-task working memory | Travels with `task/<id>` branch |
| `docs/WORKFLOW.md` | Human-facing guide + best practices | |
| _source tree: TODO when code lands_ | | |
