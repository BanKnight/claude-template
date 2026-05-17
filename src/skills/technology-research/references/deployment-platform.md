# Deployment Platform 决策方法

本 reference 用于评估部署平台、serverless、edge runtime、容器平台和相关运行时约束。

## 常见决策对象

- Edge/serverless：Cloudflare Workers、Vercel、Netlify、AWS Lambda、Deno Deploy。
- Container：Fly.io、Render、Railway、ECS、Kubernetes。
- Static hosting：Cloudflare Pages、Vercel、Netlify、S3/R2 + CDN。
- Data/storage：D1、KV、R2、Durable Objects、Postgres、Redis、object storage。

## 判断维度

- 运行时限制：Node API 支持、CPU 时间、内存、请求/响应大小、连接限制。
- 状态模型：无状态、KV、对象存储、关系数据库、Durable Object、队列。
- 部署模型：preview、staging、production、回滚、灰度、版本化部署。
- 配置模型：环境变量、secret、binding、region、compatibility date。
- 本地开发：模拟环境是否足够接近生产。
- 观测能力：日志、trace、metrics、alert、错误追踪。
- 供应商锁定与迁移成本。

## Cloudflare 类平台判断

评估 Cloudflare Workers / Pages 时，应确认：

- 是否适合 edge runtime，而不是需要完整 Node.js server。
- 是否依赖不兼容的 Node API 或 native addon。
- 数据是否适合 D1/KV/R2/Durable Objects/Queues 等绑定模型。
- wrangler 配置、compatibility_date、observability、环境隔离是否清楚。
- 本地 Miniflare/wrangler dev 是否能覆盖关键路径。
- 回滚、preview、secret 管理和自定义域名流程是否可接受。

## 输出要点

- 推荐部署平台。
- 运行时约束和兼容风险。
- 数据/状态/存储选择。
- 环境配置与 secret 管理。
- 观测与回滚方案。
- 本地开发和 CI/CD 验证方式。
