---
name: daily-ai-skill-upgrade-codex-agent-runtime
description: "每日 AI Skill 升级流程：围绕 Codex 与 agent runtime、浏览器自动化做高活跃对象筛选与可复用升级"
version: 1
trigger_conditions:
  - "用户触发每日 AI Skill 升级流程"
  - "有新的 Codex、Agentic、浏览器自动化仓库 release 或核心功能更新"
  - "需要产出可复用的稳定性与降级策略"
inputs:
  workspace_root:
    type: path
    required: true
    description: "AI 博主视频工作区路径"
  target_topic:
    type: string
    required: true
    description: "daily-ai-skill-upgrade"
outputs:
  - README.md
  - brief.md
  - script.md
  - asset-manifest.md
  - render-notes.md
  - output/SKILL.md
  - output/github-release-notes.md
steps:
  - name: 读取治理文件
    actions:
      - "读取 AGENTS.md"
      - "读取 00_全局控制台.md"
      - "读取 02_工作区架构与命名规则_下一个Codex提示词.md"
      - "读取 03_新项目创建SOP.md"
  - name: 项目检查与创建
    actions:
      - "若当天同主题项目不存在，创建 Project_07_ai-skill-upgrade-YYYYMMDD"
      - "创建 README.md、brief.md、script.md、asset-manifest.md、render-notes.md、output/SKILL.md、output/github-release-notes.md"
  - name: 候选调研
    actions:
      - "调用 web 搜索验证当前热门 AI 应用、AI 工具、Codex/Claude/GPT 相关动态"
      - "调用 GitHub REST API 采集仓库 stars、pushed_at、updated_at、latest release、release body"
      - "对每个候选记录用途、工作原理、边界、来源、热度和适配性"
  - name: 候选筛选
    actions:
      - "按近 7 日更新频率、Stars、release 可信度、可执行边界、降级可行性评分"
      - "选出 3 个升级点"
  - name: 产物生成
    actions:
      - "写入 brief.md 和 asset-manifest.md"
      - "在 output/SKILL.md 中定义升级点、边界、验证与失败处理"
      - "更新 github release notes 与 render-notes"
  - name: 发布流程
    actions:
      - "gh auth status 或 API 替代路径"
      - "检查 git remote 与仓库可达性"
      - "提交并推送：feat: add ai skill upgrade YYYY-MM-DD"
  - name: 失败与阻塞处理
    actions:
      - "无凭据/无网络时保留本地产物，不输出发布成功"
      - "记录阻塞原因并给出下一步"
tools:
  required:
    - web 搜索能力（官方网页/公告）
    - GitHub REST API（公开端点）
    - gh CLI（若可用）
    - git
  optional:
    - 浏览器自动化能力
    - OpenAI 官方文档链接
boundaries:
  - "不得读取或提交 secrets、tokens、私域凭据"
  - "不得修改未授权的既有项目目录"
  - "不得声称发布成功却未推送成功"
validation:
  required:
    - "目录完整性（README、brief、script、asset-manifest、render-notes、output/SKILL、github-release-notes）"
    - "3 个升级点包含‘问题-增强-降级’要素"
    - "候选证据含时间戳与来源链接"
    - "发布尝试可复现（auth/remote/commit/push）"
upgrade_points:
  - id: P1
    title: "Agents SDK 工具执行前校验与契约约束"
    source: "openai/openai-agents-python"
    problem: "工具参数漂移导致模型输出不可复用、任务中断、失败难以归因"
    stronger_than_reference: "从“可运行”升级为“可审核”：使用 pre-approval 与 strict JSON-compatible contract 在执行前阻断非法参数"
    stability_improvement: "引入统一 schema 校验、输入清洗与失败 reason 归类；记录 tool call 失败上下文"
    fallback: "降级为单步执行 + 人工确认模式；或降级为 mock tool 输出模板，保证主线任务上下文不丢失"
  - id: P2
    title: "browser-use 浏览器执行前置健康校验"
    source: "browser-use/browser-use"
    problem: "网页结构与模型返回格式变化引起多步任务抖动和偶发失败"
    stronger_than_reference: "将 open-loop 自动执行转为“动作前检查 + 发布门禁 + provider 前缀兼容”"
    stability_improvement: "每步前检查页面可交互性、上下文可用性与 provider 映射；失败自动切到只读模式和重试"
    fallback: "不可恢复失败时输出可复核摘要并降级为纯抓取或人工处理任务"
  - id: P3
    title: "Codex 插件与 usage 生命周期管理"
    source: "openai/codex"
    problem: "插件发现和 usage 状态更新频繁，导致本地流程在环境漂移时频发报错"
    stronger_than_reference: "从被动依赖官方更新升级为发布后自检：统一验证插件分类与 usage 呈现"
    stability_improvement: "启动时执行健康检查：插件列表可读性、分类完整性、usage 可恢复提示"
    fallback: "远端插件端点不可用时，回退到静态插件白名单并触发人工确认"
publish:
  commit_message_pattern: "feat: add ai skill upgrade YYYY-MM-DD"
  target_repo: "Jienigoto/ai-skill-upgrades"
  failure_handling:
    - "无网络：仅本地产出并记录阻塞"
    - "gh 不可用/未认证：写入 render-notes 的阻塞原因并保留本地产物"
    - "远端仓库不存在：先尝试创建，再次推送；失败记录原因"
