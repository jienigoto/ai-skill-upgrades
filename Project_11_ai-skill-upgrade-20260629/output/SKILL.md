name: daily-ai-skill-upgrade-2026-06-29
description: "每日 AI Skill 升级 SKILL：将 browser-use、MCP transport 与 Copilot CLI 的近期变更转化为可复用稳定化执行流程"
version: 1
---

## 触发条件

- 日常自动化任务 `ai-skill-github` 到达执行时段。
- 目标对象需满足近 14 天内有显著公开变更（release/发布说明/活跃提交）。
- 需要输出可复用、可降级的 3 个 Codex SKILL 升级点。

## 输入

- `workspace_root`：AI 博主项目根目录。
- `target_date`：如 `2026-06-29`。
- `candidates`：AI 工具/API/框架候选列表。
- `publish_target`：默认为 `Jienigoto/ai-skill-upgrades`。

## 输出

- `README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`
- `output/SKILL.md`、`output/github-release-notes.md`

## 执行流程

1. 校验当天项目目录是否已存在；不存在则新建 `Project_11_ai-skill-upgrade-YYYYMMDD`。
2. 逐一抓取候选对象：
   - 公开 release 页面
   - 最新更新时间、关键变更项
   - stars/活跃信号（如更新频率、commit 数）
3. 为每个候选记录用途、功能边界、来源、热度、是否适配 Codex 重用。
4. 按评分筛选 Top3（活跃性、稳定收益、失败可降级、通用性）。
5. 生成 3 个升级点，并写入“为什么比参考更强/如何提升稳定性/失败降级”三段。
6. 尝试执行发布；如受阻记录阻塞和人工操作指引。

## 工具边界

- 可使用：`web`、浏览器页面、GitHub REST API/`gh`。
- 禁止：写 secrets、改动非当日目标目录外文件。
- 网络失败、鉴权失败时只做本地留存，不做误报。

## 三个升级点

### U1：browser-use 执行护栏

- 解决问题：浏览器自动化中模型前缀变更、发布流程差异与执行不可复现。
- 比较优势：基于 0.13.2 发布记录中的 model 前缀兼容与 release 环境护栏，可更稳定地统一“执行前置检查 + 结果复核”。
- 提升稳定性：
  - 执行前检查模型名、DOM 复杂度、超时阈值。
  - 执行中保留任务 plan + 关键动作快照。
  - 执行后进行字段完整性与成功码校验。
- 降级策略：
  - 连续失败回退到只读抓取模式。
  - 禁止写操作，输出待人工确认清单。

### U2：MCP transport 协议兼容层

- 解决问题：MCP 1.x 与 v2 alpha 并行时的连接与工具调用不一致。
- 比较优势：v2 alpha 的 `mode='auto'` 与 stateless 协商能力可作为自动 protocol 路由入口，优于硬编码客户端路径。
- 提升稳定性：
  - 自动探测 server/discover 与初始化分支。
  - 明确记录请求模式与会话元数据。
  - 对 `InputRequiredResult` 做结构化分类与可追踪重试。
- 降级策略：
  - 首选 v1 稳定线：锁定 `mcp<2`，在迁移窗口内不强制启用 alpha。
  - 连接失败时回退 legacy transport。

### U3：Copilot CLI 终端协作工作流模板

- 解决问题：issue/PR/gist 处理流程在终端与网页间来回切换导致上下文丢失。
- 比较优势：Tab + in-session 配置 `/mcp` `/skills` `/plugin` `/settings`，支持交互式操作闭环。
- 提升稳定性：
  - 固定命令顺序与失败码归类。
  - 新增“会话可复现日志”模板。
  - 在无法写权限时自动降级到只读流。
- 降级策略：
  - 若终端会话不可用，转为 web 控制台查询。
  - 若插件/技能源不可达，提示离线模式并保留任务摘要。

## 验证方式

- 文档完整性：所有必需文件存在。
- 来源验证：release/changelog/官方说明可打开且含发布时间与变更。
- 可复用性：每个升级点都有“前置校验、执行、失败降级”条款。

## 失败处理

- 无网络：仅落本地，不上传；记录时间和重试窗口。
- 无 gh 凭据/权限不足：输出完整阻塞原因与所需人工命令。
- 工具不可用：仅保留最小候选摘要与复现步骤。
- 与环境约束冲突：不写无关文件，补齐记录并等待人工确认。

### 验证输出（每次运行必检）

- 产物清单齐全。
- Top3 与本次目标一致。
- 发布结果有提交 id 或阻塞原因。
