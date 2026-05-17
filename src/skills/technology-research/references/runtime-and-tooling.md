# Runtime 与 Tooling 决策方法

本 reference 用于评估运行时、包管理器、构建工具、测试工具、lint/format 工具等工程基础设施。

## 常见决策对象

- Runtime：Node.js、Bun、Deno、Edge runtime。
- Package manager：Bun、pnpm、npm、Yarn。
- Build/bundle：Vite、Rolldown、esbuild、Rspack、Webpack、Bun bundler。
- Test：Vitest、Bun test、Jest、Playwright。
- Lint/format：ESLint、typescript-eslint、Oxc/Oxlint、Biome、Prettier。

## 判断维度

- 与项目现有生态兼容性。
- 对 TypeScript、ESM/CJS、workspace、path alias 的支持。
- CI/CD 和部署平台是否原生支持。
- 插件生态与社区成熟度。
- 性能收益是否能抵消迁移成本。
- 出错时团队是否有调试经验。
- 是否会和现有工具职责重叠。

## Bun 类工具判断

评估 Bun 时不要只看速度，应确认：

- 项目是否需要 Bun runtime，还是只需要 Bun package manager / test / script。
- Node.js API 兼容性是否足够。
- 现有依赖是否依赖 Node 特定行为。
- lockfile、CI、部署平台是否统一使用 Bun。
- 测试 runner 是否满足现有 mock、coverage、snapshot、watch 需求。

## Oxc 类工具判断

评估 Oxc/Oxlint 时应确认：

- 是替代 ESLint、补充快速 lint，还是仅用于 parser/transformer。
- 所需规则是否覆盖现有规则集。
- 是否需要 type-aware lint；如果需要，成熟度和限制是什么。
- 是否仍保留 TypeScript 类型检查。
- 是否与 Prettier/Biome/ESLint 产生职责冲突。

## 输出要点

- 工具职责边界。
- 替代还是补充现有工具。
- 迁移成本。
- CI 与本地开发影响。
- 回滚策略。
