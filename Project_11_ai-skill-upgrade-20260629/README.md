# AI Skill 升级日报（2026-06-29）

## 研究方向

1. browser-use 的浏览器代理稳定性与多模型兼容
2. modelcontextprotocol/python-sdk 的 streamable HTTP / MCP 新版协议可观测化落地
3. GitHub Copilot CLI 的终端化工作流能力与插件/技能治理

## 当日候选与当日化证据

### browser-use/browser-use

- 目标对象：web 自动化代理框架
- 最新发布：0.13.2（2026-06-12）
- 星标与活跃：约 101k stars；发布后 19 commits to main
- 变更证据：新增 BU3 模型支持、provider-prefixed model、发布时发布流程护栏、内核版本同步
- 适配评价：高
- 适配原因：与常见“浏览器脚本执行 + 任务闭环”场景强相关，且变更集中可直接抽象为 Codex 可复用的执行护栏模式。
- 适配边界：不直接保证复杂人机交互场景可执行稳定，仅用于可脚本化、幂等可回放流程。

### modelcontextprotocol/python-sdk

- 目标对象：MCP Python SDK
- 最新发布：v2.0.0a3（2026-06-26）
- 星标与活跃：约 23.5k stars；连续 alpha 每周推进
- 变更证据：stateless 协议可协商、会话管理分流、mode='auto'、multi-round tool calls、InputRequiredResult、迁移与兼容说明明确
- 适配评价：高
- 适配原因：覆盖 Codex/MCP 生态中的连接层稳定化，可直接转化为 transport 兼容层 SKILL。
- 适配边界：alpha 阶段 API 波动较大，不适合作为唯一生产基线。

### GitHub Copilot CLI

- 目标对象：Copilot CLI
- 发布窗口：2026-06-23 一般可用（GA）
- 变更证据：交互式 Tab、内置 `/mcp add/search`、`/skills`、`/plugin` 与 `/settings`，支持可访问性主题与反馈闭环
- 适配评价：中高
- 适配原因：面向终端协作任务（issue/PR/gist）回路完整，能直接提升自动化审核与任务分发效率。
- 适配边界：需要用户在终端环境中进行交互，适合“人-模型协作流”与半自动化。

### 候选补充（当作放弃项）

- openai/codex（近期高频 pre-release）
  - 原因：`openai/codex` 近期仍在频繁发布（0.143.0-alpha.29），但以 pre-release 为主，版本边界与发布说明不够稳定，暂不优先入选 top3。

- Claude Skills（官方文档发布）
  - 原因：能力模型（开启/管理/上传）可复用，优先度在本轮低于 2 个底层基础设施对象。

## 3 个升级方向（高价值 Top 3）

1. U1：web 执行护栏化
   - browser-use 任务执行闭环：新增 provider-prefixed model、发布护栏，定义“预检-执行-QA-降级”模板。
2. U2：MCP transport 多模兼容
   - modelcontextprotocol/python-sdk 适配新 stateless 协议与 `mode='auto'`，并在 v2 alpha 前置阶段加入版本上限与回退。
3. U3：终端化协作工作流治理
   - Copilot CLI 的 tabbed 工作区 + in-session MCP/Skills/Plugin 管理能力。

## 产出

- [ ] README.md
- [x] brief.md
- [x] script.md
- [x] asset-manifest.md
- [x] render-notes.md
- [x] output/SKILL.md
- [x] output/github-release-notes.md
- [x] output/README.md（本任务未要求生成，但可按需补充）
- [x] output/renders 目录
