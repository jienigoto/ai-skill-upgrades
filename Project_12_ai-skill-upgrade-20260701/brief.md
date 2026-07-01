# 当日候选筛选（2026-07-01）

## 研究方向

1. browser-use 0.13.2（2026-06-12）
2. modelcontextprotocol/python-sdk v2.0.0a3（2026-06-26）
3. anthropics/claude-code v2.1.197（2026-06-30）
4. openai/codex rust-v0.142.4（2026-06-29，作为边界案例）

## 候选对象记录

| 对象 | 用途 | 核心功能 | 工作原理 | 边界 | 来源/热度证据 | 是否入选 |
|---|---|---|---|---|---|---|
| browser-use/browser-use 0.13.2 | 浏览器代理自动化/任务执行 | 网页导航、点击/表单、内容抽取、模型驱动任务编排 | 发布日志展示 `ChatBrowserUse` 模型命名增强、`provider-prefixed models`、`publish_to_pypi` 环境门控 | 不适合完全无头或高度动态前端场景；仍依赖页面稳定性 | `Released 12 Jun`；101k stars；`0.13.2` 最新发布 | ✅ |
| modelcontextprotocol/python-sdk v2.0.0a3 | MCP 工具调用协议运行时 | 支持协议版本协商、stateless HTTP、OAuth 与多轮工具返回规范化 | `mode='auto'` 自动协商路径，`ClientSession` 与 `Client` 升级；输入输出类型更丰富 | 处于 alpha 阶段，API 易变 | `released this 26 Jun`；23.5k stars；v2 alpha、`latest_protocol_version=2026-07-28` | ✅ |
| anthropics/claude-code v2.1.197 | 终端化 AI 代码代理工作流 | 终端交互、代码修改、工作流封装、Git 流程协同 | 将 Claude Sonnet 5 设为默认模型，使用 1M token 上下文并提供成本指标 | 需要终端可交互环境和工具链权限；与项目权限策略绑定 | `released this 30 Jun`；135k stars；更新内容面向成本+上下文 | ✅ |
| openai/codex rust-v0.142.4 | Codex 客户端/代理维护 | 以稳定性维护为主的内部变更 | 发布页仅标记“无用户可见变更”，不适合单独转为当天升级模板 | 适合作为对照对象，表明筛选规则 | `released this 29 Jun`；`No user-facing changes were identified` | ⚪ |

## 3 个升级方向（Top3）

### U1：browser-use 任务执行护栏化

- 解决什么问题：网页自动化在跨模型与任务链切换时容易因模型名/环境差异导致不可复现失败。
- 比参考更强：直接将 `provider-prefixed models` 与发布环境门控抽象成统一的前置检查，较单个 PR/单脚本配置更稳定。
- 提升稳定性：标准化 4 阶段 `precheck → execute → validate → fallback` 流程；任务执行前校验模型前缀、目标交互动作、超时与重试预算。
- 降级策略：失败连续超过阈值时切换只读模式，输出 DOM 快照+关键链接摘要供人工决策。

### U2：MCP Transport 兼容与协议路由升级

- 解决什么问题：v2 alpha 与 v1.x 并行时，客户端连接与工具返回结构易错配。
- 比参考更强：用 `mode='auto'` 与 `discover/adopt` 流替代固定协议分支，减少手工切换与环境判断错误。
- 提升稳定性：在连接时做 `server/discover` 级探测 + `initialize` 回退，并对 `InputRequiredResult` 的重试与请求状态进行显式分支。
- 降级策略：Alpha 失败时自动回退到 legacy/稳定线；禁止强制开启会破坏向后兼容的拆解特性。

### U3：Claude Code 上下文与成本治理模板

- 解决什么问题：复杂任务常因为上下文扩展和成本不可控导致执行中断。
- 比参考更强：以 `Claude Sonnet 5` 新默认模型作为 baseline，先做成本预算（token 上限）再执行；并将模型/上下文策略内置于 SKILL 入口。
- 提升稳定性：默认采用 1M-token 上下文门槛、执行前成本预估、每轮输出质量校验，必要时自动缩小 diff 范围。
- 降级策略：当 CLI/插件/模型配置失败时，回退到只读解释模式 + 人工确认；或锁定较低成本模型策略。

## 复用说明

- 对 `browser-use` 与 `MCP transport` 的分层预检思路可直接复用到其它 Agent 工具链（如 Codex 插件、数据抽取工作流）。
- 建议每轮保留 1 小时内的变更快照（release link + changelog commit）以便回溯。
