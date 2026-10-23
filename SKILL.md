---
name: guofeng-poem-poster
description: "Generate a 9:16 vertical Chinese classical poetry poster from just a poem title. Analyzes the poem (poetic relationship, dramatic eye, imagery, three-tier color palette, mood, composition, text layout, chosen verses) and outputs a complete poster prompt where AI generates the entire poster in one shot — illustration, title, verse, author signature and red seal all rendered together. Follows 新中式细线设色装饰插画 aesthetic with traditional Chinese colors (stone blue, malachite green, cinnabar, ochre red, rouge, gamboge). Use when user wants a poster from a Chinese classical poem. Triggers on phrases like '《静夜思》国风海报', '生成《浪淘沙》海报', '做成海报', '古诗词海报', 'guofeng poetry poster'. NOT for videos, NOT for storyboards, NOT for prompt-only outputs."
version: 2.0.0
agent_created: true
---

# 古诗词国风海报生成

输入一首古诗词的名称，让 AI **一次性直出完整海报**——插画、标题、诗句、作者署名、朱印全部由模型在同一张图中生成，采用新中式细线设色装饰插画风格。

本 skill 的输出是一段**完整海报提示词**，用户丢进 MJ / Flux / ComfyUI / Agnes 就能一步到位得到成品。不再走"AI 生成无字底图 + HTML 后期叠字"的路子（HTML 模板保留作为兜底方案，见末尾）。

## 什么时候用这个 Skill

用户明确表达「想从古诗词做海报」时触发：

- 「帮我做《静夜思》的国风海报」
- 「生成《浪淘沙》海报」
- 「把《水调歌头·明月几时有》做成海报」
- 「输入《如梦令》，出海报」
- 「古诗词海报」「国风诗词海报」「古风海报」+ 一首诗名
- 用户只说「海报」但上下文清楚是指古诗词海报

不要在以下场景触发：

- 用户要**视频**或**分镜**（走 `poetry-comfyui-pipeline` 或 `songci-guofeng-visualizer`）
- 用户只要**分镜提示词**（走 `video-shotcraft`）
- 纯诗词**赏析 / 翻译 / 讲解**（文学问题，不需要海报）
- 现代诗、散文、外语诗歌（超出范围，可礼貌说明并询问是否借用宋诗视觉法）

## 输入处理

用户可能给的形式：

- 完整诗名：《静夜思》、《浪淘沙》、《声声慢·寻寻觅觅》
- 词牌名 + 首句：《如梦令·昨夜雨疏风骤》
- 只有词牌名：《如梦令》——多首同名时选最常见一首，注明假设
- 直接粘贴诗句原文：从首句识别作品

如果不确定原文，用内部知识给出最常见的版本；实在拿不准（多首同名、生僻、异体字冲突），先跟用户确认，不要硬编全文。

## 工作流

### Step 1：回忆并输出原文

把古诗词完整原文列出（含作者、朝代）。原文要准确，不要漏句、不要错字。用户没要求赏析，就不要展开讲词意，只列原文。

### Step 2：按分析指南做深度拆解

加载 `references/poem-analysis-guide.md`，按其中的框架完成以下 8 项拆解：

1. **诗意关系**——远近/高低/动静/冷暖/聚散/时间变化/情感转折/虚实（选一主导 + 一两个辅助）
2. **戏眼**——主角的一个具体动作或瞬间
3. **核心意象**——2–4 个具体可拍的画面元素
4. **色彩层级**——主色 + 辅助色 + 点睛色（用传统色：石青/石绿/朱砂/赭红/胭脂/藤黄/花青/暖米白）
5. **情绪氛围**——一个词定性 + 一句画面化描述
6. **构图规划**——横向三分 / 纵向散点 / 对角线 / 圆形构图，明确观看路径
7. **文字版式规划**——标题/诗句/署名/朱印的位置、字号、纵排方向
8. **入画佳句**——从全诗中挑 1–2 句最有画面感、最能代表全诗情绪的句子

拆解结果先在回复里给用户看一次，让用户能一眼确认方向对不对；用户没异议后进入 Step 3。

### Step 3：生成完整海报提示词

加载 `references/poster-prompt-template.md`，按其中的 8 章节模板结构填好一份**完整海报提示词**。

关键铁律：

- **AI 直出文字**——提示词必须明确要求"文字真实出现在最终画面中"，不写 `no text`
- **禁错字/伪汉字**——负面提示词明确排除 `garbled text, misspelled chinese, deformed characters, pseudo-chinese`
- **9:16 竖版**——比例参数写死
- **传统色**——用石青/石绿/朱砂/赭红/胭脂/藤黄/花青/暖米白，不用现代色
- **暖米白留白占 20-30%**——留白参与空间组织
- **文字位置明确**——标题/诗句/署名/朱印的位置、字号、排布都写清楚
- **禁摄影写实/水墨/3D/油画/动漫**——负面提示词明确排除
- **不装饰性堆意象**——不为凑气氛加龙/凤/仙鹤/祥云

产出格式（示例，详见 `references/poster-prompt-template.md` 完整章节）：

```
【古诗词】
标题：{诗词标题}
作者：{作者}
入画原文：{本次希望呈现在画面中的诗句}

【核心意象】{2-4 个具体可拍的意象}

【主色调】{主色 + 辅助色 + 点睛色}

【情绪氛围】{一个词定性 + 一句画面化描述}

【画幅比例】9:16

根据以上古诗词，创作一张完整的新中式细线设色装饰插画海报……

## 一、诗意与场景
{具体说明诗意关系、季节、时段、环境、人物活动、情绪关系}

## 二、构图与空间组织
{具体说明视角、散点透视、观看路径、留白位置}

## 三、树木、花卉与自然景物
{具体说明植物种类、形态、层次}

## 四、建筑、人物与地面水面
{具体说明建筑类型、人物动作、留白位置}

## 五、色彩与绘画方式
{具体说明主色、辅助色、点睛色、留白比例}

## 六、细节主次与稀碎感控制
{默认段，见模板}

## 七、文字版式与位置
{具体说明标题、诗句、署名、朱印的位置、字号、排布}

## 八、整体限制与完成标准
{默认段，见模板}
```

### Step 4：交付并给出用户操作指引

生成一个文件到 `./guofeng-posters/{poem-title-slug}/`：

```
guofeng-posters/{poem-title-slug}/
└── prompt.md          # Step 3 产出的完整海报提示词 + 多后端参数
```

同时输出目录结构和一个 3 步使用指引：

1. 打开 `prompt.md`，把"完整海报提示词"复制到 Midjourney（推荐 `--ar 9:16 --v 6.1 --style raw`）或 Flux
2. 生成图片，检查标题、诗句、作者署名、朱印是否准确（原文的"完成标准"）
3. 如果出现错字/漏字，让模型针对文字区域重新生成；如果文字渲染完全失败，切换到 HTML 兜底方案（见下文）

## 与 HTML 兜底方案的分工

- **主方案（Step 3 提示词）**：让 AI 一次性直出完整海报。适用于 Midjourney v6+、Flux、部分优化后的 SDXL、Agnes 等对中文文字渲染较好的模型。一步到位，成品感强。
- **兜底方案（`assets/poster-template.html`）**：当用户用的模型中文文字渲染较差（如未配 LoRA/ControlNet 的 SDXL），先生成"无字底图"，再用 HTML 模板把诗句叠上去。

**选择建议**：先用本 skill 的完整提示词跑一次 MJ / Flux，如果文字不理想再切换到 HTML 方案。

## 输出自检清单

交付前逐条检查：

- [ ] 诗词原文准确，无错字、无漏句
- [ ] 8 项拆解（诗意关系/戏眼/意象/色彩层级/情绪/构图/文字版式/佳句）齐全
- [ ] 提示词明确要求"文字真实出现在画面中"
- [ ] 提示词明确禁"错字/伪汉字/日文字混入"
- [ ] 明确画幅 9:16
- [ ] 主色用传统色词（石青/石绿/朱砂/赭红/胭脂/藤黄/花青）
- [ ] 明确暖米白留白（20-30%）
- [ ] 明确标题/诗句/署名/朱印的位置和排布
- [ ] 未使用"极致美学/高级感拉满/震撼视觉/masterpiece/8k"等空词
- [ ] 未使用"电影感/电影级/cinematic"等暗示摄影写实的词
- [ ] 未使用"仙侠/宫斗/大女主/仙鹤/凤凰/魔法"等偏离新中式插画的元素
- [ ] 交付指引清楚，用户能自己完成最后一步

## Anti-Slop 规则

生成内容与输出时避开这些 AI 味和风格偏移：

- 不写"极致美学""高级感拉满""震撼视觉"这类空词，除非用户明确要
- 不写"意境深远""情感饱满"这种文学腔，写具体画面
- 提示词里不堆 `masterpiece, 8k, best quality, ultra-detailed` 等无意义词
- 不把宋诗硬套成唐诗美学（唐代更华丽、宋代理性、明清风雅）
- **禁摄影写实**——不写"电影感""电影级""cinematic""photorealistic""natural lighting"
- **禁厚重水墨**——不写"ink wash""watercolor bleed""wet ink splash"
- **禁 3D/CGI/油画**——不写"3d render""cgi""oil painting""thick brushstrokes"
- **禁动漫风**——不写"anime""manga""cartoon""pixar"
- **禁做旧质感**——不写"vintage film wear""old photo texture""burnt edges"
- **禁装饰性意象**——不为凑气氛加龙、凤、仙鹤、祥云、魔法特效
- 情绪不能落回情绪，要写成画面里的具体元素（见分析指南）
- 不确定原作风格归属时（比如一首诗有异文），先问用户不要猜

## 与已有 Skill 的分工

| Skill | 什么时候用 |
|---|---|
| **本 skill** (`guofeng-poem-poster`) | 想要一张 9:16 竖版**完整海报成品**（AI 一次性直出，含诗句入画） |
| `songci-guofeng-visualizer` | 只想要**分镜提示词**（MJ + Seedance 视频），不做海报 |
| `poetry-comfyui-pipeline` | 想要**完整视频流水线**（分镜+生图+生视频），走本地 ComfyUI |
| `video-shotcraft` | 想要**视频分镜提示词**（非诗词题材通用） |

## 参考文件

- `references/poem-analysis-guide.md`：诗词分析框架，含诗意关系/戏眼/意象/色彩层级/情绪/构图/文字版式/佳句的判定原则
- `references/poster-prompt-template.md`：完整海报提示词模板（含 8 章节结构、传统色词库、多后端参数、通用负面提示词、《浪淘沙》完整示例）
- `assets/poster-template.html`：兜底方案——当 AI 中文文字渲染差时，用 HTML 模板后期叠字
