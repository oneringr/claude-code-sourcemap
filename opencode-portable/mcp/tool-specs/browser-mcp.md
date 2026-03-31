# 浏览器型 MCP

来源：

- `restored-src/src/utils/claudeInChrome/prompt.ts`
- `restored-src/src/skills/bundled/claudeInChrome.ts`

浏览器型 MCP 与普通知识库型 server 不同，建议独立处理：

1. 先获取 tab context
2. 再决定复用现有 tab 还是开新 tab
3. 控制调用次数，避免在失败动作上死循环
4. 优先读取 console、DOM、截图，而不是用自然语言猜前端状态
5. 避免触发 alert / confirm / prompt 这类阻塞式对话框
