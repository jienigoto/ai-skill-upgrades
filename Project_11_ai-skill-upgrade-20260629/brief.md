# Brief（AI Skill 升级）

## 研究方向（2026-06-29）

- browser-use 0.13.2：聚焦 web 自动化执行稳定性
- modelcontextprotocol/python-sdk v2.0.0a3：聚焦 MCP transport 兼容与可观测性
- Copilot CLI GA：聚焦终端任务协作与插件治理

## 候选对象记录

| 对象 | 用途 | 核心功能 | 工作原理 | 边界 | 来源证据 | 热度/活跃证据 | 是否入选 |
|---|---|---|---|---|---|---|---|
| browser-use | 浏览器代理自动化 | 网页导航、表单/点击/提取、任务执行 | 任务型 agent + LLM tool 调度 | 不适合复杂多步骤手工 UI 纠偏环 | GitHub release 0.13.2 | 101k star；12 Jun 发布；19 commits | ✅ |
| modelcontextprotocol/python-sdk | MCP 工具接入与模型协议路由 | 传输层、工具调用、会话管理 | 客户端/服务端 transport 与协议协商 | alpha 变更频繁，不适合单独作为稳定生产默认 | GitHub release v2.0.0a3 | 23.5k star；26 Jun 发布 | ✅ |
| GitHub Copilot CLI | 终端化研发/评审协作 | Tabbed 浏览、`/mcp add/search`、`/skills`、`/plugin`、`/settings` | 会话内命令引导与上下文流 | 仍依赖终端交互，较难完全无人值守 | GitHub Changelog 2026-06-23 | 6/23 GA 官方发布与功能点详述 | ✅ |
| openai/codex | Coding agent 与 CLI/工具扩展 | usage/plugins 等治理与代理执行 | Rust/CLI release 线快速迭代 | 近期多为 alpha 预发布，边界抖动高 | GitHub releases 页 | 2026-06-26 为 latest 的近期 release（alpha） | ⚪ |
| Claude Skills | 对话式可复用工作流 | Skills 切换、上传、共享、治理 | 账号/组织能力控制与插件目录 | 需依赖官方权限与 code execution 环境 | Anthropic Help Center（2026-05） | 官方能力说明明确，非高频发布页 | ⚪ |

## 3 个升级点

### U1：browser-use 执行护栏（web 任务稳定化）

- 问题：多步网页任务跨模型与多执行环境易抖动。
- 比较优势：借助 provider-prefixed 模型和发布护栏，将 provider 与 runtime 版本变化收敛到统一 precheck 与可复现动作。
- 稳定性增强：新增任务前置检查（模型前缀、DOM 复杂度、超时阈值）、动作快照、结果校验（必填字段、失败码映射）。
- 失败降级：Web 路径失败后输出只读抓取（当前页面关键 DOM + 链接摘要）并进入人工确认列表；若关键动作失败连续超过阈值，自动降级到“信息提取”模式。

### U2：MCP transport 可切换层（协议兼容）

- 问题：MCP 协议升级阶段，`v1` 与 `v2 alpha` 并行时期最容易出现连接抖动与类型不兼容。
- 比较优势：`mode='auto'` + discover/adopt 流可自动选择协议路径，减少手工条件分支。
- 稳定性增强：统一封装 `ClientSession.send_request` 超时/重试、版本探测和回退策略，并对 InputRequiredResult 分支输出结构化状态。
- 失败降级：遇到签名、鉴权、schema 校验异常时，自动降级到 `legacy` 或 `v1` 已知约束依赖（示例：`mcp<2` 下限与安全白名单）。

### U3：Copilot CLI 终端协作闭环

- 问题：issue/PR 审核与工具配置经常在终端与网页之间反复切换。
- 比较优势：tab 带来“单会话内发现→定位→引用→反馈”闭环，插件/技能/设置可在会话内完成。
- 稳定性增强：新增“命令可重放清单”，固定命令顺序、超时策略与访问记录，减少会话漂移。
- 失败降级：缺少会话能力时回退到 CLI 传统命令（仅最小只读查询），必要时提示用户切换到 web 控制台。

## 验证结论（用于本次 SKILL）

- Top3 已按近 7 天活跃发布 + 可复用性 + 可降级策略选定。
- 所有 Top3 均可映射为“执行前置检查、执行、回退”三段式 SKILL。
- 重点文档：`output/SKILL.md`。
