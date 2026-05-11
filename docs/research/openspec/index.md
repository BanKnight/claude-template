# OpenSpec local project sample

本目录按“本地项目接入 OpenSpec 后”的形态保存参考内容，而不是 OpenSpec 文档镜像。

## 子目录

- [.claude](./.claude/) — Claude Code 项目内 OpenSpec skills 与 opsx slash commands 的生成结果样例。
- [openspec](./openspec/) — OpenSpec 仓库自身正在使用的真实 `openspec/` 工作区快照，包含 `specs/`、`changes/`、`archive/` 与 explorations。
- [schemas](./schemas/) — OpenSpec 包级内置 schema 定义与模板目录，包含 `schema.yaml` 与对应 artifact 模板文件。

## 说明

- `.claude/skills/openspec-*` 与 `.claude/commands/opsx/*` 根据 OpenSpec 1.3.1 源码模板生成，用于观察安装到项目后的技能形态。
- `openspec/` 直接复制自 `~/repos/OpenSpec/openspec`，用于观察真实项目中 OpenSpec 的目录与内容组织。
- `schemas/` 直接复制自 `~/repos/OpenSpec/schemas`，用于对照不同 workflow schema 的 artifact 图谱、apply 配置与模板内容。
