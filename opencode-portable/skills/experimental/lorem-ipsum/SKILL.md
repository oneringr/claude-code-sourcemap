---
name: lorem-ipsum
description: Generate filler text for long-context or token-budget testing.
status: experimental
when_to_use: Use only for testing context limits, parser behavior, or token-heavy workflows.
argument_hint: "[token_count]"
allowed_tools: []
source_paths:
  - ../../../../restored-src/src/skills/bundled/loremIpsum.ts
required_capabilities:
  - none
portable_notes:
  - Research utility only; not recommended for normal product workflows.
---

# lorem-ipsum

## Goal
Generate placeholder text of approximately the requested size.

## Steps

### 1. Parse the requested token count
If none is given, choose a safe default. Reject invalid counts.

**Success criteria**: A numeric token target is available.

### 2. Generate filler output
Produce low-value placeholder text sized for context-window experiments.

**Success criteria**: The output roughly matches the requested scale.
