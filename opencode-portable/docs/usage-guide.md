# 使用说明

这份资产包面向“已经有 agent 运行环境，但缺少高质量 prompt / skill / MCP 设计”的场景。最推荐的接入顺序如下。

## 1. 先落基础 prompt

先从下面 3 个 prompt 开始：

- [../prompts/stable/system-engineering-rules.md](../prompts/stable/system-engineering-rules.md)
- [../prompts/stable/verification-agent.md](../prompts/stable/verification-agent.md)
- [../prompts/stable/mcp-tool-discovery.md](../prompts/stable/mcp-tool-discovery.md)

它们分别解决：

- 默认工程行为
- 结果验证
- MCP 工具的按需发现与上下文节流

## 2. 再接 skill

推荐按这组顺序引入：

1. `update-config`
2. `verify`
3. `simplify`
4. `keybindings-help`
5. `batch`
6. `claude-in-chrome`

如果你的平台还没有子代理、浏览器自动化或计划模式，先只接 `stable` 目录中的 skill。

## 3. 最后再接 MCP

接 MCP 时不要一开始就把所有 server 和所有 schema 塞给模型。优先采用下面的链路：

1. 列出 server 或可用资源
2. 选择目标 server / resource
3. 只加载相关 tool schema
4. 先读资源，再执行动作

对应材料：

- [../mcp/tool-specs/core-capabilities.md](../mcp/tool-specs/core-capabilities.md)
- [../mcp/tool-specs/deferred-tool-search.md](../mcp/tool-specs/deferred-tool-search.md)
- [../mcp/tool-specs/resources.md](../mcp/tool-specs/resources.md)
- [../mcp/workflows/knowledge-base.md](../mcp/workflows/knowledge-base.md)

## 4. 推荐的最小可用组合

如果你只想先做一个“够用”的版本，建议直接落这套：

- system prompt: `system-engineering-rules`
- verifier prompt: `verification-agent`
- skills: `update-config`, `verify`, `simplify`
- MCP 工作流: `resource-first`, `tool-discovery`

## 5. 你需要自己替换的能力位点

这包里的文档和 prompt 有一些默认命名来自 Claude Code。迁移时至少要替换这些：

- `Read` / `Edit` / `Write` / `Bash`
- `ToolSearch`
- `Skill`
- `Agent`
- `mcp__server__tool`
- `claude-in-chrome`

对应替换建议见 [migration-notes.md](migration-notes.md)。
