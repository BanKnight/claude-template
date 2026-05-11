# .workflow 治理规范

本文件是 `.workflow/` 的常驻治理上下文。

## 定位

- `.workflow/` 是运行态与流程态区域。
- 原始想法、决策变更、进度、版本计划、任务执行、质量对齐结果放在这里。
- 长期沉淀内容不要放在 `.workflow/`，应迁移或整理到 `docs/`。

## 固定结构

必须包含：

- `.workflow/decisions.md`：原始想法、关键决策、决策变更。
- `.workflow/progress.md`：全局进度、阻塞项、下一步。
- `.workflow/versions/`：每个版本的计划、任务、证据与收口记录。
- `.workflow/templates/`：项目本地可编辑模板副本，由 setup skill 的内置模板复制生成。
- `.workflow/GOVERNANCE.md`：本治理规范。

## templates 规则

`.workflow/templates/` 存放工作流关键文件模板，供用户按项目情况修改。

模板来源关系为：

```text
setup skill bundled templates → .workflow/templates/ project-local editable copy
```

后续创建版本计划、任务、质量证据或收口记录时，应优先使用 `.workflow/templates/` 中的项目本地模板。

## 工作流模型

- Clarify：澄清问题、拓展思路、记录原始意图。
- Design：按需路由到产品原型、技术架构、API、数据库、业务逻辑、异常处理等设计子域。
- Version Planning：规划版本目标、范围、里程碑与完成定义。
- Version Loop：按版本顺序推进。
- Task Loop：在版本内逐个完成任务。
- Quality Alignment：通过 trace、review、test、security 等证据确认实现与设计一致。
- Reopen：若质量对齐不通过，版本回到进行中并生成修正任务。
- Distill：版本结束后，将可长期复用的内容沉淀到 `docs/`。

## 版本目录建议

每个版本可使用：

- `plan.md`
- `tasks.md`
- `trace.md`
- `review.md`
- `test.md`
- `security.md`
- `close.md`
