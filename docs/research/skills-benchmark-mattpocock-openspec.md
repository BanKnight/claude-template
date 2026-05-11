# skills 对标调研：mattpocock/skills 与 Fission-AI/OpenSpec

本文聚焦两类目标：

1. 工作流约束（包含输入输出规范）。
2. 效果提升（如何让技能更稳定、更高质量）。

## 一、来源与范围

- 仓库 A：`/home/deploy/repos/skills`（mattpocock/skills）
- 仓库 B：`/home/deploy/repos/OpenSpec`（Fission-AI/OpenSpec）

本调研重点抽取可迁移到我们技能体系（`guid/setup + clarify/design/version/task/align/distill + quality skills`）的机制。

## 二、工作流约束与输入输出规范（可直接借鉴）

### 1) 强约束的依赖与阶段门禁

| 模式 | 来源 | 借鉴点 |
|---|---|---|
| 硬依赖与软依赖分离 | `skills/docs/adr/0001-explicit-setup-pointer-only-for-hard-dependencies.md` | 仅在硬依赖场景强提示 setup，避免每个技能都重复初始化提示。 |
| 阶段不可跳跃 | `skills/skills/engineering/diagnose/SKILL.md` | 例如“未复现就不能进入下一阶段”，可用于 `clarify -> design`、`task -> align` 的门禁。 |
| 依赖图驱动可执行状态 | `OpenSpec/src/core/artifact-graph/graph.ts` | 用 blocked/ready/all_done 机制明确下一步，不靠口头判断。 |
| apply 前置条件 | `OpenSpec/src/commands/workflow/instructions.ts` | 没有 tasks 或 tasks 为空时不可执行，实现“先设计后实现”的硬约束。 |

### 2) 输入输出标准化模板

| 模式 | 来源 | 借鉴点 |
|---|---|---|
| 固定章节模板（PRD/Issue/Design/Tasks） | `skills/skills/engineering/to-prd/SKILL.md`、`to-issues/SKILL.md`、`OpenSpec/schemas/spec-driven/templates/*.md` | 为每个技能定义必填段落，降低输出风格漂移。 |
| 任务格式可解析 | `OpenSpec/schemas/spec-driven/templates/tasks.md` | 使用严格 checkbox 编号格式，便于状态机追踪。 |
| 规则文本常驻导入 | `OpenSpec/docs/customization.md` + `.openspec.yaml` 体系 | 我们已在 `CLAUDE.md/AGENTS.md` 导入治理文档，后续可继续强化“配置优先级”。 |
| 机器可读输出 | `OpenSpec ... --json` 命令群 | 我们可在技能执行结果中固定输出“状态摘要块”（可机读）。 |

### 3) 质量门禁与回流

| 模式 | 来源 | 借鉴点 |
|---|---|---|
| 严格验证（结构错误直接失败） | `OpenSpec/src/core/validation/validator.ts` | 对关键文档（spec/tasks/trace）做硬校验。 |
| 软告警（允许继续但需确认） | `OpenSpec/src/core/archive.ts` | 对非致命问题保留人工确认，避免过度阻塞。 |
| 失败后回流修正 | `OpenSpec/docs/workflows.md`、`docs/opsx.md` | 对应我们的 `reopen` 机制：quality 不通过则回到 version/task loop。 |

## 三、效果提升策略（针对我们技能）

### A. 约束强化（防跑偏）

1. `clarify`
   - 以 `grill-with-docs` 作为综合型质量参考：需求收集、意图澄清、docs/code grounding、术语校准、范围边界、场景反例测试与沉淀判断一起完成。
   - 以 OpenSpec 的阶段合同约束输出：`blocked / needs_research / clarified`，并明确输入、门禁、后续阶段输入与落盘规则。

2. `design`
   - 采用 design-router，但每次路由必须输出“为什么走这些子域，为什么不走其他子域”。
   - 对跨模块变更，强制附带 `风险与权衡` 段落。

3. `version`
   - 增加状态机：`draft -> planned -> in_progress -> quality_check -> closed | reopened`。
   - 状态迁移必须有证据字段（引用 trace/review/test/security）。

4. `task`
   - 使用统一可解析任务格式（编号 + checkbox + blockedBy）。
   - 发布顺序按依赖拓扑，而非书写顺序。

5. `align`
   - 固定检查维度：`Completeness / Correctness / Coherence`。
   - 输出分级：`CRITICAL / WARNING / SUGGESTION`。

### B. 可观测性强化（可量化改进）

建议在 `.workflow/progress.md` 固定记录以下指标：

- blocked 任务数与持续时长
- quality gate 首次通过率
- reopen 次数
- spec 变更冲突次数
- tasks 完成率（按版本）

这些指标直接对应 OpenSpec 的状态驱动思想，能够把“感觉质量好不好”转成“有数据可追踪”。

## 四、对我们现有技能的映射清单

| 我们的技能 | 主要学习来源 | 可落地增强点 |
|---|---|---|
| `guid` | `OpenSpec` onboarding workflow | 保持交互式引导，严格不自动执行 setup。 |
| `setup` | `skills` 的 hard-dependency 思想 + `OpenSpec` 配置优先级 | 明确“初始化成功 + 索引是否需重建”的双输出。 |
| `clarify` | `grill-with-docs` + OpenSpec | 作为综合入口：grill 式需求收集、意图澄清、docs/code grounding、术语/场景压测、状态门禁与 `.workflow` 落盘。 |
| `design` | OpenSpec 结构化模板 | design-router 输出标准化，含决策理由与风险。 |
| `version` | OpenSpec artifact graph/state | 状态机与迁移门禁。 |
| `task` | OpenSpec tasks 模板 + to-issues | 可解析任务格式 + 依赖顺序发布。 |
| `align` | OpenSpec verify/validate | 分级质量结论 + 失败回流。 |
| `distill` | skills 的 durability writing discipline | 强调长期可复用表达，避免易失细节。 |
| `review/test/security/simplify` | OpenSpec gates + skills checklists | 强/软双门禁与固定 checklist。 |

## 五、建议的下一步（按优先级）

1. 先定义四个核心技能的 IO 合同（`clarify/design/version/align`）。
2. 给 `version/task` 建立状态机和可解析字段。
3. 把 quality gate 结果结构化到 `.workflow/versions/<v>/` 标准文件中。
4. 再补 `task/distill` 与质量类技能细化。

这样可以先保证主干闭环，再扩展枝叶能力。
