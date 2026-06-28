---
name: daily-ai-skill-upgrade-2026-06-28
description: "Daily upgrade skill converting high-velocity AI tool/runtime changes into a stable Codex workflow"
version: 1
---

## 触发条件

- 每日自动化执行 `ai-skill-github` 时段到达。
- 目标对象为近 14 天内有 release/活跃提交的 AI 应用、AI 工具或模型编排能力。
- 需要输出可直接落地的 3 个 SKILL 升级点与降级路径。

## 输入

- `workspace_root`：AI 博主空间根目录。
- `target_date`：如 `2026-06-28`。
- `top_repo_candidates`：候选仓库列表（GitHub repo）。
- `publish_target`：默认 `Jienigoto/ai-skill-upgrades`。

## 输出

- `Project_10_ai-skill-upgrade-YYYYMMDD` 目录及文件：
  - `README.md`
  - `brief.md`
  - `script.md`
  - `asset-manifest.md`
  - `render-notes.md`
  - `output/SKILL.md`
  - `output/github-release-notes.md`

## 工具和能力边界

- 使用 `gh API`、`web` 搜索与浏览器能力获取公开信息。
- 仅对公开仓库页面和 release 进行读操作；不执行删除、提权或仓库管理动作。
- 结果可生成 Markdown 文档，不直接修改远端服务配置。
- 不处理用户凭据、不存储 tokens；仅读取其是否可用（`gh auth status`）。

## 执行流程

1. 校验治理文件存在性；不存在则写入 `render-notes` 告警并继续执行最小路径。
2. 生成项目目录（若当日同名不存在）：
   - `Project_10_ai-skill-upgrade-20260628`
3. 通过 `gh` 与公开页面抓取候选仓库：
   - release 列表与 release 详情
   - 最近提交摘要
   - stars/updated_at
4. 按“近期活跃 + 边界清晰 + 可复用性 + 风险可控 + 降级路径”打分，保留 3 个升级点。
5. 为每个升级点产出：问题、强项、稳定性增强、失败降级。
6. 完成产物写入并尝试发布：
   - git add / commit / push。
7. 发布/阻塞结果写入 `output/github-release-notes.md` 和 `render-notes.md`。

## 升级点（3）

### U1：browser-use 网页执行任务闭环

- 代表对象：
  - https://github.com/browser-use/browser-use/releases/tag/0.13.2
  - https://github.com/browser-use/browser-use/commits
- 解决问题：
  - 浏览器动作在多步任务中易抖动、模型前缀变化导致模型适配失败、任务不可复现。
- 为什么比参考对象更强：
  - 0.13.2 明确加入 provider-prefixed model 与 BU3 适配、发布流程门控与核心包版本同步，适合先做“预检+执行+QA”三段闭环。
- 稳定性提升：
  - 任务前做环境与模型可用性预检（超时、DOM 规模、页面状态）。
  - 执行时保留 action plan 与关键上下文快照，支持失败重试与复盘。
  - 结果层新增 `qa` 风格校验（成功率、命中率、关键字段完整性）。
- 失败降级：
  - 浏览器链失败时回退到“只读抓取 + 人工审核清单”。
  - 无法进入 GUI 自动化时输出最小安全建议，不进行写入操作。

### U2：MCP transport 稳态增强（python-sdk）

- 代表对象：
  - https://github.com/modelcontextprotocol/python-sdk/releases/tag/v1.28.1
  - https://github.com/modelcontextprotocol/python-sdk/commits
- 解决问题：
  - 多工具服务器接入时，长连接流式事件和 transport 安全策略变更后，易出现超时放大与不可观测失败。
- 为什么比参考对象更强：
  - v1.28.1 的 per-request streamable HTTP、TransportSecuritySettings 明确了事件缓冲与安全参数，可作为稳定接入层统一封装。
- 稳定性提升：
  - 统一接入层抽象：超时、重试、幂等标记和安全参数在统一层校验。
  - 失败时返回结构化错误码，支持回退到下一可用 transport。
  - 记录传输维度元数据（请求 id、请求大小、超时类型）用于可观测性。
- 失败降级：
  - WebSocket/stream 超时或 auth 异常时自动回退 HTTP/批量模式（或仅生成人工操作票据）。

### U3：pydantic-ai 能力契约与工具事件化

- 代表对象：
  - https://github.com/pydantic/pydantic-ai/releases/tag/v2.0.0
  - https://github.com/pydantic/pydantic-ai/commits
- 解决问题：
  - 复杂 agent 工作流缺少统一的输入输出约束，导致工具调用失败定位困难、回放不可复用。
- 为什么比参考对象更强：
  - v2 的 capabilities 设计把工具、hooks、模型设置打包为可复用单元，天然适配 Codex 可复用流程。
- 稳定性提升：
  - 对外接口统一 schema 校验；失败时给出类型错误和边界错误分类。
  - 工具调用输出事件化，便于可视化追踪与自动化回归。
  - 统一 `manual_review` 兜底，避免自动执行误触发高风险动作。
- 失败降级：
  - 若 schema 校验失败，进入 `manual_review`：输出原始请求、失败摘要、建议步骤。
  - 若模型 provider 缺失，输出可执行的配置补齐清单（不触发写操作）。

## 验证方式

- 版本与活动性证据：release 最近时间、提交时间、stars / updated_at 校验。
- 产物一致性：所有 required 文件存在。
- 发布一致性：本地提交、远端 push 成功且可在 `github-release-notes` 中追踪提交 id。

## 失败处理

- 无网络：
  - 停止远端 push，本地保留产物，`render-notes` 记录错误与重试窗口。
- 无凭据/gh 不可用：
  - 仅落地文档，禁止远端操作，写明 `gh auth status` 输出。
- 工具不可用：
  - 回退到最小证据路径（仅保留筛选记录与已抓取元数据）并打标“待复测”。
- 发布受阻：
  - 不重试无限循环，记录错误码、建议修复动作（gh login、权限确认、仓库权限）。
