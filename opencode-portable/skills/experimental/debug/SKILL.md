---
name: debug
description: Read session debug logs and explain likely causes of failures or misbehavior.
status: experimental
when_to_use: Use when the user is debugging the agent platform itself rather than the target application.
argument_hint: "[issue description]"
allowed_tools:
  - Read
  - Grep
source_paths:
  - ../../../../restored-src/src/skills/bundled/debug.ts
required_capabilities:
  - session_debug_logs
portable_notes:
  - Strongly platform-specific; adapt only if your platform exposes session logs.
---

# debug

## Goal
Diagnose issues in the current agent session by reading platform debug logs.

## Steps

### 1. Locate the session log
Find the current session log file or enable logging for future steps.

**Success criteria**: A concrete log source exists.

### 2. Read the tail and grep the full file
Look for errors, warnings, stack traces, and repeated failure patterns.

**Success criteria**: The relevant diagnostic patterns are extracted.

### 3. Explain findings in plain language
Translate log noise into likely causes and next steps.

**Success criteria**: The user gets actionable debugging guidance.
