# 工作流技能总览

本文沉淀当前规划中的工作流技能体系，分为阶段型工作流 Skill 与不分阶段的通用 Skill。

## 阶段型工作流 Skill

| 阶段 | 目标 | 主要产物 | 存放位置 | Skill |
|---|---|---|---|---|
| 0. Setup | 初始化/校准工作流基础结构 | 治理文档、目录结构、索引重建检查 | `docs/`、`.workflow/`、`CLAUDE.md`、`AGENTS.md` | `setup` |
| 1. Clarify | 澄清问题、拓展思路、记录原始意图 | 原始想法、约束、成功标准、关键决策 | `.workflow/decisions.md` | `clarify` |
| 2. Design Router | 根据需求智能路由设计子域 | 需要执行的设计子任务清单 | `.workflow/progress.md` / `docs/specs/` | `design` |
| 2a. Product Design | 产品原型、用户流程、交互说明 | 产品原型/流程/spec | `docs/specs/` | `design-product` |
| 2b. Architecture Design | 模块划分、系统边界、技术架构 | 架构说明/ADR | `docs/specs/` 或 `docs/adr/` | `design-architecture` |
| 2c. API Design | 接口定义、请求响应、错误码 | API spec | `docs/specs/` | `design-api` |
| 2d. Database Design | 表结构、迁移、索引、一致性 | DB spec | `docs/specs/` | `design-database` |
| 2e. Business Rules | 业务规则、状态机、边界条件 | 业务规则文档 | `docs/specs/` | `design-business` |
| 2f. Exception Design | 异常路径、失败处理、降级策略 | 异常处理 spec | `docs/specs/` | `design-exception` |
| 3. Version Planning | 规划版本目标、范围、里程碑、完成定义 | version plan | `.workflow/versions/<version>/plan.md` | `version` |
| 4. Task Planning | 将版本拆成可执行任务 | task list、依赖、状态 | `.workflow/versions/<version>/tasks.md` | `task-plan` |
| 5. Task Loop | 在版本内逐个完成 task | 实现记录、进度更新 | `.workflow/versions/<version>/tasks.md` + `.workflow/progress.md` | `task-run` |
| 6. Quality Alignment | 确认实现与设计一致 | trace/review/test/security 证据 | `.workflow/versions/<version>/` | `align` |
| 7. Reopen | 质量不过关时重新打开版本 | 修正任务、失败原因、回流记录 | `.workflow/versions/<version>/tasks.md` / `review.md` | `reopen` |
| 8. Version Close | 版本收口与复盘 | close report、遗留项、达成情况 | `.workflow/versions/<version>/close.md` | `close` |
| 9. Distill | 沉淀长期可复用内容 | 调研、规格、ADR、设计经验等 | `docs/` | `distill` |

## 不分阶段的通用 Skill

| Skill | 用途 | 典型触发时机 | 主要产物 / 存放位置 |
|---|---|---|---|
| `guid` | 交互式工作流向导 | 用户想了解怎么开始、有哪些工作流能力 | 不落盘；引导用户是否使用 `setup` |
| `research` | 调研事实、资料、技术选型、方案比较 | 任何阶段遇到不确定、需要外部资料或对比时 | `docs/research/` |
| `sync-progress` | 同步全局进度 | 阶段切换、任务完成、上下文变长时 | `.workflow/progress.md` |
| `risk-scan` | 快速识别范围/质量/进度/安全风险 | 版本中期、重大决策前、上线前 | `.workflow/progress.md` 或 version 下风险记录 |
| `context-summary` | 汇总上下文，防止长跑丢信息 | 长会话、中断前、切换版本/阶段前 | `.workflow/progress.md` |
| `docs-index` | 由 Agent 阅读文档后维护同层 `index.md` | 新增/修改/删除 `docs/` 文档时；旧项目重建索引时 | `docs/**/index.md` |
| `trace` | 建立设计 → 任务 → 实现映射 | 任务完成后、质量对齐前 | `.workflow/versions/<version>/trace.md` |
| `review` | 代码/方案审查 | 实现完成后、版本收口前 | `.workflow/versions/<version>/review.md` |
| `test` | 测试计划、执行、结果归档 | 实现完成后、质量对齐前 | `.workflow/versions/<version>/test.md` |
| `security` | 安全检查 | 涉及权限、输入、数据、依赖、外部接口时 | `.workflow/versions/<version>/security.md` |
| `simplify` | 降复杂度、去冗余、改善可维护性 | review 后、实现变复杂时 | 代码修改 + review 记录 |
