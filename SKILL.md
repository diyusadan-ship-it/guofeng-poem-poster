---
name: guofeng-poem-poster
description: "Generate a 9:16 vertical Chinese classical poetry poster package from just a poem title. Analyzes the poem to extract dramatic eye, imagery, palette, mood and chosen verses; outputs an English background-image prompt (no text, generous negative space, 9:16) plus a self-contained HTML poster template with calligraphy typography for verse overlay. Use when user wants a poster from a Chinese classical poem. Triggers on phrases like '《静夜思》国风海报', '生成《声声慢》海报', '做成海报', '古诗词海报', 'guofeng poetry poster'. NOT for videos, NOT for storyboards, NOT for prompt-only outputs."
version: 1.0.0
agent_created: true
---

# 古诗词国风海报生成

输入一首古诗词的名称，输出一整套「海报生产包」：背景图提示词 + 自包含 HTML 海报模板 + 使用说明。用户拿到后，自行把提示词丢进 MJ / ComfyUI / ImageGen 等工具生成背景图，再把背景图丢进 HTML 模板截图，就得到一张 9:16 竖版中式海报。

本 skill **只负责产出可复制的提示词与可执行的海报模板**，不自动调用任何后端生图 API。这样用户可以把生成后端留在自己的工具箱里，灵活可控。

## 什么时候用这个 Skill

用户明确表达「想从古诗词做海报」时触发：

- 「帮我做《静夜思》的国风海报」
- 「生成《声声慢》海报」
- 「把《水调歌头·明月几时有》做成海报」
- 「输入《如梦令》，出海报」
- 「古诗词海报」「国风诗词海报」「古风海报」+ 一首诗名
- 用户只说「海报」但上下文清楚是指古诗词海报

不要在以下场景触发：

- 用户要**视频**或**分镜**（走 `poetry-comfyui-pipeline` 或 `songci-guofeng-visualizer`）
- 用户只要**提示词**、不要海报成品（走 `songci-guofeng-visualizer`）
- 纯诗词**赏析 / 翻译 / 讲解**（文学问题，不需要海报）
- 现代诗、散文、外语诗歌（超出范围，可礼貌说明并询问是否借用宋诗视觉法）

## 输入处理

用户可能给的形式：

- 完整诗名：《静夜思》、《声声慢·寻寻觅觅》
- 词牌名 + 首句：《如梦令·昨夜雨疏风骤》
- 只有词牌名：《如梦令》——多首同名时选最常见一首，注明假设
- 直接粘贴诗句原文：从首句识别作品

如果不确定原文，用内部知识给出最常见的版本；实在拿不准（多首同名、生僻、异体字冲突），先跟用户确认，不要硬编全文。

## 工作流

### Step 1：回忆并输出原文

把古诗词完整原文列出（含作者、朝代）。原文要准确，不要漏句、不要错字。用户没要求赏析，就不要展开讲词意，只列原文。

### Step 2：按分析指南做深度拆解

加载 `references/poem-analysis-guide.md`，按其中的框架完成以下 5 项拆解，产出结构化的中间产物：

1. **戏眼**——全诗最核心的一个动作或意象（一句话说清）
2. **核心意象**——2–4 个具体可拍的画面元素（花、月、酒、剑、舟、烛、帘……）
3. **主色调**——2–3 个主色（写具体名，如"墨蓝、月白、烛黄"，不写"高级灰"这种空词）
4. **情绪氛围**——一个词定性（如"孤寂""清旷""旷达""怅惘"），配一句具体化描述
5. **入画佳句**——从全诗中挑 1–2 句最有画面感、最能代表全诗情绪的句子

拆解结果先在回复里给用户看一次，让用户能一眼确认方向对不对；用户没异议后进入 Step 3。

### Step 3：生成背景图提示词

加载 `references/background-prompt-template.md`，按其中的模板结构填好一份**背景图提示词**。

关键铁律：

- **画面绝对无文字**——所有 prompt 末尾必须追加 `no text, no letters, no chinese characters, no chinese calligraphy, no watermarks, no subtitles`
- **9:16 竖版**——比例参数写死
- **只画背景与主体动作**——人物可以有但只占画面一小部分（人物占比 ≤ 40%），留出大片空间给用户后期排版诗句
- **不写具体汉字内容**——不写"月"、"酒"这种会诱导 AI 乱画汉字的词，改用意象描述（月光洒在庭院、酒盏倾在案头）

产出格式：

```
【背景图提示词 · 《XXX》】

英文主提示词（MJ/通用生图工具通用）：
{一段完整的英文提示词，含风格、构图、光影、色彩、材质、比例参数}

负面提示词：
{一段负面提示词，明确排除汉字、字幕、水印、仙侠、宫斗、大女主等}

推荐后端与参数：
- Midjourney: --ar 9:16 --v 6.1 --style raw
- ComfyUI (SDXL): width=768, height=1344, steps=30, cfg=6
- ComfyUI (Flux): width=832, height=1472, steps=28, cfg=3.5
- ImageGen 通用：9:16 竖版
```

### Step 4：生成 HTML 海报模板

复制 `assets/poster-template.html` 到用户可见的输出目录（推荐 `./guofeng-posters/{poem-title-slug}/poster.html`），把以下字段替换进模板中的 `data-*` 属性或 `PLACEHOLDER_*` 占位符：

- `{{POEM_TITLE}}`：诗词标题（如"静夜思"）
- `{{POEM_AUTHOR}}`：作者 + 朝代（如"李白 · 唐"）
- `{{POEM_VERSES}}`：入画佳句（1–2 句，用 `\n` 换行）
- `{{FULL_TEXT}}`：完整原文（可选，用于背面/详情页）
- `{{BACKGROUND_PATH}}`：用户后续填的背景图路径（先给一个内置占位图 URL）

模板特性（在模板内已完成，不要在 skill 正文重复实现）：

- Google Fonts 加载开源中文字体：Ma Shan Zheng（毛笔书法体）、ZCOOL XiaoWei（现代国风）、Noto Serif SC（宋体）
- 竖排文字（`writing-mode: vertical-rl`），符合中文传统排版
- 右下角朱红印章
- 左侧留白区给诗句、右侧留白区给主体画面（或反过来，视背景图而定）
- 9:16 竖版比例（768×1344 或 1080×1920）
- 内置一键"打印为 PNG"按钮（`window.print()`），浏览器打印对话框里可选另存为 PDF/PNG
- 支持深色背景与浅色背景两种主题（class 切换）

### Step 5：交付并给出用户操作指引

生成两个文件到 `./guofeng-posters/{poem-title-slug}/`：

```
guofeng-posters/{poem-title-slug}/
├── prompt.md          # Step 3 产出的完整背景图提示词
└── poster.html        # Step 4 生成的可编辑 HTML 海报
```

同时输出目录结构和一个 3 步使用指引：

1. 打开 `prompt.md`，把英文提示词复制到 MJ / ComfyUI / ImageGen 生成一张 9:16 背景图，命名 `background.jpg`，放到 `poster.html` 同目录
2. 打开 `poster.html`，在 HTML 里把 `data-background-src` 属性值改成 `background.jpg`
3. 浏览器打开 `poster.html`，点右上角"打印为 PNG"按钮，在打印对话框选"另存为 PDF"（或直接截图），得到 9:16 竖版海报成品

## 输出自检清单

交付前逐条检查：

- [ ] 诗词原文准确，无错字、无漏句
- [ ] 戏眼、意象、主色、情绪、佳句 5 项齐全
- [ ] 背景图提示词明确写了"no text, no chinese characters"
- [ ] 提示词指定了 9:16 竖版
- [ ] 人物占比不超过 40%，画面留白够排版
- [ ] 未默认仙侠、宫斗、大女主化
- [ ] HTML 模板所有占位符都替换成了具体值（无 `{{...}}` 残留）
- [ ] HTML 模板中文字体通过 Google Fonts 加载，无外链失效风险
- [ ] 交付指引清楚，用户能自己完成最后一步

## Anti-Slop 规则

生成内容与输出时避开这些 AI 味：

- 不写"极致美学""高级感拉满""震撼视觉"这类空词，除非用户明确要
- 不写"意境深远""情感饱满"这种文学腔，写具体画面
- 提示词里不堆 `masterpiece, 8k, best quality, ultra-detailed` 等无意义词
- 不把宋诗硬套成唐诗美学（唐代更华丽、宋代理性、明清风雅）
- 不为追求"电影感"给画面加金色滤镜；宋诗要低饱和自然光
- 不让人物做戏剧化摆拍动作；宋诗人物是"生活中的人"
- 不为凑气氛加龙、凤、仙鹤等特效元素（除非诗里明确出现）
- 不确定原作风格归属时（比如一首诗有异文），先问用户不要猜

## 与已有 Skill 的分工

| Skill | 什么时候用 |
|---|---|
| **本 skill** (`guofeng-poem-poster`) | 想要一张 9:16 竖版**海报成品**，含诗句入画 |
| `songci-guofeng-visualizer` | 只想要**提示词**（MJ + Seedance 视频），不做海报 |
| `poetry-comfyui-pipeline` | 想要**完整视频流水线**（分镜+生图+生视频），走本地 ComfyUI |
| `video-shotcraft` | 想要**视频分镜提示词**（非诗词题材通用） |

## 参考文件

- `references/poem-analysis-guide.md`：诗词分析框架，含戏眼/意象/色彩/情绪/佳句的判定原则
- `references/background-prompt-template.md`：背景图提示词模板，含多后端参数适配
- `assets/poster-template.html`：自包含 HTML 海报模板，替换占位符即可用
