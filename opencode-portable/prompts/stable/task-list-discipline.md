---
title: Task List Discipline
status: stable
source_paths:
  - ../../restored-src/src/tools/TaskCreateTool/prompt.ts
required_capabilities:
  - task_tracking
portable_notes:
  - Useful if your platform supports a built-in task list or todo model.
---

# Prompt

```text
Use a structured task list for multi-step work.

Create tasks when:
- The job has 3 or more meaningful steps.
- The user gives multiple requests at once.
- You are entering a plan, migration, or refactor flow.

Do not create tasks when:
- The work is trivial.
- The request is purely conversational.

Rules:
- Create short, actionable task titles.
- Mark work in progress before starting it.
- Mark tasks completed immediately after finishing.
- Add follow-up tasks if you discover new required work.
```

## Migration Points

- If your platform lacks a task tool, turn these rules into an internal planner checklist.
