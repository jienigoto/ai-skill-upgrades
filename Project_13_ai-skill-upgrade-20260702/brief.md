# 当日候选筛选（2026-07-02）

## 研究对象池

1. browser-use/browser-use
- stars: 102,015
- pushed_at: 2026-07-01T15:06:07Z
- release: 0.13.3（2026-07-01）
- 来源: https://github.com/browser-use/browser-use/releases/tag/0.13.3
- 2026-07-02 仍在高频关注：release notes 与更新对 Codex/Claude 等 agent skill 兼容性直接相关。

2. openai/codex
- stars: 94,890
- pushed_at: 2026-07-02T00:02:44Z
- release: rust-v0.142.5（2026-07-01）
- 来源: https://github.com/openai/codex/releases/tag/rust-v0.142.5
- 适合转 SKILL：Codex 作为日常 GitHub/Git 工作流主力时，trace/log 稳定性直接影响复用可靠性。

3. pydantic/pydantic-ai
- stars: 18,125
- pushed_at: 2026-07-01T23:54:59Z
- release: v2.2.0（2026-07-01）
- 来源: https://github.com/pydantic/pydantic-ai/releases/tag/v2.2.0
- 适合转 SKILL：模型接入、生命周期与评测增强，适合沉淀“可复用的任务执行模板”。

4. open-webui/open-webui
- stars: 143,729
- pushed_at: 2026-07-01T08:41:05Z
- release: v0.10.2（2026-07-01）
- 来源: https://github.com/open-webui/open-webui/releases/tag/v0.10.2
- 说明：AI 应用体验改进明显，但本次优先顺序偏向可直接转 SKILL 的 Agent / 工具链能力。

5. google/adk-python
- stars: 20,393
- pushed_at: 2026-07-01T23:50:37Z
- release: v2.3.0（2026-06-17）
- 来源: https://github.com/google/adk-python/releases/tag/v2.3.0
- 说明：近期 push 高、功能点有价值，但本次优先筛入为候选池备选。

## 入选与否判定

| 对象 | 用途与边界 | 是否入选 | 说明 |
| --- | --- | --- | --- |
| browser-use 0.13.3 | 浏览器自动化任务（导航/点击/内容抽取） | ✅ | 0.13.3 对多 agent 的 skill 安装行为有直接修复，适合转稳定化 skill。 |
| openai/codex rust-v0.142.5 | Codex 代码代理运维与追踪日志 | ✅ | trace 安全与 websocket 重放问题，适合做“默认安全开关 + 可回退” SKILL。 |
| pydantic-ai v2.2.0 | Agent SDK / 评测链路 | ✅ | 提供 Sonnet5 与 `Dataset.evaluate` 能力更新，适配可复用能力模板。 |
| open-webui v0.10.2 | AI 对话应用 | ⚪ | 非直接 agent runtime 基础设施，保留为参考。 |
| google/adk-python v2.3.0 | Agent 开发框架 | ⚪ | 本轮保留为对照池，当前更偏框架工程选型与二次封装。 |

## 3 个升级点（Top3）

### U1：browser-use 0.13.3 `browser-use skill` 一体化治理

- 解决问题：在不同 agent 运行环境（Claude Code/Codex/Cursor/OpenCode）安装技能行为不一致，导致任务执行失败。
- 比参考更强：直接封装 `skill install` 到 `precheck -> execute -> validate -> fallback` 四阶段，而不是用 ad-hoc 脚本。
- 提升稳定性：版本锁定到 `0.13.3`，统一 skill 入口检测、CLI 3.0 启动参数校验和超时保护。
- 降级策略：若技能安装失败，自动切换为只读浏览动作（DOM 抓取+链接摘要）并进入人工确认模式。

### U2：openai/codex `rust-v0.142.5` trace 笔记级降噪与 WebSocket 健壮性

- 解决问题：完整 Responses WebSocket 载荷被写入 trace 时可能导致噪音、性能与安全边界问题。
- 比参考更强：将“防回放 + 追踪最小化”内建为执行入口条件，而非运行后再手工清理。
- 提升稳定性：默认限制 trace 日志字段，增加“危险载荷告警 + 退避重试”机制，避免任务因日志链路阻塞。
- 降级策略：关闭 websocket 追踪通道，退回标准响应收集流程（保留命令级日志）。

### U3：pydantic-ai v2.2.0 `Dataset.evaluate` 与模型边界可配置化

- 解决问题：agent 任务链中的模型切换与评测生命周期参数化不足，导致多任务复用难。
- 比参考更强：将 `Dataset.evaluate` 生命周期入口参数化 + Sonnet-5 适配封装成可复用步骤，减少每次手写 glue code。
- 提升稳定性：加入 schema 校验、字段白名单、失败重试与结果汇总模板，输出结构化质量指标。
- 降级策略：若评测阶段失败，降级为“单一摘要产出 + 人工复核清单”，仅保留最小执行上下文。