# distill-change 命令

## 定位

`distill-change` 用于在 `verify-change` 之后，把单个 change 中已经验证过、值得长期复用的知识沉淀回 `docs/`。

它统一处理长期知识沉淀：

- 长期 WHAT：`docs/specs/`
- 长期 design：`docs/design/`
- 长期 HOW / architecture / ADR：`docs/architecture/`
- project big picture：按需更新 `docs/project.md`

`distill-change` 不是归档命令。它只负责让长期知识变成主线文档，归档由后续 `archive-version` 处理。

## 长期 specs 合并原则

- change specs 是增量，不是主线全量文档。
- 回写长期 specs 时，应按 ADDED / MODIFIED / REMOVED / RENAMED 语义合并。
- 合并应尽量幂等；重复执行不应产生重复 requirement。
- 发现冲突时，应明确报告并要求人工处理。
- 本命令还要从 verified design、实现结果和验证证据中提炼长期 design / architecture / ADR。

## change 参数

执行本命令时必须确定目标 change。

- 如果用户传入 change 名称，使用该名称。
- 如果用户没有传入 change 名称，先读取 `.workflow/roadmap.md` 与 `.workflow/changes/`，列出当前活跃 changes，并询问用户要 distill 哪个 change。
- 如果上下文里似乎能推断 change，也不能直接猜测或自动选择；必须让用户确认。
- 不要在未确认 change 的情况下更新 `docs/`。

选择 change 时，应优先展示 3-4 个最相关或最近活跃的 change，并显示：

- change-id
- 所属 version / roadmap 阶段
- 当前状态 / 下一步
- change 路径
- verify 结论（如可读取）

## 主动触发

当满足以下情况时，AI 可以主动建议或进入 `distill-change`：

- `verify-change` 已通过，且 change 准备收口或归档。
- 用户要求“沉淀”“同步长期文档”“更新 docs”“归纳本次变更经验”。
- change specs/design/verify 中存在未来版本会复用的 WHAT、HOW、架构决策、项目认知或 runbook 信息。
- archive 前发现 change 的长期知识尚未写回 `docs/`。

不要在以下情况强行进入：

- 目标 change 尚未确认。
- change 还没有 verify 证据。
- `verify.md` 中存在未解决 CRITICAL。
- 用户仍在 clarify、roadmap、spec、design、plan 或 implementation 阶段。

## 输入

必须读取：

```text
.workflow/roadmap.md
.workflow/changes/<change-id>/intents.md
.workflow/changes/<change-id>/specs/
.workflow/changes/<change-id>/design/
.workflow/changes/<change-id>/verify.md
```

按需读取：

```text
.workflow/changes/<change-id>/plan.md
.workflow/changes/<change-id>/tasks.md
.workflow/changes/<change-id>/artifacts/
docs/project.md
docs/specs/
docs/design/
docs/architecture/
docs/runbooks/
```

按需检查：

```text
代码变更
git diff / git status
测试输出、截图、日志、benchmark
```

读取规则：

- `verify.md` 是进入长期沉淀的质量门禁。
- specs 是 WHAT 增量来源。
- design 是 HOW 候选来源。
- plan/tasks/implementation 是实际落地结果来源。
- docs 是长期主线知识目标，更新前必须读取现有内容，避免重复、冲突或覆盖历史。
- 如果长期文档不存在，可以创建；如果已存在，优先增量更新。

## 不负责

`distill-change` 不负责：

- 修改功能实现。
- 重新设计方案。
- 重新拆解任务。
- 修复 verify 问题。
- 归档 version 或移动 change 目录。

如果发现实现、spec、design 或 verify 仍需要修正，应停止沉淀并回流到对应命令。

## 状态检查

确认目标 change 后，先检查：

1. `.workflow/changes/<change-id>/` 是否存在。
2. `specs/` 是否存在。
3. `design/` 是否存在，或是否有充分理由不需要 HOW 沉淀。
4. `verify.md` 是否存在。
5. `verify.md` 是否存在未解决 CRITICAL。
6. roadmap 中该 change 是否处于 verify 之后、archive 之前。
7. `docs/` 中是否已有相关长期 specs/design/architecture/project 文档。

根据状态处理：

- 如果缺少 `verify.md`，停止并提示先执行 `verify-change`。
- 如果 verify 结论为不通过或存在未解决 CRITICAL，停止并提示回流修正。
- 如果 specs 缺失，只能沉淀 HOW/project knowledge，且必须说明没有 WHAT 可同步。
- 如果 design 缺失，只能沉淀 WHAT/project knowledge，且必须说明没有 HOW 可提炼。
- 如果长期 docs 已经包含等价内容，不要重复写入；记录“无需更新”。

## 执行规则

1. 确认目标 change。
2. 检查 change 当前状态和 verify 结论。
3. 读取 specs/design/verify，以及必要的 plan/tasks/artifacts/代码变更。
4. 读取相关长期 docs，判断哪些知识需要沉淀。
5. 分别处理 WHAT / HOW / project knowledge。
6. 更新对应 `docs/` 文件。
7. 更新对应目录的 `index.md`。
8. 汇报更新了哪些长期文档、哪些内容无需沉淀、哪些内容仍阻塞。

## WHAT 沉淀规则

WHAT 沉淀目标是：

```text
docs/specs/<capability>/spec.md
```

处理规则参考 OpenSpec specs sync：

- `## ADDED Requirements`：新增到长期 spec；如果同名 requirement 已存在，则更新为 change 中版本，避免重复。
- `## MODIFIED Requirements`：替换长期 spec 中同名 requirement。
- `## REMOVED Requirements`：从长期 spec 中移除同名 requirement，并保留必要迁移说明。
- `## RENAMED Requirements`：按 FROM/TO 重命名；如果内容也变化，按新名称应用 MODIFIED。

合并要求：

- 使用 `### Requirement:` 标题作为匹配标识。
- 保持 requirement + scenario 的可验证结构。
- 不把 HOW、任务或实现细节写入 `docs/specs/`。
- 重复执行不应产生重复 requirement。
- 找不到要修改或移除的 requirement 时，报告冲突，不要猜测合并。

如果 capability 对应长期 spec 不存在，创建：

```text
docs/specs/<capability>/spec.md
```

并补充必要的 Purpose / Requirements 骨架。

## HOW / design 沉淀规则

HOW 沉淀目标包括：

```text
docs/design/
docs/architecture/
docs/architecture/adr/
```

从以下来源提炼：

- `.workflow/changes/<change-id>/design/`
- `verify.md` 中被证实有效的设计决策
- 实现结果与测试证据
- plan/tasks 中实际完成的关键路径

沉淀原则：

- 不复制整个 change design。
- 只提炼未来版本会复用的设计结论、模式、边界、约束或决策。
- 只沉淀已被实现和 verify 支撑的内容。
- 未验证或被推翻的设计只能保留在 change 上下文，不进入长期 docs。
- 如果是当前系统结构说明，写入 `docs/architecture/`。
- 如果是特定功能或流程设计，写入 `docs/design/`。
- 如果是关键架构取舍，写入 `docs/architecture/adr/ADR-YYYYMMDD-*.md`。

长期 HOW 文档应面向“当前主线状态”，不是 change 过程记录。

## project knowledge 沉淀规则

如果本 change 改变或补充了项目整体认知，应按需更新：

```text
docs/project.md
```

适合写入 `docs/project.md` 的内容：

- 项目定位变化。
- 关键用户场景变化。
- 领域概念或术语变化。
- 长期工程原则变化。
- 对后续 roadmap/design/implementation 有持续影响的项目级事实。

不要把单次实现细节或临时任务状态写入 `docs/project.md`。

## docs 索引规则

凡新增、移动、删除或修改 `docs/` 下文档，必须同步维护对应目录的 `index.md`。

规则：

- 每层目录只索引当前层的直接子目录和直接文档。
- 每个文档条目必须有一句话描述。
- 描述必须基于阅读文档内容后编写，不能从文件名猜测。
- 如果新增目录，也要创建或更新该目录的 `index.md`。

## 完成后输出

完成后简短汇报：

- 目标 change。
- verify 结论。
- 更新了哪些长期 WHAT 文档。
- 更新了哪些长期 HOW / architecture / ADR 文档。
- 是否更新了 `docs/project.md`。
- 哪些内容判断为无需沉淀。
- 是否仍有冲突或阻塞。
- 解锁的下一步：`archive-version`。

## 退出条件

当满足以下条件时，`distill-change` 可以结束：

- 目标 change 已确认。
- 已读取 verify 证据，且不存在未解决 CRITICAL。
- 已检查 WHAT / HOW / project knowledge 是否需要长期沉淀。
- 需要沉淀的 specs 已合并到 `docs/specs/`。
- 需要沉淀的 design/architecture/ADR 已写入 `docs/design/` 或 `docs/architecture/`。
- 如影响 project big picture，`docs/project.md` 已更新。
- 对应 docs 索引已同步维护。
- 没有只留在 `.workflow/changes/<change-id>/` 中、但未来仍需要复用的知识。
- 下一步可以进入 `archive-version`。
