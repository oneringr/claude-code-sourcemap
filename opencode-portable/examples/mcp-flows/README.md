# MCP 流程示例

## 文档型 server

1. 先列资源
2. 选择 URI
3. 读取资源
4. 仅在需要写操作时再加载工具

## 开发型 server

1. 先根据任务搜索工具
2. 只加载 1-2 个相关 schema
3. 调用前确认权限粒度

## 浏览器型 server

1. 先获取 tab context
2. 再选择具体 browser tool
3. 遇到 alert / confirm 这类阻塞对话框时立即停下
