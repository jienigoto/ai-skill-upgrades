---
name: daily-ai-skill-upgrade-20260703
description: "将 2026-07-03 近日报告转成可复用且具备失败降级链路的 Codex SKILL"
version: 1
---

## 触发条件

- 运行 `ai-skill-github` 自动化且今日尚未完成同主题项目创建。
- 在 2026-07-03 及附近日期捕捉到高活跃 AI 工具/Codex/MCP 更新，可用于工作流复用。

## 输入

- `workspace_root`（默认 `C:\Users\86152\Documents\AI博主视频`）
- `target_date`（默认执行日）
- `candidate_feed`（来自 web 搜索 + gh api 的候选集合）
- 工具可用性标记：`web`、`gh`、`git`

## 输出

- 项目目录内文件：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`
- `output/SKILL.md`
- `output/github-release-notes.md`
- `output/README.md`
- `renders/README.md`

## 工具与能力边界

允许：
- `gh api` / GitHub API（公开仓库与 release 数据）
- web 搜索（公开源）
- 本地文件写入（仅当前项目目录）
- `git commit`、`git push`

禁止：
- 修改无关旧项目
- 存储 secrets / token / private personal data
- 无凭据下宣称发布成功
- 破坏性文件操作（删除未确认资源）

## 执行流程

1. `gh auth status` 与 `git remote -v` 检查发布链路。
2. 若当日同主题项目不存在，则创建 `Project_14_ai-skill-upgrade-20260703`，包含 `output`、`renders`。
3. 使用 `web + gh api` 抓取候选，记录：用途、核心功能、工作原理、边界、来源、热度、是否适配。
4. 依据“近日报导更新强度 + 可复用性 + 可降级性”打分，挑 3 个点。
5. 产出 brief / script / asset-manifest / render-notes。
6. 输出 `SKILL.md` 并补齐触发条件、输入输出、验证方式、失败处理。
7. 尝试提交与推送：
   - commit message: `feat: add ai skill upgrade YYYY-MM-DD`
   - 推送到 `Jienigoto/ai-skill-upgrades`
8. 将发布结果与阻塞原因写入 `output/github-release-notes.md`。

## 3 个升级点

### U1：browser-use 0.13.3 的 skill 安装与入口统一

- 解决问题
  - 多客户端对 browser-use 的安装入口不统一，容易出现“某环境可用、某环境不可执行”的漂移。
- 比参考更强
  - 明确使用 `0.13.3` 的 `browser-use skill` 与 CLI 3.0 入口，避免手工按客户端脚本分叉处理。
  - 把 Harness 约束写入 precheck（如浏览器能力、版本、可执行路径）。
- 提升稳定性
  - 加入安装前环境检查：`browser-use --version`、`browser-use version`、`browser-use skill list`。
  - 统一写入会话标签（profile/session）与连接模式（headless/headed/connect）的可追踪变量。
  - 失败时输出结构化故障摘要（命令、错误码、建议动作）。
- 降级策略
  - 安装失败：退回只读抓取流程（仅 `state/get title/get text`），不执行写入动作。
  - Harness 异常：切换到最小命令序列并要求人工确认关键输入。

### U2：openai/codex trace 与 websocket 负载防护

- 解决问题
  - 复杂任务期间 trace/ws 通道可能写入大量响应体，既污染日志也影响排障效率。
- 比参考更强
  - 以 `rust-v0.142.5` 的修复策略为基线，加入“默认安全 trace + 条件扩展采样”。
- 提升稳定性
  - 启动级设置白名单：仅记录必要字段，禁用高体量响应写入 trace。
  - ws 失败时自动切换降采样日志并保留核心执行摘要。
  - 统一返回码映射，支持重试上限、退避与停机保护。
- 降级策略
  - 当 trace 初始化失败：跳过 trace，仅输出安全命令摘要。
  - 当 websocket 连续失败 3 次：降级为离线批处理命令模式并提示人工二次确认。

### U3：modelcontextprotocol/python-sdk 会话稳定性模板

- 解决问题
  - StreamableHTTP 在高并发场景下，首次流 priming 行为不稳定会影响工具调用可见性。
- 比参考更强
  - 以 `v1.28.1` 的 per-request stream 机制为基线，新增“会话预热 + priming 事件兜底”标准模板。
- 提升稳定性
  - 对每次会话执行：先 probe 再流处理，超时则重试并记录会话 ID。
  - 统一协议错误分类（超时/内容类型/连接错误），可按错误类回退。
  - 在输出中保留 minimal-metadata（时间、请求 ID、方法名、重试次数）。
- 降级策略
  - StreamableHTTP 异常：自动切换到 CLI 可用的稳定传输路径（如 stdio/simplified client）。
  - 连续失败后停止自动重试，转为人工审阅并给出最小可执行命令清单。

## 验证方式

- 结构验证：产物完整性（10 个必需文件存在）
- 内容验证：3 个升级点均含“解决问题 / 更强方案 / 稳定性 / 降级”
- 数据验证：每个候选记录 stars、pushed_at、release、release body 摘要
- 发布验证：`git status`、`git push` 成功与 commit hash 记录

## 失败处理

- web 不可达：记录阻塞原因与待重试窗口，不伪造线上成功。
- gh/网络不可用：仅保留本地成果，写明重试步骤（`gh auth status`、`gh api`）。
- 权限不足：阻塞推送并写明 `Permission denied` / `401` 等具体错误。
- 工具不可用：保留候选与 SKILL 草案，注明缺失步骤。

## 无凭据 / 网络失败 / 工具不可用降级

- 无凭据：只执行本地文档产出，`render-notes.md` 写明阻塞。
- 无网络：写入可复用候选模板（仅使用已抓取本地快照），暂停发布。
- 工具不可用：以“只读更新记录”模式生成目录和说明，供人工在可用环境重跑。
