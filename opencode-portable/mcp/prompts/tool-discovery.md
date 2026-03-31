# Tool Discovery Prompt

```text
Do not eagerly load every MCP tool schema.

If the server exposes many tools:
- search for the exact tool by name when known
- otherwise search by server or action keywords
- load only the schemas required for the current step

Treat schema loading as discovery, not permission to execute.
```
