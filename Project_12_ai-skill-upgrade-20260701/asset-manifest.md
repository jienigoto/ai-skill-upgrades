# Asset Manifest（2026-07-01）

## 资产清单与证据

| 资产 | 类别 | 证据来源 | 说明 |
|---|---|---|---|
| browser-use 0.13.2 | GitHub Release | https://github.com/browser-use/browser-use/releases/tag/0.13.2 | 2026-06-12 发布；包含 provider-prefixed models 与 PyPI 发布门控 |
| modelcontextprotocol/python-sdk v2.0.0a3 | GitHub Release | https://github.com/modelcontextprotocol/python-sdk/releases | 2026-06-26 发布；pre-release + `mode='auto'` 协议协商 + streamable HTTP 调整 |
| anthropics/claude-code v2.1.197 | GitHub Release | https://github.com/anthropics/claude-code/releases/tag/v2.1.197 | 2026-06-30 发布；默认模型升级到 Sonnet 5，支持 1M token 并公布促销价格 |
| openai/codex rust-v0.142.4 | GitHub Release | https://github.com/openai/codex/releases/tag/rust-v0.142.4 | 2026-06-29 发布；“No user-facing changes”，用于对照和回归风险解释 |

## 输出映射

- 输入：本次候选对象、发布页、仓库元数据、当前环境状态
- 输出：3 个可复用升级点、执行流程、失败降级、GitHub 发布报告
