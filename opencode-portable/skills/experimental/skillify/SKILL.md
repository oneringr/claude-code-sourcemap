---
name: skillify
description: Convert a repeatable workflow from the current session into a reusable skill draft.
status: experimental
when_to_use: Use near the end of a successful workflow that should become a reusable skill.
argument_hint: "[description of the workflow to capture]"
allowed_tools:
  - Read
  - Edit
  - Write
  - AskUser
source_paths:
  - ../../../../restored-src/src/skills/bundled/skillify.ts
required_capabilities:
  - conversation_memory
  - interactive_questions
portable_notes:
  - Requires a platform that can inspect the recent session and ask structured follow-up questions.
---

# skillify

## Goal
Draft a reusable skill from a workflow that just succeeded.

## Steps

### 1. Analyze the session
Identify the repeatable goal, inputs, steps, tools, success artifacts, and user corrections.

**Success criteria**: The workflow skeleton is explicit.

### 2. Interview the user
Ask for the final name, trigger phrases, arguments, save location, and any hard rules.

**Success criteria**: The intended reusable shape is confirmed.

### 3. Draft a skill file
Write a reusable `SKILL.md` with inputs, goal, steps, success criteria, and invocation guidance.

**Success criteria**: The draft is ready for review and save.
