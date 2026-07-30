# 人生重开模拟器 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建一个暗黑奇幻风的人生重开模拟器单文件 HTML 游戏，包含完整的开局配置、事件驱动的人生模拟和结局评分系统。

**Architecture:** 纯单文件 `index.html`，内联 CSS + JS。采用状态机驱动主流程 + 数据驱动事件引擎的混合架构。所有游戏内容（种族/身份/愿景/天赋/事件）以纯数据形式定义，状态机按阶段渲染不同屏幕。

**Tech Stack:** 纯 HTML + CSS + vanilla JavaScript，无框架无依赖，localStorage 本地存储。

---

## 文件结构

```
life-restart-simulator/
├── index.html          # 唯一文件，包含所有 HTML/CSS/JS
└── docs/
    └── superpowers/
        ├── specs/
        │   └── 2026-05-27-life-restart-simulator-design.md
        └── plans/
            └── 2026-05-27-life-restart-simulator-plan.md
```

---

### Task 1: HTML 骨架与暗黑奇幻风 CSS

**Files:**
- Create: `index.html`

- [ ] **Step 1: 创建 HTML 骨架和全局 CSS**

写入 `index.html`：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>人生重开模拟器</title>
<style>
/* === Reset & Base === */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html, body { height: 100%; }
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background: #0d0d0d;
  color: #ccc;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  min-height: 100vh;
  padding: 16px;
}
#app {
  width: 100%;
  max-width: 420px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* === Typography === */
h1, h2, h3 { font-family: "Times New Roman", "Songti SC", "SimSun", serif; }
h1 { font-size: 32px; color: #c9a84c; letter-spacing: 6px; text-align: center; }
h2 { font-size: 22px; color: #c9a84c; margin-bottom: 12px; }
h3 { font-size: 16px; color: #c9a84c; margin-bottom: 8px; }
.subtitle { font-size: 13px; color: #666; margin-bottom: 20px; text-align: center; }

/* === Cards === */
.card {
  background: #1a1a2e;
  border: 1px solid #2a2a2a;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
  cursor: pointer;
  transition: border-color 0.2s;
}
.card:hover, .card.selected { border-color: #c9a84c; }
.card.selected { box-shadow: 0 0 8px rgba(201,168,76,0.3); }
.card.disabled { opacity: 0.4; pointer-events: none; }

/* === Card Grid === */
.card-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
.card-grid.single-col { grid-template-columns: 1fr; }

/* === Buttons === */
.btn {
  display: block; width: 100%; padding: 14px;
  border: 1px solid #c9a84c; border-radius: 8px;
  background: transparent; color: #c9a84c;
  font-size: 16px; font-family: "Times New Roman", serif;
  cursor: pointer; text-align: center; margin-bottom: 10px;
  transition: background 0.2s, color 0.2s;
}
.btn:hover { background: #c9a84c; color: #0d0d0d; }
.btn.primary { background: #c9a84c; color: #0d0d0d; }
.btn.secondary { border-color: #555; color: #888; }
.btn.secondary:hover { background: #555; color: #fff; }
.btn.danger { border-color: #c41e3a; color: #c41e3a; }
.btn.danger:hover { background: #c41e3a; color: #fff; }
.btn:disabled { opacity: 0.3; pointer-events: none; }

/* === Attribute Row === */
.attr-row {
  display: flex; justify-content: space-between; align-items: center;
  padding: 8px 0; border-bottom: 1px solid #1a1a2e;
}
.attr-name { font-size: 14px; color: #ccc; }
.attr-value { font-size: 14px; color: #c9a84c; font-weight: bold; }
.attr-controls { display: flex; gap: 8px; align-items: center; }
.attr-btn {
  width: 32px; height: 32px; border: 1px solid #555; border-radius: 4px;
  background: transparent; color: #ccc; font-size: 18px; cursor: pointer;
  display: flex; align-items: center; justify-content: center;
}
.attr-btn:hover { border-color: #c9a84c; color: #c9a84c; }
.attr-btn:disabled { opacity: 0.2; pointer-events: none; }

/* === Tags === */
.tag {
  display: inline-block; padding: 2px 8px; border-radius: 4px;
  font-size: 11px; margin-right: 4px;
}
.tag-positive { background: #1a3a1a; color: #4caf50; border: 1px solid #2a5a2a; }
.tag-negative { background: #3a1a1a; color: #f44336; border: 1px solid #5a2a2a; }
.tag-gold { background: #2a2a1a; color: #c9a84c; border: 1px solid #4a4a2a; }
.tag-locked { background: #1a1a1a; color: #666; border: 1px solid #333; }

/* === Narrative Box === */
.narrative {
  background: #1a1a2e; border-left: 3px solid #c9a84c;
  padding: 16px; border-radius: 4px; margin-bottom: 16px;
  font-style: italic; font-size: 14px; line-height: 1.7;
}

/* === Progress Bar === */
.progress-bar {
  width: 100%; height: 4px; background: #1a1a2e; border-radius: 2px;
  margin-bottom: 16px;
}
.progress-fill { height: 100%; background: #c9a84c; border-radius: 2px; transition: width 0.3s; }

/* === Footer Info === */
.stage-info {
  display: flex; justify-content: space-between;
  font-size: 12px; color: #666; margin-bottom: 12px;
}

/* === Options List === */
.option-item {
  background: #1a1a2e; border: 1px solid #2a2a2a; border-radius: 6px;
  padding: 12px; margin-bottom: 8px; cursor: pointer;
  transition: border-color 0.2s;
}
.option-item:hover { border-color: #c9a84c; }
.option-item.locked { opacity: 0.5; cursor: not-allowed; border-color: #333; }
.option-text { font-size: 14px; margin-bottom: 4px; }
.option-effects { font-size: 12px; }
.effect-pos { color: #4caf50; }
.effect-neg { color: #f44336; }
.effect-neutral { color: #888; }

/* === Reroll Button === */
.reroll-info { text-align: center; font-size: 13px; color: #888; margin: 8px 0; }

/* === History List === */
.history-item {
  background: #1a1a2e; border: 1px solid #2a2a2a; border-radius: 8px;
  padding: 12px; margin-bottom: 8px;
}
.history-title { font-size: 16px; color: #c9a84c; }
.history-meta { font-size: 12px; color: #666; margin-top: 4px; }

/* === Scrollable content === */
.screen-content { flex: 1; overflow-y: auto; padding-bottom: 20px; }

/* === Score display === */
.score-badge {
  display: inline-block; padding: 4px 12px; border-radius: 4px;
  font-size: 13px; font-weight: bold;
}
.score-SS { background: #c9a84c; color: #0d0d0d; }
.score-S  { background: #d4a574; color: #0d0d0d; }
.score-A  { background: #4caf50; color: #fff; }
.score-B  { background: #2196f3; color: #fff; }
.score-C  { background: #ff9800; color: #fff; }
.score-D  { background: #f44336; color: #fff; }

/* === Ending Screen === */
.ending-header { text-align: center; margin-bottom: 24px; }
.ending-title { font-size: 28px; color: #c9a84c; letter-spacing: 4px; margin-bottom: 8px; }
.ending-grade { font-size: 64px; color: #c9a84c; margin: 16px 0; text-align: center; }
.ending-narrative {
  background: #1a1a2e; border-radius: 8px; padding: 16px;
  font-style: italic; line-height: 1.8; text-align: center; margin-bottom: 20px;
}
</style>
</head>
<body>
<div id="app"></div>

<script>
// === Game State ===
const state = {
  screen: 'title',          // 当前屏幕
  difficulty: '',           // 难度
  rhythm: '',               // 节奏: 'compact' | 'standard'
  race: '',                 // 种族 id
  identity: '',             // 身份 id
  vision: '',               // 愿景 id
  talents: [],              // 已选天赋 id 列表
  attrs: {                  // 当前属性
    力量: 10, 智慧: 10, 魅力: 10, 敏捷: 10, 体质: 10, 运气: 10,
    财富: 100, 声望: 0, 道德: 0
  },
  stageIndex: 0,            // 当前阶段序号
  drawnTalents: [],         // 当前抽到的天赋卡
  remainingRerolls: 0,      // 剩余重roll次数
  allocPoints: 0,           // 剩余可分配点数
  selectedTalents: [],      // 本轮选中的天赋
  currentEvents: [],        // 当前阶段事件
  completedEvents: [],      // 已完成事件 ID
};

// The rest of the game will be built in subsequent tasks
console.log('人生重开模拟器 initialized');
</script>
</body>
</html>
```

- [ ] **Step 2: 在浏览器中打开验证**

打开 `index.html`，验证页面无报错，body 背景为深黑色 `#0d0d0d`。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: add HTML skeleton with dark fantasy CSS theme"
```

---

### Task 2: 游戏数据定义

**Files:**
- Modify: `index.html`（在 `<script>` 标签内 state 对象之后追加）

- [ ] **Step 1: 定义种族/身份/愿景/天赋数据**

在 `index.html` 的 `<script>` 中，state 定义之后追加以下数据定义：

```javascript
// === Game Data ===

// 节奏配置
const RHYTHMS = {
  compact: {
    name: '精简型',
    desc: '5个阶段，快速过完一生',
    stages: ['幼年', '少年', '青年', '中年', '老年'],
  },
  standard: {
    name: '标准型',
    desc: '8个阶段，完整人生体验',
    stages: ['婴儿', '童年', '少年', '青年', '壮年', '中年', '中老年', '老年'],
  },
};

// 难度配置
const DIFFICULTIES = {
  hard: {
    name: '大逆风', icon: '🔴',
    desc: '步步惊心，活着就是胜利',
    attrMultiplier: 0.5, drawCount: 5, pickCount: 1,
    negativeBias: true, eventBias: 'negative', rerolls: 0, allocPoints: 5,
  },
  normal: {
    name: '普通', icon: '🟡',
    desc: '平凡之路，自己书写命运',
    attrMultiplier: 1.0, drawCount: 5, pickCount: 2,
    negativeBias: false, eventBias: 'neutral', rerolls: 1, allocPoints: 15,
  },
  easy: {
    name: '顺风', icon: '🟢',
    desc: '顺风顺水，前途一片光明',
    attrMultiplier: 1.2, drawCount: 6, pickCount: 3,
    negativeBias: false, eventBias: 'positive', rerolls: 2, allocPoints: 22,
  },
  heaven: {
    name: '龙傲天', icon: '👑',
    desc: '天命所归，世间无敌',
    attrMultiplier: 1.5, drawCount: 7, pickCount: 3,
    negativeBias: false, eventBias: 'lucky', rerolls: 3, allocPoints: 30,
  },
};

// 种族数据
const RACES = {
  human: {
    id: 'human', name: '人类', icon: '👤',
    desc: '大陆上最普遍的种族，适应性极强，在各领域均有建树。',
    bonuses: { 力量: 2, 智慧: 2, 魅力: 2, 敏捷: 2, 体质: 2, 运气: 2 },
  },
  elf: {
    id: 'elf', name: '精灵', icon: '🧝',
    desc: '古老的森林种族，寿命悠长，天生与魔法共鸣。',
    bonuses: { 智慧: 5, 魅力: 3, 体质: -2 },
  },
  dwarf: {
    id: 'dwarf', name: '矮人', icon: '🧔',
    desc: '山中之民，以锻造和烈酒闻名，身体如磐石般坚韧。',
    bonuses: { 力量: 5, 体质: 3, 魅力: -2 },
  },
  dragonborn: {
    id: 'dragonborn', name: '龙裔', icon: '🐉',
    desc: '远古龙族的后裔，血液中流淌着龙之力，稀有而强大。',
    bonuses: { 力量: 4, 体质: 3, 魅力: 3, 智慧: -3 },
  },
  darkelf: {
    id: 'darkelf', name: '暗精灵', icon: '🌑',
    desc: '地底世界的住民，暗影的宠儿，致命而优雅。',
    bonuses: { 敏捷: 5, 智慧: 3, 魅力: -2 },
  },
};

// 身份数据 — 每个种族独立定义
const IDENTITIES = {
  human: [
    { id: 'human_noble', name: '贵族后裔', desc: '出身显赫，自小锦衣玉食。', bonuses: { 魅力: 3, 声望: 20, 财富: 400 } },
    { id: 'human_crafter', name: '工匠子弟', desc: '父亲是城中知名铁匠。', bonuses: { 智慧: 3, 力量: 2, 财富: 150 } },
    { id: 'human_commoner', name: '平民出身', desc: '普通人家，一切靠自己。', bonuses: { 财富: 50 } },
  ],
  elf: [
    { id: 'elf_noble', name: '月之贵族', desc: '精灵王庭的远支血脉。', bonuses: { 魅力: 4, 声望: 30, 财富: 300 } },
    { id: 'elf_warden', name: '森林巡者', desc: '守护森林的巡林人家族。', bonuses: { 敏捷: 4, 智慧: 2, 财富: 100 } },
    { id: 'elf_exile', name: '流亡者', desc: '因故被放逐的精灵后裔。', bonuses: { 敏捷: 3, 道德: -10, 财富: 30 } },
  ],
  dwarf: [
    { id: 'dwarf_smith', name: '锻造世家', desc: '世代相传的铁匠家族。', bonuses: { 力量: 4, 财富: 250, 声望: 10 } },
    { id: 'dwarf_warrior', name: '战士氏族', desc: '山中之城的守卫者血统。', bonuses: { 力量: 3, 体质: 3, 声望: 15, 财富: 80 } },
    { id: 'dwarf_miner', name: '矿工子弟', desc: '在矿脉中长大的坚韧之人。', bonuses: { 体质: 4, 财富: 60 } },
  ],
  dragonborn: [
    { id: 'dragon_clan', name: '龙族嫡系', desc: '纯正的龙族血脉继承者。', bonuses: { 力量: 5, 声望: 25, 财富: 500 } },
    { id: 'dragon_wanderer', name: '流浪龙裔', desc: '远离族群的独行龙裔。', bonuses: { 体质: 3, 力量: 2, 财富: 30 } },
    { id: 'dragon_outcast', name: '混血弃儿', desc: '不被纯血龙裔承认的混血。', bonuses: { 魅力: 3, 道德: -5, 财富: 20 } },
  ],
  darkelf: [
    { id: 'darkelf_shadow', name: '影刃家族', desc: '地底暗杀者行会的成员。', bonuses: { 敏捷: 5, 声望: -5, 财富: 200 } },
    { id: 'darkelf_scholar', name: '地底学者', desc: '研究禁忌魔法的学者世家。', bonuses: { 智慧: 4, 财富: 120 } },
    { id: 'darkelf_slave', name: '获释奴隶', desc: '从地底角斗场逃出生天。', bonuses: { 体质: 4, 道德: 10, 财富: 10 } },
  ],
};

// 愿景数据
const VISIONS = [
  { id: 'hero', name: '传说勇者', icon: '⚔️', desc: '击败魔王，拯救世界。', condition: { 力量: 150, 声望: 500 } },
  { id: 'sage', name: '大贤者', icon: '🧙', desc: '智慧通神，触碰真理之门。', condition: { 智慧: 150 } },
  { id: 'wealthy', name: '富可敌国', icon: '💰', desc: '积累足以买下一个国家的财富。', condition: { 财富: 100000 } },
  { id: 'shadow', name: '暗影之主', icon: '🗡️', desc: '成为地底世界真正的主宰。', condition: { 敏捷: 130, 道德: -80 } },
  { id: 'saint', name: '圣者', icon: '✨', desc: '以善行感化世间。', condition: { 魅力: 120, 道德: 80, 声望: 600 } },
  { id: 'immortal', name: '不死传说', icon: '💀', desc: '超越死亡，名垂千古。', condition: { 体质: 160 } },
  { id: 'dragonSlayer', name: '屠龙者', icon: '🐲', desc: '以凡人之躯斩杀远古巨龙。', condition: { 力量: 140, 敏捷: 100 } },
  { id: 'adventurer', name: '传奇探险家', icon: '🗺️', desc: '踏遍世界每一个角落。', condition: { 敏捷: 120, 运气: 100 } },
  { id: 'king', name: '万王之王', icon: '👑', desc: '统一大陆，加冕为王。', condition: { 声望: 800, 魅力: 130 } },
  { id: 'peaceful', name: '平凡幸福', icon: '🏡', desc: '无需惊天动地，只求一生安稳。', condition: { 运气: 80, 道德: 40 } },
];

// 天赋卡数据
const TALENTS = [
  // 正面天赋
  { id: 't_str', name: '天生神力', type: 'positive', desc: '你的力量远超常人。', effects: { 力量: 15 } },
  { id: 't_int', name: '过目不忘', type: 'positive', desc: '任何知识过眼即记。', effects: { 智慧: 15 } },
  { id: 't_chr', name: '倾城之姿', type: 'positive', desc: '美貌令人无法移开目光。', effects: { 魅力: 15 } },
  { id: 't_agi', name: '矫健如风', type: 'positive', desc: '身手敏捷，来去如电。', effects: { 敏捷: 15 } },
  { id: 't_con', name: '钢筋铁骨', type: 'positive', desc: '身体坚固如城堡。', effects: { 体质: 15 } },
  { id: 't_lck', name: '天选之人', type: 'positive', desc: '命运之神眷顾于你。', effects: { 运气: 20 } },
  { id: 't_dragon', name: '龙族血统', type: 'positive', desc: '血脉中觉醒的龙之力。', effects: { 力量: 8, 体质: 5, 魅力: 3 } },
  { id: 't_business', name: '商业奇才', type: 'positive', desc: '赚钱对你来说轻而易举。', effects: { 财富: 500, 智慧: 3 } },
  { id: 't_nobleSoul', name: '高贵灵魂', type: 'positive', desc: '人们自然而然地敬重你。', effects: { 声望: 50, 魅力: 5 } },
  { id: 't_ironWill', name: '钢铁意志', type: 'positive', desc: '没有什么能击垮你。', effects: { 体质: 8, 力量: 5 } },
  { id: 't_spellborn', name: '魔法之子', type: 'positive', desc: '生来就与魔法之流共鸣。', effects: { 智慧: 10, 运气: 5 } },
  { id: 't_shadowStep', name: '影步', type: 'positive', desc: '暗影随你而行。', effects: { 敏捷: 10, 运气: 5 } },
  { id: 't_goldenTongue', name: '金舌头', type: 'positive', desc: '说服任何人做任何事。', effects: { 魅力: 10, 财富: 200 } },
  { id: 't_berserker', name: '狂战士之血', type: 'positive', desc: '受伤越重越强。', effects: { 力量: 10, 体质: 5 } },
  { id: 't_luckyCoin', name: '幸运金币', type: 'positive', desc: '一枚似乎永远不会用完的金币。', effects: { 运气: 8, 财富: 300 } },
  { id: 't_ancientScroll', name: '上古卷轴', type: 'positive', desc: '记载着失传的知识。', effects: { 智慧: 12, 声望: 20 } },
  { id: 't_divineBless', name: '神圣祝福', type: 'positive', desc: '天界的祝福加持于你。', effects: { 魅力: 8, 道德: 20, 运气: 5 } },
  { id: 't_elfBlood', name: '精灵血脉', type: 'positive', desc: '你的血统中混有远古精灵之血。', effects: { 敏捷: 8, 智慧: 5, 魅力: 3 } },
  { id: 't_giantStrength', name: '巨人末裔', type: 'positive', desc: '巨人族的血脉在你体内流淌。', effects: { 力量: 10, 体质: 6, 敏捷: -2 } },
  { id: 't_adventurous', name: '冒险精神', type: 'positive', desc: '你永远不会安于现状。', effects: { 运气: 8, 敏捷: 5 } },
  // 负面天赋
  { id: 'tn_weak', name: '体弱多病', type: 'negative', desc: '从小药罐子不离身。', effects: { 体质: -12 } },
  { id: 'tn_shy', name: '社交恐惧', type: 'negative', desc: '人群让你窒息。', effects: { 魅力: -12 } },
  { id: 'tn_cursed', name: '厄运缠身', type: 'negative', desc: '似乎总有人在暗处盯着你。', effects: { 运气: -15 } },
  { id: 'tn_spendthrift', name: '挥霍无度', type: 'negative', desc: '钱在手里留不住三天。', effects: { 财富: -300 } },
  { id: 'tn_cursedMark', name: '诅咒印记', type: 'negative', desc: '一个古老的诅咒刻在你身上。', effects: { 运气: -8, 魅力: -5 } },
  { id: 'tn_frail', name: '脆骨症', type: 'negative', desc: '骨头像玻璃一样易碎。', effects: { 力量: -10, 体质: -5 } },
  { id: 'tn_dull', name: '思维迟钝', type: 'negative', desc: '理解东西总是比别人慢。', effects: { 智慧: -12 } },
  { id: 'tn_clumsy', name: '笨手笨脚', type: 'negative', desc: '仿佛上天忘了给你运动神经。', effects: { 敏捷: -12 } },
  { id: 'tn_outcast', name: '被放逐者', type: 'negative', desc: '人们看你的时候眼神总是怪怪的。', effects: { 声望: -30, 魅力: -5 } },
  { id: 'tn_debt', name: '一贫如洗', type: 'negative', desc: '除了身上的衣服，你一无所有。', effects: { 财富: -100 } },
];
```

- [ ] **Step 2: 刷新浏览器，确认控制台无报错**

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: add game data definitions - races, identities, visions, talents"
```

---

### Task 3: 状态机与屏幕渲染框架

**Files:**
- Modify: `index.html`（在游戏数据定义之后追加渲染函数）

- [ ] **Step 1: 实现屏幕渲染调度器**

在数据定义之后追加：

```javascript
// === Rendering Engine ===

function render() {
  const app = document.getElementById('app');
  app.innerHTML = '';

  switch (state.screen) {
    case 'title': renderTitle(app); break;
    case 'difficulty': renderDifficulty(app); break;
    case 'rhythm': renderRhythm(app); break;
    case 'race': renderRace(app); break;
    case 'identity': renderIdentity(app); break;
    case 'vision': renderVision(app); break;
    case 'talentDraw': renderTalentDraw(app); break;
    case 'allocPoints': renderAllocPoints(app); break;
    case 'lifeEvent': renderLifeEvent(app); break;
    case 'ending': renderEnding(app); break;
    case 'history': renderHistory(app); break;
    default: renderTitle(app);
  }
}

// Placeholder renderers (to be implemented in subsequent tasks)
function renderTitle(app) {
  app.innerHTML = `<div class="screen-content" style="display:flex;flex-direction:column;justify-content:center;align-items:center;text-align:center;">
    <h1 style="margin-bottom:8px;">人 生 重 开</h1>
    <p class="subtitle">Life Restart Simulator</p>
    <div style="width:100%;margin-top:40px;">
      <button class="btn primary" onclick="startNewGame()">新 游 戏</button>
      <button class="btn" onclick="continueGame()" id="continueBtn">继 续 游 戏</button>
      <button class="btn secondary" onclick="viewHistory()">生 平 回 顾</button>
    </div>
  </div>`;
  // 隐藏继续按钮（如果没有存档）
  const progress = loadProgress();
  document.getElementById('continueBtn').style.display = progress ? 'block' : 'none';
}

function startNewGame() {
  state.screen = 'difficulty';
  state.difficulty = '';
  state.rhythm = '';
  state.race = '';
  state.identity = '';
  state.vision = '';
  state.talents = [];
  state.attrs = { 力量: 10, 智慧: 10, 魅力: 10, 敏捷: 10, 体质: 10, 运气: 10, 财富: 100, 声望: 0, 道德: 0 };
  state.stageIndex = 0;
  state.drawnTalents = [];
  state.remainingRerolls = 0;
  state.selectedTalents = [];
  state.currentEvents = [];
  state.completedEvents = [];
  render();
}

function continueGame() {
  const progress = loadProgress();
  if (!progress) return;
  Object.assign(state, progress);
  state.screen = 'lifeEvent';
  render();
}

function viewHistory() {
  state.screen = 'history';
  render();
}

// Placeholder stubs — will be replaced in subsequent tasks
function renderDifficulty(app) { app.innerHTML = '<p>难度选择 - 待实现</p>'; }
function renderRhythm(app) { app.innerHTML = '<p>节奏选择 - 待实现</p>'; }
function renderRace(app) { app.innerHTML = '<p>种族选择 - 待实现</p>'; }
function renderIdentity(app) { app.innerHTML = '<p>身份选择 - 待实现</p>'; }
function renderVision(app) { app.innerHTML = '<p>愿景选择 - 待实现</p>'; }
function renderTalentDraw(app) { app.innerHTML = '<p>天赋抽卡 - 待实现</p>'; }
function renderAllocPoints(app) { app.innerHTML = '<p>属性加点 - 待实现</p>'; }
function renderLifeEvent(app) { app.innerHTML = '<p>人生阶段 - 待实现</p>'; }
function renderEnding(app) { app.innerHTML = '<p>结局画面 - 待实现</p>'; }
function renderHistory(app) { app.innerHTML = '<p>生平回顾 - 待实现</p>'; }

// Initialize
render();
```

- [ ] **Step 2: 打开浏览器验证**

点击"新游戏"按钮应能进入占位页面，显示"难度选择 - 待实现"。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: add state machine and screen rendering framework"
```

---

### Task 4: 开局选择屏幕（难度/节奏/种族/身份/愿景）

**Files:**
- Modify: `index.html`（替换占位渲染函数）

- [ ] **Step 1: 实现难度选择屏幕**

替换 `renderDifficulty`：

```javascript
function renderDifficulty(app) {
  app.innerHTML = renderScreenWrapper('⚙️ 选择难度', '命运的齿轮即将转动...', `
    ${Object.entries(DIFFICULTIES).map(([key, d]) => `
      <div class="card" onclick="selectDifficulty('${key}')">
        <h3>${d.icon} ${d.name}</h3>
        <p style="font-size:13px;color:#888;">${d.desc}</p>
        <p style="font-size:12px;color:#666;margin-top:4px;">
          属性倍率 ×${d.attrMultiplier} | 抽${d.drawCount}选${d.pickCount} | 重roll ${d.rerolls}次
        </p>
      </div>
    `).join('')}
  `);
}

function selectDifficulty(key) {
  state.difficulty = key;
  state.screen = 'rhythm';
  render();
}
```

- [ ] **Step 2: 实现节奏选择屏幕**

替换 `renderRhythm`：

```javascript
function renderRhythm(app) {
  app.innerHTML = renderScreenWrapper('📋 选择节奏', '你希望以怎样的速度走完这一生？', `
    ${Object.entries(RHYTHMS).map(([key, r]) => `
      <div class="card" onclick="selectRhythm('${key}')">
        <h3>${r.name}</h3>
        <p style="font-size:13px;color:#888;">${r.desc}</p>
        <p style="font-size:12px;color:#666;margin-top:4px;">
          阶段：${r.stages.join(' → ')}
        </p>
      </div>
    `).join('')}
  `);
}

function selectRhythm(key) {
  state.rhythm = key;
  state.screen = 'race';
  render();
}
```

- [ ] **Step 3: 实现种族选择屏幕**

替换 `renderRace`：

```javascript
function renderRace(app) {
  app.innerHTML = renderScreenWrapper('🧬 选择种族', '你的血脉决定了你的起点', `
    ${Object.values(RACES).map(r => `
      <div class="card" onclick="selectRace('${r.id}')">
        <h3>${r.icon} ${r.name}</h3>
        <p style="font-size:13px;color:#888;">${r.desc}</p>
        <p style="font-size:12px;margin-top:4px;">
          ${formatAttrChanges(r.bonuses)}
        </p>
      </div>
    `).join('')}
  `);
}

function selectRace(key) {
  state.race = key;
  state.screen = 'identity';
  render();
}
```

- [ ] **Step 4: 实现身份选择屏幕**

替换 `renderIdentity`：

```javascript
function renderIdentity(app) {
  const identities = IDENTITIES[state.race];
  const race = RACES[state.race];
  app.innerHTML = renderScreenWrapper('🎭 选择身份', `作为${race.icon} ${race.name}，你的出身是...`, `
    ${identities.map(idt => `
      <div class="card" onclick="selectIdentity('${idt.id}')">
        <h3>${idt.name}</h3>
        <p style="font-size:13px;color:#888;">${idt.desc}</p>
        <p style="font-size:12px;margin-top:4px;">
          ${formatAttrChanges(idt.bonuses)}
        </p>
      </div>
    `).join('')}
  `);
}

function selectIdentity(key) {
  state.identity = key;
  state.screen = 'vision';
  render();
}
```

- [ ] **Step 5: 实现愿景选择屏幕**

替换 `renderVision`：

```javascript
function renderVision(app) {
  app.innerHTML = renderScreenWrapper('🌟 选择愿景', '你此生追求的是什么？', `
    ${VISIONS.map(v => `
      <div class="card" onclick="selectVision('${v.id}')">
        <h3>${v.icon} ${v.name}</h3>
        <p style="font-size:13px;color:#888;">${v.desc}</p>
        <p style="font-size:11px;color:#666;margin-top:4px;">
          目标：${Object.entries(v.condition).map(([k, val]) => `${k} ≥ ${val}`).join('，')}
        </p>
      </div>
    `).join('')}
  `);
}

function selectVision(key) {
  state.vision = key;
  state.screen = 'talentDraw';
  // 初始化天赋抽取
  const diff = DIFFICULTIES[state.difficulty];
  state.drawnTalents = drawTalents(diff.drawCount, diff.negativeBias);
  state.selectedTalents = [];
  state.remainingRerolls = diff.rerolls;
  render();
}
```

- [ ] **Step 6: 添加辅助函数**

在数据定义之后、渲染函数之前添加：

```javascript
// === Helper Functions ===

function renderScreenWrapper(title, subtitle, content) {
  return `<div class="screen-content">
    <h2>${title}</h2>
    <p class="subtitle">${subtitle}</p>
    ${content}
  </div>`;
}

function formatAttrChanges(bonuses) {
  if (!bonuses) return '';
  return Object.entries(bonuses).filter(([, v]) => v !== 0).map(([k, v]) => {
    const cls = v > 0 ? 'tag-positive' : 'tag-negative';
    const sign = v > 0 ? '+' : '';
    return `<span class="tag ${cls}">${k} ${sign}${v}</span>`;
  }).join(' ');
}

// 天赋抽取
function drawTalents(count, negativeBias) {
  const positive = TALENTS.filter(t => t.type === 'positive');
  const negative = TALENTS.filter(t => t.type === 'negative');
  let pool = [];
  // 至少保证 1 张正面卡
  const negCount = negativeBias ? Math.min(count - 1, negative.length) : 0;
  const posCount = count - negCount;
  pool = shuffle([...positive]).slice(0, Math.max(1, posCount))
    .concat(shuffle([...negative]).slice(0, negCount));
  return shuffle(pool);
}

function shuffle(arr) {
  const a = [...arr];
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
```

- [ ] **Step 7: 浏览器验证**

依次点击：新游戏 → 选择难度 → 选择节奏 → 选择种族 → 选择身份 → 选择愿景。确认每一步都正确跳转。

- [ ] **Step 8: Commit**

```
git add index.html
git commit -m "feat: implement setup screens - difficulty, rhythm, race, identity, vision"
```

---

### Task 5: 天赋抽卡屏幕

**Files:**
- Modify: `index.html`（替换 `renderTalentDraw`）

- [ ] **Step 1: 实现天赋抽卡渲染**

替换占位的 `renderTalentDraw`：

```javascript
function renderTalentDraw(app) {
  const diff = DIFFICULTIES[state.difficulty];
  const selectedCount = state.selectedTalents.length;
  const maxSelect = diff.pickCount;

  app.innerHTML = renderScreenWrapper('🎴 抽取天赋', `选择 ${maxSelect} 个天赋（已选 ${selectedCount}/${maxSelect}）`, `
    <div class="card-grid single-col">
      ${state.drawnTalents.map(t => {
        const isSelected = state.selectedTalents.includes(t.id);
        const canSelect = selectedCount < maxSelect || isSelected;
        const cls = isSelected ? 'selected' : '';
        return `
          <div class="card ${cls} ${!canSelect && !isSelected ? 'disabled' : ''}"
               onclick="toggleTalentSelection('${t.id}')">
            <h3 style="display:flex;justify-content:space-between;">
              ${t.name}
              <span class="tag ${t.type === 'positive' ? 'tag-positive' : 'tag-negative'}">${t.type === 'positive' ? '正面' : '负面'}</span>
            </h3>
            <p style="font-size:13px;color:#888;">${t.desc}</p>
            <p style="font-size:12px;margin-top:4px;">
              ${formatAttrChanges(t.effects)}
            </p>
          </div>
        `;
      }).join('')}
    </div>
    <div class="reroll-info">🎲 剩余重抽次数：${state.remainingRerolls}</div>
    <button class="btn ${state.remainingRerolls > 0 ? '' : 'secondary'}"
            ${state.remainingRerolls === 0 ? 'disabled' : ''}
            onclick="rerollTalents()">🔄 重抽</button>
    <button class="btn primary" onclick="confirmTalents()"
            ${selectedCount !== maxSelect ? 'disabled' : ''}>
      确认选择 (${selectedCount}/${maxSelect})
    </button>
  `);
}

function toggleTalentSelection(id) {
  const diff = DIFFICULTIES[state.difficulty];
  const idx = state.selectedTalents.indexOf(id);
  if (idx >= 0) {
    state.selectedTalents.splice(idx, 1);
  } else if (state.selectedTalents.length < diff.pickCount) {
    state.selectedTalents.push(id);
  }
  render();
}

function rerollTalents() {
  if (state.remainingRerolls <= 0) return;
  state.remainingRerolls--;
  const diff = DIFFICULTIES[state.difficulty];
  state.drawnTalents = drawTalents(diff.drawCount, diff.negativeBias);
  state.selectedTalents = [];
  render();
}

function confirmTalents() {
  state.talents = [...state.selectedTalents];
  const diff = DIFFICULTIES[state.difficulty];
  state.allocPoints = diff.allocPoints;
  // 应用种族 + 身份 + 天赋的基础属性，再乘以难度倍率
  applyBaseAttributes();
  state.screen = 'allocPoints';
  render();
}

function applyBaseAttributes() {
  // 重置为基础值
  state.attrs = { 力量: 10, 智慧: 10, 魅力: 10, 敏捷: 10, 体质: 10, 运气: 10, 财富: 100, 声望: 0, 道德: 0 };
  const diff = DIFFICULTIES[state.difficulty];
  const race = RACES[state.race];
  const identity = IDENTITIES[state.race].find(i => i.id === state.identity);

  // 种族加成（仅六维）
  Object.entries(race.bonuses).forEach(([k, v]) => {
    if (state.attrs[k] !== undefined) state.attrs[k] += v;
  });

  // 身份加成
  if (identity && identity.bonuses) {
    Object.entries(identity.bonuses).forEach(([k, v]) => {
      if (state.attrs[k] !== undefined) state.attrs[k] += v;
    });
  }

  // 天赋加成
  state.talents.forEach(tid => {
    const talent = TALENTS.find(t => t.id === tid);
    if (talent) {
      Object.entries(talent.effects).forEach(([k, v]) => {
        if (state.attrs[k] !== undefined) state.attrs[k] += v;
      });
    }
  });

  // 难度倍率（仅六维）
  ['力量','智慧','魅力','敏捷','体质','运气'].forEach(k => {
    state.attrs[k] = Math.max(1, Math.round(state.attrs[k] * diff.attrMultiplier));
  });

  // 财富和声望下限
  state.attrs.财富 = Math.max(0, state.attrs.财富);
}
```

- [ ] **Step 2: 浏览器验证完整开局流程**

新游戏 → 难度 → 节奏 → 种族 → 身份 → 愿景 → 抽天赋（测试选择、重抽、确认）→ 进入加点屏幕。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: implement talent draw screen with reroll and selection"
```

---

### Task 6: 属性加点屏幕

**Files:**
- Modify: `index.html`（替换 `renderAllocPoints`）

- [ ] **Step 1: 实现属性加点渲染**

替换占位的 `renderAllocPoints`：

```javascript
function renderAllocPoints(app) {
  const sixAttrs = ['力量', '智慧', '魅力', '敏捷', '体质', '运气'];
  const socialAttrs = ['财富', '声望', '道德'];

  app.innerHTML = renderScreenWrapper('📊 分配属性点', `剩余点数：<span style="color:#c9a84c;font-weight:bold;">${state.allocPoints}</span>（每点+2属性值）`, `
    <div style="margin-bottom:16px;">
      <p style="font-size:13px;color:#888;margin-bottom:8px;">基础六维</p>
      ${sixAttrs.map(k => `
        <div class="attr-row">
          <span class="attr-name">${k}</span>
          <div class="attr-controls">
            <button class="attr-btn" onclick="adjustAttr('${k}', -1)" ${state.allocPoints <= 0 ? 'disabled' : ''}>−</button>
            <span class="attr-value">${state.attrs[k]}</span>
            <button class="attr-btn" onclick="adjustAttr('${k}', 1)" ${state.allocPoints <= 0 ? 'disabled' : ''}>+</button>
          </div>
        </div>
      `).join('')}
    </div>
    <div style="margin-bottom:16px;">
      <p style="font-size:13px;color:#888;margin-bottom:8px;">社会属性（开局固定）</p>
      ${socialAttrs.map(k => `
        <div class="attr-row">
          <span class="attr-name">${k}</span>
          <span class="attr-value">${state.attrs[k]}</span>
        </div>
      `).join('')}
    </div>
    <button class="btn primary" onclick="confirmAlloc()">开始人生</button>
  `);
}

function adjustAttr(attr, delta) {
  if (delta > 0 && state.allocPoints <= 0) return;
  if (delta < 0) {
    state.allocPoints++;
    state.attrs[attr] -= 2;
  } else {
    state.allocPoints--;
    state.attrs[attr] += 2;
  }
  render();
}

function confirmAlloc() {
  state.stageIndex = 0;
  state.completedEvents = [];
  state.screen = 'lifeEvent';
  saveProgress();
  render();
}
```

- [ ] **Step 2: 验证加点流程**

确认加减按钮正常工作，确认按钮状态正确，点击"开始人生"进入人生阶段（暂为占位页）。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: implement attribute point allocation screen"
```

---

### Task 7: 事件引擎与人生阶段屏幕

**Files:**
- Modify: `index.html`（替换 `renderLifeEvent`，添加事件数据和引擎）

- [ ] **Step 1: 定义事件数据**

在数据定义区域（TALENTS 之后）追加事件数据。由于事件数据量大，定义约50个事件覆盖各阶段：

```javascript
// === 事件数据 ===
const EVENTS = [
  // ===== 婴儿/幼年 =====
  {
    id: 'baby_01', stages: ['婴儿', '幼年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '第一次开口',
    narrative: '你发出了人生中第一个有意义的音节。家人围在你身边，满怀期待。',
    options: [
      { text: '喊"妈妈"', require: {}, effects: { 魅力: 3, 道德: 2 }, narrative: '母亲激动得热泪盈眶。' },
      { text: '喊"爸爸"', require: {}, effects: { 魅力: 2, 力量: 2 }, narrative: '父亲骄傲地把你举过头顶。' },
      { text: '保持沉默', require: {}, effects: { 智慧: 3 }, narrative: '你静静地观察着一切，似乎在思考什么。' },
    ],
  },
  {
    id: 'baby_02', stages: ['婴儿', '幼年'], minAttr: {}, vision: '', difficulty: '',
    weight: 8, title: '意外发现',
    narrative: '你在家中的角落里翻出了一件奇怪的物品。',
    options: [
      { text: '拿给大人看', require: {}, effects: { 魅力: 3, 声望: 5 }, narrative: '原来是一枚祖传的徽章！' },
      { text: '偷偷藏起来', require: {}, effects: { 敏捷: 3, 道德: -2 }, narrative: '你把它藏在了只有你知道的地方。' },
    ],
  },
  {
    id: 'baby_03', stages: ['婴儿', '幼年'], minAttr: {}, vision: '', difficulty: '',
    weight: 8, title: '玩伴',
    narrative: '邻居家的孩子来找你玩。',
    options: [
      { text: '一起玩游戏', require: {}, effects: { 魅力: 3, 运气: 2 }, narrative: '你们成了最好的朋友。' },
      { text: '独自看书', require: {}, effects: { 智慧: 4, 魅力: -1 }, narrative: '你觉得书比玩伴有趣得多。' },
    ],
  },

  // ===== 童年 =====
  {
    id: 'child_01', stages: ['童年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '启蒙教育',
    narrative: '一位游历的学者路过你的家乡，开设了临时学堂。',
    options: [
      { text: '认真学习', require: {}, effects: { 智慧: 8, 声望: 5 }, narrative: '学者对你印象深刻，留下了一本书。' },
      { text: '逃课去玩', require: {}, effects: { 敏捷: 5, 运气: 3 }, narrative: '你在野外度过了一段快乐的时光。' },
      { text: '帮家里干活', require: {}, effects: { 力量: 5, 体质: 3, 道德: 3 }, narrative: '你用行动帮了家人。' },
    ],
  },
  {
    id: 'child_02', stages: ['童年'], minAttr: {}, vision: '', difficulty: '',
    weight: 8, title: '小镇集市',
    narrative: '一年一度的大集市来了，街道上热闹非凡。',
    options: [
      { text: '观看角斗表演', require: {}, effects: { 力量: 5 }, narrative: '角斗士的勇武让你热血沸腾。' },
      { text: '逛魔法摊位', require: {}, effects: { 智慧: 5 }, narrative: '魔法商人的把戏让你大开眼界。' },
      { text: '帮商贩打零工', require: {}, effects: { 财富: 50, 魅力: 2 }, narrative: '你用劳动赚到了第一笔钱。' },
    ],
  },
  {
    id: 'child_03', stages: ['童年'], minAttr: {}, vision: '', difficulty: '',
    weight: 6, title: '神秘梦境',
    narrative: '你连续三天做着同一个奇怪的梦——一个声音在呼唤你的名字。',
    options: [
      { text: '试图记住梦境', require: {}, effects: { 运气: 5, 智慧: 3 }, narrative: '你似乎瞥见了命运的丝线。' },
      { text: '被吓到不敢睡觉', require: {}, effects: { 体质: -3, 智慧: 2 }, narrative: '你变得敏感而警觉。' },
    ],
  },

  // ===== 少年 =====
  {
    id: 'youth_01', stages: ['少年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '命运的抉择',
    narrative: '你到了必须选择人生方向的年纪。村里的长者给出了几个建议。',
    options: [
      { text: '加入冒险公会', require: {}, effects: { 力量: 10, 敏捷: 5 }, narrative: '你签下了冒险者的契约。' },
      { text: '拜入魔法塔', require: { 智慧: 40 }, effects: { 智慧: 12, 声望: 10 }, narrative: '魔法师们收下了你。' },
      { text: '继承家业', require: {}, effects: { 财富: 200, 体质: 3 }, narrative: '你开始学习经营家业。' },
    ],
  },
  {
    id: 'youth_02', stages: ['少年'], minAttr: {}, vision: 'hero', difficulty: '',
    weight: 15, title: '勇者试炼（愿景专属）',
    narrative: '王国的勇者选拔开始了。你的愿景驱使你前往参加。',
    options: [
      { text: '参加试炼', require: { 力量: 50 }, effects: { 力量: 15, 声望: 30, 体质: 8 }, narrative: '你通过了第一阶段！' },
      { text: '先去修炼', require: {}, effects: { 力量: 5, 智慧: 3 }, narrative: '你觉得还不到时候。' },
    ],
  },
  {
    id: 'youth_03', stages: ['少年'], minAttr: {}, vision: '', difficulty: 'hard',
    weight: 20, title: '饥荒之年',
    narrative: '一场严重的饥荒席卷了你的家乡。食物变得极度稀缺。',
    options: [
      { text: '外出狩猎', require: { 敏捷: 30 }, effects: { 敏捷: 8, 体质: 5, 财富: 50 }, narrative: '你打到了足够一家人吃的猎物。' },
      { text: '省下自己的口粮', require: {}, effects: { 体质: -8, 道德: 15, 声望: 10 }, narrative: '你把食物留给了更需要的人。' },
      { text: '逃离家乡', require: {}, effects: { 敏捷: 5, 道德: -10 }, narrative: '你独自踏上了未知的旅途。' },
    ],
  },

  // ===== 青年 =====
  {
    id: 'adult_01', stages: ['青年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '竞技大会',
    narrative: '王国最大的竞技大会正在举行，胜者将获得丰厚奖励和无上荣誉。',
    options: [
      { text: '报名参赛', require: { 力量: 60 }, effects: { 力量: 10, 声望: 40, 财富: 500 }, narrative: '你击败了数名对手，一战成名！' },
      { text: '下注赌一把', require: {}, effects: { 财富: 300, 运气: -3 }, narrative: '你押中了冠军。' },
      { text: '观战学习', require: {}, effects: { 智慧: 5, 敏捷: 3 }, narrative: '你从高手身上学到了很多。' },
    ],
  },
  {
    id: 'adult_02', stages: ['青年'], minAttr: {}, vision: '', difficulty: '',
    weight: 8, title: '遗迹探险',
    narrative: '一支探险队正在招募队员，目标是传说中的古代遗迹。',
    options: [
      { text: '立即加入', require: { 敏捷: 50, 体质: 50 }, effects: { 力量: 8, 财富: 800, 声望: 30 }, narrative: '你发现了被遗忘的宝藏。' },
      { text: '谨慎观望', require: {}, effects: { 运气: 3 }, narrative: '探险队几乎全军覆没——你庆幸没去。' },
      { text: '提供物资支持', require: { 财富: 500 }, effects: { 财富: 200, 声望: 20 }, narrative: '探险队成功归来，分了你一份战利品。' },
    ],
  },
  {
    id: 'adult_03', stages: ['青年'], minAttr: {}, vision: 'wealthy', difficulty: '',
    weight: 15, title: '商机乍现（愿景专属）',
    narrative: '一个绝佳的商机摆在你面前——但需要承担不小的风险。',
    options: [
      { text: '砸锅卖铁投资', require: { 财富: 1000 }, effects: { 财富: 3000, 运气: 8 }, narrative: '你赌对了！财富翻了三倍！' },
      { text: '小额试探', require: {}, effects: { 财富: 500, 智慧: 3 }, narrative: '稳健的投资获得了不错的回报。' },
      { text: '放弃这次机会', require: {}, effects: { 运气: -3 }, narrative: '机会稍纵即逝。' },
    ],
  },

  // ===== 壮年 =====
  {
    id: 'prime_01', stages: ['壮年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '魔物入侵',
    narrative: '一支魔物大军正在逼近王国边境。征兵令已经下达。',
    options: [
      { text: '应征入伍', require: { 力量: 70, 体质: 60 }, effects: { 力量: 15, 声望: 50, 体质: 5 }, narrative: '你在战场上立下赫赫战功！' },
      { text: '用钱雇佣替身', require: { 财富: 3000 }, effects: { 财富: -2000, 道德: -10 }, narrative: '有钱能使鬼推磨。' },
      { text: '逃往他国', require: {}, effects: { 敏捷: 8, 声望: -20, 道德: -5 }, narrative: '你背井离乡，保住了性命。' },
    ],
  },
  {
    id: 'prime_02', stages: ['壮年'], minAttr: {}, vision: '', difficulty: 'normal',
    weight: 8, title: '家族危机',
    narrative: '你的家族陷入了危机——一笔巨大的债务即将到期。',
    options: [
      { text: '变卖家产还债', require: { 财富: 2000 }, effects: { 财富: -1500, 声望: 15, 道德: 10 }, narrative: '虽然清贫，但你守住了家族的声誉。' },
      { text: '外出借债', require: { 魅力: 80 }, effects: { 财富: 1000, 声望: -5 }, narrative: '你靠着人脉勉强渡过难关。' },
      { text: '赖账跑路', require: {}, effects: { 声望: -30, 道德: -15 }, narrative: '你带着仅有的财产消失在了黑夜里。' },
    ],
  },
  {
    id: 'prime_03', stages: ['壮年'], minAttr: {}, vision: 'sage', difficulty: '',
    weight: 15, title: '禁忌的知识（愿景专属）',
    narrative: '你发现了一座被遗忘的魔法图书馆，里面藏有禁断的典籍。',
    options: [
      { text: '研读禁书', require: { 智慧: 100 }, effects: { 智慧: 20, 道德: -20, 运气: 5 }, narrative: '你触碰了不应触碰的知识。' },
      { text: '仅翻阅许可部分', require: {}, effects: { 智慧: 10, 声望: 10 }, narrative: '你学到了许多珍贵的正统魔法。' },
      { text: '向教会举报', require: {}, effects: { 声望: 30, 道德: 15, 智慧: 5 }, narrative: '教会表彰了你的正直。' },
    ],
  },

  // ===== 中年 =====
  {
    id: 'mid_01', stages: ['中年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '人生十字路口',
    narrative: '你站在人生的中点回望来路，前方似乎还有无数可能。',
    options: [
      { text: '继续冒险', require: { 体质: 80 }, effects: { 力量: 8, 敏捷: 8, 运气: 5 }, narrative: '宝刀未老！' },
      { text: '著书立传', require: { 智慧: 90 }, effects: { 声望: 40, 智慧: 5, 财富: 300 }, narrative: '你的故事传遍大陆。' },
      { text: '退隐田园', require: {}, effects: { 体质: 8, 道德: 10, 运气: 8 }, narrative: '平淡也是一种幸福。' },
    ],
  },
  {
    id: 'mid_02', stages: ['中年'], minAttr: {}, vision: '', difficulty: '',
    weight: 7, title: '仇敌来袭',
    narrative: '一位你多年前得罪过的人找上门来了。',
    options: [
      { text: '正面迎战', require: { 力量: 90 }, effects: { 力量: 10, 声望: 20 }, narrative: '你以实力让仇敌知难而退。' },
      { text: '化敌为友', require: { 魅力: 100 }, effects: { 魅力: 8, 道德: 15 }, narrative: '曾经的敌人成了你最强的盟友。' },
      { text: '赔钱了事', require: { 财富: 5000 }, effects: { 财富: -3000 }, narrative: '金钱买来了安宁。' },
    ],
  },

  // ===== 中老年 =====
  {
    id: 'late_01', stages: ['中老年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '传承之责',
    narrative: '你开始考虑，该把毕生所学传给谁。',
    options: [
      { text: '收徒授业', require: { 声望: 200 }, effects: { 声望: 60, 魅力: 5 }, narrative: '你的弟子们将在未来改变世界。' },
      { text: '著书传世', require: { 智慧: 100 }, effects: { 智慧: 5, 声望: 50 }, narrative: '你的著作成了后世的经典。' },
      { text: '埋藏秘密', require: {}, effects: { 运气: 5, 道德: -5 }, narrative: '有些知识，还是让它消失在历史中吧。' },
    ],
  },
  {
    id: 'late_02', stages: ['中老年'], minAttr: {}, vision: '', difficulty: '',
    weight: 8, title: '王国的召唤',
    narrative: '国王亲自召见你——王国需要你的智慧。',
    options: [
      { text: '出任顾问', require: { 声望: 300 }, effects: { 声望: 80, 财富: 2000 }, narrative: '你成了国王最信任的顾问。' },
      { text: '婉拒', require: {}, effects: { 声望: -10 }, narrative: '你更愿意做一个自由的人。' },
    ],
  },

  // ===== 老年 =====
  {
    id: 'old_01', stages: ['老年'], minAttr: {}, vision: '', difficulty: '',
    weight: 10, title: '最后的冒险',
    narrative: '尽管年事已高，但你的冒险之魂从未熄灭。',
    options: [
      { text: '踏上最后的征途', require: { 体质: 100 }, effects: { 声望: 40, 力量: 5, 体质: -15 }, narrative: '你在冒险中受了重伤，但死而无憾。' },
      { text: '安享晚年', require: {}, effects: { 体质: 5, 运气: 10, 财富: -100 }, narrative: '你在温暖的炉火旁度过了最后的时光。' },
    ],
  },
  {
    id: 'old_02', stages: ['老年'], minAttr: { 体力: 30 }, vision: '', difficulty: '',
    weight: 6, title: '寿元将尽',
    narrative: '你感觉到自己的身体正在快速衰弱。',
    options: [
      { text: '准备后事', require: {}, effects: { 声望: 20, 道德: 10 }, narrative: '你平静地安排好了一切。' },
      { text: '疯狂享乐', require: { 财富: 5000 }, effects: { 财富: -5000, 运气: 5 }, narrative: '你在最后的狂欢中耗尽了一切。' },
      { text: '寻找续命之法', require: { 智慧: 130 }, effects: { 体质: 10, 道德: -15 }, narrative: '你找到了一种禁忌的延寿之术......' },
    ],
  },

  // ===== 通用事件（多阶段可用） =====
  {
    id: 'common_01', stages: ['青年', '壮年', '中年'], minAttr: {}, vision: '', difficulty: '',
    weight: 6, title: '奇遇·神秘商人',
    narrative: '一个身披斗篷的神秘商人出现在你面前。"这些货物来自世界的尽头。"',
    options: [
      { text: '购买稀有道具', require: { 财富: 2000 }, effects: { 财富: -2000, 力量: 10, 运气: 5 }, narrative: '你得到了一把会发光的剑。' },
      { text: '砍价', require: { 魅力: 70 }, effects: { 财富: -800, 运气: 3 }, narrative: '你用一个好价钱买下了它。' },
      { text: '离开', require: {}, effects: {}, narrative: '商人消失在阴影之中。' },
    ],
  },
  {
    id: 'common_02', stages: ['少年', '青年', '壮年'], minAttr: {}, vision: '', difficulty: 'easy,heaven',
    weight: 12, title: '天上掉馅饼',
    narrative: '你捡到了一个装满了金币的钱袋！',
    options: [
      { text: '据为己有', require: {}, effects: { 财富: 1500, 道德: -5 }, narrative: '横财也是财！' },
      { text: '寻找失主', require: {}, effects: { 声望: 20, 道德: 15 }, narrative: '失主是一位富商，对你感激不尽。' },
    ],
  },
  {
    id: 'common_03', stages: ['青年', '壮年', '中年', '中老年'], minAttr: {}, vision: '', difficulty: 'hard',
    weight: 12, title: '暗杀',
    narrative: '你在深夜的街道上被一伙蒙面人包围了。',
    options: [
      { text: '反击', require: { 力量: 80, 敏捷: 60 }, effects: { 力量: 8, 声望: 15 }, narrative: '你击退了刺客。' },
      { text: '逃跑', require: { 敏捷: 70 }, effects: { 敏捷: 8 }, narrative: '你消失在夜色之中。' },
      { text: '破财消灾', require: { 财富: 3000 }, effects: { 财富: -2500 }, narrative: '钱没了可以再赚，命只有一条。' },
    ],
  },
  {
    id: 'death_01', stages: ['青年', '壮年', '中年', '中老年', '老年'], minAttr: {}, vision: '', difficulty: 'hard',
    weight: 8, title: '致命陷阱',
    narrative: '你不小心触发了致命的陷阱。刀锋从四面八方飞来。',
    options: [
      { text: '极限闪避', require: { 敏捷: 120 }, effects: { 敏捷: 10, 运气: 3 }, narrative: '你在千钧一发之际躲开了。' },
      { text: '硬扛', require: { 体质: 100 }, effects: { 体质: -30, 力量: 5 }, narrative: '你活了下来，但付出了巨大代价。' },
      { text: '绝望', require: {}, effects: {}, narrative: '', death: true, deathNarrative: '你的冒险在此终结。刀刃贯穿了你的身体。' },
    ],
  },
];
```

- [ ] **Step 2: 实现事件引擎和人生阶段渲染**

替换占位的 `renderLifeEvent`，并添加事件筛选函数：

```javascript
// === Event Engine ===

function filterEvents(stageName) {
  const diff = DIFFICULTIES[state.difficulty];
  const vision = VISIONS.find(v => v.id === state.vision);

  let candidates = EVENTS.filter(e => {
    // 阶段匹配
    if (!e.stages.includes(stageName)) return false;
    // 已完成事件排除
    if (state.completedEvents.includes(e.id)) return false;
    // 难度过滤
    if (e.difficulty) {
      const allowedDiffs = e.difficulty.split(',');
      if (!allowedDiffs.includes(state.difficulty)) return false;
    }
    // 属性门槛
    if (e.minAttr && Object.keys(e.minAttr).length > 0) {
      for (const [k, v] of Object.entries(e.minAttr)) {
        if ((state.attrs[k] || 0) < v) return false;
      }
    }
    return true;
  });

  // 事件偏向
  if (diff.eventBias === 'negative') {
    // 偏向包含致死选项的事件
    const deadly = candidates.filter(e => e.options.some(o => o.death));
    const normal = candidates.filter(e => !e.options.some(o => o.death));
    candidates = shuffle([...deadly, ...deadly, ...normal]); // 致死事件权重翻倍
  } else if (diff.eventBias === 'lucky') {
    // 奇遇事件（财富大幅增加的事件）优先
    const lucky = candidates.filter(e => e.options.some(o => (o.effects.财富 || 0) > 500));
    const normal = candidates.filter(e => !e.options.some(o => (o.effects.财富 || 0) > 500));
    candidates = shuffle([...lucky, ...lucky, ...normal]);
  } else if (diff.eventBias === 'positive') {
    // 正面事件优先（无致死选项的）
    candidates = shuffle(candidates.filter(e => !e.options.some(o => o.death)));
  }

  // 愿景专属事件优先
  const visionEvents = candidates.filter(e => e.vision === state.vision);
  const otherEvents = candidates.filter(e => e.vision !== state.vision);
  candidates = shuffle([...visionEvents, ...visionEvents, ...otherEvents]); // 愿景事件权重 ×3

  // 权重 + 随机抽取 2-3 个
  const weighted = [];
  candidates.forEach(e => {
    for (let i = 0; i < (e.weight || 10); i++) weighted.push(e);
  });
  const shuffled = shuffle(weighted);
  const count = Math.min(2 + Math.floor(Math.random() * 2), shuffled.length); // 2-3个
  const picked = [];
  const seen = new Set();
  for (const e of shuffled) {
    if (picked.length >= count) break;
    if (!seen.has(e.id)) {
      seen.add(e.id);
      picked.push(e);
    }
  }
  return picked;
}

function renderLifeEvent(app) {
  const stages = RHYTHMS[state.rhythm].stages;
  const stageName = stages[state.stageIndex];
  const isLastStage = state.stageIndex >= stages.length - 1;

  if (state.currentEvents.length === 0) {
    state.currentEvents = filterEvents(stageName);
  }

  const progressPct = ((state.stageIndex) / stages.length) * 100;

  let html = `<div class="screen-content">
    <h2>${isLastStage ? '🌅 ' : '🎭 '}${stageName}</h2>
    <div class="stage-info">
      <span>阶段 ${state.stageIndex + 1}/${stages.length}</span>
      <span>${state.difficulty ? DIFFICULTIES[state.difficulty].icon + ' ' + DIFFICULTIES[state.difficulty].name : ''}</span>
    </div>
    <div class="progress-bar"><div class="progress-fill" style="width:${progressPct}%"></div></div>`;

  if (state.currentEvents.length === 0) {
    html += `<p style="text-align:center;color:#888;">这个阶段风平浪静...</p>
      <button class="btn primary" onclick="nextStage()">继续</button>`;
  } else {
    const event = state.currentEvents[0];
    html += `
      <div class="narrative">"${event.narrative}"</div>
      ${event.options.map((opt, i) => {
        const canChoose = checkOptionRequirement(opt);
        const cls = canChoose ? '' : 'locked';
        return `
          <div class="option-item ${cls}" ${canChoose ? `onclick="chooseEventOption(${i})"` : ''}>
            <div class="option-text">${String.fromCharCode(65 + i)}. ${opt.text}
              ${!canChoose ? '<span class="tag tag-locked">🔒</span>' : ''}
              ${opt.death ? '<span class="tag tag-negative">⚠ 致命</span>' : ''}
            </div>
            <div class="option-effects">
              ${formatOptionEffects(opt.effects)}
              ${!canChoose && opt.require ? `<span style="color:#666;">需要 ${Object.entries(opt.require).map(([k,v]) => `${k} ≥ ${v}`).join('，')}</span>` : ''}
            </div>
          </div>`;
      }).join('')}`;
  }

  // 当前属性概要
  html += `<div style="margin-top:16px;font-size:11px;color:#555;border-top:1px solid #1a1a2e;padding-top:8px;">
    力量 ${state.attrs.力量} · 智慧 ${state.attrs.智慧} · 魅力 ${state.attrs.魅力} · 敏捷 ${state.attrs.敏捷} · 体质 ${state.attrs.体质} · 运气 ${state.attrs.运气} | 财富 ${state.attrs.财富} · 声望 ${state.attrs.声望} · 道德 ${state.attrs.道德}
  </div>`;

  html += '</div>';
  app.innerHTML = html;
}

function checkOptionRequirement(opt) {
  if (!opt.require) return true;
  for (const [k, v] of Object.entries(opt.require)) {
    if ((state.attrs[k] || 0) < v) return false;
  }
  return true;
}

function formatOptionEffects(effects) {
  return Object.entries(effects).map(([k, v]) => {
    const sign = v > 0 ? '+' : '';
    let cls = 'effect-neutral';
    if (v > 0) cls = 'effect-pos';
    if (v < 0) cls = 'effect-neg';
    return `<span class="${cls}">${k} ${sign}${v}</span>`;
  }).join(' · ');
}

function chooseEventOption(idx) {
  const event = state.currentEvents[0];
  const option = event.options[idx];
  if (!checkOptionRequirement(option)) return;

  // 应用效果
  Object.entries(option.effects).forEach(([k, v]) => {
    if (state.attrs[k] !== undefined) {
      state.attrs[k] += v;
      // 边界限制
      if (['力量','智慧','魅力','敏捷','体质','运气'].includes(k)) {
        state.attrs[k] = Math.max(1, Math.min(200, state.attrs[k]));
      }
      if (k === '财富') state.attrs[k] = Math.max(0, Math.min(999999, state.attrs[k]));
      if (k === '声望') state.attrs[k] = Math.max(0, Math.min(1000, state.attrs[k]));
      if (k === '道德') state.attrs[k] = Math.max(-100, Math.min(100, state.attrs[k]));
    }
  });

  state.completedEvents.push(event.id);
  state.currentEvents.shift();

  // 检查死亡
  if (option.death) {
    state.screen = 'ending';
    state.deathCause = option.deathNarrative || '你的旅途在此终结。';
    state.deathType = 'accident';
    saveEnding();
    clearProgress();
    render();
    return;
  }

  // 检查愿景完成
  if (checkVisionComplete()) {
    state.screen = 'ending';
    state.deathType = 'vision';
    state.deathCause = '你完成了此生的使命！';
    saveEnding();
    clearProgress();
    render();
    return;
  }

  // 检查特殊终结
  if (state.attrs.道德 >= 100 || state.attrs.道德 <= -100) {
    state.screen = 'ending';
    state.deathType = state.attrs.道德 >= 100 ? 'special' : 'special';
    state.deathCause = state.attrs.道德 >= 100 ? '你的善行感化了整个大陆。' : '你已堕入深渊，再无回头之路。';
    saveEnding();
    clearProgress();
    render();
    return;
  }
  if (state.attrs.财富 <= 0) {
    state.screen = 'ending';
    state.deathType = 'special';
    state.deathCause = '你身无分文，在一个寒冷的冬夜静静睡去。';
    saveEnding();
    clearProgress();
    render();
    return;
  }

  // 当前阶段事件处理完毕
  if (state.currentEvents.length === 0) {
    // 自动下一阶段（最后一个阶段通过事件自然过渡到结局，或显示"继续"按钮）
  }

  saveProgress();
  render();
}

function nextStage() {
  const stages = RHYTHMS[state.rhythm].stages;
  if (state.stageIndex >= stages.length - 1) {
    // 结束人生
    state.screen = 'ending';
    state.deathType = 'natural';
    state.deathCause = '你在平静中走完了自己的一生。';
    saveEnding();
    clearProgress();
    render();
    return;
  }
  state.stageIndex++;
  state.currentEvents = [];
  saveProgress();
  render();
}

function checkVisionComplete() {
  const vision = VISIONS.find(v => v.id === state.vision);
  if (!vision) return false;
  for (const [k, v] of Object.entries(vision.condition)) {
    if ((state.attrs[k] || 0) < v) return false;
  }
  return true;
}
```

- [ ] **Step 3: 浏览器验证**

完整走一遍游戏：开局 → 进入人生阶段 → 看到事件 → 选择选项 → 属性变化 → 进入下一阶段。

- [ ] **Step 4: Commit**

```
git add index.html
git commit -m "feat: implement event engine and life stage screen with full event data"
```

---

### Task 8: 结局画面与评分系统

**Files:**
- Modify: `index.html`（替换 `renderEnding`，添加评分逻辑）

- [ ] **Step 1: 实现结局画面和评分**

替换占位的 `renderEnding`，并添加评分函数：

```javascript
// === Scoring ===

function calculateScore() {
  const sixAttrs = ['力量', '智慧', '魅力', '敏捷', '体质', '运气'];
  const avg = sixAttrs.reduce((s, k) => s + state.attrs[k], 0) / sixAttrs.length;
  let grade;
  if (avg >= 160) grade = 'SS';
  else if (avg >= 130) grade = 'S';
  else if (avg >= 100) grade = 'A';
  else if (avg >= 70) grade = 'B';
  else if (avg >= 40) grade = 'C';
  else grade = 'D';
  return grade;
}

function getAttrGrade(val) {
  if (val >= 180) return { grade: 'SS', cls: 'score-SS' };
  if (val >= 150) return { grade: 'S', cls: 'score-S' };
  if (val >= 120) return { grade: 'A', cls: 'score-A' };
  if (val >= 80)  return { grade: 'B', cls: 'score-B' };
  if (val >= 40)  return { grade: 'C', cls: 'score-C' };
  return { grade: 'D', cls: 'score-D' };
}

function getEndingTitle() {
  const diff = DIFFICULTIES[state.difficulty];
  const grade = calculateScore();
  if (state.deathType === 'vision') {
    const vision = VISIONS.find(v => v.id === state.vision);
    return vision ? vision.name : '命运之子';
  }
  const titles = {
    SS: '传奇不朽', S: '时代之光', A: '人中龙凤',
    B: '平凡之路', C: '坎坷一生', D: '悲剧人生',
  };
  return titles[grade] || '无人知晓';
}

function generateNarrative() {
  const vision = VISIONS.find(v => v.id === state.vision);
  const grade = calculateScore();
  const parts = [];

  if (state.deathType === 'accident') {
    parts.push(state.deathCause);
  } else if (state.deathType === 'natural') {
    parts.push('你在' + RHYTHMS[state.rhythm].stages[RHYTHMS[state.rhythm].stages.length - 1] + '安然离世。');
  } else if (state.deathType === 'vision') {
    parts.push('你完成了「' + (vision ? vision.name : '未知') + '」的愿景！');
  } else {
    parts.push(state.deathCause || '你的故事到此结束。');
  }

  if (vision && checkVisionComplete()) {
    parts.push('此生无憾矣。');
  } else if (vision) {
    parts.push('可惜你未能完成「' + vision.name + '」的愿景。');
  }

  if (state.attrs.财富 > 50000) parts.push('你留下了巨额财富。');
  if (state.attrs.声望 > 500) parts.push('你的名字被世人铭记。');
  if (Math.abs(state.attrs.道德) > 80) {
    parts.push(state.attrs.道德 > 0 ? '人们称你为圣者。' : '人们惧怕你的名字。');
  }

  return parts.join(' ');
}

function renderEnding(app) {
  const grade = calculateScore();
  const title = getEndingTitle();
  const vision = VISIONS.find(v => v.id === state.vision);
  const visionDone = checkVisionComplete();
  const sixAttrs = ['力量', '智慧', '魅力', '敏捷', '体质', '运气'];

  app.innerHTML = `<div class="screen-content">
    <div class="ending-header">
      <div style="font-size:14px;color:#666;margin-bottom:8px;">${DIFFICULTIES[state.difficulty].icon} ${DIFFICULTIES[state.difficulty].name} · ${RHYTHMS[state.rhythm].name}</div>
      <div class="ending-title">「${title}」</div>
      <div class="ending-grade">${grade}</div>
      <div style="font-size:14px;color:#c9a84c;">综合评分</div>
    </div>

    <div class="ending-narrative">
      <p>"${generateNarrative()}"</p>
    </div>

    <div style="margin-bottom:20px;">
      <h3>📊 属性评定</h3>
      ${sixAttrs.map(k => {
        const g = getAttrGrade(state.attrs[k]);
        return `
          <div class="attr-row">
            <span class="attr-name">${k}</span>
            <span style="font-size:14px;color:#ccc;">${state.attrs[k]}</span>
            <span class="score-badge ${g.cls}">${g.grade}</span>
          </div>`;
      }).join('')}
    </div>

    <div style="margin-bottom:20px;">
      <h3>🏛️ 社会成就</h3>
      <div class="attr-row">
        <span class="attr-name">财富</span>
        <span style="color:#ccc;">${state.attrs.财富.toLocaleString()} 金币</span>
      </div>
      <div class="attr-row">
        <span class="attr-name">声望</span>
        <span style="color:#ccc;">${state.attrs.声望}</span>
      </div>
      <div class="attr-row">
        <span class="attr-name">道德</span>
        <span style="color:${state.attrs.道德 >= 0 ? '#4caf50' : '#f44336'};">${state.attrs.道德 >= 0 ? '+' : ''}${state.attrs.道德}</span>
      </div>
    </div>

    <div style="margin-bottom:20px;">
      <h3>🌟 愿景</h3>
      <p>
        ${vision ? vision.icon + ' ' + vision.name + ' ' : ''}
        <span style="color:${visionDone ? '#4caf50' : '#f44336'};">
          ${visionDone ? '✅ 已完成' : '❌ 未完成'}
        </span>
      </p>
    </div>

    <button class="btn primary" onclick="startNewGame()">再来一局</button>
    <button class="btn" onclick="viewHistory()">生平回顾</button>
    <button class="btn secondary" onclick="state.screen='title';render();">返回标题</button>
  </div>`;
}
```

- [ ] **Step 2: 浏览器验证完整游戏循环**

从标题页开始，完整玩一局到结局，确认结局画面显示所有信息（评分、属性、社会成就、愿景状态）。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: implement ending screen with scoring and narrative generation"
```

---

### Task 9: 本地存储系统

**Files:**
- Modify: `index.html`（在 Helper Functions 区域添加存储函数）

- [ ] **Step 1: 实现存储函数**

在辅助函数区域添加：

```javascript
// === Storage ===

function saveProgress() {
  const progress = {
    difficulty: state.difficulty,
    rhythm: state.rhythm,
    race: state.race,
    identity: state.identity,
    vision: state.vision,
    talents: state.talents,
    attrs: { ...state.attrs },
    stageIndex: state.stageIndex,
    completedEvents: [...state.completedEvents],
    currentEvents: state.currentEvents.map(e => e.id),
    selectedTalents: [...state.selectedTalents],
    drawnTalents: [...state.drawnTalents],
    remainingRerolls: state.remainingRerolls,
    allocPoints: state.allocPoints,
    screen: state.screen,
  };
  try {
    localStorage.setItem('life_progress', JSON.stringify(progress));
  } catch (e) {
    console.warn('Failed to save progress:', e);
  }
}

function loadProgress() {
  try {
    const raw = localStorage.getItem('life_progress');
    if (!raw) return null;
    return JSON.parse(raw);
  } catch (e) {
    return null;
  }
}

function clearProgress() {
  try {
    localStorage.removeItem('life_progress');
  } catch (e) {}
}

function saveEnding() {
  const record = {
    id: new Date().toISOString().replace(/[:.]/g, '-'),
    race: RACES[state.race]?.name || state.race,
    identity: (IDENTITIES[state.race] || []).find(i => i.id === state.identity)?.name || state.identity,
    vision: VISIONS.find(v => v.id === state.vision)?.name || state.vision,
    difficulty: DIFFICULTIES[state.difficulty]?.name || state.difficulty,
    rhythm: RHYTHMS[state.rhythm]?.name || state.rhythm,
    talents: state.talents.map(tid => TALENTS.find(t => t.id === tid)?.name || tid),
    finalAttrs: { ...state.attrs },
    title: getEndingTitle(),
    score: calculateScore(),
    narrative: generateNarrative(),
    deathType: state.deathType,
    deathCause: state.deathCause,
    visionDone: checkVisionComplete(),
    timestamp: Date.now(),
  };
  try {
    let history = [];
    const raw = localStorage.getItem('life_history');
    if (raw) history = JSON.parse(raw);
    history.push(record);
    // 最多保存50条
    if (history.length > 50) history = history.slice(-50);
    localStorage.setItem('life_history', JSON.stringify(history));
  } catch (e) {
    console.warn('Failed to save ending:', e);
  }
}

function loadHistory() {
  try {
    const raw = localStorage.getItem('life_history');
    return raw ? JSON.parse(raw).reverse() : [];
  } catch (e) {
    return [];
  }
}
```

- [ ] **Step 2: Commit**

```
git add index.html
git commit -m "feat: implement localStorage save/load for progress and history"
```

---

### Task 10: 生平回顾屏幕

**Files:**
- Modify: `index.html`（替换 `renderHistory`）

- [ ] **Step 1: 实现生平回顾**

替换占位的 `renderHistory`：

```javascript
function renderHistory(app) {
  const history = loadHistory();

  app.innerHTML = `<div class="screen-content">
    <h2>📜 生平回顾</h2>
    <p class="subtitle">${history.length === 0 ? '还没有任何记录' : `共 ${history.length} 条人生记录`}</p>
    ${history.length === 0 ? `
      <div style="text-align:center;padding:40px 0;">
        <p style="font-size:48px;margin-bottom:16px;">📖</p>
        <p style="color:#666;">开始你的第一局游戏吧</p>
      </div>
    ` : history.map(h => `
      <div class="history-item">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <span class="history-title">「${h.title}」</span>
          <span class="score-badge score-${h.score}">${h.score}</span>
        </div>
        <div class="history-meta">
          ${h.race} · ${h.identity} · ${h.difficulty} · ${h.rhythm}
        </div>
        <div class="history-meta">
          愿景：${h.vision} ${h.visionDone ? '✅' : '❌'} | ${new Date(h.timestamp).toLocaleDateString('zh-CN')}
        </div>
        <p style="font-size:12px;color:#888;margin-top:4px;font-style:italic;">"${h.narrative}"</p>
      </div>
    `).join('')}
    <button class="btn secondary" onclick="state.screen='title';render();">返回标题</button>
  </div>`;
}
```

- [ ] **Step 2: 验证**

玩一局到结局 → 返回标题 → 生平回顾 → 确认记录显示。测试"继续游戏"：玩游戏到某个阶段 → 关闭页面 → 重新打开 → 点击"继续游戏" → 应回到之前的阶段。

- [ ] **Step 3: Commit**

```
git add index.html
git commit -m "feat: implement history review screen"
```

---

### Task 11: 收尾打磨

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 补充边缘事件，确保每个阶段都有足够事件**

检查事件数据覆盖所有阶段。现有事件已覆盖婴儿/幼年、童年、少年、青年、壮年、中年、中老年、老年 + 通用事件（跨阶段）。确认精简型（5阶段）和标准型（8阶段）的每个阶段都至少有3-5个候选事件。

- [ ] **Step 2: 补充事件数据**

追加约10个额外事件，填补空白阶段，提高多样性：

```javascript
// 在 EVENTS 数组中追加以下事件：
{
  id: 'baby_04', stages: ['婴儿'], minAttr: {}, vision: '', difficulty: '',
  weight: 6, title: '洗礼仪式',
  narrative: '按照传统，新生儿需要接受牧师的洗礼。',
  options: [
    { text: '接受祝福', require: {}, effects: { 运气: 5, 道德: 3 }, narrative: '你得到了神明的祝福。' },
    { text: '家人不愿参加', require: {}, effects: { 道德: -2 }, narrative: '你的家人对教会心存芥蒂。' },
  ],
},
{
  id: 'child_04', stages: ['童年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '森林迷路',
  narrative: '你和伙伴们在森林里走散了。',
  options: [
    { text: '寻找出路', require: {}, effects: { 敏捷: 5, 智慧: 3 }, narrative: '你靠着自己的判断找到了回家的路。' },
    { text: '等待救援', require: {}, effects: { 运气: 5 }, narrative: '搜救队在天黑前找到了你。' },
  ],
},
{
  id: 'youth_04', stages: ['少年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '初恋',
  narrative: '你的心跳漏了一拍。那个人的微笑让你难以忘怀。',
  options: [
    { text: '勇敢表达', require: { 魅力: 50 }, effects: { 魅力: 8, 运气: 5 }, narrative: '你们许下了青涩的誓言。' },
    { text: '埋藏心底', require: {}, effects: { 智慧: 3 }, narrative: '有些故事还没开始就结束了。' },
  ],
},
{
  id: 'adult_04', stages: ['青年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '神秘委托',
  narrative: '一个戴着面具的陌生人递给你一封信。"报酬很丰厚——前提是你活着回来。"',
  options: [
    { text: '接下委托', require: { 敏捷: 70 }, effects: { 财富: 2000, 声望: 20, 体质: -5 }, narrative: '你完成了九死一生的任务。' },
    { text: '拒绝', require: {}, effects: { 运气: 3 }, narrative: '后来你听说那个委托人坑了不少冒险者。' },
  ],
},
{
  id: 'prime_04', stages: ['壮年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '旧友重逢',
  narrative: '一位多年未见的老友出现在你门前。他看起来苍老了许多。',
  options: [
    { text: '热情款待', require: {}, effects: { 魅力: 5, 道德: 8, 财富: -100 }, narrative: '你花了不少钱，但友情无价。' },
    { text: '冷漠以对', require: {}, effects: { 魅力: -3 }, narrative: '老友失望地离开了。' },
    { text: '借他一大笔钱', require: { 财富: 5000 }, effects: { 财富: -3000, 声望: 30 }, narrative: '老友感激涕零，逢人便宣扬你的慷慨。' },
  ],
},
{
  id: 'mid_03', stages: ['中年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '子女的前程',
  narrative: '你的孩子长大了，面临人生的第一个重大选择。',
  options: [
    { text: '支持他追梦', require: {}, effects: { 道德: 10, 财富: -500 }, narrative: '你全力支持，不让他有遗憾。' },
    { text: '让他走你安排的路', require: { 声望: 200 }, effects: { 声望: 20, 道德: -5 }, narrative: '你动用人脉铺好了路，但他未必快乐。' },
  ],
},
{
  id: 'late_03', stages: ['中老年'], minAttr: {}, vision: '', difficulty: '',
  weight: 8, title: '故地重游',
  narrative: '你回到了阔别数十年的故乡。',
  options: [
    { text: '重建家乡', require: { 财富: 10000 }, effects: { 财富: -5000, 声望: 60, 道德: 20 }, narrative: '你一掷千金，让家乡焕然一新。' },
    { text: '默默怀念', require: {}, effects: { 道德: 5 }, narrative: '物是人非，你感慨万千。' },
  ],
},
{
  id: 'old_03', stages: ['老年'], minAttr: {}, vision: '', difficulty: '',
  weight: 7, title: '临终遗嘱',
  narrative: '你召集了所有重要的人，准备立下遗嘱。',
  options: [
    { text: '平分遗产', require: {}, effects: { 声望: 30, 道德: 15 }, narrative: '人人有份，皆大欢喜。' },
    { text: '捐给王国', require: { 财富: 50000 }, effects: { 财富: -50000, 声望: 80 }, narrative: '你的名字被刻在了王国广场的纪念碑上。' },
  ],
},
{
  id: 'common_04', stages: ['青年', '壮年', '中年'], minAttr: {}, vision: '', difficulty: '',
  weight: 5, title: '暴风雨之夜',
  narrative: '一场百年难遇的暴风雨席卷了城镇。',
  options: [
    { text: '救助灾民', require: { 体质: 60 }, effects: { 声望: 25, 道德: 15 }, narrative: '你冒着生命危险救出了十几个人。' },
    { text: '加固自己的房屋', require: {}, effects: { 体质: 5 }, narrative: '你保住了自己的财产。' },
    { text: '趁机发灾难财', require: {}, effects: { 财富: 1000, 道德: -20, 声望: -15 }, narrative: '你趁乱收购了大量物资，高价卖出。' },
  ],
},
{
  id: 'common_05', stages: ['少年', '青年'], minAttr: {}, vision: '', difficulty: '',
  weight: 5, title: '名师指点',
  narrative: '一位德高望重的老师注意到了你的潜力。',
  options: [
    { text: '拜师学艺', require: {}, effects: { 智慧: 8, 声望: 10, 财富: -100 }, narrative: '你花了些钱拜师，但收获远超付出。' },
    { text: '自学成才', require: {}, effects: { 智慧: 5, 运气: 3 }, narrative: '你坚信自己能走出一条路。' },
  ],
},
```

- [ ] **Step 3: 应用种族属性加成到初始属性（如体力→体质等）**

检查事件数据中的属性门槛（如 `{ 体力: 30 }`）与属性系统的一致性。将 `体力` 改为 `体质`：

```
修改事件 `old_02` 的 minAttr 从 `{ 体力: 30 }` 改为 `{ 体质: 30 }`
```

- [ ] **Step 4: 完善 mobile 适配**

在 CSS 中追加：

```css
@media (max-width: 374px) {
  body { padding: 8px; }
  h1 { font-size: 24px; }
  .card { padding: 12px; }
}
```

- [ ] **Step 5: 全流程测试**

完整测试：新游戏 → 各难度 → 各节奏 → 完整人生 → 结局 → 生平回顾 → 继续游戏。验证 localStorage 存取正常。

- [ ] **Step 6: Commit**

```
git add index.html
git commit -m "feat: add remaining events, fix attribute consistency, polish mobile styles"
```
