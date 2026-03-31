---
name: simplify
description: Review recent changes for reuse, quality, and efficiency, then clean up what does not need to be there.
status: stable
when_to_use: Use after finishing an implementation and before final handoff, especially if the diff is medium or large.
argument_hint: "[additional focus]"
allowed_tools:
  - Read
  - Edit
  - Bash
  - Agent
source_paths:
  - ../../../../restored-src/src/skills/bundled/simplify.ts
required_capabilities:
  - file_read
  - file_edit
  - optional_subagents
portable_notes:
  - If your platform has no subagents, run the three review passes sequentially.
---

# simplify

## Goal
Reduce unnecessary code before shipping.

## Steps

### 1. Inspect the diff
Review the changed files or the most recent edits.

**Success criteria**: You know the full scope of the recent implementation.

### 2. Run three review passes
Check for:
- missed reuse opportunities
- code quality issues and unnecessary complexity
- avoidable work or inefficient hot-path behavior

**Success criteria**: Each pass produces either concrete cleanup items or a clean bill of health.

### 3. Fix or reject findings explicitly
Apply the useful cleanups. If a finding is not worth acting on, say why and move on.

**Success criteria**: The remaining code is simpler or you have a clear rationale for keeping it.
