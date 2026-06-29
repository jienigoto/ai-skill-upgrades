# 执行脚本（2026-06-29）

1. 记录环境状态与治理文件存在性。
   - 尝试读取 `AGENTS.md`、`00_全局控制台.md`、`02_工作区架构与命名规则_下一个Codex提示词.md`、`03_新项目创建SOP.md`。
   - 本次实际为“未发现”，在 `render-notes.md` 写入阻塞原因。
2. 在工作区创建 `Project_11_ai-skill-upgrade-20260629`。
3. 搜索并抓取候选对象：
   - web/repo：browser-use release、modelcontextprotocol release、GitHub Copilot CLI changelog。
4. 记录候选对象用途、边界、来源、热度、是否可转译为 Codex SKILL。
5. 按规则筛选 3 个升级点（活跃性、稳定性增益、失败可降级）。
6. 产出固定文件：
   - README.md
   - brief.md
   - script.md
   - asset-manifest.md
   - render-notes.md
   - output/SKILL.md
   - output/github-release-notes.md
7. 运行发布：
   - `git add Project_11_ai-skill-upgrade-20260629`
   - `git commit -m "feat: add ai skill upgrade 2026-06-29"`
   - `git push`
8. 记录成功/阻塞信息到 `render-notes.md` 与 `output/github-release-notes.md`。

> 任何一步遇到 `无凭据/网络/gh API` 异常时只落本地，不执行误报。
