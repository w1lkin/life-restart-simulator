# 人生重开模拟器（Life Restart Simulator）

纯前端单机文字养成游戏：随机天赋与属性，模拟一段人生轨迹。

## 单机版特性

- **纯静态**：`index.html`（内联 CSS/JS）+ `share-card.html`，零依赖、无构建步骤。
- **无需联网**：模拟逻辑全部在浏览器本地运行。
- **数据本地**：无账号、无后端，结果即时生成。
- **分享卡**：`share-card.html` 生成 1200×1600 分享图（二维码需联网）。

## 本地运行

```sh
cd life-restart-simulator
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

> 分享卡片的二维码依赖 `api.qrserver.com`，必须经 `http(s)` 来源加载，请用上面的本地服务器方式，不要直接 `file://` 打开。

## 文件结构

```
life-restart-simulator/
├── index.html       # 主游戏
└── share-card.html  # 分享卡片生成
```

## 部署

可部署到 Cloudflare Pages。

## 版本

当前分支：`release/1.0.0`
