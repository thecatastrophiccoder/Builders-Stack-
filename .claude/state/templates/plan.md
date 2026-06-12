# Plan: <title>

**TL;DR:** <one line — approach + step count.>

**Task ID:** <task-id>
**Author:** planner agent
**Spec:** ./spec.md | none — mini-spec below (Build lane)
**Branch:** task/<task-id>
**Claimed paths:** <globs, mirrored into index.md>

## Goal (mini-spec — only when no spec.md exists)
2–3 lines: what + why now.
- Accept: <observable check a reviewer can verify having read nothing else>
- Non-goals: <if any>

## Approach
2–5 sentences: the design and why; alternatives rejected and why.

## Interfaces & data shapes
Module boundaries, function signatures, types the builder must not invent.

## Decisions made
Decisions the builder must NOT revisit:
- D1: ...

## Files touched
| File | Change |
|---|---|
| path | create/modify — what |

## Steps
Each step leaves the repo green (compiles, tests pass) and ends in a commit.

### Step 1: <summary>  [parallel-group: A | sequential]
- Files: ...
- Do: precise instructions — names, signatures, error handling, edge cases.
- Verify: `<command>` — expected result.

### Step 2: ...

## Risks
- R1: ... → mitigation

## Rollback
How to abandon safely mid-way (branch is isolated; revert step N first, ...).
