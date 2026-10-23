# 国风古诗词海报 Skill · guofeng-poem-poster

一个 WorkBuddy Skill 包：输入一首古诗词的名字，输出「9:16 竖版国风海报生产包」——**背景图提示词 + 自包含 HTML 海报模板**。

## 灵感来源

X 帖 [@leo_xiaolei](https://x.com/leo_xiaolei/status/2113471465429561416) 的"中式海报风格"提示词模板。

## 设计思路

传统 AI 生图无法稳定渲染汉字——所以这个 Skill **不硬让 AI 画字**，而是分两半：

1. **背景图**：给一份精致的英文提示词（明确要求 `no text, no chinese characters`），留白给用户后期排版
2. **文字层**：用 HTML + CSS 把诗句竖排排到背景上，字体走 Google Fonts 的开源书法体（Ma Shan Zheng / ZCOOL XiaoWei / Noto Serif SC）

## 使用方式

安装到 WorkBuddy：

```bash
# 把这个目录复制到用户级 skills 目录
cp -r guofeng-poem-poster ~/.workbuddy/skills/
```

然后跟 WorkBuddy 说：

> 帮我做《静夜思》的国风海报

Skill 会自动加载，走 5 步工作流（回忆原文 → 深度拆解 → 生成背景图提示词 → 生成 HTML 海报 → 交付），产出：

```
./guofeng-posters/静夜思/
├── prompt.md    # 背景图英文提示词 + 多后端参数
└── poster.html  # 自包含 HTML 海报模板
```

用户拿到后 3 步出成品：

1. 把 `prompt.md` 里的英文提示词丢进 MJ / ComfyUI / ImageGen 生成背景图
2. 把背景图放到 `poster.html` 同目录，改一下 `data-background-src`
3. 浏览器打开 `poster.html`，点"打印 / 另存 PDF"按钮

## 文件结构

```
guofeng-poem-poster/
├── SKILL.md                             # Skill 主文件（触发条件 + 工作流 + anti-slop 规则）
├── references/
│   ├── poem-analysis-guide.md           # 诗词分析框架（戏眼 / 意象 / 主色 / 情绪 / 佳句）
│   └── background-prompt-template.md    # 背景图提示词模板（MJ / SDXL / Flux / Agnes 参数）
└── assets/
    └── poster-template.html             # 自包含 HTML 海报模板（含书法字体、竖排、印章、导出按钮）
```

## 与相关 Skill 的分工

| Skill | 用途 |
|---|---|
| **本 Skill** | 一张 9:16 竖版**海报成品**，含诗句入画 |
| `songci-guofeng-visualizer` | 只要**提示词**（MJ + Seedance 视频），不做海报 |
| `poetry-comfyui-pipeline` | **完整视频流水线**（分镜 + 生图 + 生视频），走本地 ComfyUI |

## 版本

- 1.0.0 — 初始版本

## 授权

MIT License
