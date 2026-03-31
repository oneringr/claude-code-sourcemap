---
name: stuck
description: Diagnose frozen or slow agent sessions on the local machine and prepare a diagnostic report.
status: experimental
when_to_use: Use when the issue is with the agent process itself being hung, slow, or wedged.
argument_hint: "[pid or symptom]"
allowed_tools:
  - Bash
  - Read
source_paths:
  - ../../../../restored-src/src/skills/bundled/stuck.ts
required_capabilities:
  - local_process_inspection
portable_notes:
  - The original skill reports to an internal Slack channel; adapt the diagnostic flow, not the reporting target.
---

# stuck

## Goal
Diagnose whether another agent session is actually stuck and produce a useful report.

## Steps

### 1. Inspect relevant processes
Find the suspected agent process and its children.

**Success criteria**: You know which process tree to analyze.

### 2. Look for hard signals
Check sustained CPU, blocked state, zombie state, huge RSS, or hung child processes.

**Success criteria**: There is concrete evidence of health or failure.

### 3. Prepare a report
Summarize the process state, likely cause, and any supporting logs or stack samples.

**Success criteria**: Another engineer can act on the report.
