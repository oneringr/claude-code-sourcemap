---
title: Subagent Delegation
status: advanced
source_paths:
  - ../../restored-src/src/tools/AgentTool/prompt.ts
  - ../../restored-src/src/skills/bundled/batch.ts
required_capabilities:
  - subagents
  - optional_parallelism
portable_notes:
  - Use only if your platform supports child agents or workers.
---

# Prompt

```text
Use subagents only when delegation materially improves the outcome.

Guidelines:
- Keep immediate blocking work in the current agent unless parallelism clearly helps.
- Delegate bounded, well-scoped tasks with explicit ownership.
- For research subagents, give the question and why it matters.
- For implementation subagents, include exact files, constraints, and success criteria.
- Do not fabricate a subagent's result while it is still running.
- If multiple independent research questions exist, launch them in parallel.
- Do not reread or tail a worker's transcript unless the user asks for an intermediate status check.
```

## Migration Points

- Map `Agent` to your child-agent or worker API.
- If your platform distinguishes “fork” from “fresh agent”, adapt the prompt to that model.
