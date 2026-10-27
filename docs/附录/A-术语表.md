# 附录 A 术语表

> 统一用词，避免"副本/实例/任务"混用导致设计错误。

## A.1 核心对象

| 术语 | 英文 | 定义 |
|---|---|---|
| MaaS | Model-as-a-Service | 以服务形式提供模型能力，承担 SLO、计量与合规责任 |
| 副本 | replica | 一个可独立对外服务的推理进程组，可能跨多卡/多机；调度原子 |
| 实例 | instance | 副本内的一个进程/容器；不作为调度单位 |
| 任务 | task | 一次具体生成请求；队列单位，可重试 |
| 租约 | lease | worker 对任务的临时独占权，带 TTL 与心跳 |
| 队列 | queue | 按优先级/模态组织的待执行任务集合 |

## A.2 调度与基础设施

| 术语 | 英文 | 定义 |
|---|---|---|
| Gang 调度 | gang scheduling | 作业的 N 个 Pod 要么全起、要么全不起 |
| LWS | LeaderWorkerSet | 跨机副本的控制器，保证 leader + workers 原子调度 |
| MIG | Multi-Instance GPU | 把一张卡切成多个隔离实例 |
| DRA | Dynamic Resource Allocation | K8s 的结构化设备分配机制 |
| 装箱 | bin-packing | 尽量把负载集中到少数节点 |
| 分散 | spreading | 尽量把副本分散到不同节点/机架 |
| leaf / spine | — | 数据中心网络层级；leaf 接服务器，spine 接 leaf |
| rail | — | IB 网络中按端口分组的优化拓扑 |
| burn-in | — | 新节点上架前的满载验收测试 |
| straggler | 慢节点 | 不报错但持续拖慢整体的节点 |

## A.3 推理与性能

| 术语 | 英文 | 定义 |
|---|---|---|
| TTFT | Time To First Token | 首 token 延迟 |
| TPOT | Time Per Output Token | 每输出 token 的间隔 |
| MFU | Model FLOPs Utilization | 实测模型算力 / 硬件峰值算力 |
| SM Active | — | SM 活跃时间占比，比 GPU-Util 更能反映有效计算 |
| 连续批处理 | continuous batching | 动态加入/移出请求的批处理 |
| 前缀缓存 | prefix caching | 复用相同前缀的 KV，降低 prefill |
| KV Cache | — | 注意力键值缓存，随并发与上下文增长 |
| PD 分离 | Prefill/Decode disaggregation | 把 prefill 与 decode 拆到不同池 |
| 投机解码 | speculative decoding | 用小模型草稿加速大模型生成 |
| 量化 | quantization | FP8/INT4 等降低权重精度以省显存/提吞吐 |

## A.4 任务与计费

| 术语 | 英文 | 定义 |
|---|---|---|
| 幂等键 | idempotency key | 客户端提供的去重键 |
| 毒任务 | poison task | 连续失败、无法自动恢复的任务 |
| 死信队列 | dead letter queue | 存放毒任务的队列 |
| outbox | transactional outbox | 与状态同事务写入的事件表，异步投递 |
| 预扣 | hold | 冻结额度，不产生收入 |
| 结算 | capture | 冻结转实收 |
| 释放 | release | 解冻/退款 |
| 对账 | reconciliation | 任务、计费、回调三方核对 |

## A.5 产品与合规

| 术语 | 英文 | 定义 |
|---|---|---|
| 别名 | alias | 对外的稳定模型名，指向具体版本 |
| 规格卡 | spec card | 部署规格：精度、并行、卡数、吞吐、延迟 |
| 显式标识 | explicit mark | 人眼可见的"AI 生成"标记 |
| 隐式标识 | implicit mark | 元数据/数字水印，用户不易察觉 |
| SLO | Service Level Objective | 内部目标（如 TTFT p99 ≤ 900ms） |
| SLA | Service Level Agreement | 对外合同承诺（含赔付） |
| 错误预算 | error budget | (1 − SLO) × 总量，用于控制变更 |
| 备案 | filing | 生成式 AI 服务的监管备案〔核验〕 |
