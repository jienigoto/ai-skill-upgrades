# 今日研究 brief（2026-06-22）

## 候选对象与筛选证据

### 1. openai/openai-agents-python
- 用途：多智能体工作流框架，覆盖 tool use、沙箱、checkpoint、memory 等核心能力。
- 核心功能：轻量可组合 agent、模型运行时、工具调用与输出处理。
- 工作原理：通过 Runner 与工具执行链编排任务，把每步工具输入输出以结构化协议返回给模型。
- 边界：`runner` 生态对稳定工具与模型调用约定依赖强；复杂场景下仍需外部治理。
- 热度证据：
  - 2026-06-19 发布 `v0.17.6`
  - Release notes 显示 `pre-approval tool input guardrails`、`SDK-only custom data for tool outputs`
  - GitHub 指标（当日采集）：`pushed_at=2026-06-19T06:45:24Z`, stars 27305
- 是否适合转 Skill：适合。可直接输出“任务执行前的工具输入守卫 + 输出契约断言”模板。

### 2. modelcontextprotocol/python-sdk
- 用途：标准化工具协议（MCP）客户端与服务端 SDK，支撑多模型/多系统接入。
- 核心功能：工具暴露、资源管理、通信传输抽象。
- 工作原理：为工具接入提供标准会话与 transport 封装，支持协议层可替换。
- 边界：协议演进快，旧 API 在 1.x 到 2.0 将逐步移除。
- 热度证据：
  - 2026-06-16 发布 `v1.28.0`
  - Release notes 显式宣布 WebSocket transport 与 experimental tasks API 即将弃用，预警机制新增
  - GitHub 指标：`pushed_at=2026-06-21T18:34:20Z`, stars 23394
- 是否适合转 Skill：非常适合。可转为“可迁移通信协议的稳定接入模板+版本兼容层”。

### 3. browser-use/browser-use
- 用途：面向网页自动化的 AI agent 框架。
- 核心功能：多模型浏览器动作规划、DOM 交互、会话执行。
- 工作原理：模型输出动作/命令后由浏览器执行层执行，再反馈结果给模型。
- 边界：网页结构变更导致稳定性波动，依赖 Playwright 与模型响应稳定性。
- 热度证据：
  - 2026-06-12 发布 `0.13.2`
  - 最近提交与维护频率高（`pushed_at=2026-06-20T18:21:29Z`）
  - stars 99939，open issues 较多但可见问题边界
- 是否适合转 Skill：适合。可形成“网页操作前置检查 + 降级为只读抓取/重试”模板。

### 4. agno-agi/agno（未入选）
- 用途：agent 平台框架，功能广。
- 原因：能力点重心偏组织化平台整合，且与本次“单一、可落地、低耦合”Skill 边界不够匹配；可作为下一期升级对象。

### 5. Claude（官方 Release Notes）
- 来源：`https://support.claude.com/en/articles/12138966-release-notes`
- 价值：用于能力边界比较（权限、connector、computer use、模型与安全策略）
- 原因：未完全对应可直接落地的单一 repo 代码更新路径，不纳入本次 3 点。

## 3 个升级方向最终对比

1. `openai/openai-agents-python`：从“能跑”到“可审计、可约束”
2. `modelcontextprotocol/python-sdk`：从“可用”到“迁移可控、可追踪弃用风险”
3. `browser-use/browser-use`：从“自动化”到“可复现、可回退的任务执行”

## 选型结论

- 优先级 1：`openai/openai-agents-python`
- 优先级 2：`modelcontextprotocol/python-sdk`
- 优先级 3：`browser-use/browser-use`
