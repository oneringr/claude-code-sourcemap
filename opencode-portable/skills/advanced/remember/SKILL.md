---
name: remember
description: Review memory layers and propose what should be promoted, cleaned up, or left in temporary memory.
status: advanced
when_to_use: Use when the user wants to review durable preferences, promote memory into repo docs, or clean up stale memory entries.
argument_hint: "[memory review focus]"
allowed_tools:
  - Read
  - Edit
source_paths:
  - ../../../../restored-src/src/skills/bundled/remember.ts
required_capabilities:
  - persistent_memory
  - layered_instructions
portable_notes:
  - Assumes the platform distinguishes temporary memory from durable instruction files.
---

# remember

## Goal
Propose memory cleanup and promotion without making silent changes.

## Steps

### 1. Gather memory layers
Read the project instructions, local overrides, and any persistent memory summaries.

**Success criteria**: All durable instruction layers are visible.

### 2. Classify each memory
Decide whether it belongs in:
- project-wide instructions
- personal instructions
- shared team memory
- temporary memory only

**Success criteria**: Each entry has a proposed destination or is marked ambiguous.

### 3. Identify duplicates and conflicts
Look for entries that are stale, duplicated, or contradictory across layers.

**Success criteria**: Cleanup opportunities are explicit.

### 4. Present proposals before editing
Show promotions, deletions, updates, and ambiguous cases. Do not silently apply changes.

**Success criteria**: The user can approve or reject each proposal.
