---
name: verify
description: Verify that a change actually works by running it, not by rereading the code.
status: stable
when_to_use: Use after non-trivial implementation work, especially backend, UI, CLI, infra, or migration changes.
argument_hint: "[task summary or changed files]"
allowed_tools:
  - Read
  - Bash
  - WebFetch
  - Browser
source_paths:
  - ../../../../restored-src/src/skills/bundled/verify.ts
  - ../../../../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts
required_capabilities:
  - subprocess
  - test_runner
  - optional_browser
portable_notes:
  - Best used as a dedicated verifier skill or subagent.
---

# verify

## Goal
Produce an evidence-based PASS / FAIL / PARTIAL verdict.

## Steps

### 1. Establish the real verification path
Read the project docs and find the actual build, test, run, and lint commands.

**Success criteria**: You know the commands that should prove the change works.

### 2. Run baseline checks
Run build, tests, and type / lint checks where applicable.

**Success criteria**: Baseline health is known.

### 3. Exercise the changed behavior directly
Run the script, hit the endpoint, click the flow, or operate the UI with real tools.

**Success criteria**: The changed behavior was exercised in reality.

### 4. Try to break it
Run at least one adversarial probe: malformed input, boundary value, concurrency, idempotency, or missing reference.

**Success criteria**: The verification covers more than the happy path.

### 5. Report with evidence
For every check, include the command or action, observed output, and PASS or FAIL. End with a single verdict line.

**Success criteria**: Another engineer could reproduce your verdict from the report alone.
