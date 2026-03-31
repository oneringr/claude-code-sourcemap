---
name: keybindings-help
description: Help users modify keyboard shortcuts without clobbering the existing keybindings file.
status: stable
when_to_use: Use when the user asks to rebind a key, add a chord, remove a shortcut, or troubleshoot keyboard shortcut conflicts.
argument_hint: "[keybinding request]"
allowed_tools:
  - Read
  - Edit
  - Write
source_paths:
  - ../../../../restored-src/src/skills/bundled/keybindings.ts
required_capabilities:
  - file_read
  - file_edit
portable_notes:
  - Replace the config path and action names with your platform's keybinding schema.
---

# keybindings-help

## Goal
Safely customize keybindings while preserving existing mappings and explaining conflicts.

## Steps

### 1. Read the current keybindings file
Always read the current file first. If it does not exist, create the smallest valid file instead of replacing a hypothetical full config.

**Success criteria**: You know the current bindings state.

### 2. Apply additive changes
Prefer merging, unbinding, or rebinding specific keys instead of rewriting the whole file.

**Success criteria**: Only the requested bindings change.

### 3. Check syntax and reserved shortcuts
Watch for invalid keystroke syntax, conflicting system shortcuts, and invalid action names.

**Success criteria**: The requested binding is valid or the user is told exactly why it is not.
