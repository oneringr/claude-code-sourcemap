# opencode-portable

这是一个从 Claude Code 恢复源码里提炼出来的“便携资产包”。它不复制 Claude Code 的运行时实现，而是把高价值内容重组为更容易迁移到 `opencode` 或其他 agent 的三类资产：

- `prompts/`: 可直接复制到 system / developer / task prompt 的模板
- `skills/`: 可直接导入或改造的 `SKILL.md`
- `mcp/`: MCP 的调用提示、工作流、权限与配置模板

这个目录默认按“通用便携包”设计，不假设目标平台已经兼容 Claude Code 的内部接口或命名。

## 快速开始

1. 先读 [docs/usage-guide.md](docs/usage-guide.md)
2. 再选用 `prompts/stable/` 里的基础 prompt
3. 按需导入 `skills/stable/` 或 `skills/advanced/`
4. 如果要接外部系统，先看 `mcp/tool-specs/` 和 `mcp/workflows/`
5. 最后用 [docs/migration-notes.md](docs/migration-notes.md) 把 Claude Code 专有能力映射到你的平台

## 状态分层

- `stable`: 通用、高价值、可直接复用
- `advanced`: 依赖子代理、浏览器、调度器、远程触发器等高级能力
- `experimental`: 内部、实验性、强平台绑定或仅适合研究参考

## 目录概览

```text
opencode-portable/
  README.md
  manifest.json
  prompts/
  skills/
  mcp/
  docs/
  examples/
```

## 设计边界

- Prompt 资产：直接可复制，但你需要把 Claude Code 工具名替换成目标平台的能力名
- Skill 资产：优先保留“流程结构”和“成功标准”，不强绑 Claude Code 的命令路由
- MCP 资产：提供可接入说明和模板，不包含 Claude Code 的 TypeScript runtime

## 溯源

- 详细来源索引见 [docs/source-index.md](docs/source-index.md)
- 现有提炼底稿见 [../docs/claude-code-prompts-skills-mcp-guide.md](../docs/claude-code-prompts-skills-mcp-guide.md)
