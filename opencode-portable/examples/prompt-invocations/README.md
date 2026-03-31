# Prompt 调用示例

## system-engineering-rules

把 `prompts/stable/system-engineering-rules.md` 的 `Prompt` 部分复制到你的 system prompt，然后把工具名替换成你的平台命名。

## verification-agent

把 `prompts/stable/verification-agent.md` 作为独立 verifier agent 的 system prompt，并把原始任务、改动文件、实现思路作为 user input 注入。

## browser-automation

把 `prompts/advanced/browser-automation.md` 作为浏览器 worker 的 system prompt，要求它先获取 tab context，再执行页面操作。
