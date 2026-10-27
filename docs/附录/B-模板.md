# 附录 B 模板

> 四个模板：部署规格卡、任务契约、ADR、故障复盘。

## B.1 部署规格卡

```yaml
specCard:
  modelAlias: llm-pro
  modelVersion: "2026-10-01"
  artifact:
    sha256: "..."
    tokenizerHash: "..."
    chatTemplateHash: "..."
  gpuProfile: h100-80g-nvswitch
  precision: fp8
  parallelism: tp=8
  minGpus: 8
  maxContext: 32768
  maxOutputTokens: 8192
  maxConcurrency: 32
  kvCacheGB: 40
  engine: vllm-x.y
  performance:
    ttftP50Ms: 400
    ttftP99Ms: 900
    tpotP50Ms: 35
    tpotP99Ms: 60
    outputTokensPerSec: 2500
  warmupSeconds: 60
  compliance:
    filingId: "..."
    markPolicy: explicit+implicit
  owner: engine-team
  reviewedAt: "2026-10-27"
```

## B.2 任务契约

```json
{
  "task_id": "vid_abc",
  "tenant_id": "t_123",
  "type": "video",
  "idempotency_key": "order-12345",
  "params": {
    "model": "video-pro",
    "prompt": "...",
    "duration": 5,
    "resolution": "720p",
    "audio": true
  },
  "priority": "normal",
  "deadline": "2026-10-27T11:00:00Z",
  "max_attempts": 3,
  "callback_url": "https://customer.example/cb",
  "outputs": [],
  "billing": {"state": "held", "amount": 19.0, "currency": "CNY"}
}
```

状态取值：`queued | leased | running | succeeded | failed | canceled | expired`。

## B.3 ADR 模板

```markdown
# ADR-XXX 标题

- 状态：提议 / 已接受 / 已废弃
- 日期：YYYY-MM-DD
- 决策人：
- 关联章节：

## 背景
为什么现在要决定？约束是什么？

## 选项
| 选项 | 优点 | 缺点 | 成本 |
|---|---|---|---|

## 决定
选了什么，为什么。

## 代价
接受什么坏处；什么情况下会推翻。

## 复核时点
具体日期或触发条件。
```

## B.4 故障复盘模板

```markdown
# 故障复盘：<标题>

- 时间：开始 / 发现 / 恢复
- 影响：租户数、请求数、时长、金额
- 级别：P0/P1/P2

## 时间线
| 时间 | 事件 |
|---|---|

## 根因
直接原因 + 根本原因（5 Whys）

## 处置
检测 → 隔离 → 恢复 → 验证

## 改进项
| 项 | 负责人 | 截止 | 验收 |
|---|---|---|---|

## 未解决
```

## B.5 回调契约

```http
POST <callback_url>
X-Event-ID: evt_...
X-Timestamp: 1760000000
X-Signature: sha256=...
Content-Type: application/json
```

```json
{
  "event": "task.succeeded",
  "task_id": "vid_abc",
  "status": "succeeded",
  "outputs": [{"type": "video", "url": "...", "sha256": "..."}],
  "usage": {"unit": "second", "quantity": 5},
  "occurred_at": "2026-10-27T10:05:00Z"
}
```

接收方要求：验签、校验时间戳（±5 分钟）、按 `X-Event-ID` 去重、返回 2xx。
