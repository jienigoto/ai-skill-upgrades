# 执行脚本（2026-07-04）

## 1. 前置校验

1. 执行 `gh auth status` 与 `git remote -v`。
2. 检查当天同主题项目是否已存在：`Project_15_ai-skill-upgrade-20260704`，若存在则跳过创建项目。
3. 初始化产物目录：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`、`output/`、`renders/`。
4. 尝试读取治理文件：
   - `AGENTS.md`
   - `00_全局控制台.md`
   - `02_工作区架构与命名规则_下一个Codex提示词.md`
   - `03_新项目创建SOP.md`

## 2. 数据与候选抓取

1. 使用 `gh repo view` + `gh release view`：
   - `browser-use/browser-use`
   - `openai/codex`
   - `modelcontextprotocol/python-sdk`
   - `anthropics/claude-code`
   - `pydantic/pydantic-ai`
2. 记录每个候选：stars、pushedAt、latest release tag、release note body 中的关键变更。
3. 采用 1 段评估矩阵写入 `brief.md`：用途、核心功能、工作原理、边界、来源链接、热度、是否入选原因。

## 3. 选型与 SKILL 落地

1. 生成 3 个升级点（默认：browser-use、openai/codex、MCP v2 beta）。
2. 每个点需包含：问题、相比参考对象的增强、稳定性、降级方案。
3. 输出 `output/SKILL.md`，至少包含：
   - frontmatter（name/description）
   - 触发条件、输入输出、完整执行流程
   - 工具边界、验证方法、失败处理（无凭据/网络/工具不可用降级）

## 4. 产物

1. 补齐以下文件：
   - `brief.md`
   - `script.md`
   - `asset-manifest.md`
   - `render-notes.md`
   - `output/SKILL.md`
   - `output/github-release-notes.md`
2. 生成 `output/README.md`、`renders/README.md` 说明发布目录使用方式。

## 5. 发布流程

1. `git add Project_15_ai-skill-upgrade-20260704`
2. `git commit -m "feat: add ai skill upgrade 2026-07-04"`
3. `git push origin master`
4. 结果失败需写入 `render-notes.md` 与 `output/github-release-notes.md` 的阻塞原因，不写成功语句。

