# 执行脚本（2026-07-01）

1. 记录环境与治理文件状态。
   - 检查 `AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`。
   - 实际结果：本次工作区未发现上述治理文件。
2. 新建项目目录：`Project_12_ai-skill-upgrade-20260701/`（含 `output/`、`renders/`）。
3. 按 `web -> GitHub releases -> REST API/gh` 路线采集候选对象信息与发布时间：
   - browser-use/releases/tag/0.13.2
   - modelcontextprotocol/python-sdk/releases
   - anthropics/claude-code/releases/tag/v2.1.197
   - openai/codex/releases/tag/rust-v0.142.4
4. 对每个候选记录：用途、功能、工作原理、边界、来源、热度/活跃（发布日期、stars、提交量）、转 SKILL 适配性。
5. 评分后筛选四类：
   - 可复用性、可落地性、失败可降级能力、发布稳定性；自动产出 Top3。
6. 产出必需文件：
   - README.md / brief.md / script.md / asset-manifest.md / render-notes.md
   - output/SKILL.md / output/github-release-notes.md
7. 本地验证：文件齐全性与 Top3 一致性。
8. 发布：
   - 提交信息：`feat: add ai skill upgrade 2026-07-01`
   - 尝试 `git push` 到 `origin=Jienigoto/ai-skill-upgrades`。
9. 发布失败时不误报：写明阻塞原因与下一步人工指令。
