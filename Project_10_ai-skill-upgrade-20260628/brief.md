# Brief（2026-06-28）

## 目标

以 Codex 可复用的方式抽取当日高活跃 AI 能力的**可执行增强点**，输出三条可直接落地的 SKILL 升级定义：网页执行治理、MCP transport 强化、agent 契约稳定。

## 选题范围

- 主对象必须有近期更新（近 14 天内有 release/提交）。
- 优先对象：可解释“为什么更稳”、边界清晰、可降级。
- 覆盖对象类型：AI 应用、AI 工具、Codex/Claude/GPT 生态能力、可复用工作流。

## 当日候选与筛选

- browser-use/browser-use（`0.13.2`）
  - 2026-06-12 release，含 provider-prefixed model、BU-3 调整、发布流程门控与 PDF/metadata 改进。
  - 适合：网页执行失败可通过预检和降级路径切走，易转化为稳定性 SKILL。
- modelcontextprotocol/python-sdk（`v1.28.1`）
  - 2026-06-26 release，新增 per-request streamable HTTP 事件处理、TransportSecuritySettings 和 production marker。
  - 适合：对多工具生态的稳定接入和安全边界控制价值高，天然可拆解成重试与超时策略。
- pydantic/pydantic-ai（`v2.0.0`）
  - 2026-06-23 稳定版，能力对象（Capability）收敛、工具调用与 usage、模型设置边界更完整。
  - 适合：工具链治理和失败归因的高价值转换。
- openai/codex
  - 2026-06-27 有 alpha 预发布与日更提交，当前以维护性与版本推进为主，未形成单一明显可操作的“能力点升级”。
- open-webui/open-webui
  - 6 月内更新较多但模块体积过大，不利于本次单日最小化 SKILL 交付，列为观察对象（未入选）。

## 当日 3 个升级点（Top）

1. browser-use 任务闭环与 QA 回退链。
2. MCP transport 可观测化与超时/流式事件稳态化。
3. pydantic-ai capability 契约与模型调用防回退链路。

## 约束

- 不改动非目标目录。
- 不写入 secrets、password、token。
- 若认证/网络/工具不可用，保留本地产物并在 render-notes 标注阻塞与下一步。 
