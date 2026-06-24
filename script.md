# 执行脚本（2026-06-24）

1. 读取 AGENTS 与 00/02/03 文件
2. 识别同主题项目：若当天无则创建 `Project_08_ai-skill-upgrade-20260624`
3. 采集候选数据（web + GitHub REST）
4. 记录用途、边界、来源、热度
5. 选取 3 个升级点
6. 生成 README/brief/script/asset-manifest/render-notes/output 文件
7. 生成 SKILL（含降级）与发布说明
8. 尝试上传 `Jienigoto/ai-skill-upgrades`，记录失败原因为主结果
