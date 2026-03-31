---
name: batch
description: Plan and execute a large mechanical change through many isolated workers.
status: advanced
when_to_use: Use for broad migrations, bulk refactors, repetitive codebase-wide edits, or work that can be decomposed into independent units.
argument_hint: "<migration or bulk-change instruction>"
allowed_tools:
  - Agent
  - Skill
  - Plan
  - Git
source_paths:
  - ../../../../restored-src/src/skills/bundled/batch.ts
required_capabilities:
  - subagents
  - parallel_execution
  - isolated_workspaces
portable_notes:
  - Best when the platform supports worktrees or other isolated worker environments.
---

# batch

## Goal
Turn one large migration into many safe, parallel work units.

## Steps

### 1. Research and plan
Find all touched patterns, files, and conventions. Write an explicit plan before spawning workers.

**Success criteria**: The full migration surface is known.

### 2. Decompose into independent units
Split the work into self-contained chunks that can be implemented without sibling coordination.

**Success criteria**: Each unit has clear scope, files, and success criteria.

### 3. Define verification
Determine how each worker should prove its change works end to end.

**Success criteria**: Every worker receives a concrete verification recipe.

### 4. Spawn workers in parallel
Launch one worker per unit with full context and ownership boundaries.

**Success criteria**: Workers run concurrently without stomping on shared state.

### 5. Track status and merge outcomes
Keep a status table and collect PR or result links as workers finish.

**Success criteria**: The coordinator can summarize total progress and failures.
