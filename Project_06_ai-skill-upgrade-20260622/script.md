# 今日执行脚本（2026-06-22）

## 0. 预检
- 读取并遵守：`AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`
- 检查已有项目：发现 `Project_06_ai-skill-upgrade-20260622` 已存在且同主题，复用当日文件夹

## 1. 证据采集（Web + GitHub REST）
- 获取仓库元数据：`openai/openai-agents-python`、`browser-use/browser-use`、`modelcontextprotocol/python-sdk`
- 获取 release metadata：`/releases/latest`
- 官方来源补充：OpenAI Agents SDK 官方页面、Claude Release Notes 官方页面

## 2. 筛选
- 按以下维度打分：近7天活跃性、文档可追溯性、可落地 Skill 可复用性、失败边界可控性
- 形成候选池并剔除不适配对象

## 3. 文档生成
- 生成/更新：`brief.md`、`asset-manifest.md`、`render-notes.md`、`output/SKILL.md`、`output/github-release-notes.md`

## 4. 发布尝试
- `gh auth status`（通过）
- `git remote -v`（确认 origin）
- `git add/commit`（`feat: add ai skill upgrade 2026-06-22`）
- `git push -u origin master`

## 5. 结束输出
- 在最终回复中给出研究方向、3 个升级点、publish 状态、人工确认清单
