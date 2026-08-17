# Aether 网关接口文档

Aether AI 网关的完整接口文档。基于 **[fawney19/Aether](https://github.com/fawney19/Aether)**（Rust/axum 实现）代码逐路由核对生成，覆盖管理 API、客户端兼容接口、数据库结构与运维最佳实践。

> ⚠️ 本文档不包含任何真实密钥 / token / 节点信息，示例中的 `<...>` 均为占位符。

## 适用版本

- 网关：`fawney19/Aether` main 分支（Rust/axum，workspace 30+ crates）
- 节点 agent：`aether-tunnel`（协议 v3）
- 历史版本：旧 Python 实现在 `aether-python` 分支（API 表面相似，细节不同）

## 目录

| 章节 | 说明 |
|---|---|
| [架构与部署](./docs/architecture.md) | 组件、路由分组、compose、格式转换 |
| [鉴权机制与管理令牌](./docs/admin-api/01-auth-and-tokens.md) | `ae-` 管理令牌、31 权限域、JWT 会话 |
| [Admin API 总览](./docs/admin-api/00-overview.md) | 34 个路由家族、调用约定、错误格式 |
| [API 密钥管理](./docs/admin-api/02-api-keys.md) | 独立余额 Key CRUD |
| [全局模型管理](./docs/admin-api/03-models.md) | 全局模型与 model_mappings |
| [供应商与端点](./docs/admin-api/04-providers-endpoints.md) | providers/endpoints、body_rules 入口 |
| [用量查询](./docs/admin-api/05-usage.md) | usage records/curl/replay、查库 SQL |
| [其余 Admin 家族（端点级）](./docs/admin-api/06-others.md) | 34 个家族端点表 + 示例（[families/](./docs/admin-api/families/)） |
| [完整路由清单](./docs/admin-api/_route-inventory.md) | 365 方法×路径（代码提取） |
| [客户端 v1 API](./docs/client-api/v1.md) | OpenAI/Claude/Gemini 兼容接口 |
| [数据库速查](./docs/database.md) | 8 个逻辑 schema 全部表字段 |
| [body_rules 语法](./docs/ops/body-rules.md) | 规则结构与实战示例 |
| [运维排查手册](./docs/ops/playbook.md) | 排查流程与已知问题 |

## 快速上手

```bash
# 1. 创建管理令牌（需现有管理令牌或 Web 登录态）
curl -sS https://<网关域名>/api/admin/management-tokens \
  -H "Authorization: Bearer <token>" -H "Content-Type: application/json" \
  -d '{"name":"ops","permissions":["models:read","models:write","usage:read"]}'

# 2. 客户端调用（sk- 用户 key）
curl https://<网关域名>/v1/responses \
  -H "Authorization: Bearer sk-xxx" -H "Content-Type: application/json" \
  -d '{"model":"<模型>","input":[{"role":"user","content":[{"type":"input_text","text":"hi"}]}]}'
```

## 文档维护

- 路由清单由代码提取：`apps/aether-gateway/src/control/route/admin/*.rs` + `handlers/admin/**/routes.rs`
- 数据库字段来源：`crates/aether-data/runtime/schema/logical/*.toml`
- 修改线上配置前务必阅读 [备份与回滚](./docs/ops/body-rules.md#四备份与回滚改任何端点前先做)
