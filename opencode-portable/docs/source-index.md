# 来源索引

这份索引把 `opencode-portable/` 中的主要资产映射回恢复源码中的原始来源，方便继续深挖。

## Prompt 资产

| 资产 | 状态 | 主要来源 |
| --- | --- | --- |
| `prompts/stable/system-engineering-rules.md` | stable | `restored-src/src/constants/prompts.ts` |
| `prompts/stable/verification-agent.md` | stable | `restored-src/src/tools/AgentTool/built-in/verificationAgent.ts`, `restored-src/src/skills/bundled/verify.ts` |
| `prompts/stable/mcp-tool-discovery.md` | stable | `restored-src/src/tools/ToolSearchTool/prompt.ts`, `restored-src/src/tools/ListMcpResourcesTool/prompt.ts`, `restored-src/src/tools/ReadMcpResourceTool/prompt.ts` |
| `prompts/stable/task-list-discipline.md` | stable | `restored-src/src/tools/TaskCreateTool/prompt.ts` |
| `prompts/advanced/browser-automation.md` | advanced | `restored-src/src/utils/claudeInChrome/prompt.ts`, `restored-src/src/skills/bundled/claudeInChrome.ts` |
| `prompts/advanced/memory-extraction.md` | advanced | `restored-src/src/services/extractMemories/prompts.ts`, `restored-src/src/services/SessionMemory/prompts.ts` |
| `prompts/advanced/session-compaction.md` | advanced | `restored-src/src/services/compact/prompt.ts` |
| `prompts/advanced/subagent-delegation.md` | advanced | `restored-src/src/tools/AgentTool/prompt.ts`, `restored-src/src/skills/bundled/batch.ts` |
| `prompts/advanced/magic-doc-updater.md` | advanced | `restored-src/src/services/MagicDocs/prompts.ts` |

## Skill 资产

| 资产 | 状态 | 主要来源 |
| --- | --- | --- |
| `skills/stable/update-config` | stable | `restored-src/src/skills/bundled/updateConfig.ts` |
| `skills/stable/keybindings-help` | stable | `restored-src/src/skills/bundled/keybindings.ts` |
| `skills/stable/verify` | stable | `restored-src/src/skills/bundled/verify.ts`, `restored-src/src/tools/AgentTool/built-in/verificationAgent.ts` |
| `skills/stable/simplify` | stable | `restored-src/src/skills/bundled/simplify.ts` |
| `skills/advanced/remember` | advanced | `restored-src/src/skills/bundled/remember.ts` |
| `skills/advanced/batch` | advanced | `restored-src/src/skills/bundled/batch.ts` |
| `skills/advanced/claude-in-chrome` | advanced | `restored-src/src/skills/bundled/claudeInChrome.ts`, `restored-src/src/utils/claudeInChrome/prompt.ts` |
| `skills/advanced/loop` | advanced | `restored-src/src/skills/bundled/loop.ts` |
| `skills/advanced/schedule-remote-agents` | advanced | `restored-src/src/skills/bundled/scheduleRemoteAgents.ts`, `restored-src/src/tools/RemoteTriggerTool/prompt.ts` |
| `skills/advanced/claude-api` | advanced | `restored-src/src/skills/bundled/claudeApi.ts` |
| `skills/experimental/debug` | experimental | `restored-src/src/skills/bundled/debug.ts` |
| `skills/experimental/skillify` | experimental | `restored-src/src/skills/bundled/skillify.ts` |
| `skills/experimental/stuck` | experimental | `restored-src/src/skills/bundled/stuck.ts` |
| `skills/experimental/lorem-ipsum` | experimental | `restored-src/src/skills/bundled/loremIpsum.ts` |

## MCP 资产

| 资产 | 类型 | 主要来源 |
| --- | --- | --- |
| `mcp/tool-specs/core-capabilities.md` | mcp-doc | `restored-src/src/services/mcp/client.ts`, `restored-src/src/services/mcp/utils.ts` |
| `mcp/tool-specs/naming-and-permissions.md` | mcp-doc | `restored-src/src/services/mcp/utils.ts`, `restored-src/src/services/mcp/client.ts` |
| `mcp/tool-specs/deferred-tool-search.md` | mcp-doc | `restored-src/src/tools/ToolSearchTool/prompt.ts`, `restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts` |
| `mcp/tool-specs/resources.md` | mcp-doc | `restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts`, `restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts` |
| `mcp/tool-specs/browser-mcp.md` | mcp-doc | `restored-src/src/utils/claudeInChrome/prompt.ts`, `restored-src/src/skills/bundled/claudeInChrome.ts` |
| `mcp/workflows/knowledge-base.md` | mcp-doc | `restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts`, `restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts` |
| `mcp/workflows/developer-tools.md` | mcp-doc | `restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts`, `restored-src/src/services/mcp/client.ts` |
| `mcp/workflows/browser-automation.md` | mcp-doc | `restored-src/src/utils/claudeInChrome/prompt.ts` |
| `mcp/prompts/resource-first.md` | mcp-doc | `restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts`, `restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts` |
| `mcp/prompts/tool-discovery.md` | mcp-doc | `restored-src/src/tools/ToolSearchTool/prompt.ts`, `restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts` |
| `mcp/prompts/browser-runbook.md` | mcp-doc | `restored-src/src/utils/claudeInChrome/prompt.ts` |
| `mcp/config-templates/server-template.jsonc` | template | `restored-src/src/services/mcp/client.ts` |
| `mcp/config-templates/permissions-template.jsonc` | template | `restored-src/src/services/mcp/utils.ts` |
| `mcp/config-templates/browser-server-template.md` | template | `restored-src/src/utils/claudeInChrome/prompt.ts`, `restored-src/src/skills/bundled/claudeInChrome.ts` |

## 未纳入主包但值得知道的项

- `dream`, `hunter`, `runSkillGenerator`：在 bundled index 里以 feature gate 形式引用，但本恢复源码目录里没有对应实现文件，无法做稳定提取。
- `officialRegistry.ts`：与 Anthropic 官方 MCP 注册表判断有关，保留了思路，但没有单独做成资产包条目。
- `verifyContent.ts` / `claudeApiContent.ts`：分别引用了内联打包内容或超大文档集，本包只保留可迁移结构，不原样搬运大体积内容。
