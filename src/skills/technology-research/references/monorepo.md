# Monorepo 决策方法

monorepo 不是默认最佳实践，应根据项目结构和协作方式判断。

## 适合考虑 monorepo 的情况

- 多个包或应用需要共享代码、类型、配置或发布节奏。
- 前后端、SDK、CLI、文档站等需要在同一变更中协同演进。
- 需要统一 lint、test、build、release、依赖版本和代码生成。
- 多 package 之间存在频繁跨包修改。

## 不适合 monorepo 的情况

- 只有一个应用，拆包只是为了显得规范。
- 团队还没有处理 workspace、缓存、构建图、版本策略的能力。
- 包之间发布节奏、权限、安全边界差异很大。
- 会把本可独立的系统强行耦合。

## 需要调研的工具维度

- package manager workspace：Bun / pnpm / npm / Yarn。
- task runner / build graph：Turborepo / Nx / Lage / moon / 自定义脚本。
- changeset / release 工具。
- TypeScript project references。
- lint/test/build 缓存策略。

## 判断问题

- 共享的是源码、类型、配置、构建产物还是发布流程？
- 是否需要独立版本发布？
- CI 是否能只跑受影响部分？
- 本地开发是否会变复杂？
- 对部署平台是否有路径、包大小或构建限制？
