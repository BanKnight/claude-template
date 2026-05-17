# TypeScript 决策方法

本 reference 用于评估 TypeScript 配置、类型检查边界、构建集成和 monorepo 组织方式。

## 判断维度

- 项目类型：库、应用、全栈项目、monorepo、多运行时项目。
- 是否需要 emit：只类型检查（noEmit）还是由 tsc 输出声明/JS。
- module/moduleResolution 是否匹配运行时和 bundler。
- strictness 是否符合团队和代码成熟度。
- path alias 是否与 bundler、runtime、test runner 一致。
- 是否需要 project references 管理多包依赖图。
- 类型检查与 lint/transform/bundle 的职责是否分清。

## 配置问题

调研 TypeScript 时，应确认：

- 当前推荐的 module/moduleResolution 组合。
- 目标运行时对 ESM/CJS 的要求。
- 构建工具是否负责转译，TypeScript 是否只做 typecheck。
- 是否需要 declaration output。
- 是否需要 incremental build 或 project references。
- tsconfig 是否被 test runner、bundler、IDE、runtime 一致读取。

## 常见误区

- 用 Oxc/Babel/esbuild 转译后误以为不需要 TypeScript 类型检查。
- path alias 只在 tsconfig 生效，但运行时或测试环境不生效。
- monorepo 中一个巨大 tsconfig 导致类型检查慢且边界不清。
- 不区分应用 tsconfig、库 tsconfig、测试 tsconfig。

## 输出要点

- 推荐 tsconfig 分层。
- 是否使用 strict / noEmit / declaration / project references。
- 与 runtime/bundler/test/lint 的职责边界。
- monorepo 包依赖图和类型检查策略。
