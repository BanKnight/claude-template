---
name: setup
description: 初始化或更新本工作流模板项目的 `.workflow/` 与 `docs/` 基础结构。用户要求 setup、初始化、更新、修复、重新执行工作流结构、治理文件、`.workflow/templates/`、docs 索引、AGENTS.md 或 CLAUDE.md 加载规则时，应使用本技能。本技能执行时必须使用系统任务追踪工具，并负责老项目的 docs 索引重建。
---

# setup

使用本技能初始化或更新项目的工作流基础结构。

setup 的目标仅限于两个核心目录及其加载入口：

- `.workflow/`：运行态与流程态区域。
- `docs/`：长期文档沉淀区域。
- `AGENTS.md` 与 `CLAUDE.md`：必须加载两个治理文件的入口文件。

不要用 setup 澄清产品想法、设计功能、规划版本、执行任务、审查代码、运行测试或沉淀业务知识。这些属于后续 workflow 技能。

## 执行纪律

执行 setup 时，从一开始就使用系统任务追踪工具。

创建并维护以下任务：

1. 检查当前项目结构。
2. 初始化或更新 `.workflow/`。
3. 初始化或更新 `docs/`。
4. 创建或更新 `.workflow/GOVERNANCE.md` 与 `docs/GOVERNANCE.md`。
5. 将治理加载规则注入 `AGENTS.md` 与 `CLAUDE.md`。
6. 将 skill 内置模板复制到 `.workflow/templates/`。
7. 必要时重建 `docs` 索引。
8. 验证并汇总 setup 结果。

每一步开始前，将对应任务标记为 `in_progress`；完成后立即标记为 `completed`。如果出现阻塞，保持相关任务未完成，并说明阻塞项。

## setup 流程

### 1. 先检查

编辑前先读取或列出相关现有文件：

- `.workflow/`
- `docs/`
- `AGENTS.md`
- `CLAUDE.md`

保留用户已有内容。优先做最小编辑，不要整文件重写。

### 2. 初始化或更新 `.workflow/`

确保存在以下结构：

```text
.workflow/
├── GOVERNANCE.md
├── decisions.md
├── progress.md
├── versions/
└── templates/
```

使用本技能内置的 `templates/workflow/` 作为 `.workflow/templates/` 的源模板。

`.workflow/templates/` 的项目本地副本应包含：

```text
.workflow/templates/
├── governance.md
├── decisions.md
├── progress.md
└── versions/
    ├── plan.md
    ├── tasks.md
    ├── trace.md
    ├── review.md
    ├── test.md
    ├── security.md
    └── close.md
```

这里的冗余是有意设计：

```text
setup skill 内置模板 → .workflow/templates/ 项目本地可编辑副本
```

skill 内置模板是原始种子，`.workflow/templates/` 是用户可修改的项目副本。不要把它解释成 `.workflow/` 运行态文件与 `.workflow/templates/` 之间的冗余。

如果 `.workflow/templates/` 中已经存在文件，不要覆盖，除非用户明确要求重置或同步模板。更新时只补齐缺失模板。

### 3. 初始化或更新 `docs/`

确保存在以下结构：

```text
docs/
├── GOVERNANCE.md
└── index.md
```

`docs/` 是长期文档沉淀区域。`docs/` 下每一层目录都应有自己的 `index.md`。

### 4. 维护治理文件

创建或更新：

```text
.workflow/GOVERNANCE.md
docs/GOVERNANCE.md
```

缺失时，使用内置模板作为初始内容：

- `templates/workflow/governance.md`
- `templates/docs/governance.md`

如果文件已存在，保留项目已有修改，只补齐 setup 关键规则。

### 5. 注入治理加载规则

确保两个根入口文件都会加载治理文件：

```text
AGENTS.md
CLAUDE.md
```

使用受管理区块，方便后续 setup 安全更新：

```md
<!-- WORKFLOW:GOVERNANCE:START -->
## 治理文档导入
- @docs/GOVERNANCE.md
- @.workflow/GOVERNANCE.md

必须读取并遵循：
1) .workflow/GOVERNANCE.md 负责运行态/流程态治理。
2) docs/GOVERNANCE.md 负责长期文档态治理。
<!-- WORKFLOW:GOVERNANCE:END -->
```

如果区块已存在，只更新该区块。如果文件存在但没有区块，则追加区块。如果文件不存在，则创建带有简短标题和该区块的文件。

### 6. 为老项目重建 docs 索引

当 `docs/` 中已经存在文档时，检查 `docs/` 下每个目录的 `index.md` 是否完整。

以下情况需要重建索引：

- 目录缺少 `index.md`。
- 索引遗漏当前层级的直接子目录。
- 索引遗漏当前层级的直接 Markdown 文档。
- 条目缺少一句话描述。

重建时，必须先阅读每个文档内容，再写一句话描述。可以用脚本辅助列出文件，但不能用脚本或文件名猜测描述。

每个 `index.md` 只描述当前目录层级：直接子目录和直接文档。不要级联展开孙级内容。

### 7. 验证

汇报完成前，验证：

- `.workflow/GOVERNANCE.md` 存在。
- `docs/GOVERNANCE.md` 存在。
- `.workflow/templates/` 存在，并包含预期模板集合。
- `AGENTS.md` 与 `CLAUDE.md` 包含治理加载区块。
- 如果存在老项目文档，`docs/` 各层目录有最新的 `index.md`。

最后用简短摘要说明变更内容和仍需用户决定的事项。
