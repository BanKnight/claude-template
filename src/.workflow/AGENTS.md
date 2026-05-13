# .workflow 治理规则模板

本文件定义 `.workflow/` 的运行态治理规则。

## 定位

- `.workflow/` 是运行态与流程态区域。
- 原始意图、路线图、单次变更、执行证据与归档上下文放在这里。
- 长期沉淀内容不要放在 `.workflow/`，应整理到 `docs/`。

## 固定结构

```text
.workflow/
├── AGENTS.md
├── intents.md
├── roadmap.md
├── templates/
├── changes/
└── archive/
```

## 基本规则

- `intents.md` 记录全局 what/why、关键意图与意图变更。
- `roadmap.md` 记录全局路线图、阶段、进度、阻塞与下一步。
- `changes/<change-id>/` 保存单次变更上下文。
- `<change-id>` 使用语义化变更标识，不使用数字编号。
- `archive/` 保存已完成变更的完整上下文。
