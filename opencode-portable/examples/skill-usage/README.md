# Skill 使用示例

```text
/verify 修复注册接口 400/500 混淆的问题
/simplify 重点检查重复逻辑和无效状态
/update-config 允许 bun test 不再弹权限确认
/batch 把所有 fetch 调用统一迁移到 api client
/loop 5m 检查 staging deploy 状态
```

如果目标平台没有 slash command，可以把 `SKILL.md` 的正文当成“任务模板”，由编排层在匹配到对应意图时注入。
