---
name: daily-ai-skill-upgrade-20260704
description: "每日AI Skill升级：以 browser-use、openai/codex、modelcontextprotocol/python-sdk v2 预发布治理能力为核心，输出可复用的 Codex Skill"
version: 1
---

## 触发条件

- 当日需要生成一版 AI Skill 升级流程产物（日报、brief、publish notes）。
- 目标仓库 `Jienigoto/ai-skill-upgrades` 可访问，且用户允许自动提交。
- 检测到候选对象在近 7 天有活跃更新或存在关键修复可提升工具稳定性/安全性。

## 输入

- `workspace_root`：工作区根路径，默认 `C:\Users\86152\Documents\AI博主视频`
- `target_date`：例如 `2026-07-04`
- 可选：`candidate_overrides`（当日候选对象覆盖名单）

## 输出

- 项目文件：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`、`renders/README.md`。
- 发布文件：`output/SKILL.md`、`output/github-release-notes.md`、`output/README.md`。
- 成果：本地 git commit 与可选 GitHub 提交 URL。

## 核心升级点

### U1 Browser Use CLI 3.0 与 skill 安装统一化（browser-use/browser-use）
- 依据：`0.13.3` release notes。
- 目标：在 Codex / Claude / 其他 Agent 环境下，稳定生成“可复用安装动作”。
- 能力边界：仅处理 browser-use 安装、版本固定和 CLI 可用性检查，不替代浏览器业务脚本逻辑。
- 升级实现：
  1. 优先校验 `browser-use --version` 可执行性。
  2. 运行 `browser-use skill` 安装路径健康检查命令（按能力探测自动判断命令名）。
  3. 校验 `--help`/`--version` 结果包含 0.13.3 或更新语义。
  4. 产出可复用命令模板，供其他 SKILL 直接引用。

### U2 Codex WebSocket 请求载荷隐私保护（openai/codex）
- 依据：`rust-v0.142.5` release note（防止 full request payload 写入 trace 日志）。
- 目标：降低敏感载荷泄露并改善 trace 可读性。
- 能力边界：仅对 Codex 会话日志链路进行最小化采样与遮蔽，不承担全链路安全审计。
- 升级实现：
  1. 检查 `codex` 版本是否支持上述修复。
  2. 提供 trace 采样策略（默认关闭请求体级明文写入）。
  3. 在 `script.md` 中固定“成功/失败日志字段清单”，避免误泄露。
  4. 记录异常场景并要求人工确认敏感数据审阅。

### U3 MCP v2 预发布兼容调度（modelcontextprotocol/python-sdk）
- 依据：`v2.0.0b1` release notes（auto mode、streamable HTTP/stdio 与降级策略）。
- 目标：为多版本 MCP 服务实现兼容型能力路由，减少协议升级导致中断。
- 能力边界：v2.0.0b1 为预发布；稳定生产不强制；必须保留 v1 线。
- 升级实现：
  1. 若 `python` 环境可用，验证 `mcp>=1.27,<2` 约束策略是否存在。
  2. 自动判断并优先走稳定 v1；如显式开启实验模式，改走 v2 auto client 流程。
  3. 输出兼容建议（pin exact version + fallback checklist）。
  4. 在失败时默认回退 v1 并输出迁移步骤。

## 执行流程

1. 准备阶段
   1. 检查 `gh auth status` 与 `git remote -v`。
   2. 检查候选项目是否已生成；未生成则创建 `Project_15_ai-skill-upgrade-20260704`。
   3. 建立输出目录和标准文件。
2. 信息采集
   1. `gh repo view` 与 `gh release view` 采集 stars、pushedAt、release tag、release body。
   2. 如任一命令失败，记录失败原因并退回到人工确认流程。
3. 评估与筛选
   1. 为每个候选写入用途、功能、工作原理、边界、来源、热度、选/不选理由。
   2. 挑选 3 个最有复用价值升级点。
4. SKILL 编写
   1. 生成 frontmatter 与能力边界文档。
   2. 输出执行流程、验证方法、失败处理、降级策略与无凭据规则。
5. 发布与归档
   1. 生成 `output/github-release-notes.md`。
   2. 提交：`feat: add ai skill upgrade 2026-07-04`。
   3. 推送到 `Jienigoto/ai-skill-upgrades`。

## 工具边界

- 可使用：`web`/`gh`/`git`/终端命令，写入文本文件。
- 不可替代：真实网络安全审计系统、生产服务监控、跨组织权限申请。
- 禁止：存储 secret、token、私有凭据、一次性隐私数据。

## 验证方式

- 结构验证：检查项目文件齐备且路径存在。
- 数据验证：对每个候选记录 stars、pushedAt、release tag 是否符合当日抓取。
- 风险验证：确认每个升级点都有失败回退方案（v1 降级、敏感日志停用、安装失败降级）。
- 发布验证：`git status` 空、`git push` 成功、commit 能被远端读取。

## 失败处理与降级

- 无 `gh` 凭据：仅生成本地文件并在 render-notes、github-release-notes 明确标注 block reason。
- 网络异常：中断候选抓取并保留已采集部分；标注缺口，不编造数据。
- 工具不可用（web、browser-use、codex 或 mcp API 不可达）：将该升级点转为人工审批路径，回退到“手动验证与说明模板”。
- 若 GitHub 推送失败：保留本地产物，发布 blocker 与下一步手工命令。

## 回退与降级策略（简版）

- browser-use step 失败 → 保留 `script` 和 `brief`，不阻断其余升级点，提示手动安装命令。
- Codex trace 安全项失败 → 仅输出“仅最小日志 + 手动核验 trace 配置”。
- MCP v2 失败 → 强制走 MCP v1 稳定线，并附上 pin `<2` 的约束与迁移清单。

## 复用方式

- 可直接在 Codex 任务中按步骤执行 `script.md` 与 `brief.md`。
- `output/SKILL.md` 可复制到新项目做下一步日更基线。

