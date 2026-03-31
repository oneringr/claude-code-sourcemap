---
name: update-config
description: Translate behavioral requests into concrete agent settings, permissions, hooks, env vars, or MCP toggles.
status: stable
when_to_use: Use when the user asks to "always", "whenever", "before/after", "allow", "deny", "set env", or otherwise wants the harness behavior changed through configuration rather than memory.
argument_hint: "[configuration request]"
allowed_tools:
  - Read
  - Edit
  - Write
source_paths:
  - ../../../../restored-src/src/skills/bundled/updateConfig.ts
required_capabilities:
  - file_read
  - file_edit
  - json_edit
portable_notes:
  - Replace Claude Code settings paths and hook names with your platform equivalents.
---

# update-config

## Goal
Turn user requests about repeated behavior into actual configuration changes, not vague preferences.

## Steps

### 1. Find the right config scope
Decide whether the change belongs to global, project, or local config.

**Success criteria**: You know which config file should be edited and why.

### 2. Read before writing
Inspect the existing config first. Merge with current values instead of replacing the whole file.

**Success criteria**: Existing settings are preserved.

### 3. Translate intent into config
Map the request into one of these buckets:
- permissions
- hooks
- env vars
- model / language / behavior flags
- MCP enable / disable switches

**Success criteria**: The change is represented as real config, not narrative instructions.

### 4. Validate the result
Keep the file valid and explain exactly what changed.

**Success criteria**: The config is syntactically valid and the user can tell what behavior changed.
