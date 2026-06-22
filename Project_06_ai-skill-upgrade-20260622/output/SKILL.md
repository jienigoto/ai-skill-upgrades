---
name: "daily-ai-skill-upgrade-agentic-runtime"
description: "每日 AI Skill 升级流程，聚焦 Agent 运行时稳定性与工具链可复用性"
version: 1
trigger_conditions:
  - "用户触发每日 AI Skill 升级流程"
  - "需要筛选近期高频 AI 生态更新并产出可复用 Skill"
  - "需要可追溯的发布日志与失败降级策略"
inputs:
  workspace_root:
    type: path
    required: true
    description: "AI 博主视频工作区根目录"
  target_topic:
    type: string
    required: true
    description: "daily ai skill upgrade"
  sources:
    type: list
    required: false
    description: "候选对象来源列表，默认使用 web + GitHub REST"
outputs:
  - "brief.md"
  - "script.md"
  - "asset-manifest.md"
  - "render-notes.md"
  - "output/SKILL.md"
  - "output/github-release-notes.md"
  - "README.md"
steps:
  - "读取 AGENTS.md 与 00/02/03 全局流程文件"
  - "确认当日同主题项目目录，若不存在则创建 Project_XX_ai-skill-upgrade-YYYYMMDD"
  - "调用 web 与 GitHub REST 采集候选对象（元数据 + 最新 release）"
  - "为每个候选补齐：用途、功能、原理、边界、来源、热度证据"
  - "选择 3 个最值得升级的点，落地到 output/SKILL.md"
  - "进行 GitHub 鉴权、远端可达性、提交与推送检查"
  - "生成 publish notes 与 render-notes 阻塞记录"
execution:
  tools:
    required:
      - "web.search_query 或等效 Web 搜索能力"
      - "GitHub REST API（公开仓库数据 + releases）"
      - "gh cli（若可用）"
      - "git"
    boundaries:
      - "不得读取或提交用户 secrets / tokens"
      - "不得跨项目修改未明确目标的旧项目"
      - "不得在发布失败时伪造成功"
validation:
  required:
    - "目录齐全度"
    - "至少 1 条官方来源与 3 条高质量候选"
    - "3 个升级点有清晰问题定义/优势说明/失效降级"
    - "发布记录可复现"
upgrade_points:
  - id: P1
    name: "Agents SDK 预审批与工具输出可验证化"
    from: openai/openai-agents-python
    problem: "工具输入/输出格式漂移导致执行失败与不可审计行为"
    stronger_than_ref: "相较于旧流程，新增 pre-approval guardrails + strict JSON contract 约束工具交互边界"
    stability_improvement: "在执行前后统一做 schema 校验与失败归因，阻断非法参数并生成明确错误上下文"
    fallback: "当无法通过校验时，降级到最小单步执行 + 人工确认模式，不影响历史上下文"
  - id: P2
    name: "MCP 传输与兼容层迁移"
    from: modelcontextprotocol/python-sdk
    problem: "版本迁移不充分导致 transport/API 弃用风险在长期运行中放大"
    stronger_than_ref: "使用 1.28.0 的弃用警告机制，提前建立显式兼容层和告警"
    stability_improvement: "在 SKILL 内加入 transport 能力检测与自动回退到 streamable HTTP 的路径"
    fallback: "降级到本地工具接口与 mock transport；保留原生调用日志用于复盘"
  - id: P3
    name: "browser-use 动作执行鲁棒性"
    from: browser-use/browser-use
    problem: "网页结构变化与模型回包差异使多步自动化失败率高"
    stronger_than_ref: "借鉴新版 provider-prefixed model 与发布门禁设计，形成执行前检查与安全边界"
    stability_improvement: "在每步动作前执行 DOM 与上下文就绪检查，失败时自动执行重试与读取优先路径"
    fallback: "降级为只读抓取（read-only）模式，触发人工复核任务"
verify_and_publish:
  precheck:
    - "gh auth status"
    - "git remote -v"
    - "gh repo view Jienigoto/ai-skill-upgrades --json nameWithOwner,url"
  artifact_validation:
    - "render-notes.md"
    - "github-release-notes.md"
    - "asset-manifest.md"
  publish_strategy: "本地提交成功即保留产物，发布失败时保留完整阻塞日志，不输出成功"
failure_strategy:
  no_network: "切换为仅本地缓存和上一次稳定快照，产出可重试列表"
  web_fail: "写入候选证据缺口与待补证据项，不阻塞本地文档生成"
  gh_not_available: "标记为发布阻塞原因，保留本地 commit 与产物目录"
  mcp_deprecation: "优先输出迁移清单，使用显式 deprecation warning 并要求更新路径"
