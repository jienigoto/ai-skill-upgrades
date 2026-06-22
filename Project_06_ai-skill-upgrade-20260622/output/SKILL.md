---
name: daily-ai-skill-upgrade-workflow-v2
description: "每天自动筛选并升级高价值 AI 应用 / SKILL 工作流，输出可复用的 Codex 技能模板，并内置发布阻塞降级策略"
trigger:
  - 用户要求执行每日 AI Skill 升级并落库
  - 需要将近期热门 AI 应用转为可复用 Codex 技能
  - 研究结论需同步生成发布说明与复盘记录
version: 1
versioned_from: AI博主视频
inputs:
  required:
    - name: workspace_root
      type: string
      desc: 工作区绝对路径，例如 C:\Users\86152\Documents\AI博主视频
    - name: project_prefix
      type: string
      desc: 默认 ai-skill-upgrade
  optional:
    - name: target_date
      type: string
      desc: YYYY-MM-DD，默认当天
outputs:
  required:
    - README.md
    - brief.md
    - script.md
    - asset-manifest.md
    - render-notes.md
    - output/SKILL.md
    - output/github-release-notes.md
scope:
  include:
    - web 搜索与 GitHub 开源信息抓取
    - 候选对象评估（用途/原理/边界）
    - SKILL 文档自动构建
    - GitHub 发布预检与阻塞记录
  exclude:
    - 私有仓库与凭据抓取
    - 删除或修改非目标项目文件
steps:
  - phase: preflight
    actions:
      - 读取并遵守 AGENTS.md 与 00/02/03 控制文件
      - 若当日同主题项目存在则复用同日新建规则；否则创建 Project_XX_ai-skill-upgrade-YYYYMMDD
  - phase: discovery
    actions:
      - web 搜索 + GitHub release/主页 + 官方文档
      - 记录每个候选的用途、核心功能、工作原理、边界与来源链接
      - 保存热度证据（发布时间、最近更新、版本信息）
  - phase: selection
    actions:
      - 基于实用性、稳定性、可复用性、失败可控性进行打分
      - 自动选择 3 个可升级点写入 SKILL
  - phase: artifact
    actions:
      - 生成 required files 与项目结构
      - 产出 github-release-notes 与 render-notes（含阻塞证据）
  - phase: publish
    actions:
      - 尝试 gh auth / gh repo view / 推送
      - 失败则不冒充成功，输出明确阻塞原因与下一步

upgrade_points:
  - name: 浏览器自动化可靠性增强
    source_reference: browser-use
    problem: "浏览器动作链路在页面变化、元素延迟或权限变化下易抛错"
    what_is_better: "增加标准化动作编排 + 重试策略模板 + DOM 快照快照比对，输出统一失败上下文"
    reliability_plan:
      - "统一设置 action timeout 与重试上限"
      - "元素定位策略改为优先稳定选择器 + 容错降级链"
      - "执行前做最小状态快照并加入异常截图"
    fallback:
      - "切到静态抓取路径（read-only）并触发人工 review flag"
      - "标记该步骤为待人工确认并保留上下文日志"

  - name: Agent 记忆与编排稳定化
    source_reference: agno
    problem: "多 agent 流程在上下文漂移或工具失败时难以稳定闭环"
    what_is_better: "加入 memory TTL、schema 检验与执行预算控制，形成可恢复的 team/workflow 运行模式"
    reliability_plan:
      - "所有工具返回先做 schema 验证，失败时回退到简化路径"
      - "为 memory 设置保留策略，防止上下文污染"
      - "建立异常分类（超时、格式错、授权失败）对应降级动作"
    fallback:
      - "停用 memory，切到 stateless 单步执行"
      - "仅保留最近一次成功上下文并人工触发续跑"

  - name: 本地优先发布链路稳定化
    source_reference: open-webui
    problem: "依赖外部 API 与网络时，发布与验证链路易受外部抖动影响"
    what_is_better: "在 local-first 条件下保持任务队列、缓存和离线验证可运行，减少外部服务耦合"
    reliability_plan:
      - "引入可配置的离线模式和最小权限最小链路"
      - "对发布前置步骤做 dry-run 与清单化校验"
      - "将 API 失败转为阻塞原因文件化，不阻断本地产物保存"
    fallback:
      - "网络失效时自动跳过发布步骤，仅保留本地 artifacts 和阻塞记录"
      - "下次运行时重放发布步骤"

validation:
  checks:
    - 目录结构齐全
    - 候选材料包含用途/原理/边界/热度证据
    - 3 个升级点完整（问题/增强/稳定性/降级）
    - 本地提交成功
  failure_evidence:
    - 无凭据时记录精确报错文本
    - 无网络时回退到本地缓存与历史候选
    - 工具不可用时改写为待办清单并留痕

failure_handling:
  no_network:
    action: "记录候选来源为上一次缓存并标注置信度"
  gh_not_logged_in:
    action: "阻塞发布并写入 render-notes，要求人工先执行 gh auth login"
  missing_repo_or_permission:
    action: "确认仓库存在与权限；无法确认时不做任何虚假成功声明"
  tool_outage:
    action: "保持 output/ 项目文件完整，后续恢复后重跑 publish 阶段"

notes:
  - do_not_store: secret、token、凭据或一次性隐私数据
  - this_skill_is_local_first: true
---

# daily-ai-skill-upgrade-workflow-v2

该 SKILL 直接产出三项可复用能力：
1. AI 工具候选评估模板
2. 可靠性强化的 Skill 设计模板
3. 发布阻塞可追溯流程
