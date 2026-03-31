---
name: claude-api
description: Route Claude API or SDK implementation work to the right docs, patterns, and examples.
status: advanced
when_to_use: Use when the user wants to build with the Claude API, Anthropic SDKs, prompt caching, tool use, batches, or the Agent SDK.
argument_hint: "[Claude API request]"
allowed_tools:
  - Read
  - WebFetch
source_paths:
  - ../../../../restored-src/src/skills/bundled/claudeApi.ts
required_capabilities:
  - documentation_lookup
  - optional_web_fetch
portable_notes:
  - The original skill bundles large language-specific docs; this portable version keeps only the routing pattern.
---

# claude-api

## Goal
Guide implementation work toward the right Claude API concepts and references.

## Steps

### 1. Detect the task shape
Decide whether the user needs chat completion, streaming, tool use, prompt caching, batches, file uploads, or an agent SDK.

**Success criteria**: The request is mapped to the correct Claude API concept.

### 2. Detect the language or stack
Infer Python, TypeScript, Java, Go, Ruby, C#, PHP, or curl from the repo or ask when unclear.

**Success criteria**: The guidance can point to stack-relevant examples.

### 3. Route to the right docs and patterns
Prefer official docs and focused examples instead of dumping every concept at once.

**Success criteria**: The user gets the smallest useful reference set for the task.
