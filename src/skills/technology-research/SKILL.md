---
name: technology-research
description: 当需要为项目选择、引入、升级或评估技术栈、框架、库、SDK、运行时、构建工具、部署平台或工程实践时使用本技能。尤其是用户提到 Bun、Cloudflare、TanStack、Oxc、TypeScript、monorepo、部署平台、工具链或“最佳实践/技术选型/当前推荐方案/是否该用某技术”时，必须使用本技能先建立调研方法和判断依据；本技能不是固定结论库，而是指导 Agent 如何检索当前资料、对比项目约束并产出可追溯技术判断。
---

# technology-research

使用本技能为技术选型、技术栈升级、依赖引入、工具链调整和部署平台选择建立可追溯判断。

本技能的重点不是背诵某个技术的固定最佳实践，而是让 Agent 针对当前项目、当前版本和当前约束，找到可靠资料并形成可解释结论。

## 使用时机

当出现以下任一情况时使用：

- 选择或评估框架、库、SDK、运行时、构建工具或部署平台。
- 新增、替换或升级依赖。
- 用户询问某技术是否适合当前项目。
- design-change 涉及技术栈、版本、外部服务或工程实践。
- 需要判断 monorepo、包管理器、构建工具、测试工具、lint/format 工具、部署平台等工程方案。
- 用户提到 Bun、Cloudflare、TanStack、Oxc、TypeScript 等具体技术，或同类技术栈。

## 不负责

`technology-research` 不负责：

- 直接实现代码。
- 替代 `design-change` 输出完整设计文档。
- 替代 `plan-change` 拆任务。
- 把检索结果硬编码成永久规则。

它产出的应该是技术判断依据，供 design/plan/implementation 使用。

## 工作方式

1. 先明确当前问题属于哪类技术决策。
2. 读取对应 reference，使用其中的判断清单。
3. 查询当前官方文档、release notes、包元数据、安全公告或部署平台文档。
4. 对照项目现状：已有技术栈、运行环境、团队约束、部署路径、性能/安全/维护要求。
5. 输出推荐方案、取舍原因、不选方案、风险和后续验证点。

## References

按需读取：

- [methodology.md](./references/methodology.md) — 通用技术研究方法论与输出结构。
- [dependency-safety.md](./references/dependency-safety.md) — 依赖与供应链安全检查，尤其 npm 7 天规则。
- [monorepo.md](./references/monorepo.md) — monorepo 是否适合、如何判断边界和工具。
- [runtime-and-tooling.md](./references/runtime-and-tooling.md) — 运行时、包管理器、构建、测试、lint/format 工具选择方法。
- [frontend-stack.md](./references/frontend-stack.md) — 前端框架、路由、数据获取、状态管理、表格等技术判断方法。
- [deployment-platform.md](./references/deployment-platform.md) — 部署平台、边缘运行时、serverless、环境变量、观测与回滚判断方法。
- [typescript.md](./references/typescript.md) — TypeScript 配置、严格度、工程集成与 monorepo 判断方法。

## 输出要求

技术研究结论应包含：

- 当前问题与项目约束。
- 检索来源与检索时间。
- 候选方案。
- 推荐方案。
- 不推荐方案及原因。
- 版本/配置/部署/供应链风险。
- 需要验证的假设。
- 后续应写入哪个 design 或 plan artifact。

如果资料不足，不要伪装成结论；记录缺口并说明还需要查什么或需要用户确认什么。
