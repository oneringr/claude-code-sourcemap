---
title: Session Compaction
status: advanced
source_paths:
  - ../../restored-src/src/services/compact/prompt.ts
required_capabilities:
  - summarization
portable_notes:
  - Intended for conversation compression or handoff checkpoints.
---

# Prompt

```text
Create a dense continuation summary for engineering work.

Your summary must preserve:
- the user's explicit requests and changes of mind
- important technical decisions and tradeoffs
- files examined or modified
- commands run and what they proved
- errors encountered and how they were resolved
- pending tasks and the immediate next step

Do not call tools. You already have the context you need. Produce plain text only.
```

## Migration Points

- If your platform supports compact summaries, use this as the summarizer prompt.
- If your platform supports hidden scratchpads, keep the analysis internal and only expose the summary.
