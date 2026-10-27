# MaaS Blueprint

> 商业化多模态 MaaS 的参考蓝本：从 GPU 集群到可售卖的服务。

**在线阅读：<https://dev.rho.social/maas-blueprint/>**

这是一份面向所有建设 MaaS 团队的参考蓝本，不是规范。书中的架构、参数、价格、流程都是
参考解，团队应结合自己的客户结构、成本、合规属地与团队规模做决策。

## 内容

- 第一篇 定位与约束：MaaS 边界、负载画像、单位经济学、合规约束、总体架构
- 第二篇 基础设施：集群网络存储、节点验收、资源模型、制品分发
- 第三篇 训练底座：Gang 调度、弹性与 checkpoint、故障闭环、混部
- 第四篇 推理平台：部署规格、引擎选型、路由、PD 分离、弹性发布、优雅退出
- 第五篇 多模态数据面：LLM、图像、视频、多模态理解
- 第六篇 任务系统：状态机、租约背压、交付回调、计费联动
- 第七篇 产品化与运营：模型目录、API、租户密钥、限流、计量与 SLO
- 第八篇 安全与合规：内容审核、标识水印、数据与备案
- 第九篇 运营与实施：可观测、容量成本、路线图、组织风险
- 附录：术语表、模板、检查清单、指标字典、待核验事实清单

## 本地构建

```bash
python -m venv .venv-docs
# Windows
.venv-docs\Scripts\pip install -r requirements-docs.txt -i https://mirrors.aliyun.com/pypi/simple/
.venv-docs\Scripts\mkdocs serve
```

构建静态站点：

```bash
.venv-docs\Scripts\mkdocs build
```

发布：仓库推送到 GitHub 后，在 `Settings → Pages → Build and deployment` 把 Source 设为
**GitHub Actions**。之后每次 push 到 `main`，`.github/workflows/pages.yml` 会自动构建并发布。

## 仓库结构

```
maas-blueprint/
├── docs/                 # 书稿与站点首页
├── mkdocs.yml            # 站点与导航配置
├── site/                 # 构建产物（不提交）
├── LICENSE
├── DISCLAIMER.md
└── CONTRIBUTING.md
```

站点使用 MkDocs Material 构建。该主题将于 **2027-05-05** 停止维护，届时可迁移到同团队的
继任者 Zensical（兼容现有 `mkdocs.yml`）或 VitePress。具体迁移时点由维护者按当时的稳定性决定。

## 许可

- 文档：CC BY 4.0
- 代码示例：Apache-2.0

详见 [LICENSE](LICENSE)。使用本蓝本产生的决策与后果由使用者自行承担，见 [DISCLAIMER.md](DISCLAIMER.md)。
