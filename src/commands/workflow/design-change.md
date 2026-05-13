# design-change 命令

## 定位

`design-change` 用于为指定 change 产出 how 设计材料。

它读取该 change 的来源意图、行为契约 specs、项目现状和必要的长期上下文，然后在以下目录中创建或更新本 change 需要的设计文件：

```text
.workflow/changes/<change-id>/design/
```

`design-change` 只回答：为了实现这个 change，应采用怎样的设计方案、边界、取舍和风险控制。

本命令不把设计结论直接写入 `docs/`。长期 design、architecture、ADR 等沉淀由后续 `distill-how` 在验证后完成。

## 阶段推进原则

`design-change` 是本项目工作流中的 change 级 design 阶段：

- 不猜测目标 change；不明确时必须让用户选择。
- 先检查当前 change 状态，再决定是否创建 design。
- 只推进 `design-change` 阶段，不跳到 `plan-task`、实现、验证或沉淀。
- 创建 design 前必须读取已完成依赖：change intents、change specs、必要的长期 docs。
- 模板、规则和上下文是给 Agent 的约束，不要把说明性注释或规则原样复制到最终 design 文件。
- 创建后必须验证文件存在，再汇报进度和解锁的下一步。

在本工作流中，`design/` 目录视为一个阶段 artifact；一次 `design-change` 可以创建本 change 需要的多个 design 子域文件，但不能创建无关子域文件，也不能继续推进后续阶段。

## design 创建条件

`design-change` 不要求每个 change 都创建完整 design 子域。

只有满足以下任一条件时，才创建独立 design 文件：

- 跨模块、跨服务、跨页面或跨 capability。
- 引入新的架构模式、外部依赖、数据模型或迁移策略。
- 涉及安全、性能、兼容性、权限、错误处理或发布风险。
- specs 中存在实现路径不明确、需要先做技术决策的要求。
- 用户明确要求先沉淀 how 设计。
- 后续 `plan-task` 无法在没有设计材料的情况下安全拆解任务。

如果 change 很轻量，且 specs 已足以指导实现，可以只创建 `overview.md` 说明：

- 为什么不需要更多设计子域。
- 可以直接进入 `plan-task` 的依据。
- 是否存在后续 `distill-how` 候选。

## change 参数

执行本命令时必须确定目标 change。

- 如果用户传入 change 名称，使用该名称。
- 如果用户没有传入 change 名称，先读取 `.workflow/roadmap.md` 与 `.workflow/changes/`，列出当前活跃 changes，并询问用户要指定哪个 change。
- 如果上下文里似乎能推断 change，也不能直接猜测或自动选择；必须让用户确认。
- 不要在未确认 change 的情况下创建 design。

选择 change 时，应优先展示 3-4 个最相关或最近活跃的 change，并显示：

- change-id
- 所属 version
- 当前状态 / 下一步
- change 路径
- 最近上下文依据（如当前焦点或 roadmap 排序）

可以把最可能的 change 标记为“推荐”，但仍必须由用户选择。

## 主动触发

当满足以下情况时，AI 可以主动建议或进入 `design-change`：

- roadmap 中某个 change 已经完成 `specify-change`，但还没有 design。
- 用户确认要开始设计某个 change 的 how。
- 后续 `plan-task` 或实现前缺少设计依据。
- change 涉及架构、接口、数据、UI/UX、业务规则、异常处理、迁移、性能、安全等需要明确取舍的内容。

不要在以下情况强行进入：

- 目标 change 尚未进入 roadmap。
- change 缺少来源 intents。
- change 缺少 specs，且没有用户明确要求先做探索性 design。
- 用户正在讨论 roadmap 编排，而不是具体 change 设计。

## 输入

必须读取：

```text
.workflow/roadmap.md
.workflow/changes/<change-id>/intents.md
.workflow/changes/<change-id>/specs/
```

按需读取：

```text
docs/specs/
docs/design/
docs/architecture/
docs/runbooks/
.workflow/changes/<change-id>/design/
```

读取规则：

- `.workflow/changes/<change-id>/intents.md` 是 change 来源上下文。
- `.workflow/changes/<change-id>/specs/` 是本 change 的 what 基线。
- `docs/` 用于识别项目现有约束、长期设计、架构边界和可复用模式。
- 已有 change design 存在时，优先更新，不要直接覆盖。
- 读取依赖材料后再创建 design，不要凭 change-id 或文件名猜测方案。

## 不负责

`design-change` 不负责：

- 重新规划 roadmap。
- 改写行为契约 specs。
- 拆分 tasks。
- 执行实现。
- 做验证、沉淀或归档。
- 更新长期 `docs/`。

这些动作交给后续 workflow 命令。

## 状态检查

确认目标 change 后，先检查当前状态：

1. `.workflow/changes/<change-id>/` 是否存在。
2. `.workflow/changes/<change-id>/intents.md` 是否存在。
3. `.workflow/changes/<change-id>/specs/` 是否存在。
4. `.workflow/changes/<change-id>/design/` 是否已存在。
5. roadmap 中该 change 的下一步是否为 `design-change`，或是否仍缺少 design。
6. 是否满足 design 创建条件；如果不满足，是否只需要 `overview.md` 说明可直接进入 `plan-task`。

根据状态处理：

- 如果 change 缺少 `intents.md`，停止并提示先回到 `plan-roadmap` 补齐 change 骨架。
- 如果 specs 缺失，默认停止并提示先执行 `specify-change`；除非用户明确要求先做探索性 design。
- 如果 design 已存在，读取现有 design 后更新，不要直接覆盖。
- 如果 design 已完整且下一步不是 `design-change`，说明当前状态，并询问是否仍要修改 design。

## 子域路由规则

`design-change` 按子域拆分 design 文件，但只创建本 change 需要的设计文件。

常见子域：

| 子域 | 触发场景 | 输出文件 |
|---|---|---|
| overview | 所有非平凡 change，用于汇总上下文、设计范围和子域索引 | `overview.md` |
| product | 用户流程、产品边界、信息架构、体验路径 | `product.md` |
| ui-ux | 页面结构、交互模式、页面状态、可用性 | `ui-ux.md` |
| frontend | 前端模块、组件边界、状态管理、路由 | `frontend.md` |
| architecture | 系统分层、模块关系、技术选型、演进策略 | `architecture.md` |
| api | 接口定义、请求响应、协议、鉴权、兼容性 | `api.md` |
| data | 数据模型、表结构、迁移、索引、一致性 | `data.md` |
| business-rules | 业务规则、状态流转、计算规则、约束 | `business-rules.md` |
| error-handling | 异常场景、错误码、失败处理、重试、降级 | `error-handling.md` |
| risks | 跨子域风险、权衡、开放问题 | `risks.md` |

规则：

- `overview.md` 用于说明本次 design 选择了哪些子域，以及为什么没有选择其他子域。
- 只有相关子域才创建对应文件。
- 不要为了显得完整而创建空设计文件。
- 如果设计过程中发现新子域必要，可以补充创建对应文件。
- 如果某个子域只是几句话，可以写入 `overview.md`，不必单独建文件。

## 执行规则

1. 确认目标 change。
2. 检查 change 当前状态。
3. 读取 change intents 与 specs。
4. 按需读取 `docs/` 中相关长期上下文。
5. 判断本 change 需要哪些设计子域。
6. 创建或更新：

```text
.workflow/changes/<change-id>/design/<subdomain>.md
```

7. 使用 `.workflow/templates/changes/design/` 下对应模板作为结构基础。
8. 设计必须说明关键决策、权衡、风险和开放问题。
9. 设计必须能指导 `plan-task`，但不要直接拆 tasks。
10. 不把长期沉淀写入 `docs/`；只在设计中标记后续可由 `distill-how` 提炼的内容。
11. 创建后验证文件存在，再汇报进度。

## 与长期沉淀的关系

`design-change` 产出的是运行态 design。

```text
.workflow/changes/<change-id>/design/
```

它不会直接更新：

```text
docs/design/
docs/architecture/
docs/architecture/adr/
```

原因：

- change 设计在实现和验证前仍可能变化。
- 长期 HOW 应基于已实现且已验证的结果沉淀。
- `distill-how` 会在 verify 之后，从本 change 的 design、实现结果和验证证据中提炼长期内容。

因此，design 文件中可以记录“后续沉淀候选”，但不能代替 `distill-how`。

## design 写入格式

优先使用模板：

```text
.workflow/templates/changes/design/<subdomain>.md
```

输出路径：

```text
.workflow/changes/<change-id>/design/<subdomain>.md
```

每个 design 文件至少应说明：

- change-id
- 输入依据
- 设计范围
- 关键决策
- 风险与权衡
- 开放问题
- 后续沉淀候选

## 完成后输出

完成后简短汇报：

- 目标 change。
- 创建或更新了哪些 design 子域文件。
- 当前阶段已完成：`design-change`。
- 解锁的下一步：`plan-task`。
- 哪些内容可能在 `distill-how` 阶段沉淀到长期 docs。
- 如果仍有阻塞，说明阻塞原因。

## 退出条件

当满足以下条件时，`design-change` 可以结束：

- 目标 change 已确认。
- change intents 与 specs 已读取。
- 需要的设计子域已明确。
- 相关子域 design 文件已创建或更新。
- 关键决策、权衡、风险和开放问题齐全。
- 没有把 tasks、实现代码或长期 docs 沉淀混入本阶段。
- 已验证 design 文件存在。
- 下一步可以进入 `plan-task`。
