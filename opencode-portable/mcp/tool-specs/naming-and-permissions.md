# MCP 命名与权限粒度

来源：

- `restored-src/src/services/mcp/utils.ts`
- `restored-src/src/services/mcp/client.ts`

Claude Code 中常见的命名模式：

- `mcp__server__tool`
- `mcp__server__prompt`
- `server:skill`

它背后的关键不是名字本身，而是权限粒度：

- server 级：允许整个 server
- server 下全部工具级：允许该 server 的所有动作
- 单工具级：只允许某一个工具

建议目标平台也支持至少两档：

1. server 级
2. 单工具级

如果做不到单工具级，至少要在 prompt 中提醒模型：加载到 schema 不等于允许执行。
