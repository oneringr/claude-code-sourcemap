---
name: loop
description: Schedule a prompt or slash command to run on a recurring interval.
status: advanced
when_to_use: Use when the user wants polling, periodic status checks, recurring prompts, or repeated slash commands.
argument_hint: "[interval] <prompt>"
allowed_tools:
  - Scheduler
  - Skill
source_paths:
  - ../../../../restored-src/src/skills/bundled/loop.ts
required_capabilities:
  - scheduler
  - recurring_tasks
portable_notes:
  - Replace cron creation and deletion tools with your platform's scheduler.
---

# loop

## Goal
Turn a one-off prompt into a recurring task.

## Steps

### 1. Parse the interval and payload
Support a leading interval, a trailing “every ...” clause, or a default interval.

**Success criteria**: You have a clean interval and a non-empty prompt.

### 2. Convert the interval into the scheduler format
Round or normalize intervals that do not map cleanly.

**Success criteria**: The scheduler receives a valid recurring cadence.

### 3. Create the recurring task
Store the prompt exactly as the user intended and return a handle for cancellation.

**Success criteria**: The job exists and the user knows how to stop it.

### 4. Execute once immediately
Do not wait for the first scheduled run before performing the task once.

**Success criteria**: The user gets immediate value plus future recurrence.
