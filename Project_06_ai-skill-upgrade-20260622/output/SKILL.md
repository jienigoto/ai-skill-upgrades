---
name: daily-ai-skill-upgrade-workflow
version: 1.0.0
description: "将高价值 AI 应用或工作流转化为可复用 Codex 技能：包含候选筛选、能力边界定义、失败降级和验证闭环"
trigger:
  - "用户要求进行每日 AI 技术/技能升级"
  - "需要选择并输出可复用的 Codex SKILL"
  - "要求做 GitHub 研究、验证、发布的日常流程"
inputs:
  - name: target_date
    description: "yyyy-mm-dd，用于项目命名与归档"
    required: false
  - name: project_root
    description: "AI 博主视频工作区绝对路径"
    required: true
  - name: max_candidates
    description: "候选对象扫描数量上限"
    required: false
outputs:
  - path: README.md
    description: "项目总览与结论摘要"
  - path: brief.md
    description: "研究方向与选题依据"
  - path: script.md
    description: "执行流程与操作记录"
  - path: asset-manifest.md
    description: "数据源与产物清单"
  - path: render-notes.md
    description: "验证记录与发布阻塞说明"
  - path: output/SKILL.md
    description: "可复用的技能定义"
  - path: output/github-release-notes.md
    description: "发布说明与发布结果"
steps:
  - name: "环境预检"
    run:
      - "读取工作区 AGENTS.md 与三份控制文件"
      - "确认非目标项目文件不被修改"
      - "检查当天是否已有同主题项目"
  - name: "创建项目"
    run:
      - "新建 Project_XX_ai-skill-upgrade-YYYYMMDD"
      - "创建 README.md/brief.md/script.md/asset-manifest.md/render-notes.md/output/renders"
  - name: "候选收集"
    tools:
      - chrome/browser
      - web 搜索
      - GitHub API/gh/REST
    run:
      - "按‘近期活跃+可复用+可解释机制+落地能力’抓取 6~10 个候选"
      - "记录每个候选的用途、核心功能、工作原理、边界、链接、热度证据"
      - "标记可落库为 skill 的优先级"
  - name: "候选评分与筛选"
    run:
      - "对候选执行可复用价值评分(实用性/稳定性/复用难度/失败可控性)"
      - "强制输出 3 个升级点"
  - name: "SKILL 生成"
    run:
      - "编写 output/SKILL.md 包含 Frontmatter 与边界"
      - "补齐 fallback/错误处理与验证项"
  - name: "发布准备"
    run:
      - "编写 github-release-notes.md"
      - "git add/commit，提交信息 feat: add ai skill upgrade YYYY-MM-DD"
      - "尝试 gh auth/远端提交"
  - name: "复盘"
    run:
      - "render-notes 记录阻塞原因与人工补齐项"
      - "若有可复用阻塞经验，更新全局复盘日志"
scope:
  include:
    - "web 搜索与仓库元数据抓取"
    - "候选比较与可复用性判断"
    - "技能文档结构化输出"
  exclude:
    - "未授权访问私有仓库内容"
    - "生成敏感密钥/凭证"
    - "对项目外文件进行无关改动"
verification:
  checks:
    - "项目目录含 README.md brief.md script.md asset-manifest.md render-notes.md output/ renders/"
    - "候选说明覆盖用途/核心功能/工作原理/边界/来源/热度证据/适配理由"
    - "SKILL 包含 name/version/description/frontmatter 与触发条件、流程、边界、fallback"
    - "git commit 信息符合模板"
    - "发布结果可重放且阻塞信息可追踪"
failure_modes:
  no_network:
    symptom: "搜索与 API 调用超时/无响应"
    fallback: "使用已有候选清单与离线知识优先级；标记为低置信度并补充下一轮验证需求"
  no_gh:
    symptom: "gh 未安装或未登录"
    fallback: "保留本地产物与阻塞日志，说明恢复步骤：gh auth login 后重试"
  repo_missing:
    symptom: "仓库不存在或无权限"
    fallback: "记录仓库链接与申请权限；暂存为 local-only，禁止声称发布成功"
  rate_limit_or_api_failure:
    symptom: "REST/gh 接口失败(401/403/Network)"
    fallback: "保留已采集信息并增加替代来源验证点（发布页/发行标签）"
  tool_unavailable:
    symptom: "Chrome/gh/浏览器不可用"
    fallback: "采用 web fallback + GitHub REST，或降低自动化为纯离线文档更新"
boundaries:
  network_scope: "只访问公开信息源；禁止抓取私有 repo 内容"
  safety: "不执行高风险系统命令、不改写他人项目、不存储 secret"
  output_format: "Markdown + JSON 可选摘要；文件名与项目路径固定"
  quality_bar:
    reliability: "每个候选需可复现证据（链接/时间/版本/更新说明）"
    upgrade_point: "每个 SKILL 功能点需给出降级路径"
---

# daily-ai-skill-upgrade-workflow

## 适用场景

面向“每日 AI 应用与 SKILL 升级”自动化，自动完成候选调研、价值筛选、3 点升级产出与发布预检。

## 输入

- `target_date`：运行日期，默认为当天日期（如 `2026-06-22`）。
- `project_root`：AI 博主视频全局工作区绝对路径。

## 输出

1. 项目文件：README、brief、script、asset-manifest、render-notes。
2. `output/SKILL.md`：可复用技能文档。
3. `output/github-release-notes.md`：发布说明。
4. `git` 提交：`feat: add ai skill upgrade YYYY-MM-DD`。

## 执行规则（固定）

1. 先读：`AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`。
2. 仅新增项目目录，默认 `Project_XX_ai-skill-upgrade-YYYYMMDD`，除非当日同主题已存在。
3. 优先通过公开源码仓库发布页、releases、官方文档和可证实更新日志筛选候选。
4. 记录每个候选：用途、核心功能、工作原理、边界、来源、活跃证据、是否转为 Skill。
5. 输出 3 个升级点，每点包含：
   - 解决问题
   - 比对参考对象增强点
   - 稳定性提升方法
   - 失败降级策略
6. 不可伪造上传成功：发布失败必须写入阻塞原因与重试计划。

## 升级点设计模板

- 问题定义 → 对标对象 → 当前方案改进 → 验证指标 → 回滚/降级。

## 验证点

- 文件齐全度：缺一不可。
- 3 个升级点是否可执行。
- 是否形成 `asset-manifest.md` 的来源与产物映射。
- git 提交是否成功。
- GitHub 发布阻塞信息是否可追溯。

## 示例调用

```bash
# 伪调用（由 Codex 执行）
.
# 1. 初始化项目目录
# 2. 收集候选并打分
# 3. 生成并提交产物
# 4. 尝试发布并记录阻塞原因
```
