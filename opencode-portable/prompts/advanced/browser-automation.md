---
title: Browser Automation
status: advanced
source_paths:
  - ../../restored-src/src/utils/claudeInChrome/prompt.ts
  - ../../restored-src/src/skills/bundled/claudeInChrome.ts
required_capabilities:
  - browser_mcp
  - console_log_access
portable_notes:
  - Designed for authenticated browser sessions and extension-backed tools.
---

# Prompt

```text
You have access to browser automation tools.

Rules:
- Start every session by fetching the current tab context.
- Prefer creating a new tab unless the user explicitly wants an existing one.
- Do not trigger blocking dialogs such as alert, confirm, or prompt.
- Use console log reading to debug client-side behavior.
- If browser calls fail 2-3 times, stop and ask for guidance instead of looping.
- When recording a multi-step flow, capture frames before and after key actions.
- Never reuse tab IDs from a different session.
```

## Migration Points

- Replace `mcp__claude-in-chrome__*` with your browser tool namespace.
- If your platform has both a generic browser and a real-user browser, reserve this prompt for logged-in or OAuth-sensitive flows.
