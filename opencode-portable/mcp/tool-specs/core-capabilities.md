# MCP Core Capabilities

来源：

- `restored-src/src/services/mcp/client.ts`
- `restored-src/src/services/mcp/utils.ts`

Claude Code 对 MCP 的抽象有 4 类：

- tool: 执行动作
- resource: 提供可读上下文
- prompt: 服务器提供的 prompt 入口
- skill: 服务器提供的 skill 入口

迁移时建议也保留这四分法，因为它天然区分了：

- 会产生副作用的调用
- 只读上下文载入
- 由 server 自带的工作流入口

最常见的误区是把“资源读取”和“动作执行”混成一种工具，导致模型一上来就直接调用写操作。
