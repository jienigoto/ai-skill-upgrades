# 执行脚本（2026-07-03）

## 预检

1. 使用 `gh auth status` 与 `git remote -v` 校验凭据与仓库。
2. 识别当日是否已有同主题项目；若无，创建 `Project_14_ai-skill-upgrade-20260703`。
3. 记录缺失治理文件：本次无法读取 `AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`。

## 研究与筛选

1. 用 web 搜索 + GitHub REST / gh api 收集候选：
   - `browser-use/browser-use`：repo、stars、pushed_at、release 最新版本与变更摘要
   - `openai/codex`：repo、stars、pushed_at、release 最新版本与变更摘要
   - `modelcontextprotocol/python-sdk`：repo、stars、pushed_at、release 最新版本与变更摘要
2. 为每个候选填写用途、核心功能、工作原理、边界、来源与热度证据。
3. 选出 3 个“今天优先价值”：
   - 变更显著且可复用
   - 可落地为明确定义输入/输出的 SKILL
   - 有明确失败降级策略

## 文档与 SKILL 产出

1. 创建并写入必需文件：
   - `README.md`
   - `brief.md`
   - `script.md`
   - `asset-manifest.md`
   - `render-notes.md`
2. 写入 `output/` 下：
   - `SKILL.md`
   - `github-release-notes.md`
   - `README.md`（输出说明）
3. 创建 `renders/README.md` 作为发布占位索引。

## 验证与发布

1. 校验以下文件存在。
2. 提交：
   - 目标 commit：`feat: add ai skill upgrade 2026-07-03`
3. 尝试 `git push origin master` 到 `Jienigoto/ai-skill-upgrades`。
4. 若网络/鉴权/工具异常：
   - 不允许写“发布成功”
   - 在 `render-notes.md` 与 `output/github-release-notes.md` 写明阻塞原因与人工重试命令
