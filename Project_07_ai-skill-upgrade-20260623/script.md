# 今日执行脚本（2026-06-23）

## 0. 预检
- 读取并遵守：`AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`
- 检查当日是否有同主题项目：不存在 `20260623` 同主题目录，创建 `Project_07_ai-skill-upgrade-20260623`

## 1. 资料采集
- Web 搜索：OpenAI、GitHub、AI Agent 工具与插件更新路径
- GitHub REST API（公开端点）：`openai/openai-agents-python`、`browser-use/browser-use`、`openai/codex`
- 对 Release 文本与仓库元数据做时间戳、stars、更新频率采样

## 2. 筛选与打分
- 维度：近 7 日活跃性、可复用性、失败边界可控性、降级可执行性、证据完整性
- 形成候选池后保留 3 个高价值对象

## 3. 文档产出
- 生成：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`
- 生成：`output/SKILL.md`（触发条件、输入输出、完整流程、边界、验证方式、失败策略、降级策略）
- 生成：`output/github-release-notes.md`

## 4. 发布尝试
- 检查 `gh auth status`
- 检查 `git remote -v` 与目标仓库配置
- `git add` / `git commit -m "feat: add ai skill upgrade 2026-06-23"`
- `git push`（若认证通过）
