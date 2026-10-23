# 背景图提示词模板

按本文件生成一份可直接复制的背景图提示词。所有变量用 Step 2 拆解结果填充，占位符用 `{{...}}` 表示。

## 提示词模板（英文主提示词）

```
{POETIC_SUBJECT_DESC}, {DRAMATIC_FOCAL_POINT}, {SCENE_SETTING},
{LIGHTING_AND_TIME}, {PALETTE: {{PALETTE_EN}}},
{COMPOSITION_NOTE: vertical 9:16 poster, generous negative space for typography overlay,
subject occupying no more than 40% of the frame},
traditional Chinese aesthetic painting meets cinematic photography,
{{ERA_QUALITY}}, low saturation color grading,
natural light only, no studio lighting, no golden filter,
realistic fabric texture, natural skin texture, subtle film grain,
soft depth of field, quiet contemplative mood,
{NEGATIVE_HINTS_INLINE: no text, no chinese characters, no chinese calligraphy,
no letters, no subtitles, no watermarks, no logos, no branding},
--ar 9:16
```

## 变量填充指南

### `POETIC_SUBJECT_DESC`

一句话描述画面主体（谁在做什么）。

- 例：`A solitary young poet sitting by a wooden window`
- 例：`A lonely fisherman in a straw cape rowing a small boat`
- 例：`An old scholar pouring wine toward the full moon`

### `DRAMATIC_FOCAL_POINT`

戏眼的英文具体化——一个动作、一个瞬间。

- 例：`his hand resting on the window frame, gazing up at the moon`
- 例：`raising a wine cup toward the sky in a silent question`
- 例：`head bowed, a single tear falling onto the chess board`

### `SCENE_SETTING`

场景 + 时代质感。宋诗用宋代，唐诗用唐代，别串。

**宋代锚点库**：
`Song dynasty lifestyle aesthetic, wooden window lattice, bamboo blind, celadon wine cup, low wooden couch, bronze incense burner, plain silk robe, osmanthus tree in courtyard, wet stone path, aged wooden door, grey-white walls`

**唐代锚点库**：
`Tang dynasty aesthetic, grander architecture, silk brocade robe, jade hairpin, bronze mirror, lantern with tassel, imperial courtyard`

### `LIGHTING_AND_TIME`

自然光，不要影棚光。

- 雨后清晨：`soft post-rain morning light, misty air`
- 午后斜阳：`warm afternoon slanting sun, long shadows`
- 月夜：`cool moonlight, deep blue night sky`
- 烛光夜：`warm candlelight against darkness, flickering glow`
- 雪夜：`cold diffused snow light, muted whites`

### `PALETTE_EN`

把主色调翻成英文，用逗号分隔。

- "墨蓝 + 月白 + 烛黄" → `muted indigo, moonlit white, candlelight amber`
- "嫩绿 + 淡鹅黄" → `fresh spring green, pale cream yellow`
- "陈木褐 + 素绢白" → `aged wood brown, raw silk white`

### `ERA_QUALITY`

时代质感一句话。

- 宋代：`Song dynasty everyday life, natural silk and cotton fabric texture, muted tones, no palace opulence`
- 唐代：`Tang dynasty elegance, silk brocade, warmer tones, aristocratic but not exaggerated`

## 多后端参数适配

生成完英文主提示词后，附上目标后端的参数。

### Midjourney（推荐首选）

```
{主提示词}
--ar 9:16 --v 6.1 --style raw
```

- `--ar 9:16`：竖版比例
- `--v 6.1`：稳定版；也可用 `--v 6` 或 `--v 5`
- `--style raw`：降低 MJ 的自动美化，保留提示词原意
- **不要**加 `--stylize` 太高，会把画面推向"高级感 AI 味"

### ComfyUI (SDXL)

```
positive_prompt: {主提示词}
negative_prompt: {负面提示词}
width: 768
height: 1344
steps: 30
cfg: 6.0
sampler: dpmpp_2m
scheduler: karras
checkpoint: sd_xl_base_1.0.safetensors（或已安装的国风/古风大模型）
```

### ComfyUI (Flux)

```
positive_prompt: {主提示词}
width: 832
height: 1472
steps: 28
cfg: 3.5
sampler: euler
scheduler: normal
unet: flux1-dev.safetensors
clip: t5xxl_fp16.safetensors
```

### 通用图像生成 API（如 Agnes / OpenAI DALL-E / 其他）

```
prompt: {主提示词}
aspect_ratio: 9:16
negative_prompt: {负面提示词}
```

## 负面提示词（必备）

所有后端都必须追加这段负面提示词：

```
text, chinese characters, chinese calligraphy, letters, subtitles,
watermarks, logos, branding, watermark text, any written text,
signature, seal, stamp with characters,
anime, 3d render, cgi, plastic skin, oversaturated colors,
golden filter, warm orange tint,
modern elements, fashion photography, luxury hanfu, palace clothing,
fairy costume, fantasy, xianxia, immortal robes, magic effects,
studio lighting, perfect pose, artificial smile,
oversharpened, low quality, worst quality, blurry, deformed, ugly,
multiple people, group scene, busy background
```

**关键**：`text, chinese characters, chinese calligraphy, letters, subtitles, watermarks` 这一串不能漏——背景图必须有充足留白给用户后期排版诗句。

## 反 slop 提示词检查

生成完提示词后自查：

- [ ] 没有出现 `masterpiece, 8k, best quality, ultra-detailed, stunning, breathtaking, amazing, exquisite, elegant beauty, beautiful woman`
- [ ] 没有出现 `fairy, immortal, fantasy, xianxia, magic, dragon, phoenix, spirit` 等仙侠元素（除非诗里明确出现）
- [ ] 没有 `golden filter, warm orange tint, cinematic lighting` 等影棚/滤镜暗示
- [ ] 没有指定人物"完美身材"、"标准脸"、"网红脸"
- [ ] 没有 `fashion photography, magazine cover, model shoot`
- [ ] 明确写了 `no text, no chinese characters`
- [ ] 明确写了 `9:16` 或 `--ar 9:16`
- [ ] 明确写了 `negative space for typography overlay`
- [ ] 主提示词控制在 60–120 个英文单词之间（太短 AI 不知道细节，太长会被截断）

## 完整示例

以《静夜思》为例，最终生成的 `prompt.md` 内容：

```markdown
# 《静夜思》 · 李白（唐）

## 诗词拆解

- **戏眼**：从床上坐起，抬头望月
- **核心意象**：明月、床、窗棂、孤烛
- **主色调**：墨黑 + 月白 + 烛黄
- **情绪**：孤寂——画面里只有一个人，桌上一盏未熄的烛，窗外一轮月
- **入画佳句**：举头望明月，低头思故乡

## 背景图提示词

### 英文主提示词

A solitary young Tang dynasty poet sitting on a low wooden couch indoors,
his hand resting on the wooden window frame, gazing up at a bright full moon
through the window lattice. A half-burned candle on a small wooden side table
casts a warm amber glow on the wall. Cool moonlight spills across the floor
from outside. The room is dim and quiet. Vertical 9:16 poster composition,
the figure occupying only the lower-left quarter of the frame, leaving generous
negative space in the upper-right for typography overlay. Traditional Chinese
painting meets cinematic photography, Tang dynasty elegant aesthetic with
silk robe texture, muted tones, low saturation color grading in muted indigo
black, moonlit white, and candlelight amber. Natural light only, cool moonlight
dominant, warm candlelight accent, subtle film grain, soft depth of field,
quiet contemplative mood. No text, no chinese characters, no chinese calligraphy,
no letters, no subtitles, no watermarks, no logos, no branding, no signature,
no seal with characters.

### 负面提示词

text, chinese characters, chinese calligraphy, letters, subtitles, watermarks,
logos, branding, watermark text, any written text, signature, seal, stamp with
characters, anime, 3d render, cgi, plastic skin, oversaturated colors, golden
filter, warm orange tint, modern elements, fashion photography, luxury hanfu,
palace clothing, fairy costume, fantasy, xianxia, immortal robes, magic
effects, studio lighting, perfect pose, artificial smile, oversharpened, low
quality, worst quality, blurry, deformed, ugly, multiple people, group scene,
busy background

### 后端参数

**Midjourney**:
```
{主提示词}
--ar 9:16 --v 6.1 --style raw
```

**ComfyUI (SDXL)**: width=768, height=1344, steps=30, cfg=6.0

**ComfyUI (Flux)**: width=832, height=1472, steps=28, cfg=3.5

**Agnes / 通用 API**: aspect_ratio=9:16
```
