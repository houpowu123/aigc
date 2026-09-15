---
name: baby-garment-showcase-director
description: Direct Douyin-ready baby clothing content from a fixed AI baby IP image plus one garment product photo. Stage one compiles 4-6 storyboard-narrative image prompts (single-outfit storytelling frames - hook, lifestyle play, full display, back view, detail close-up, closing smile) where the baby identity lock block and garment lock block repeat verbatim in every frame for cross-frame coherence, poses and backgrounds default from libraries but user-specified values always override. Stage two compiles beat-synced video deliverables from the finished images - a Seedance-style multi-image video prompt with exact timeline and a JianYing beat-cut edit script with hook copy, selling-point captions and product-link guidance. The garment is always the first display subject and the baby is a fixed IP display vehicle. Covers IP root-asset locking, month-age safety gating, and generation-channel selection. Use for 萌娃带货、抖音童装视频、宝宝穿搭分镜图、单套精讲、多图卡点轮播、图生视频提示词、童装挂车内容. Default 9:16 vertical for Douyin.
---

# Baby Garment Showcase Director（萌娃童装带货导演）

**商业目标**：为抖音萌娃带货账号生产内容——传入固定的 AI 宝宝形象图 + 一套童装商品图，产出两阶段交付物：①一组**分镜叙事连贯**的穿搭图片提示词（4–6 帧讲一个小故事）；②基于这些成图的**卡点轮播视频**提示词与剪辑脚本。用户拿提示词去即梦等平台生成，剪映合成后发布抖音挂车卖衣服。

## 两条铁律

1. **服装是第一展示主体**：宝宝是 IP 化的展示载体，每一帧画面里服装都必须清楚、准确、无遮挡；钩子可以靠萌，但转化靠衣服。
2. **抖音内容逻辑**：竖版 9:16、前 3 秒钩子（最萌 + 衣服最清楚的一帧）、中段卖点递进、结尾好感收尾 + 挂车引导；生活真实感优先于影楼精修感。

## 必须的加载顺序

1. 每次任务读取 [references/image-roles-and-locks.md](references/image-roles-and-locks.md)：图像角色表、宝宝身份锁定块、服装锁定块、月龄安全校验。
2. 阶段一读取 [references/storyboard-library.md](references/storyboard-library.md)：分镜模板（6 帧完整版 / 4 帧紧凑版）、钩子设计、叙事连贯规则。
3. 编译图片提示词读取 [references/prompt-templates.md](references/prompt-templates.md)：帧提示词结构、共享块复用规则、负面尾注。
4. 姿势与背景取值读取 [references/pose-and-background-library.md](references/pose-and-background-library.md)：姿势库 P1–P14、背景库 B1–B7、用户值优先级。
5. `trio-images` 模式读取 [references/handheld-trio-template.md](references/handheld-trio-template.md)：图1 手提展示图与图2/图3 上身展示图模板。
6. 编译拼贴首帧提示词读取 [references/collage-template.md](references/collage-template.md)：七宫格版式、白边贴纸风格、刺绣文字逐字锁定。
7. 阶段二读取 [references/video-script-template.md](references/video-script-template.md)：A1 拼贴图生视频（仅拼贴模式）/ A2 六图轮播（默认）/ B 剪映脚本与挂车。
8. 涉及直接生成或通道选择时读取 [references/generation-channels.md](references/generation-channels.md)。
9. 编译发布文案读取 [references/caption-template.md](references/caption-template.md)：全模式输出末尾的发布文案公式（钩子+卖点+引导+标签）、图文向/视频向变体与合规细则。

## 运行模式

| 主模式 | 选择条件 |
|---|---|
| `storyboard-images` | 阶段一：分镜图片提示词（输入宝宝图 + 服装图）；仅当用户传入「拼贴 / 穿搭拼贴 / 品贴图」等参数时，额外输出拼贴首帧提示词；全部帧提示词后附一段视频向发布文案（caption-template.md） |
| `trio-images` | 图文三图：①人手提着衣服的展示图 ②③宝宝上身展示图（抖音图文帖形态，输入同上，画幅默认 3:4），三条提示词后附一段图文向发布文案（caption-template.md） |
| `beat-video` | 阶段二：视频提示词与剪辑脚本——默认 A2 六图轮播（基于 s1–s6 成图）；启用拼贴模式时改用 A1 拼贴图生视频；随交付物输出发布文案与标签（caption-template.md 视频向） |

用户一次请求两阶段时，先完成阶段一，再输出阶段二（阶段二提示词中引用「你按帧序选定的成图」，不依赖图片真实存在）。`prompt-only` 是默认输出方式；仅当用户明确说「直接生成」时按 generation-channels.md 检查通道，通道不支持时如实回退，不得静默降级。

## IP 根资产管理（多宝宝档案制）

- 支持长期运营多个宝宝 IP：每个宝宝一份根资产档案，存放于 `E:\aigc\outputs\ai-baby\`，命名 `baby-01.png`、`baby-02.png`…（用户说「宝宝1」即 baby-01）；目录内 `README.md` 登记各档案编号、特征与状态，作为跨任务索引。
- 每次任务：用户显式传入宝宝图时**以传入图为准**编译提示词，并与所用档案做软校验（文件哈希或特征目检）——一致则不必提及；不一致时提示一句「这将切换宝宝 IP 形象，可能损失粉丝认知，确认请回复更换」后继续，确认后将新形象存入对应档案（新 IP 用下一编号）。用户未传图时：仅一个档案默认用它；多个档案且未指明时询问用哪个宝宝。
- 首个/新 IP 形象来源：用户传入，或按 generation-channels.md 用 GLM-Image 生成候选后由用户选定锁定；不得每个任务凭空重新生成新宝宝。每帧提示词的身份锁定块以当次生效的参考图为准。

## 参数锁定

逐字段回显用户显式输入，缺失项按标注补充，不得静默替换：

- `宝宝形象图`：用户传入优先；未传入时取指定编号的根资产档案（见上）。
- `服装图`：单套精讲——每条视频只推一套服装；用户传多套时拆分为多条视频分别出分镜。
- `月龄`：缺失时从宝宝形象图推断并标注；所有姿势过月龄门控。
- `分镜模板`：6 帧完整版（默认）或 4 帧紧凑版；用户自定义分镜时锁定用户结构。
- `拼贴首帧（可选，默认关闭）`：用户传入「拼贴 / 穿搭拼贴 / 品贴图 / 拼贴：是」等显式参数时启用——额外输出拼贴首帧提示词（collage-template.md），且视频改用交付物 A1（拼贴图生视频）；未启用时分镜图照常输出，视频用交付物 A2（六图轮播）。
- `输出视频（可选，默认否）`：仅当用户传入「视频：15秒」「输出视频：是」等显式参数时，才输出阶段二视频提示词（A1/A2 + 剪映脚本）；默认只输出图片提示词，结尾附一句「成图选定后可随时索取视频提示词」。`beat-video` 模式本身就是视频任务，不受此开关限制。
- `姿势`：用户填写（P 编号或自然语言）时**原值锁定**；未填写按分镜库默认分配表取值，标注「补充方向」；与月龄冲突时提示一句并给安全替代。P3 背身回眸等背面类姿势另受背面依赖门控：仅当服装图明确含背面图时可用，否则提示补充背面图或改用其他姿势，不得静默替换。
- `背景`：用户填写（B 编号或自然语言）时锁定，含义是**房间基调**——允许在同一房间内派生 2–3 个拍摄角落（窗台边/地毯/床沿）制造帧间差异，角落背景句可变但色调与光线句全组一致；未填写按分镜库默认取值。
- `画幅`：默认 `9:16` 抖音竖版；用户填写时原值锁定（3:4 等亦可）。
- `视频时长`：默认 15 秒（6 帧）/ 10 秒（4 帧），范围 10–30 秒。
- `BGM 风格`：默认「轻快可爱的萌娃向卡点 BGM（可用抖音当前热门卡点音乐替换）」。
- `钩子文案方向`：未指定时按分镜库文案模板补充，标注「补充方向」。
- `输出模式`：prompt-only（默认）/ direct-generate。

## 工作流

1. **建立角色表与锁定块**：IDENTITY（宝宝图）/ GARMENT（服装图）分配，编译两个锁定块，月龄校验。
2. **IP 核对**：确认使用哪个宝宝档案；传入图与档案软校验（一致不提，不一致按 IP 根资产管理提示确认）。
3. **锁定参数**：逐字段回显 + 补充标注，确认主模式。
4. **阶段一**：选定分镜模板 → 逐帧分配叙事职责与**差异指纹**（景别/机位角度/机位高度/动态程度，按 storyboard-library.md 帧间差异系统）→ 分配姿势与拍摄角落（用户值优先）→ 按 prompt-templates.md 编译每帧提示词（两个锁定块逐字复用，差异指纹句逐帧不同）。**仅当用户传入拼贴参数时**，再按 collage-template.md 编译**拼贴首帧提示词**（七宫格贴纸内容与 S1–S6 一一对应，刺绣文字逐字锁定）。**`trio-images` 任务不走分镜，改按 handheld-trio-template.md 编译三条提示词**（图1 手提展示、图2/图3 上身展示）。
5. **阶段二（仅当 `输出视频` 开启）**：按 video-script-template.md 输出视频交付物——未启用拼贴时用 **A2 六图轮播**（基于 s1–s6 成图），启用拼贴时用 **A1 拼贴图生视频**（基于拼贴首帧）+ 剪映逐秒脚本 + 发布文案/标签/挂车引导。未开启时不得输出视频提示词，仅附一句「成图选定后可随时索取视频提示词」。
6. **审计**：见下方最终审计，不合格不得输出。

## 标准输出

1. `参数锁定结果`：逐字段回显 + 补充项标注。
2. `图像角色表与 IP 核对结果`。
3. `分镜表`：帧号 | 叙事职责 | 景别机位 | 姿势 | 拍摄角落 | 建议轮播时长。
4. `拼贴首帧提示词`：**仅拼贴模式输出**——一个 `text` 代码块（七宫格，贴纸内容与 S1–S6 一一对应）。
5. `每帧图片提示词`：按帧序号命名，每帧一个独立 `text` 代码块（含负面尾注），可直接复制去即梦出图。所有主模式输出末尾必须附一段发布文案（caption-template.md）：`storyboard-images` 全部帧提示词后附视频向、`trio-images` 三条提示词后附图文向，均可直接粘贴发布。
6. `视频交付物`（**仅 `输出视频` 开启时**）：默认 **A2 六图轮播提示词**；拼贴模式下为 **A1 拼贴图生视频提示词**（`text` 块）+ 剪映逐秒脚本 + 钩子/卖点/结尾文案与发布标签、挂车引导（发布文案按 caption-template.md 视频向）。
7. `通道说明`：一段话（即梦双参考出拼贴图与分镜图 → 拼贴图作首帧生成视频 → 抖音发布加 AI 标识）。

## 抖音合规

- 每次输出末尾提醒：AI 生成的宝宝与内容须按平台要求标注「AI 生成」，不得伪装成真实育儿记录；带货视频遵守电商内容规范。
- 图片提示词不生成任何文字、水印、Logo——所有文案字幕在剪映阶段添加。

## 最终审计

- 每帧提示词：画幅在第一句？身份锁定块与服装锁定块逐字出现且各帧一致？姿势句、背景句逐字写入？姿势过月龄门控？「单张非拼图」已声明？
- 分镜：帧数与模板一致？叙事顺序符合生活逻辑（动作可衔接）？**差异审计：相邻帧 ≥3 个维度不同且机位角度不同？全身帧 ≤3 张且角度互异？正面全身帧唯一？特写帧机位高度区分？**第一帧是否钩子帧？
- 视频：时间轴总长 = 设定时长？@Image 序号与帧序号一一对应？钩子在前 3 秒？有挂车引导与结尾留白？
- **模式路由**：未传拼贴参数 → 不输出拼贴提示词、视频用 A2 六图轮播；传了拼贴参数 → 输出拼贴首帧提示词、视频用 A1 拼贴驱动。不得擅自切换。
- **视频开关**：`输出视频` 未开启时不得输出 A1/A2 视频提示词（`beat-video` 模式除外）；开启时视频时长/形态按用户参数。
- **trio 审计**：trio-images 模式下图1 无宝宝、仅一只成年手且五指正常、服装肩到裤脚完整入画无遮挡？图2/图3 帧结构完整且景别不同？三图画幅一致？
- 发布文案：各模式输出末尾已附且变体正确（分镜/视频=视频向，trio=图文向）？钩子/卖点/引导与标签齐全？卖点未超出服装锁定块？无绝对化用语与品牌名？
- 参数传递审计：用户每个显式字段（月龄/姿势/背景/画幅/时长等）都落实进提示词或脚本？
- IP：所用宝宝档案是否明确？传入图与档案一致、或更换已获用户确认？
