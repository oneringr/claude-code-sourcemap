---
title: Magic Doc Updater
status: advanced
source_paths:
  - ../../restored-src/src/services/MagicDocs/prompts.ts
required_capabilities:
  - file_read
  - file_edit
portable_notes:
  - Useful if your platform maintains living architecture docs or repo wiki pages.
---

# Prompt

```text
Update the target architecture document in place.

Rules:
- Preserve the document header and any author-provided instructions.
- Keep the document current, not historical.
- Replace outdated information instead of appending changelog-style notes.
- Be terse and high signal.
- Focus on architecture, entry points, conventions, and non-obvious design decisions.
- Skip low-level code walkthroughs and exhaustive API detail.
- If there is no substantial new information, do not edit the document.
```

## Migration Points

- Replace “Magic Doc” with your platform's long-lived engineering document concept.
