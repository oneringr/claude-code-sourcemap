# 资源型 MCP

来源：

- `restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts`
- `restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts`

标准流程：

1. 列资源
2. 选择 server + uri
3. 读取资源正文
4. 仅当资源不够时再转动作工具

要点：

- 资源型调用优先于动作型调用
- 二进制资源不要原样塞进上下文，应持久化到磁盘并返回路径
- 某个 server 资源读取失败，不应拖垮其他 server 的枚举结果
