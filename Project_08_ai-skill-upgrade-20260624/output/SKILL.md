---
name: daily-ai-skill-upgrade-2026-06-24
description: "daily AI skill upgrade workflow with browser-use, openai/codex and pydantic-ai"
version: 1
trigger_conditions:
  - 每日 AI Skill 升级流程
  - 需要从近期热门 AI 工具/应用抽取可复用 Codex 能力
  - 需要形成可发布的工作流产物
inputs:
  workspace_root:
    type: path
    required: true
  target_date:
    type: string
    required: true
  candidate_repo_urls:
    type: array
    required: true
    description: 待评估候选对象
outputs:
  - output/SKILL.md
  - output/github-release-notes.md
  - brief.md
  - asset-manifest.md
  - render-notes.md
steps:
  - 读取治理文件，确认新建项目而非修改旧项目
  - 通过 web、浏览器、GitHub API 获取候选指标（stars/push/release）
  - 记录来源、原理、边界与可复用价值
  - 选择三大升级点并形成落地方案
  - 生成 SKILL（触发、输入输出、流程、边界、验证、失败与降级）
  - 进行本地产物校验并尝试上传
required_tools:
  - web
  - 浏览器
  - GitHub REST API
  - gh CLI（可选）
scope:
  in:
    - 产物组织
    - 候选筛选
    - SKILL 文档生成
    - 上传状态记录
  out:
    - 不修改既有非目标项目
    - 不写入 secrets
    - 不做未经确认的远端破坏性操作
upgrade_points:
  - id: U1
    title: browser-use 任务执行护栏
    source: https://github.com/browser-use/browser-use
    problem: 浏览器动作在模型切换/发布环境变化时易出现无法复现失败。
    why_stronger: 0.13.2 的 provider-prefixed model 与 release env 改动说明其朝稳定执行方向收敛。
    stability: 设置模型白名单、动作超时、页面快照和退避重试。
    fallback: 失败时切换为只读抽取 + 人工确认清单。
  - id: U2
    title: codex usage/plugins 治理
    source: https://github.com/openai/codex
    problem: usage 额度不可见导致重复执行与失控重试。
    why_stronger: /usage 与 /plugins 的更新可支持执行前资源检查。
    stability: 引入 usage 前置检查、插件来源分层、重试上限。
    fallback: 仅输出任务计划，暂停写操作。
  - id: U3
    title: pydantic-ai V2 结构化能力
    source: https://github.com/pydantic/pydantic-ai
    problem: 纯提示驱动流程缺少统一契约导致边界漂移。
    why_stronger: V2 stable 与 capabilities-first 提供更清晰的工具契约。
    stability: 统一 schema 验证、错误码映射与日志链。
    fallback: schema 失败时降级到人工确认模式。
validation:
  - 确认 3 个候选对象指标齐备
  - 确认 output/SKILL.md 包含完整降级策略
  - 确认发布记录写入 github-release-notes.md
failure_handling:
  no_network:
    detect: 网络或 API 不可达
    action: 停止上传，记录阻塞原因，保持本地产物
  no_credentials:
    detect: gh 未登录或 token 失效
    action: 仅本地保存并提示人工登录
  tool_unavailable:
    detect: gh CLI 不可用
    action: 以 REST API 可达性替代；仍不可达则标记 blocked
  malformed_output:
    detect: 文件缺字段或格式错误
    action: 最小可复用字段补齐后重写
---
