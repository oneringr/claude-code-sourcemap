# 延迟工具搜索

来源：

- `restored-src/src/tools/ToolSearchTool/prompt.ts`
- `restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts`

Claude Code 默认把很多 MCP tool 视为 deferred tool，也就是：

- 初始上下文里只有名字
- 真正调用前要先加载 schema
- 只按需暴露少量工具，避免 prompt 膨胀

推荐链路：

1. 如果知道精确工具名，直接 `select:<tool_name>`
2. 如果只知道功能或 server，用关键词搜索
3. 一次只加载当前步骤需要的几个 schema

这套机制最适合：

- OpenAPI 生成的巨大 MCP server
- browser / SaaS / enterprise 工具集
- 多 connector 并存的会话
