# 模板冗余规则

`setup` 的模板冗余关系是：

```text
setup skill bundled templates → .workflow/templates/ project-local editable copy
```

skill 内置模板是原始种子；`.workflow/templates/` 是复制到项目里的本地可编辑副本。

这样设计的目的：

- 用户可以直接修改 `.workflow/templates/` 来适配自己的项目。
- 后续 workflow 执行时优先使用项目本地模板，减少 Agent 临时发挥。
- skill 原始模板保持稳定，不因单个项目定制而被污染。

不要把“冗余”解释成 `.workflow/` 实际运行文件和 `.workflow/templates/` 模板文件之间的重复。
