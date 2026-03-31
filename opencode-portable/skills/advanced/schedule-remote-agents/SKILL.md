---
name: schedule-remote-agents
description: Create, list, update, or run cloud-scheduled remote coding agents.
status: advanced
when_to_use: Use when the user wants a remote agent to run on a schedule, poll external systems, or execute a recurring cloud task with MCP access.
argument_hint: "[remote trigger request]"
allowed_tools:
  - ToolSearch
  - RemoteTrigger
  - AskUser
source_paths:
  - ../../../../restored-src/src/skills/bundled/scheduleRemoteAgents.ts
  - ../../../../restored-src/src/tools/RemoteTriggerTool/prompt.ts
required_capabilities:
  - remote_triggers
  - cloud_agents
  - optional_mcp_connectors
portable_notes:
  - Strongly platform-specific; keep the workflow but swap out the cloud-trigger API.
---

# schedule-remote-agents

## Goal
Help the user manage scheduled remote agents without exposing raw auth tokens.

## Steps

### 1. Determine the desired action
Create, list, update, or run a trigger.

**Success criteria**: The requested remote-agent action is unambiguous.

### 2. Gather runtime context
Determine repo source, environment, required connectors, schedule, and prompt payload.

**Success criteria**: The trigger has enough context to run autonomously.

### 3. Use the remote-trigger API through the platform tool
Do not use raw curl when a first-class trigger tool exists.

**Success criteria**: Trigger management happens through the trusted platform path.

### 4. Return a concrete summary
Explain what is scheduled, where it runs, and which connectors it depends on.

**Success criteria**: The user can audit the remote agent setup.
