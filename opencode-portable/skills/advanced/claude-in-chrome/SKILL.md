---
name: claude-in-chrome
description: Use a real logged-in Chrome session for browser automation, debugging, and screenshots.
status: advanced
when_to_use: Use when the user needs authenticated browser actions, OAuth flows, console inspection, screenshots, or automation in a real Chrome session.
argument_hint: "[browser task]"
allowed_tools:
  - BrowserMCP
source_paths:
  - ../../../../restored-src/src/skills/bundled/claudeInChrome.ts
  - ../../../../restored-src/src/utils/claudeInChrome/prompt.ts
required_capabilities:
  - browser_mcp
  - screenshots
  - console_logs
portable_notes:
  - Replace the `claude-in-chrome` namespace with your own browser tool namespace.
---

# claude-in-chrome

## Goal
Operate the user's real browser safely and predictably.

## Steps

### 1. Activate the browser toolset
Load or enable the browser MCP tools before trying to use them.

**Success criteria**: Browser tools are available.

### 2. Fetch tab context first
Understand the current tabs before creating new ones or reusing an existing tab.

**Success criteria**: You know which browser context to operate on.

### 3. Execute the task with evidence
Use clicks, fills, navigation, screenshots, and console reading as needed.

**Success criteria**: The target browser behavior is demonstrated with observable evidence.

### 4. Avoid dead ends
Do not trigger blocking dialogs. Stop after repeated failures instead of looping.

**Success criteria**: The session remains responsive and debuggable.
