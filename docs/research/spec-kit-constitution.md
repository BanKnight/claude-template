# spec-kit constitution 机制调研

## 背景

本调研用于理解 `github/spec-kit` 中 constitution 命令与模板的设计，并对比当前工作流模板中的 `constitution-workflow`。

参考文件：

- `/home/deploy/repos/spec-kit/templates/commands/constitution.md`
- `/home/deploy/repos/spec-kit/templates/constitution-template.md`
- `/home/deploy/repos/spec-kit/presets/lean/commands/speckit.constitution.md`
- `/home/deploy/repos/spec-kit/presets/self-test/templates/constitution-template.md`

## spec-kit 的 constitution 定位

spec-kit 的 constitution command 描述为：

> Create or update the project constitution from interactive or provided principle inputs, ensuring all dependent templates stay in sync.

它的目标文件是：

```text
.specify/memory/constitution.md
```

如果目标文件不存在，则从以下模板初始化：

```text
.specify/templates/constitution-template.md
```

这说明 spec-kit 的 constitution 不是普通说明文档，而是项目规格工作流中的治理源。它负责把项目原则落到可被后续 spec / plan / tasks 模板检查的约束中。

## spec-kit constitution template 结构

`templates/constitution-template.md` 的核心结构是：

```text
# [PROJECT_NAME] Constitution

## Core Principles

### [PRINCIPLE_1_NAME]
[PRINCIPLE_1_DESCRIPTION]

...

## [SECTION_2_NAME]
[SECTION_2_CONTENT]

## [SECTION_3_NAME]
[SECTION_3_CONTENT]

## Governance
[GOVERNANCE_RULES]

Version / Ratified / Last Amended
```

关键特征：

- 以 `Core Principles` 记录项目不可轻易违反的核心准则。
- 支持额外约束区，例如安全、性能、开发流程、质量门禁等。
- `Governance` 用于说明修订流程、版本策略和合规检查方式。
- 文件底部保留版本、批准日期和最后修订日期。

## spec-kit constitution command 的关键步骤

从 `templates/commands/constitution.md` 可以看到，它的执行流程包含：

1. 读取 `.specify/memory/constitution.md`。
2. 找出模板中的占位符，例如 `[PROJECT_NAME]`、`[PRINCIPLE_1_NAME]`。
3. 从用户输入或仓库上下文中收集/推导具体值。
4. 按语义版本规则决定 constitution 版本变化：
   - MAJOR：删除或重定义原则，可能导致既有治理不兼容。
   - MINOR：新增原则或显著扩展指导。
   - PATCH：澄清措辞、修复错误、非语义调整。
5. 替换占位符，生成没有未解释 token 的 constitution。
6. 执行一致性传播检查。
7. 在 constitution 顶部写入 Sync Impact Report。
8. 验证版本、日期、原则措辞是否清晰可检查。
9. 写回 `.specify/memory/constitution.md`。
10. 输出版本变化、后续跟进项和建议 commit message。

## spec-kit 的一致性传播

spec-kit 的一个重要特点是：constitution 更新后，会检查依赖模板是否同步。

它明确要求读取并检查：

```text
.specify/templates/plan-template.md
.specify/templates/spec-template.md
.specify/templates/tasks-template.md
.specify/templates/commands/*.md
README.md / docs/quickstart.md / agent guidance files
```

也就是说，constitution 不只是“写一份原则”，还会反向约束 spec-kit 的模板体系：

```text
constitution
  ↓
spec-template
  ↓
plan-template
  ↓
tasks-template
```

如果 constitution 增加了测试、观测、版本策略等原则，后续模板必须能体现这些要求。

## spec-kit 的 hook 机制

spec-kit constitution command 还会检查扩展钩子：

```text
.specify/extensions.yml
hooks.before_constitution
hooks.after_constitution
```

它区分 optional hook 与 mandatory hook：

- optional hook：提示用户可以执行。
- mandatory hook：要求执行并等待结果。

当前工作流模板还没有 extensions / hook 层，因此没有吸收这部分机制。

## 与当前 workflow constitution 的差异

| 维度 | spec-kit constitution | 当前 workflow constitution |
|---|---|---|
| 目标位置 | `.specify/memory/constitution.md` | `.workflow/constitution.md` |
| 主要服务对象 | spec-first 工作流 | 全工程 workflow |
| 约束重点 | spec / plan / tasks 模板一致性 | roadmap / specify / design / plan / implement / verify / distill 全链路 |
| 模板传播 | 明确检查并同步 spec-kit templates | 当前先要求后续命令读取 constitution 作为约束 |
| hook 支持 | 支持 before/after constitution hooks | 暂未引入 hook 层 |
| 文件结构 | Core Principles + Governance + 版本信息 | 核心原则 + 项目约束 + 治理规则 + 版本信息 |
| 版本策略 | MAJOR / MINOR / PATCH | 已吸收 MAJOR / MINOR / PATCH 思路 |
| Sync Impact Report | 写入 constitution 顶部 | 当前尚未设计，可后续考虑 |

## “规格驱动项目准则”的含义

spec-kit 的 constitution 更偏“规格驱动项目准则”，意思是：

它的准则主要用于约束规格驱动链路中的 artifact 生成与检查：

```text
constitution → spec → plan → tasks
```

例如：

- spec 是否必须包含某类需求或约束。
- plan 是否必须执行 Constitution Check。
- tasks 是否必须包含测试、观测、版本治理等原则驱动任务。
- constitution 改了以后，相关模板是否需要同步更新。

它不是只写团队习惯，而是让原则进入后续规格生产流程。

## 当前 workflow 的适配方向

当前工作流的 constitution 应服务更完整的工程链路：

```text
constitution → roadmap → specify → design → plan → implement → verify → distill
```

因此当前适配时保留了：

- 项目级准则定位。
- 交互式创建/更新。
- 语义版本策略。
- Governance 区。
- 可执行、可检查的原则要求。

但没有直接照搬：

- `.specify/` 路径。
- spec-kit 的模板占位符机制。
- Sync Impact Report 写入方式。
- extension hook 机制。

## 后续可考虑吸收的机制

1. **Constitution Impact Report**

   类似 spec-kit 的 Sync Impact Report，在修改 `.workflow/constitution.md` 时记录：

   - 版本变化。
   - 新增/修改/删除的准则。
   - 可能影响的 commands 或 templates。
   - 需要人工检查的 artifact。

2. **模板一致性检查**

   constitution 更新后，检查：

   ```text
   src/commands/workflow/*.md
   src/.workflow/templates/**
   src/docs/templates/**
   ```

   确认后续命令与模板没有违反新准则。

3. **准则引用机制**

   后续 `plan-change`、`verify-implementation` 可以要求引用相关 constitution 准则，提升可追踪性。

4. **hook/extension 层**

   如果未来工作流支持插件或自动检查，可以再考虑类似 spec-kit 的 before/after hook。
