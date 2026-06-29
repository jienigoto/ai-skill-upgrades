# Asset Manifest（2026-06-29）

| 资产 | 类别 | 证据来源 | 说明 |
|---|---|---|---|
| browser-use 0.13.2 | GitHub release | https://github.com/browser-use/browser-use/releases/tag/0.13.2 | `provider-prefixed model`、release env gate、版本 bump |
| modelcontextprotocol/python-sdk v2.0.0a3 | GitHub release | https://github.com/modelcontextprotocol/python-sdk/releases | stateless 协议可协商、`mode='auto'`、multi-round tool calls |
| GitHub Copilot CLI 2026-06-23 GA | 官方 changelog | https://github.blog/changelog/2026-06-23-copilot-cli-new-terminal-interface-is-generally-available/ | tabbed UI + `/mcp` `/skills` `/plugin` + `/settings` |
| openai/codex | GitHub releases | https://github.com/openai/codex/releases | 近期 alpha 更新频繁，边界抖动较大，作为候选但未入选 |
| Claude Skills 官方文档 | 官方支持页 | https://support.claude.com/en/articles/12512180-use-skills-in-claude | Skills 的开启、上传、组织共享与风控边界 |

## 本次 SKILL 输入/输出映射

- 输入：候选对象列表、发布页面、工具可用性、日期上下文
- 输出：3 个可复用升级点、稳定化策略、失败回退路径、发布检查清单
