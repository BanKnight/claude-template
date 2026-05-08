# Test 记录

## 测试范围

## 测试命令或步骤

## 测试结果

| 项目 | 结果 | 证据 |
| --- | --- | --- |

## 未覆盖项

## 示例

## 测试范围

验证 setup 是否能在目标项目中建立基础工作流结构。

## 测试命令或步骤

1. 执行 setup。
2. 检查 `.workflow/`、`docs/`、`AGENTS.md`、`CLAUDE.md`。
3. 检查 `.workflow/templates/` 是否包含项目本地模板副本。
4. 如果 `docs/` 中已有文档，检查各层 `index.md`。

## 测试结果

| 项目 | 结果 | 证据 |
| --- | --- | --- |
| `.workflow/GOVERNANCE.md` 存在 | pass | 文件已创建 |
| `.workflow/templates/versions/plan.md` 存在 | pass | 模板副本已创建 |
| `CLAUDE.md` 包含治理加载区块 | pass | 存在 `WORKFLOW:GOVERNANCE` 区块 |

## 未覆盖项

- 未在大型老项目 docs 目录上验证索引重建耗时。
