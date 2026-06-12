# Workflow Index

> **TL;DR:** Live map of every task — the only board. Read this before any
> other state file; update it in the same commit as the change it describes.

## Tasks

| ID | Status | Size | Branch | Owner | Head | Claimed paths | Digest |
|---|---|---|---|---|---|---|---|
| _none yet_ | | | | | | | |

Statuses: `backlog → spec → planned → building → review → done`
(`blocked` is a side-state entered from `building`; returns to `building`
once the planner resolves the blocker. Lanes enter mid-chain: Build-lane
tasks start at `planned`, Fix-lane tasks at `building`. Work predating
the stack joins onboard-as-you-go — when a lane first touches it.)

Column conventions:
- **Owner** — email or session-id while claimed; `—` when free. A claim is
  stale after ~2h with no pushed commit on the task branch; stale claims may
  be taken over (see CLAUDE.md → Collaboration).
- **Head** — short SHA of the task branch's last pushed commit. Cheap
  staleness check: if `git log -1 origin/task/<id>` disagrees, git wins —
  fix this row.
- **Claimed paths** — globs the plan touches (e.g. `src/query/**`). Tasks
  with overlapping claims are serialized, never parallel.
- **Digest** — ≤15 words: current state + next action. If a reader needs
  more, they open `tasks/<id>/progress.md` — not before.

## Archive

Rows for merged/abandoned tasks move here; their dirs move to
`.claude/state/archive/<id>/`.

| ID | Outcome | Merged SHA | Digest |
|---|---|---|---|
| _none yet_ | | | |
