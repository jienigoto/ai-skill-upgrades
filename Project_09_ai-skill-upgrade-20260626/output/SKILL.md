---
name: daily-ai-skill-upgrade-2026-06-26
description: "Daily AI Skill upgrade workflow for browser automation, coding agents, and typed agent contracts"
version: 1
trigger_conditions:
  - 每日 AI Skill 升级自动化任务被触发。
  - 需要对 Codex/Claude/GPT 类能力与高活跃 AI Agent 工具做可复用改造。
  - 允许访问 web 浏览器、GitHub API 与 GitHub CLI。
inputs:
  workspace_root:
    type: path
    required: true
    description: 当前 AI 博主工作区根目录。
  target_date:
    type: string
    required: true
    example: "2026-06-26"
  candidate_repos:
    type: array
    required: true
    description: 待评估候选仓库列表。
  publish_target:
    type: string
    required: true
    default: "Jienigoto/ai-skill-upgrades"
outputs:
  - README.md
  - brief.md
  - script.md
  - asset-manifest.md
  - render-notes.md
  - output/SKILL.md
  - output/github-release-notes.md
required_tools:
  - web（搜索与页面验证）
  - GitHub REST API
  - gh CLI（可选，但优先使用）
  - 浏览器（可用于可见性复核）
tools_scope:
  in:
    - 公开信息抓取（release、issue、commit、stars、更新日志）
    - 技能文本生成与升级策略编排
    - GitHub 发布记录组织
  out:
    - 不能执行任何破坏性仓库改动
    - 不能读写用户密钥
    - 不能调用未授权的系统命令
upgrade_points:
  - id: U1
    title: browser-use 任务闭环能力增强
    source: https://github.com/browser-use/browser-use/releases/tag/0.13.2
    problem: "网页任务在动态页面、跨标签页和多步流程中易出现不可复现失败，人工回溯成本高。"
    why_stronger: "0.13.2 引入与持续提交集中在模型前缀适配、tab 切换与会话稳定性、以及 BU3 模型接入，能直接降低常见执行抖动。"
    stability_plan:
      - "为每个网页任务加预检（可访问性、DOM 规模、超时）"
      - "将任务拆分为 action plan + 结果校验（最小状态 + QA skill）"
      - "关键步骤保存可复现上下文（task id、输入参数、截面日志）"
    fallback: "若模型调用或浏览器会话失败，切换到只读采集模式（抓取页面文本与链接）并生成需人工确认清单。"
  - id: U2
    title: openai/codex 运行治理与安全边界
    source: https://github.com/openai/codex/releases/tag/rust-v0.142.2
    problem: "编码代理在不同环境（尤其 Windows）下偶发阻塞，且工具依赖或环境变量缺失时难以快速定位。"
    why_stronger: "通过 codex 的持续更新与 CI 修复，可以将执行前置检查前置化：权限、运行时和工具依赖都做显式 gate。"
    stability_plan:
      - "加入任务前 `environment guard`（token、runtime、目录权限、超时阈值）"
      - "按可恢复失败分类（可重试/不可重试）输出退化路径"
      - "失败时输出最小安全操作集，避免重试放大风险"
    fallback: "网络/模型不可用时输出计划化任务清单，停留在 diff 生成与建议级别，不执行写操作。"
  - id: U3
    title: pydantic-ai 契约化工具调用
    source: https://github.com/pydantic/pydantic-ai
    problem: "技能链路中工具输入输出缺少统一契约时，结果漂移与异常处理不可控。"
    why_stronger: "新增工具事件与契约能力可用于构建可观测流水线：输入输出可验证，失败可归类。"
    stability_plan:
      - "定义统一 schema（请求、响应、失败码）并在技能边界做强校验"
      - "记录 tool_choice 与输出事件，用于回放与断言"
      - "在输出侧加入类型和边界回退（可读文本 + 人工确认）"
    fallback: "schema 校验失败时进入 `manual_review`：输出原始结构、问题摘要、最小安全建议。"

steps:
  - 读取 AGENTS.md 与 00/02/03 规则文件；确认不改动非目标项目。
  - 检查当天是否已有同主题项目，若有则延后到下一个编号。
  - 使用 web 与 GitHub API 抓取候选能力来源、release、提交与活跃指标。
  - 对每个候选做用途、边界、活跃性与转 SKILL 可行性评分。
  - 自动择优输出 3 个升级点，输出问题/强项/稳定/降级。
  - 产出项目文件与 output/SKILL.md。
  - 尝试 GitHub 发布；失败则输出阻塞原因。

verification:
  - "候选对象至少 3 个且有明确 evidence（来源链接+release/提交时间+变更摘要）"
  - "output/SKILL.md 包含触发条件、输入输出、边界、验证、故障处理、降级"
  - "发布文件（github-release-notes）记录实际状态并附 commit/阻塞说明"

failure_handling:
  no_network:
    detect: "web/gh API 请求超时、DNS 失败、SSL 错误"
    action: "保留本地文件，render-notes 记录阻塞，提示人工切到稳定网络后重跑。"
  no_credentials:
    detect: "gh auth status 失败、token 失效"
    action: "仅在本地落盘；render-notes 明确说明并记录需要人工登录。"
  repo_not_found_or_no_permission:
    detect: "仓库不存在或缺少写权限"
    action: "尝试 `gh repo view` 后给出新建/提权路径，不执行远端破坏操作。"
  tool_unavailable:
    detect: "gh/浏览器能力不可用"
    action: "退化为 web 工具结果汇总+本地产物，不提交 GitHub。"
  malformed_output:
    detect: "某文件缺失关键字段"
    action: "补齐字段后重新写入相关文件，保持最小差异。"

