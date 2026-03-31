# 迁移说明

这份资产包保留了 Claude Code 中最有价值的结构，但默认不要求目标平台与它同构。迁移时重点处理下面几类差异。

## 工具命名映射

建议按能力，而不是按名字做映射：

| Claude Code 名称 | 建议映射到的通用能力 |
| --- | --- |
| `Read` | 只读文件工具 |
| `Edit` | 局部补丁编辑工具 |
| `Write` | 覆盖式写文件工具 |
| `Bash` | shell / subprocess 工具 |
| `ToolSearch` | 延迟工具发现器 |
| `Skill` | slash command / skill 调用器 |
| `Agent` | 子代理 / worker / fork |

## MCP 命名映射

Claude Code 常见命名：

- `mcp__server__tool`
- `mcp__server__prompt`
- `server:skill`

迁移时不一定要保持完全同名，但要保留这 3 个概念上的分层：

- 动作工具
- 资源读取
- prompt / skill 入口

## 强平台绑定能力

下面这些能力如果目标平台没有，就不要硬迁：

- plan mode
- worktree isolation
- background agents
- claude.ai remote trigger
- ant-only auto-memory
- Chrome 扩展式浏览器 MCP

优先迁移“流程”，不要迁移“外壳”。

## 建议的最小适配策略

1. 把 `prompts/stable/` 直接接到 system/developer prompt
2. 把 `skills/stable/` 转成你平台支持的 skill 或 command 格式
3. 按 `mcp/tool-specs/` 重建 MCP 发现链路
4. 只在平台确实支持时再接 `advanced/` 与 `experimental/`

## 本包不做的事

- 不复制 Claude Code 内部 TypeScript runtime
- 不假设 `opencode` 已兼容 Claude Code 的 MCP 或 skill API
- 不把 Anthropic 内部产品功能伪装成通用能力
