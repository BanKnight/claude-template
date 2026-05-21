# verify-change 命令

## 定位

`verify-change` 用于验证指定 change 的实现是否与其承诺保持一致，并产出可追踪证据。

它是 Build 之后、Distill/Archive 之前的质量门禁。

`verify-change` 的核心不是一次性检查，而是建立并迭代验证回路：

```text
实现 → 验证 → 调整 → 再验证
```

没有可重复、可信的验证信号，不能声称 verify 通过。

## change 参数

执行本命令时必须确定目标 change。

- 如果用户传入 change 名称，使用该名称。
- 如果用户没有传入 change 名称，先读取 `.workflow/roadmap.md` 与 `.workflow/changes/`，列出当前活跃 changes，并询问用户要验证哪个 change。
- 如果上下文里似乎能推断 change，也不能直接猜测或自动选择；必须让用户确认。
- 不要在未确认 change 的情况下创建或更新 `verify.md`。

选择 change 时，应优先展示 3-4 个最相关或最近活跃的 change，并显示：

- change-id
- 所属 roadmap 阶段或 milestone
- 当前状态 / 下一步
- change 路径
- tasks 完成概况（如可读取）

## 主动触发

当满足以下情况时，AI 可以主动建议或进入 `verify-change`：

- `implement-change` 已完成一个或多个任务。
- 用户要求确认实现是否完成、是否能收口、是否能归档。
- change 准备进入 `distill-change` 或 `archive-version`。
- 实现过程出现偏离 specs/design 的风险，需要质量对齐。
- 用户要求跑测试、做 e2e、检查一致性或验收。

不要在以下情况强行进入：

- 目标 change 尚未确认。
- change 还没有 specs/tasks，缺少可验证承诺。
- 用户仍在澄清、roadmap、spec 或 design 阶段。
- 没有实现结果，也没有用户要求做预验证。

## 参考资料

执行时按需读取：

- [methodology.md](./references/verify-change/methodology.md) — verify-change 的 Trace / Delta / Scenario / Evidence 方法论。
- [harness.md](./references/verify-change/harness.md) — harness 类型、选择顺序与质量标准。
- [severity-and-reflow.md](./references/verify-change/severity-and-reflow.md) — 问题分级、误报控制与回流规则。

## 输入

必须读取：

```text
.workflow/roadmap.md
.workflow/changes/<change-id>/intents.md
.workflow/changes/<change-id>/specs/
.workflow/changes/<change-id>/design/
.workflow/changes/<change-id>/plan.md
.workflow/changes/<change-id>/tasks.md
.workflow/templates/changes/verify.md
```

按需读取：

```text
docs/specs/
docs/design/
docs/architecture/
docs/runbooks/
```

按需检查：

```text
代码变更
git diff / git status
测试文件
测试命令与输出
运行日志、截图、trace、benchmark
.workflow/changes/<change-id>/artifacts/
```

读取规则：

- specs 是 what 承诺。
- design 是 how 承诺。
- plan/tasks 是实现承诺与执行证据。
- docs 是长期约束来源。
- artifacts 是验证证据存放位置。
- 如果某类 artifact 缺失，允许降级验证，但必须在 `verify.md` 中记录跳过原因。

## 不负责

`verify-change` 不负责：

- 编写或修改功能实现。
- 重写 specs。
- 重写 design。
- 拆解任务。
- 沉淀长期 docs。
- 归档 change。

如果发现必须修改实现、spec、design 或 plan，记录问题和回流建议，交给对应命令处理。

## 状态检查

确认目标 change 后，先检查：

1. `.workflow/changes/<change-id>/` 是否存在。
2. `intents.md` 是否存在。
3. `specs/` 是否存在。
4. `design/` 是否存在，或是否有充分理由不需要 design。
5. `plan.md` 是否存在。
6. `tasks.md` 是否存在。
7. `verify.md` 是否已存在。
8. `artifacts/` 是否存在；不存在则按需创建。
9. roadmap 中该 change 是否已经进入可验证阶段。

根据状态处理：

- 如果 specs 缺失，停止并提示先执行 `specify-change`。
- 如果 plan.md 或 tasks.md 缺失，停止并提示先执行 `plan-change`。
- 如果没有实现结果，说明只能做预验证，不能给出最终通过结论。
- 如果 verify.md 已存在，读取后追加或更新本次验证轮次，不要直接覆盖历史结论。

## 验证方法

### 1. 建立验证 harness

先选择或构建可重复的验证信号。优先考虑能覆盖真实风险的 harness：

- unit test
- integration test
- e2e test
- HTTP / curl script
- CLI fixture + snapshot diff
- headless browser script
- replay trace
- throwaway harness
- property / fuzz loop
- differential loop
- HITL loop

harness 必须尽量做到：快、稳、准、可复现、可追踪。

如果无法构建可信 harness，必须在 `verify.md` 中说明尝试过什么、缺什么证据、需要用户提供什么环境或 artifact。

### 2. Trace 验证

建立承诺到证据的映射：

- intent → specs/design/tasks
- spec requirement → 实现位置
- scenario → 测试或手动验证证据
- design decision → 实现位置
- task checkbox → 代码/测试/证据

每个关键承诺都应有具体证据。代码引用使用 `file:line` 格式。

### 3. Delta 验证

检查本次实现差异：

- 是否超出 scope。
- 是否新增未被 specs/design 支撑的行为。
- 是否修改非目标区域。
- 是否引入额外风险。

### 4. Scenario 验证

从真实路径验证：

- 正常路径。
- 边界路径。
- 失败路径。
- 用户可见行为。

涉及 UI 时，应优先使用浏览器或 e2e harness 验证，而不仅是类型检查或单元测试。

### 5. Evidence 验证

收集并记录证据：

- 测试命令与结果。
- e2e/手动操作步骤。
- 截图、日志、trace、benchmark。
- 关键代码引用。
- artifacts 路径。

没有证据的“通过”不能写成通过，只能写成未验证或证据不足。

## 问题分级

每个问题必须归入：

- CRITICAL：必须修复后才能通过 verify。
- WARNING：可条件通过，但必须明确记录例外或后续项。
- SUGGESTION：不阻塞，但建议优化。

每个 CRITICAL/WARNING 必须包含：

- 问题描述。
- 对应承诺或证据。
- 影响范围。
- 建议回流命令：`implement-change` / `plan-change` / `design-change` / `specify-change`。
- 具体行动建议。

当不确定时，优先降级：

```text
CRITICAL → WARNING → SUGGESTION
```

不要用不确定判断阻塞 change；但必须说明不确定性来源和需要补充的证据。

## 迭代验证规则

`verify-change` 可以多轮执行。

每一轮必须记录：

- 轮次。
- 验证时间。
- 使用的 harness。
- 验证范围。
- 发现的问题。
- 调整建议。
- 本轮结论。

如果验证失败：

1. 记录失败与回流建议。
2. 回到相应命令修正。
3. 修正后重新执行 `verify-change`。
4. 新验证轮次必须重新运行相关 harness，不能复用旧通过结论。

## verify 写入格式

优先使用模板：

```text
.workflow/templates/changes/verify.md
```

输出路径：

```text
.workflow/changes/<change-id>/verify.md
```

`verify.md` 至少包含：

- change 概览。
- 验证轮次记录。
- harness 清单。
- Trace 验证矩阵。
- Delta 验证结论。
- Scenario 验证结论。
- Evidence 清单。
- Completeness / Correctness / Coherence 评分表。
- CRITICAL / WARNING / SUGGESTION 问题清单。
- 回流建议。
- 最终结论。

## 完成后输出

完成后简短汇报：

- 目标 change。
- 使用了哪些 harness。
- `verify.md` 创建或更新位置。
- 当前结论：通过 / 条件通过 / 不通过 / 证据不足。
- 如不通过，建议回到哪个命令。
- 如通过，解锁下一步：`distill-change`。

## 退出条件

当满足以下条件时，`verify-change` 可以结束：

- 目标 change 已确认。
- 已读取 specs/design/plan/tasks 或记录缺失原因。
- 已选择或尝试建立验证 harness。
- 已执行 Trace / Delta / Scenario / Evidence 验证，或记录无法执行原因。
- 每个问题都有分级、证据和回流建议。
- `verify.md` 已创建或更新。
- 最终结论明确。
- 如果存在 CRITICAL，明确不能进入 distill/archive。
- 如果无 CRITICAL，说明是否可以进入 `distill-change`。
