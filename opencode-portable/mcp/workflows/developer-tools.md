# 开发工具型 MCP 流程

推荐流程：

1. 明确任务目标：读信息还是写状态
2. 如果只是查询，优先 resource 或 list 类工具
3. 如果必须执行动作，再搜索具体工具 schema
4. 按最小权限粒度调用
5. 调用后把返回值当作外部数据校验

适用场景：

- issue tracker
- CI/CD
- observability
- feature flag
- ticket / project 管理工具
