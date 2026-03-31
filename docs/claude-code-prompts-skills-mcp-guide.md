# Claude Code 提效手册：从恢复源码提取 Prompt、Skills 与 MCP 用法

这份手册基于本仓库恢复出来的 Claude Code `2.1.88` 源码整理，目标读者是“想把 Claude Code 的方法迁移到任意 Agent”的用户，而不是只服务 Claude Code 原生用户。

它不试图复刻 Claude Code 的所有内部实现，而是提炼三类最有价值的资产：

- 可迁移的 prompt 原则与模板
- 值得借鉴的 skills 设计
- MCP 工具的发现、加载、资源读取与动作调用流程

注意：

- 这是一份“源码提炼手册”，不是官方文档。
- 文中把可迁移程度标成四类：`通用`、`Claude Code 专用`、`高级`、`内部/实验`。
- 只收录“实用 + 高级”的内容；明显内部或 ant-only 的部分会放到限制说明。

## 这份手册怎么用

建议按这个顺序读：

1. 先看“10 条高价值 prompt 原则”，拿走最通用的工作方法。
2. 再看“prompt 模板”，直接把模板改造成你自己的 agent 指令。
3. 如果你在设计 agent 能力层，重点看“高价值 skills 清单”。
4. 如果你在接外部系统，重点看“MCP 工具实战说明”。
5. 最后看“推荐工作流”，把原则、skills、MCP 串成完整链路。

高价值源码入口：

- 系统 prompt 与任务约束：[../restored-src/src/constants/prompts.ts](../restored-src/src/constants/prompts.ts)
- 验证代理 prompt：[../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts)
- 浏览器自动化 prompt：[../restored-src/src/utils/claudeInChrome/prompt.ts](../restored-src/src/utils/claudeInChrome/prompt.ts)
- 内置 skills 总表：[../restored-src/src/skills/bundled/index.ts](../restored-src/src/skills/bundled/index.ts)
- MCP 资源工具：[../restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts](../restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts)、[../restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts](../restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts)
- MCP 延迟加载：[../restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts](../restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts)
- MCP 注册与连接：[../restored-src/src/services/mcp/client.ts](../restored-src/src/services/mcp/client.ts)

## 从仓库里提炼出的 10 条高价值 prompt 原则

| 原则 | 你应该怎么用 | 主要来源 |
| --- | --- | --- |
| 1. 先读后改 | 用户点名某个文件或模块时，先读实现，再提出修改或方案。不要“凭印象设计改动”。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 2. 少建新文件 | 默认优先编辑现有文件；只有现有结构无法承载目标时才新建。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 3. 失败先诊断，再切策略 | 命令失败后先看报错、检查假设、做小修复；不要盲目重复，也不要一遇阻力就放弃。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 4. 风险操作先确认 | push、删文件、改共享基础设施、向外部系统发消息，默认都应先确认 blast radius。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 5. 忠实汇报验证结果 | 没跑就说没跑，失败就贴关键输出，不要把“看起来没问题”包装成“已验证通过”。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 6. 验证的目标是“找出破绽” | 验证不是为了宣布成功，而是主动尝试打破实现，尤其是最后 20% 的边角行为。 | [verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts) |
| 7. 解释不能代替命令 | 遇到需要验证的地方，优先跑真实命令、真实页面、真实接口，而不是写“我会怎么测”。 | [verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts) |
| 8. 先盘点现有能力，再说“做不到” | 在验证或自动化场景里，先检查浏览器、MCP、WebFetch 等现有工具，再决定是否需要用户介入。 | [verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts) |
| 9. 外部工具输出默认不完全可信 | 工具结果可能包含外部数据或注入内容；对外部返回值、网页、MCP 结果都要保持警惕。 | [prompts.ts](../restored-src/src/constants/prompts.ts) |
| 10. 上下文要按需加载 | MCP 工具默认延迟加载，资源按 server + URI 读取；不要一上来把所有 schema 和结果都塞进上下文。 | [ToolSearchTool/prompt.ts](../restored-src/src/tools/ToolSearchTool/prompt.ts)、[ListMcpResourcesTool.ts](../restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts) |

对普通用户最值得立即照搬的是 1、3、5、6、10。这五条几乎能直接提升任何 coding agent 的稳定性。

## 可直接复用的 prompt 模板

这一节分成“方法论模板”和“关键原文短摘”。短摘只保留极短的原始措辞，方便你回头对照源码；真正应该复用的是模板和结构。

### 模板 1：先探索再实施

标签：`通用`

用途：

- 用于任何非小修小补的开发任务
- 尤其适合“我先看看仓库怎么组织，再决定怎么改”的场景

适用场景：

- 新接手项目
- 跨模块改动
- 用户描述模糊，但仓库里可探索出真实约束

推荐模板：

```text
你是一个代码代理。先不要直接改代码。

第一阶段：探索
1. 先定位相关文件、配置、测试和入口。
2. 总结当前实现、约束和风险。
3. 只在仓库无法回答时才向用户提问。

第二阶段：实施
1. 只修改已经读过的文件。
2. 优先复用现有实现，不做额外抽象。
3. 如果方案失败，先诊断报错再切换策略。

第三阶段：验证
1. 运行与改动直接相关的构建、测试或手工验证。
2. 明确说明哪些已经验证、哪些没有验证。
```

Claude Code 对应来源：

- [prompts.ts](../restored-src/src/constants/prompts.ts)

迁移到其他 agent 时要替换的能力位点：

- `Read/Grep/Glob` 换成你的 agent 的代码浏览能力
- “只在仓库无法回答时再提问”换成你所在平台的澄清策略

关键原文短摘：

- `read it first`

### 模板 2：验证代理

标签：`通用` `高级`

用途：

- 给主代理配一个“挑刺型 verifier”
- 防止 agent 只会跑 happy path 或只会读代码不落验证

适用场景：

- 3 个以上文件改动
- 后端/API、CLI、前端、基础设施变更
- 你想把“实现”和“验收”拆给两个不同 agent

推荐模板：

```text
你是验证代理。你的任务不是证明实现看起来合理，而是尽量把它弄坏。

约束：
1. 不要修改项目文件。
2. 先读 README / CLAUDE.md / 包管理脚本，找到真实的 build/test 命令。
3. 对每一项检查都给出：命令、关键输出、结论。

最低要求：
1. 运行构建。
2. 运行测试。
3. 运行 lint/type-check（如果项目有）。
4. 做至少一个对抗性探测：边界值、并发、重复提交、异常输入、空输入、资源不存在。

报告规则：
1. 没有命令输出，不算 PASS。
2. 没跑的检查必须明确标注。
3. 最终只输出 PASS / FAIL / PARTIAL，并附最关键证据。
```

Claude Code 对应来源：

- [verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts)
- [verify.ts](../restored-src/src/skills/bundled/verify.ts)
- [verifyContent.ts](../restored-src/src/skills/bundled/verifyContent.ts)

迁移到其他 agent 时要替换的能力位点：

- 浏览器验证能力换成你自己的 Playwright/Browser 工具
- temp 目录写临时脚本的约束换成你当前环境的 scratch 目录

关键原文短摘：

- `stop. Run the command.`

### 模板 3：浏览器自动化

标签：`通用` `高级`

用途：

- 让 agent 在“真实浏览器任务”里少绕路
- 特别适合登录态页面、控制台排障、流程录制、页面交互验证

适用场景：

- 调试真实用户浏览器中的页面
- 需要截图、点击、填表、读 console、保留 GIF 记录

推荐模板：

```text
你现在负责浏览器自动化。

启动顺序：
1. 先获取当前标签上下文。
2. 只有用户明确要求复用旧标签时才复用；否则新建标签。
3. 如果标签失效或被关闭，重新获取标签上下文。

执行规则：
1. 保持任务聚焦，不做无关探索。
2. console 输出优先按 pattern 过滤。
3. 避免触发 alert / confirm / prompt 这类阻塞性对话框。
4. 多步流程需要复盘时，录制 GIF。
5. 同一动作失败 2-3 次后，汇报已尝试内容并向用户确认。
```

Claude Code 对应来源：

- [claudeInChrome/prompt.ts](../restored-src/src/utils/claudeInChrome/prompt.ts)
- [skills/bundled/claudeInChrome.ts](../restored-src/src/skills/bundled/claudeInChrome.ts)

迁移到其他 agent 时要替换的能力位点：

- `tabs_context_mcp` 换成你的浏览器上下文查询接口
- `gif_creator`、`read_console_messages` 换成你自己的浏览器工具名

关键原文短摘：

- `tabs_context_mcp first`

### 模板 4：大改动分批执行

标签：`通用` `高级`

用途：

- 把机械性、可拆分的大改动交给多个 worker 并行完成
- 适合大规模替换、迁移、批量重命名、分目录改造

适用场景：

- 很多文件会改
- 每个子任务可以在隔离 worktree 或独立分支里完成
- 你希望每个子任务有独立 PR

推荐模板：

```text
你是协调者，不是直接执行者。

阶段 1：研究与规划
1. 先做研究，找出所有受影响的目录、模式和调用点。
2. 把任务拆成 5-30 个可独立合并的单元。
3. 为每个单元写清楚：目标、文件范围、验证方法。

阶段 2：并行执行
1. 每个 worker 只负责一个独立单元。
2. worker 结束前必须做一次代码简化/清理。
3. worker 必须运行最相关的测试。
4. worker 输出统一的结果格式，方便协调者汇总。

阶段 3：汇总
1. 汇总所有单元的状态、PR、失败原因。
2. 对失败单元单独给出补救建议。
```

Claude Code 对应来源：

- [skills/bundled/batch.ts](../restored-src/src/skills/bundled/batch.ts)
- [coordinatorMode.ts](../restored-src/src/coordinator/coordinatorMode.ts)

迁移到其他 agent 时要替换的能力位点：

- `worktree` 隔离换成你的分支、工作区或沙箱机制
- PR 创建换成你所在平台的提交/审查流程

关键原文短摘：

- `5–30 isolated worktree agents`

### 模板 5：把“自动做某事”改写成配置任务

标签：`通用`

用途：

- 当用户说“每次”“之前”“之后”“从现在开始”时，避免 agent 误用记忆层
- 把自动行为落成 hook、settings、cron、workflow，而不是口头承诺

适用场景：

- 自动格式化
- 自动运行测试
- 自动记录 bash
- 权限默认值
- 项目级别的固定行为

推荐模板：

```text
如果用户要求“以后自动做某事”，先判断这是：
1. 记忆/偏好
2. 配置
3. hook
4. 定时任务

如果它需要在某个事件发生时自动触发，就不要放进“记住”，而要落成配置。

处理顺序：
1. 先澄清作用域：全局、项目、个人本地覆盖。
2. 先读现有配置，再做增量合并。
3. 对数组和规则做 merge，不要整块覆盖。
4. 写入后做语法/结构验证。
```

Claude Code 对应来源：

- [skills/bundled/updateConfig.ts](../restored-src/src/skills/bundled/updateConfig.ts)

迁移到其他 agent 时要替换的能力位点：

- `settings.json` / hooks 结构换成你的平台配置系统
- `AskUserQuestion` 换成你自己的澄清交互机制

关键原文短摘：

- `hooks, not memory`

## 高价值 skills 清单

下面只收录“对普通用户真有借鉴价值”的 skill。

### 日常提效

#### `verify`

标签：`通用` `高级` `内部/实验`

- 是什么：一个“运行型验证” skill，强调通过真实命令、真实页面、真实接口去验证改动，而不是读代码后口头宣布成功。
- 什么时候用：任务结束前；尤其是改动较大、涉及外部行为、需要独立验收时。
- 输入格式/调用示例：`/verify 检查登录修复是否真的覆盖了 session 过期场景`
- 隐含前提：项目里要有可执行的 build/test/run 路径；如果涉及前端，最好还有浏览器自动化工具。
- 是否通用可迁移：很高。几乎可以直接改写成任何 agent 的 verifier prompt。
- 风险或限制：源码里它是 ant-only 注册项；公开环境不一定直接可用。
- 源码出处：[verify.ts](../restored-src/src/skills/bundled/verify.ts)、[verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts)

#### `simplify`

标签：`通用` `高级`

- 是什么：一个“改完之后再做一次代码审查”的 skill，会从复用、质量、效率三个角度重新审视 diff。
- 什么时候用：功能做完但还没交付前；适合防止“能跑但很糙”的实现。
- 输入格式/调用示例：`/simplify 优先看有没有重复逻辑和热路径开销`
- 隐含前提：最好有 git diff，或者至少有一组最近变更的文件可供审查。
- 是否通用可迁移：高。你可以把它实现成“二次审稿 agent”。
- 风险或限制：它鼓励并行起 3 个 review agents，比较吃工具和 token。
- 源码出处：[simplify.ts](../restored-src/src/skills/bundled/simplify.ts)

#### `remember`

标签：`通用` `高级` `内部/实验`

- 是什么：一个“记忆整理” skill，用来把 auto-memory 里的内容提炼到 `CLAUDE.md`、`CLAUDE.local.md` 或 team memory。
- 什么时候用：会话积累了很多经验、约定、冲突项之后，想做一次记忆清理和晋升。
- 输入格式/调用示例：`/remember 把最近关于测试命令和提交规范的内容整理一下`
- 隐含前提：有 auto-memory 机制，且项目里有 `CLAUDE.md`/`CLAUDE.local.md` 这类记忆层。
- 是否通用可迁移：中高。核心思路可迁移，但需要你自己的 memory 分层。
- 风险或限制：源码里是 ant-only；并且它默认只“提出建议”，不直接落盘。
- 源码出处：[remember.ts](../restored-src/src/skills/bundled/remember.ts)

### 配置与环境

#### `update-config`

标签：`通用`

- 是什么：把“以后自动怎样怎样”的要求，翻译成 settings、permissions、hooks、env、MCP 开关等真实配置。
- 什么时候用：用户说“以后每次”“当 X 时自动”“允许某类命令”“加一个环境变量”时。
- 输入格式/调用示例：`/update-config 允许 npm test 无需提示，并在写入 ts/js 文件后自动跑 prettier`
- 隐含前提：有明确的配置层级，例如全局、项目、项目本地覆盖。
- 是否通用可迁移：很高。核心价值是“把自动化放进配置，不放进记忆”。
- 风险或限制：必须先读现有配置再 merge；数组和 hook 列表不能整体覆盖。
- 源码出处：[updateConfig.ts](../restored-src/src/skills/bundled/updateConfig.ts)

#### `keybindings-help`

标签：`Claude Code 专用`

- 是什么：一个专门帮助编辑 `~/.claude/keybindings.json` 的隐藏 helper skill。
- 什么时候用：用户想改快捷键、解绑默认键、增加 chord 组合键时。
- 输入格式/调用示例：实际更像用户请求触发，例如“rebind ctrl+s”或“add a chord shortcut”。
- 隐含前提：你的产品有明确的 keybinding schema、context、action 列表。
- 是否通用可迁移：中。思路可迁移，但内容高度依赖你的按键系统。
- 风险或限制：它在源码里 `userInvocable: false`，更像一个被路由系统自动调用的帮助模块。
- 源码出处：[keybindings.ts](../restored-src/src/skills/bundled/keybindings.ts)

### 自动化与高级编排

#### `batch`

标签：`通用` `高级`

- 是什么：一个批量改造协调 skill，会先研究，再拆分成 5-30 个独立单元，最后并行让 worker 在隔离 worktree 中改并开 PR。
- 什么时候用：大量文件、机械性迁移、可按目录或模块拆分的改动。
- 输入格式/调用示例：`/batch replace all uses of lodash with native equivalents`
- 隐含前提：必须在 git 仓库里；最好有 PR 流程和多 worker 能力。
- 是否通用可迁移：高。非常适合作为“大规模改造编排器”模板。
- 风险或限制：对工作区隔离、分支、PR、测试基础设施要求较高。
- 源码出处：[batch.ts](../restored-src/src/skills/bundled/batch.ts)

#### `claude-in-chrome`

标签：`Claude Code 专用` `高级`

- 是什么：把真实 Chrome 会话暴露成一组浏览器自动化 MCP 工具。
- 什么时候用：需要登录态、真实浏览器、用户现有标签页、页面交互、console 读取、截图、GIF 录制时。
- 输入格式/调用示例：`/claude-in-chrome 打开后台仪表盘，检查报错并截图`
- 隐含前提：安装扩展、允许站点权限、相关 MCP 工具可用。
- 是否通用可迁移：中高。浏览器自动化方法论可迁移，但具体工具名不可直接照搬。
- 风险或限制：必须先获取标签上下文；避免触发阻塞性对话框；某些环境还需要先经 `ToolSearch` 加载工具。
- 源码出处：[claudeInChrome.ts](../restored-src/src/skills/bundled/claudeInChrome.ts)、[claudeInChrome/prompt.ts](../restored-src/src/utils/claudeInChrome/prompt.ts)

#### `loop`

标签：`通用` `高级` `内部/实验`

- 是什么：把一个 prompt 或 slash command 包装成“按固定间隔重复执行”的任务。
- 什么时候用：轮询部署状态、周期性检查 PR、定时跑固定 prompt。
- 输入格式/调用示例：`/loop 5m check the deploy`
- 隐含前提：底层有 cron/create 之类的调度工具；系统支持 recurring prompt。
- 是否通用可迁移：中高。适合作为“轻量周期任务”模板。
- 风险或限制：源码里受 feature gate 控制；最小粒度和 cron 映射逻辑依赖具体实现。
- 源码出处：[loop.ts](../restored-src/src/skills/bundled/loop.ts)

#### `schedule`

标签：`Claude Code 专用` `高级` `内部/实验`

- 是什么：管理远程 scheduled agents，不是本地 cron，而是 Claude 云端的 remote trigger。
- 什么时候用：想让远程 agent 定时拉仓库、跑检查、发报告、带上已连接的 MCP connector。
- 输入格式/调用示例：`/schedule 每个工作日早上 9 点检查 deploy，并通过 Slack 汇报异常`
- 隐含前提：必须有 claude.ai 登录、远程环境、可用 connector、策略允许远程会话。
- 是否通用可迁移：中。思路可迁移，但实现明显绑定云端基础设施。
- 风险或限制：它不是本地自动化；connector UUID、remote environment、GitHub 接入都带强产品耦合。
- 源码出处：[scheduleRemoteAgents.ts](../restored-src/src/skills/bundled/scheduleRemoteAgents.ts)

## MCP 工具实战说明

### 先把四种对象分清楚

| 类型 | 命名方式 | 作用 | 推荐用法 |
| --- | --- | --- | --- |
| MCP tool | `mcp__server__tool` | 执行动作、调用外部能力 | 通常先 `ToolSearch`，再调用 |
| MCP prompt | `mcp__server__prompt` | 服务器提供的 prompt 型命令 | 当成命令看待，不当成资源 |
| MCP skill | `server:skill` | 服务器暴露的 skill 命令 | 在 skill 菜单或命令路由中出现 |
| MCP resource | `server + uri` | 读取静态或半静态上下文 | 先列资源，再读资源 |

来源：

- [services/mcp/utils.ts](../restored-src/src/services/mcp/utils.ts)

最重要的区别是：resource 更像“读资料”，tool 更像“做动作”。

### 命名与权限粒度

Claude Code 对 MCP 权限有三档粒度：

- `mcp__server`：整个 server 级别
- `mcp__server__*`：server 下全部工具
- `mcp__server__tool`：单个工具

这意味着你在设计自己的 agent 权限系统时，也最好支持“整组能力”和“单个能力”两层，而不是只有“全开/全关”。

来源：

- [permissionValidation.ts](../restored-src/src/utils/settings/permissionValidation.ts)
- [permissions.ts](../restored-src/src/utils/permissions/permissions.ts)

### 为什么很多 MCP 工具要先过 `ToolSearch`

仓库里把 MCP 工具默认视为 deferred tool，也就是：

- 初始 prompt 里通常只有名字，没有完整 schema
- 真正调用前，要先通过 `ToolSearch` 拉取完整定义
- 这样能显著减少初始上下文体积

对普通用户的启发：

- 不要把所有外部工具的 schema 一次性塞给模型
- 先给“发现入口”，再按需加载具体工具

来源：

- [ToolSearchTool/prompt.ts](../restored-src/src/tools/ToolSearchTool/prompt.ts)
- [ToolSearchTool.ts](../restored-src/src/tools/ToolSearchTool/ToolSearchTool.ts)

### 资源型 MCP 的标准流程

适合文档库、知识库、系统状态快照、设计规范、接口说明之类的 server。

推荐流程：

1. 先调用 `listMcpResources`，拿到有哪些资源和对应 `server`。
2. 根据 `server + uri` 选中目标资源。
3. 用 `readMcpResource` 读取正文。
4. 只有当资源不够、必须产生动作时，再去加载并调用 `mcp__server__tool`。

对应工具：

- [ListMcpResourcesTool.ts](../restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts)
- [ReadMcpResourceTool.ts](../restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts)

注意点：

- `ListMcpResourcesTool` 和 `ReadMcpResourceTool` 只有在某个 server 支持 resources 时才会被加入工具集。
- `ReadMcpResourceTool` 会把二进制内容落盘，避免把 base64 原样塞进上下文。

### 动作型 MCP 的标准流程

适合 Slack、GitHub、Datadog、Playwright、浏览器操作、数据库动作等。

推荐流程：

1. 如果你已经知道精确工具名，直接：
   - `ToolSearch(query="select:mcp__server__tool")`
   - 然后调用该工具
2. 如果你只知道大概能力，不知道确切工具名：
   - `ToolSearch(query="server action keyword")`
   - 看匹配结果
   - 再选择最具体的工具
3. 如果一个 server 同时有 resources 和 tools，优先用 resource 读上下文，再决定是否执行动作。

一个简单的心法：

- 先读上下文，再做动作
- 先加载最小工具集，再扩大
- 如果用户只要“查”，尽量别直接“改”

### 浏览器类 MCP 的专门流程

对 `claude-in-chrome` 这类浏览器型 MCP，不要按普通资源型 server 的思路来用。

推荐流程：

1. 先激活 skill：`claude-in-chrome`
2. 如果当前会话启用了 deferred tool，再用 `ToolSearch` 加载需要的 `mcp__claude-in-chrome__*` 工具
3. 第一个动作永远是获取标签上下文
4. 然后再决定是否新建标签、复用标签、读取 console、录 GIF
5. 如果标签失效，重新拉上下文，不要拿旧 tab ID 硬试

来源：

- [claudeInChrome.ts](../restored-src/src/skills/bundled/claudeInChrome.ts)
- [claudeInChrome/prompt.ts](../restored-src/src/utils/claudeInChrome/prompt.ts)

### 三组最值得迁移的 MCP 流程

#### 1. 文档 / 知识库型 MCP

适合：

- 内部文档
- API 手册
- 设计规范
- FAQ

流程：

1. `list resources`
2. `read resource`
3. 摘取需要的上下文
4. 如有必要，再加载动作工具

#### 2. 开发工具型 MCP

适合：

- GitHub
- Slack
- Datadog
- 数据库巡检
- CI 状态读取

流程：

1. `ToolSearch` 找到具体动作
2. 精确加载单一工具
3. 调用
4. 返回结构化结果
5. 不要在没有必要时加载整个 server 的所有工具

#### 3. 浏览器 / 操作型 MCP

适合：

- Playwright
- Chrome 扩展桥接
- 用户真实浏览器

流程：

1. 获取上下文
2. 选标签
3. 逐步操作
4. 需要调试时读 console
5. 多步流程需要复盘时录制

## 推荐工作流

### 工作流 1：修一个真实 bug

适合：

- API 错误
- 页面异常
- CLI 行为不对

推荐顺序：

1. 用“先探索再实施”模板定位代码与测试
2. 实施修复
3. 跑 `simplify`
4. 跑 `verify`
5. 忠实汇报 build/test/手工验证结果

### 工作流 2：把“以后自动做某事”真正落地

适合：

- 自动格式化
- 自动测试
- 命令白名单
- 会话默认行为

推荐顺序：

1. 判断这是 memory 还是 config
2. 如果需要事件触发，就用 `update-config`
3. 先读现有配置，再 merge
4. 写入后做语法/结构验证
5. 给用户一份可回滚的改动说明

### 工作流 3：做大规模机械性改造

适合：

- 批量替换 API
- 统一导入路径
- 大量重命名

推荐顺序：

1. 用 `batch` 先研究并拆单元
2. 每个 worker 独立改动
3. 每个 worker 完成后先 `simplify`
4. 对关键单元单独跑 `verify`
5. 汇总 PR、失败单元和后续合并顺序

### 工作流 4：排查真实浏览器问题

适合：

- 登录态页面
- OAuth 流程
- 线上用户页面报错

推荐顺序：

1. 激活 `claude-in-chrome`
2. 拉标签上下文
3. 新建或复用标签
4. 执行最少必要动作
5. 用 console 过滤看报错
6. 需要时截图或录 GIF

## 不建议照搬的内部 / 实验项

这些内容可以理解，但不建议原样复制到自己的 agent 里：

- ant-only skills：`verify`、`remember`、`skillify`、`stuck`、`lorem-ipsum`
- 远程 trigger 的云端细节：connector UUID 解析、环境创建、claude.ai OAuth
- feature gate 驱动的能力开关：`loop`、`schedule`、`dream`、`hunter`
- `keybindings-help` 这类产品专用 helper skill
- MCP 官方注册表判断逻辑。它对 Anthropic 自身产品有意义，但不是通用 agent 的核心能力。
- “MCP 工具默认延迟加载”的具体实现细节。你可以借鉴思路，但不必复制完全相同的机制。

对应来源：

- [skills/bundled/index.ts](../restored-src/src/skills/bundled/index.ts)
- [scheduleRemoteAgents.ts](../restored-src/src/skills/bundled/scheduleRemoteAgents.ts)
- [officialRegistry.ts](../restored-src/src/services/mcp/officialRegistry.ts)

## 附：关键原文摘录与来源索引

下面只保留极短原文，方便你回源码定位。真正应该复用的是上面的模板和结构。

### 系统 prompt

- `read it first`
- `Report outcomes faithfully`
- `prompt injection`

来源：

- [prompts.ts](../restored-src/src/constants/prompts.ts)

### 验证代理

- `verification avoidance`
- `first 80%`
- `Run the command`

来源：

- [verificationAgent.ts](../restored-src/src/tools/AgentTool/built-in/verificationAgent.ts)

### 浏览器自动化

- `GIF recording`
- `Alerts and dialogs`
- `tabs_context_mcp first`

来源：

- [claudeInChrome/prompt.ts](../restored-src/src/utils/claudeInChrome/prompt.ts)

### MCP 发现与加载

- `select:<tool_name>`
- `always deferred`
- `server + uri`

来源：

- [ToolSearchTool/prompt.ts](../restored-src/src/tools/ToolSearchTool/prompt.ts)
- [ListMcpResourcesTool.ts](../restored-src/src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts)
- [ReadMcpResourceTool.ts](../restored-src/src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts)

## 最后给普通用户的结论

如果你只想拿走最有用的三件事：

1. 把“先探索后实施 + 忠实汇报 + 对抗式验证”固化成你的默认 system prompt。
2. 把 `update-config`、`simplify`、`verify` 这三个 skill 思路迁移到你自己的 agent。
3. 设计 MCP 时优先做“按需加载 + 资源先读后动手”的工作流，而不是把所有工具一次性暴露给模型。
