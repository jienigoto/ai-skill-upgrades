name: daily-ai-skill-upgrade-2026-07-01
description: "将近期 AI 工具/AI 代理底层更新转译为可复用、可回退的 Codex SKILL 执行模板"
version: 1

---

## 触发条件

- 执行自动化 `ai-skill-github` 当日任务。
- 目标对象在近 14 天有 release/changelog 明确更新；有可复用的执行逻辑。
- 允许在无关项目约束下新建 `Project_XX_ai-skill-upgrade-YYYYMMDD`。

## 输入

- `workspace_root`: 工作区路径。
- `target_date`: `YYYY-MM-DD`。
- 候选列表（可由 web/repo 搜索输入）。
- 工具状态：`web/browser/gh/git` 可用性。

## 输出

- 顶层文件：`README.md`、`brief.md`、`script.md`、`asset-manifest.md`、`render-notes.md`
- `output/SKILL.md`（含三段式升级模板）
- `output/github-release-notes.md`（发布状态）

## 工具/能力边界

- 允许：`web` 浏览与搜索、GitHub Releases/Release Notes、`gh` API、`git`。
- 禁止：读取/写入不在当日项目目录以外的历史无关文件；不存储 secrets/凭据。
- 无网络或权限不足时：只留本地产物与阻塞说明，不误报成功。

## 完整执行流程

1. 检查工作区治理文件是否存在。
2. 检查当前是否已有当天主题项目目录；若无则创建 `Project_12_ai-skill-upgrade-YYYYMMDD`（本次为 `20260701`）。
3. 对候选对象抓取以下信息：
   - 用途/核心功能
   - 工作原理
   - 边界条件
   - 发布时间、提交量或 stars（用于活跃性判断）
   - 是否有清晰可复用落地路径
4. 进行候选筛选评分：
   - 稳定性增益
   - 可复用性
   - 降级可执行性
   - 与 Codex/GPT/Claude 工具链兼容程度
5. 产出 Top3 升级点，按每点输出：
   - 解决问题
   - 比参考更强的点
   - 稳定性加固（超时、重试、校验、日志）
   - 失败降级路径
6. 写入所有产物文件，包含证据链与人工执行提示。
7. 提交并尝试发布；发布成功写远端链接，失败写阻塞和下一步命令。

## 3 个升级点

### U1: browser-use 0.13.2 执行护栏层

- 解决问题
  - 浏览器任务在模型切换、provider 改名或发布参数变化下出现执行抖动。
- 比参考更强
  - 直接基于 release 变更（provider-prefixed models、`publish_to_pypi` 门控）封装统一前置检查，减少临时脚本分支。
- 如何提升稳定性
  - 预检：模型字符串、目标动作复杂度、超时预算。
  - 执行：记录关键动作与回放参数。
  - 校验：结构化验收（关键字段是否存在、结果一致性）与失败码映射。
- 失败降级
  - 首次失败：缩小操作范围并重试。
  - 连续失败：改为只读抓取（DOM 关键字段+链接摘要）并切换人工确认。

### U2: modelcontextprotocol/python-sdk v2.0.0a3 协议路由

- 解决问题
  - v1 与 v2 alpha 并行时的协议兼容与工具返回差异。
- 比参考更强
  - 利用 `mode='auto'` 自动发现路径并保留 legacy 回退，降低固定版本依赖。
- 如何提升稳定性
  - 统一在会话初始化阶段调用 `discover/adopt`。
  - 对 `InputRequiredResult`、`request_state` 和重试参数进行结构化分支处理。
  - 使用 streamable HTTP 新 headers 与 `protocol_version` 元数据进行可观测采样。
- 失败降级
  - auto 失败回退到 legacy；若 Alpha 特性不可用，降级到稳定 v1.x 约束下运行。

### U3: Claude Code v2.1.197 成本/上下文治理

- 解决问题
  - 长链路代码任务在上下文与成本之间缺少可控阈值。
- 比参考更强
  - 用 Sonnet 5 默认模型改造成本预算逻辑，内置 1M token 与促销定价窗口观察。
- 如何提升稳定性
  - 执行前制定预算、设定最大 token、限制单任务差异量。
  - 对高风险改动做分片执行与结果摘要校验。
- 失败降级
  - 终端/插件不可用时，切换到只读解释模式。
  - 成本超预算则改为更保守策略或暂停并输出人工确认。

## 验证方式

- 文件完整性：6 类必需产物存在。
- 证据完整性：每个候选记录有发布日期、链接、变更摘要。
- 执行完整性：Top3 与 brief/sKILL 一致，发布记录可回溯。

## 失败处理

- 无网络：停止发布步骤，写明失败时间与重试窗口。
- 无凭据/无权限：仅落地本地、禁止误报。
- 解析失败：记录失败对象和可替代对象清单。
- 工具不可用：保留“人工执行路径”和最小候选摘要。

## 验证清单（每次运行）

- [ ] Top3 升级点已明确写明“问题/增强/降级”
- [ ] `render-notes.md` 记录发布结果/阻塞原因
- [ ] `output/github-release-notes.md` 含 commit 或 blocker
