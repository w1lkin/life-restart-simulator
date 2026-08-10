# CHANGELOG

## [2.0.0] - 2026-08-10

### Docs
- 新建 `AGENTS.md`（项目架构与 AI 协作指南），合并原有的 `.claude/CLAUDE.md`
- 更新 `README.md`，统一格式

---

## [1.0.0] - 2026-07

### Added
- 人生重开模拟器初始版本：暗黑奇幻风文字养成游戏
- `index.html`（内联 CSS/JS）+ `share-card.html`，零依赖
- 状态机驱动的开局流程 + 事件引擎（68 个事件）
- 六维属性系统 + 社会三维
- 叙事标记链、天赋关联事件、瀑布流事件日志
- 移动端适配 + 微信 webview 优化

### Changed
- 战绩存储从 localStorage 迁移到 GamePlatform 云端
- 接入 GamePlatform 登录门
- 移除顶部用户栏与天梯榜浮层
- 底部 tab "首页"→"主页"，"门户"→"游戏"
- 域名改回 Cloudflare Pages 默认域名 `life-restart-simulator-a7b.pages.dev`
- 部署至 Cloudflare Pages
