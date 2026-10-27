# 第 29 章 API 产品面与版本策略

> API 是产品，不是内部接口的暴露。它的稳定性、错误语义、文档质量，直接决定客户留存。

## 29.1 端点清单

| 类别 | 端点 | 说明 |
|---|---|---|
| 模型 | `GET /v1/models`、`GET /v1/models/{id}` | 能力、上下文、状态 |
| 对话 | `POST /v1/chat/completions` | 同步/SSE |
| 向量 | `POST /v1/embeddings` | 批量输入 |
| 图像 | `POST /v1/images/generations` | 同步/短异步 |
| 视频 | `POST /v1/videos`、`GET /v1/videos/{id}` | 任务制 |
| 任务 | `GET /v1/tasks/{id}`、`POST /v1/tasks/{id}/cancel` | 统一查询 |
| 文件 | `POST /v1/files`、`GET /v1/files/{id}` | 批量与素材 |
| 批量 | `POST /v1/batches` | 离线 |
| 用量 | `GET /v1/usage` | 计量查询 |
| 账户 | `GET /v1/account`、`GET /v1/api-keys` | 自助管理 |

## 29.2 风格约定

| 约定 | 做法 |
|---|---|
| 版本 | URL 前缀 `/v1`，大版本变更才升 |
| 命名 | 资源用复数，动作用 POST 子路径 |
| 时间 | ISO 8601 UTC（`2026-10-27T10:00:00Z`） |
| ID | 前缀 + 随机（`vid_`、`tsk_`、`req_`），不可枚举 |
| 幂等 | `Idempotency-Key` 头，24 小时窗口 |
| 分页 | `limit` + `after` 游标，不用 offset |
| 部分成功 | 明确字段，不靠 HTTP 状态 |
| 长任务 | 202 + `Location` 头 |

## 29.3 兼容性规则

| 变更 | 兼容 | 处理 |
|---|---|---|
| 新增可选请求字段 | 是 | 直接发 |
| 新增响应字段 | 是 | 客户应忽略未知字段 |
| 新增枚举值 | 可能不兼容 | 提前 30 天公告 |
| 修改默认值 | 否 | 新版本或显式参数 |
| 删除/重命名字段 | 否 | 弃用 6 个月 |
| 修改错误码 | 否 | 映射表 + 公告 |
| 修改限流阈值 | 是（但影响体验） | 提前通知 |

响应头带上弃用信息：

```
Deprecation: true
Sunset: Sat, 01 May 2027 00:00:00 GMT
Link: <https://docs/deprecations>; rel="deprecation"
```

## 29.4 错误规范

统一结构（第 20 章给了示例），补充三条：

1. **`code` 是稳定的机器可读值**，`message` 可以变。
2. **`request_id` 必返**，支持工单关联。
3. **`retry_after_ms`** 只在可重试时出现。

## 29.5 SDK 与文档

| 项 | 要求 |
|---|---|
| OpenAPI | 单一来源，SDK 由它生成 |
| SDK | 至少 Python / Node / Go；或提供 OpenAI 兼容客户端 |
| 示例 | 每个端点至少一个可运行示例 |
| 错误示例 | 每个错误码一个示例 |
| 变更日志 | 按日期，标注兼容性 |
| 状态页 | 与 SLO 对应 |

不要手写多份 SDK。手写的结果是行为不一致，客户踩坑后不信任。

## 29.6 Webhook 作为产品能力

| 项 | 要求 |
|---|---|
| 事件类型 | `task.succeeded`、`task.failed`、`task.canceled` |
| 签名 | HMAC + 时间戳 |
| 重试 | 指数退避，≥ 24 小时 |
| 控制台 | 可查看投递记录、手动重放 |
| 测试工具 | 提供签名校验示例代码 |

## 29.7 限流响应头

```
X-RateLimit-Limit-Requests: 600
X-RateLimit-Remaining-Requests: 42
X-RateLimit-Reset-Requests: 2026-10-27T10:01:00Z
X-RateLimit-Limit-Tokens: 200000
X-RateLimit-Remaining-Tokens: 15320
Retry-After: 12
```

客户据此做客户端限流，比被 429 打回来体验好得多。

## 29.8 本章结论

- API 设计要围绕"稳定契约"，不是"内部实现的映射"。
- 兼容性规则要提前写，事后补会被客户拒绝。
- SDK 从 OpenAPI 生成，避免多份实现漂移。
- Webhook 与控制台重放，是任务型产品的必备能力。

## 决策点（结合自身情况）

1. 是否兼容 OpenAI 的 `/v1/completions`？会拖长维护周期。
2. 版本策略是"URL 版本"还是"日期版本"？
3. SDK 自研还是基于社区生成器？
