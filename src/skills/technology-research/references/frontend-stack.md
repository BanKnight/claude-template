# Frontend Stack 决策方法

本 reference 用于评估前端框架、路由、数据获取、状态管理、表格、表单、样式和全栈前端方案。

## 常见决策对象

- Framework：React、Vue、Svelte、Solid、Next.js、Nuxt、Astro、TanStack Start。
- Router：TanStack Router、React Router、框架内置 router。
- Server state：TanStack Query、SWR、框架 loader/action。
- Table/grid：TanStack Table、AG Grid、MUI Data Grid。
- Form：React Hook Form、TanStack Form、Formik。
- Styling：Tailwind CSS、CSS Modules、vanilla-extract、Panda、styled-components。

## 判断维度

- 应用类型：内容站、Dashboard、复杂业务系统、实时协作、SSR/SSG/SPA。
- 路由复杂度：嵌套路由、搜索参数、权限、数据预加载、代码分割。
- 数据来源：REST、GraphQL、RPC、server actions、loader。
- 缓存和同步：server state 与 client state 是否分清。
- 类型安全：路由参数、search params、API 响应、表单 schema。
- SSR/streaming/hydration 是否必要。
- 团队熟悉度与生态成熟度。

## TanStack 类技术判断

评估 TanStack Router / Query / Table / Start 时，应确认：

- 是否需要强类型路由和 search params。
- 数据加载应在 router loader、Query，还是框架 server function 中完成。
- SSR 时是否需要每请求独立 QueryClient，避免请求间数据泄漏。
- TanStack Start 是否足够成熟，是否接受 alpha/快速演进风险。
- Table 是否需要 headless 灵活性，还是更适合现成 UI grid。

## 输出要点

- 推荐前端技术组合。
- 各工具职责边界。
- 数据加载路径。
- SSR/SPA/SSG 选择。
- 迁移与学习成本。
- 风险和验证点。
