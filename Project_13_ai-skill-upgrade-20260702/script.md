# 执行脚本（2026-07-02）

1. 读取并记录治理文件状态：`AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`。本次工作区不存在上述文件，需在 `render-notes.md` 记为缺失。
2. 检查是否存在 `Project_XX_ai-skill-upgrade-20260702`，不存在则创建。
3. 采集候选对象：优先 `web + GitHub REST/gh + release` 三路线；抓取 stars、pushed_at、release tag、变更摘要。
4. 为每个候选记录用途、核心功能、工作原理、边界、来源链接、热度与活跃度。
5. 按“可复用性、执行稳定性、可降级性、与现有 Codex 工作链兼容度”打分，选出 3 个升级点。
6. 产出以下文件：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`、`output/SKILL.md`、`output/github-release-notes.md`。
7. 运行前后校验：文件齐全、Top3 一致、每个升级点含“问题/增强/稳定性/降级”项。
8. 提交：`git add` + `git commit -m "feat: add ai skill upgrade 2026-07-02"`。
9. 发布：向 `Jienigoto/ai-skill-upgrades` 推送；若失败，必须在 `render-notes.md` 明确阻塞原因与下一步命令。