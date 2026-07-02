name: daily-ai-skill-upgrade-2026-07-02
description: "将 2026-07-02 近期 AI 工具更新转译为可复用、可回退的 Codex 执行 SKILL"
version: 1

---

## 触发条件

- 执行自动化 `ai-skill-github` 的当日运行。
- 目标仓库中有公开更新且具备可复用执行逻辑的 AI 工具/工作流。
- 允许在无关旧项目外创建 `Project_13_ai-skill-upgrade-YYYYMMDD`。

## 输入

- `workspace_root`
- `target_date`（默认今日 `2026-07-02`）
- `candidates`：通过 web + GitHub REST + `gh` 自动抓取的候选列表
- 工具状态：`web`、`gh`、`git`、`browser` 可用性

## 输出

- `README.md`
- `brief.md`
- `script.md`
- `asset-manifest.md`
- `render-notes.md`
- `output/SKILL.md`（含完整升级执行模板）
- `output/github-release-notes.md`
- `output/` 与 `renders/` 目录

## 工具与能力边界

- 允许：
  - `web` 浏览/搜索（公开网页）
  - `gh`/GitHub REST（公开仓库元数据与 Release）
  - 文件写入当日项目目录
  - `git` 提交与可用时推送
- 禁止：
  - 读取/写入无关旧项目
  - 存储 secrets、token、密码、单次凭据
  - 在缺少权限时伪造上传成功
- 降级边界：网络失败/鉴权失败时，写本地产物 + 公开阻塞原因，不跳过验证记录。

## 完整执行流程

1. 记录治理文件存在性；若缺失，在 render-notes 首段标注。
2. 初始化项目目录 `Project_13_ai-skill-upgrade-20260702`（含 `output/`、`renders/`）。
3. 用 `gh api` + web 搜索抓取 24 小时内/近日报告内候选。
4. 每个候选记录：用途、核心功能、工作原理、边界、来源链接、热度证据。
5. 评分与筛选，产出 Top3 升级点。
6. 生成文档：`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`。
7. 编写 `output/SKILL.md`：三点升级、稳定性加固、失败降级。
8. 校验：3 点升级是否逐条包含“问题 / 比参考更强 / 稳定性 / 降级”。
9. 提交并尝试推送；若失败记录阻塞点和重试命令。

## 3 个升级点

### U1：browser-use 0.13.3 浏览器 skill 安装与入口治理

- 解决问题：agent 运行时环境下 skill 安装入口分散，导致同一任务在不同客户端行为不一致。
- 比参考更强：固定使用 `0.13.3` 的 `browser-use skill` + CLI 3.0 方案，将安装链路集中到统一 precheck。
- 提升稳定性：
  - 预检 `browser-use --version` 与 `harness version`，确保发布版本和依赖一致
  - 统一 skill 目录发现（Claude Code/Codex/Cursor/OpenCode）
  - 失败重试后自动降级到只读抓取模式
  - 输出动作快照与关键链接清单以支持可审计回放
- 降级：
  - 预检失败：只抓取 DOM 关键字段并提交人工确认
  - 安装失败：跳过主动编辑动作，转为只读模式 + 明确重试建议

### U2：openai/codex rust-v0.142.5 Trace 与 WebSocket 防护

- 解决问题：trace 通道记录过多响应负载，影响稳定性和安全边界。
- 比参考更强：将 trace 清洗做为默认行为之一，而非仅靠人工清理。
- 提升稳定性：
  - 开始前设定 trace 最小化规则
  - WebSocket 失败时退回轻量响应采样路径
  - 关键调用增加失败码归一化与退避重试
  - 保留必要执行日志，避免阻断主任务流
- 降级：
  - 追踪链路异常时关闭 websocket 追踪并仅使用命令级日志
  - 超时后返回“安全中断 + 人工确认”提示

### U3：pydantic-ai v2.2.0 任务评测与模型生命周期模板化

- 解决问题：评测与生命周期参数在不同任务中被重复手写，无法稳定复用。
- 比参考更强：将 Sonnet-5 与 `Dataset.evaluate` 生命周期参数抽成模板，统一输入输出。
- 提升稳定性：
  - Schema 验证与字段白名单
  - 任务拆分为 `evaluate -> summarize -> gate`
  - 指标阈值驱动的重试与降级
- 降级：
  - 评测链路错误时，自动退回摘要产出 + 人工复核点
  - 模型不支持时降级到默认模型策略（保留同一任务骨架）

## 验证方式

- 产物完整性：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`、`output/SKILL.md`、`output/github-release-notes.md` 存在。
- 逻辑完整性：3 个升级点均包含“解决问题/增强/稳定性/降级”。
- 证据完整性：每个候选至少有 release 链接 + stars + `pushed_at`。
- 发布完整性：有 `git` 提交记录；有远端推送成功则附 commit。

## 失败处理

- 无网络：停止外部抓取，写入阻塞原因，保留已抓取本地内容。
- 无凭据/鉴权失败：仅本地输出，不误报成功。
- GitHub/工具不可用：记录替代命令与下一次重试窗口。
- 文件写入失败：记录失败对象并保留最小复现步骤。

## 验证清单（每次运行）

- [ ] `AGENTS.md` 等治理文件读取记录
- [ ] 产物目录与文件齐全
- [ ] Top3 与 brief/asset-manifest 一致
- [ ] 每个升级点含降级路径
- [ ] 发布记录明确 commit 或 blocker