# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

「人生重开模拟器」—— 暗黑奇幻风人生模拟微信小游戏，纯 HTML 单文件（`index.html`），内联 CSS + JS，零依赖，localStorage 本地存储。部署到 Cloudflare Pages。

## 常用命令

```bash
# 本地预览
open index.html

# 语法检查（JS 内联在 HTML 中，需先提取）
node -e "const h=require('fs').readFileSync('index.html','utf8');new Function(h.match(/<script>([\s\S]*?)<\/script>/)[1])"
```

无构建工具、无测试框架、无包管理器。

## 架构

### 整体模式：状态机 + 数据驱动

- **开局流程**：线性状态机，`state.screen` 驱动屏幕切换（title → difficulty → rhythm → race → identity → vision → talentDraw → allocPoints → lifeEvent）
- **人生模拟**：数据驱动事件引擎，每阶段从事件池中按条件筛选匹配的事件

### 核心模块（均在 `<script>` 内，按顺序排列）

| 区域 | 行号（约） | 职责 |
|------|-----------|------|
| Game State | `const state` | 全局状态对象，包含 screen、attrs、talents、storyFlags、eventLog 等 |
| Game Data | `RHYTHMS / DIFFICULTIES / RACES / IDENTITIES / VISIONS / TALENTS` | 静态游戏数据 |
| Events | `const EVENTS = [...]` | 68 个事件，含阶段、属性门槛、难度、愿景、叙事标记等筛选条件 |
| Storage | `saveProgress / loadProgress / saveEnding / loadHistory` | localStorage 存取 |
| Helpers | `renderScreenWrapper / formatAttrChanges / drawTalents / shuffle / getTalentTypeLabel` | 通用工具函数 |
| Rendering | `render() → switch(state.screen)` | 屏幕路由，每屏一个 `renderXxx(app)` 函数 |
| Event Engine | `filterEvents / renderLifeEvent / chooseEventOption / nextStage / checkVisionComplete` | 事件筛选、瀑布流渲染、选择处理、阶段推进 |
| Share | `shareGame / showOverlay / hideShareCard` | Canvas 生成分享卡片 + 全屏弹窗 |

### 关键设计

- **瀑布流**：`state.eventLog[]` 记录每个已完成事件（标题/叙事/选择/后果），`renderLifeEvent` 先渲染历史流再渲染当前事件，自动滚到底部
- **叙事标记**：选项可设 `setFlag`，事件可设 `requireFlag`/`excludeFlag`，`filterEvents` 据此筛选，形成跨阶段的事件链（权重 ×4）
- **天赋关联**：`getTalentRelevance(event)` 检查玩家天赋与事件类型的匹配，在事件上方显示个性化提示
- **事件间节奏**：选完后 `state.loadingNext = true`，渲染"命运推演中…"加载指示器，1.5 秒后显示下一事件
- **纯叙事事件**：`narrativeOnly: true` 的事件只展示故事，无选项，单"继续"按钮
- **事件计数**：每阶段 1-2 个事件（`filterEvents` 中 `1 + Math.floor(Math.random() * 2)`）

### 事件数据结构

```
{
  id, stages[],           // 所属阶段（数组，多阶段共享）
  minAttr: {},            // 属性门槛（可选）
  vision: '',             // 愿景专属（可选）
  difficulty: '',         // 难度过滤（可选，逗号分隔）
  requireFlag: '',        // 叙事标记门槛（可选）
  excludeFlag: '',        // 互斥标记（可选）
  narrativeOnly: true,    // 纯叙事事件（可选）
  weight, title, narrative,
  options: [{ text, require, effects, narrative, setFlag, death, deathNarrative }]
}
```

### 属性系统

- **六维**：力量/智慧/魅力/敏捷/体质/运气（范围 1-200，每点加点 +2）
- **社会三维**：财富/声望/道德
- 事件选项可设属性门槛 `require`，不达标显示 🔒 灰态
- 全锁兜底：所有选项不可选时出现"硬着头皮撑过去"（体质-5 运气-3）
