# 附录 D 指标字典

> 名称、定义、公式、采集点、告警阈值。所有阈值是示例，需按实际基线校准。

## D.1 基础设施

| 指标 | 定义 | 采集点 | 告警 |
|---|---|---|---|
| `gpu_xid_total` | XID 错误计数 | DCGM | 79 立即；13/31/43 超阈值 |
| `gpu_ecc_uncorrected` | 不可纠正 ECC | DCGM | > 0 立即 |
| `gpu_temp_c` | 温度 | DCGM | > 85℃ 持续 5 分钟 |
| `gpu_clock_throttle` | 降频时长占比 | DCGM | > 5% |
| `ib_symbol_errors` | IB 误码 | 端口计数器 | 增长 > 0 |
| `nccl_bus_bw` | 集合通信带宽 | 巡检 | < 基线 90% |
| `disk_used_pct` | 本地盘使用率 | node_exporter | > 80% 预警，> 90% 停止拉取 |

## D.2 引擎

| 指标 | 定义 | 采集点 | 告警 |
|---|---|---|---|
| `engine_queue_depth` | 等待请求数 | 引擎 | > 100 持续 10 分钟 |
| `kv_cache_usage` | KV 占用 / 总容量 | 引擎 | > 95% 持续 5 分钟 |
| `prefix_cache_hit_rate` | 命中块 / 总块 | 引擎 | < 20%（视场景） |
| `batch_size_avg` | 平均批大小 | 引擎 | < 4（可能配置问题） |
| `prefill_ms` | prefill 耗时 | 引擎 | p99 > 阈值 |
| `decode_ms_per_token` | 每 token 耗时 | 引擎 | p99 > 阈值 |
| `engine_restart_total` | 重启次数 | K8s | > 0 / 小时 |

## D.3 服务

| 指标 | 定义 | 公式 | 采集点 | 告警 |
|---|---|---|---|---|
| `ttft_ms` | 首 token 延迟 | 首 chunk 时间 − 请求时间 | 网关 | p99 > 900ms |
| `tpot_ms` | 每输出 token 间隔 | (末 − 首) / (tokens − 1) | 网关 | p99 > 60ms |
| `request_success_rate` | 成功 / 总请求 | 2xx / all | 网关 | < 99.5% |
| `request_error_rate` | 5xx / 总请求 | 5xx / all | 网关 | > 1% |
| `timeout_rate` | 超时 / 总请求 | — | 网关 | > 0.5% |
| `active_requests` | 在途请求 | — | 网关 | 容量水位 |
| `p99_latency_ms` | 端到端 p99 | — | 网关 | 按模型 |

## D.4 任务

| 指标 | 定义 | 采集点 | 告警 |
|---|---|---|---|
| `task_queue_depth` | 待执行任务数 | 任务服务 | 按池 |
| `task_wait_seconds` | 创建到开始 | 任务服务 | p95 > 承诺 |
| `task_success_rate` | succeeded / 终态 | 任务服务 | < 99% |
| `task_retry_rate` | 重试次数 / 任务数 | 任务服务 | > 5% |
| `lease_expired_total` | 租约超时次数 | 任务服务 | > 0 / 小时 |
| `poison_task_total` | 进死信的任务 | 任务服务 | > 0 |
| `callback_failure_rate` | 回调失败 / 总回调 | 回调服务 | > 10% |

## D.5 业务与成本

| 指标 | 定义 | 采集点 | 用途 |
|---|---|---|---|
| `tokens_input_total` | 输入 token | 计量 | 计费 |
| `tokens_output_total` | 输出 token | 计量 | 计费 |
| `tokens_cached_total` | 缓存命中 token | 计量 | 折扣 |
| `images_total` | 生成张数 | 计量 | 计费 |
| `video_seconds_total` | 生成秒数 | 计量 | 计费 |
| `gpu_hours_total` | 卡时 | 调度/监控 | 成本 |
| `unit_cost_per_mtoken` | 元/M token | 成本 ÷ 用量 | 毛利 |
| `utilization_effective` | 有效利用率 | 有效吞吐 / 理论 | 容量 |

## D.6 SLO

| SLO | 计算 | 窗口 | 目标 |
|---|---|---|---|
| 可用性 | 成功 / (成功 + 5xx) | 30 天 | 99.9% |
| TTFT | p99 | 5 分钟滚动 | ≤ 900ms |
| TPOT | p99 | 5 分钟滚动 | ≤ 60ms |
| 任务完成率 | succeeded / 终态 | 24 小时 | ≥ 99% |
| 回调时延 | 生成完成 → 回调成功 | 24 小时 | p95 ≤ 1 分钟 |
