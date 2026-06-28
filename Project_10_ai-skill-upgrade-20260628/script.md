# 执行脚本（2026-06-28）

1. 先检查工作区根目录治理文件：
   - `AGENTS.md`
   - `00_全局控制台.md`
   - `02_工作区架构与命名规则_下一个Codex提示词.md`
   - `03_新项目创建SOP.md`
   - 说明：本地仓库缺失上述文件，故记录为环境阻塞告警，并改用既有项目惯例执行。
2. 核验是否存在当日同主题目录；若无，创建 `Project_10_ai-skill-upgrade-20260628`。
3. 用 web + `gh api` 搜集候选证据：
   - releases（时间、版本、变更要点）
   - recent commits（稳定性和活跃性）
   - stars/updated_at（热度与持续维护）
4. 对候选做用途、工作原理、边界、热度、适配度评估。
5. 输出 Top3 升级点：网页任务闭环、MCP transport 稳定性、pydantic-ai 契约。
6. 生成以下文件：
   - README.md
   - brief.md
   - script.md
   - asset-manifest.md
   - render-notes.md
   - output/SKILL.md
   - output/github-release-notes.md
   - output/、renders/
7. 在本地提交并尝试推送到 `Jienigoto/ai-skill-upgrades`：
   - commit message：`feat: add ai skill upgrade 2026-06-28`
8. 发布失败则写明阻塞原因（认证、网络、权限、仓库权限）并停止远端写入。
