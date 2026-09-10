# 垂直场景模板

从 MiniMax 官方 8 个风格 skill（docs/ 未收录原文，方法论已提炼入本文件；极简产品广告与品牌片两篇有官方中文版）提炼提示词写法层，适配 H3 字段结构。用时取对应模板打底，再按用户需求改变量；所有模板最终仍回到 SKILL.md 基础公式与 formulas.md 的字段结构。

## 模板总表

| 模板 | 适用场景 | 默认时长/画幅 |
|---|---|---|
| 极简产品广告 | 电商商品/新品发布，Apple 风高质感产品片 | 10s｜16:9 或 9:16 |
| 品牌宣传片 | 有 LOGO/品牌素材的宣传短片 | 15s｜16:9 |
| 3D 动画短片 | 风格化叙事动画，强角色一致性 | 15s 内多镜 |
| 定格纸艺科普 | 知识解释/教育内容 | 15s｜16:9 |
| MV 歌词排版 | 音乐短片、歌词卡点 | 15s 内多镜 |
| 游戏开场 | 固定 UI 框架 + 动态风格填充 | 10–15s |
| 纸拼贴概念动画 | 抽象概念/观点转视觉 B-roll | 每段 4s |
| 实拍×手绘融合 | 真人画面 + 手绘发光动画 | 15s 单镜 |
| 对话短剧 | 双人情景/带货对话 | 10–15s 多镜 |

## 节拍与镜数速查（官方口径）

5s = 3–4 拍（1–2 镜）；10s = 5–7 拍（2–4 镜）；15s = 6–9 拍（3–6 镜）。每拍一个主要动作，次级动作延迟出现；节奏按"峰值 + 制动"配比：5s=1 峰、10s=1–2 峰、15s=2–3 峰 + 2 个安静制动点（制动 = 材质特写停顿/文字稳定可读/结尾定格）。

---

## 1. 极简产品广告（Apple 风）

- **素材策略**：三张独立锚定照片（非宫格，防模型把宫格版式拍进成片）——①主视觉/刁钻主视角 ②材质或功能细节 ③结尾文案构图。上传后按 `<Picture 1/2/3>` 引用，各自任务在 `subject_definitions` 写明角色；原商品图不传给视频模型。任务类型 `[keyframe completion + reference generation]`。
- **风格锚点**：干净构图、高级光线、克制运动、留白。四选一模板：白色科技风 / 黑底轮廓光 / 品牌色块 / 生活方式轻场景。
- **产品本体颜色硬保真**：只允许背景、光线、构图转向风格模板；产品本体颜色、材质色相、可见质感全程保留——`<Subject 1>`（产品）retention 用 `fully_preserved` 并写明保留颜色与材质。
- **文案排版硬规则**：3–5 个英文词、单行、≤32 字符；两段式颜色（白色科技风前半黑/深灰 + 后半产品主色；黑底轮廓光前半才可用白）；同屏只允许一条单行文案；文字动效 = 前半先淡入/轻滑入，后半随后、前半同行轻位移让位（10–15px）；10s 片只保留 1 次中段 + 1 次结尾文案。文案分割只许写"前半/后半"，**禁止箭头/斜杠/加号**（会被渲染进画面）。
- **节拍表**：时间段｜镜头｜目的｜视觉主导｜画面/运镜｜款式状态｜文案｜文字色｜文字动效｜转场连贯｜节奏意图；写词时逐拍转成 `[Shot N] At …`。
- **结尾硬约束**：单一全画幅产品收束 + 完整单行文案的稳定画面；严禁宫格/分屏/分镜板。
- **声音**：配乐方向写法（照官方）：`Around 100 BPM, fast tech feeling, plucked synth chords over an airy noise bed, kick with sub-bass and a rising sine sweep, wooden percussion accents, sudden cut-off — everything stops within 0.5s, leaving a single pluck tail.`（写进 `non_diegetic_music`）

## 2. 品牌宣传片

- **品牌事实表先行**：精确 LOGO 版本、官方字体、主辅色（全片 ≤5 色，红色仅警示）、品牌语气；写进 `<Subject N>` 定义。LOGO 只用官方素材锚定（`<Picture N>`），**禁止模型重绘或近似**——正文写明 `the logo appears exactly as in <Picture N>`。
- **叙事脊柱按产品类型**：AI/SaaS 类 = 意图 → 规划 → 能力 → 输出 → 证明 → LOGO 收束。
- **硬约束**：程序化图形只作非写实动效层（遮罩/渐变/色块/网格/辉光/转场几何）；禁虚假 HUD/玻璃卡片/文字墙/未验证数据指标/全片同一缓动；不遮挡产品与 LOGO 安全空间；镜间动作自然衔接处留 6–12 帧重叠。
- **声音**：品牌安全纯音乐 + UI 音效写进 prompt；默认原生音频，禁双重声轨。

## 3. 3D 动画短片

- **全局风格锁**（`[Shot 1]` 前或句首，照官方）：`Pixar-inspired 3D cartoon rendering, C4D + Octane look, stylized Q-version proportions, warm SSS skin, strong character design language, clean motion, on-brand color palette`。负向：`no photorealistic live-action, no flat 2D anime, no plastic toy skin, no stiff mannequin posing`。
- **镜头表六列**：Shot ID & Duration｜Continuity Handoff（前后镜交接要素）｜Reference Anchors（空间锚：地标 + 屏幕方位，如 `door-frame: right third`；角色位置；离场角色状态；光影基线）｜Hook Type｜Shot Description（逐秒指令：动作表情/运镜/空间位置/声音 cue/向下一秒交接）｜Audio & Dialogue Track。写词时转成 `[Shot N]` 结构，空间锚写进各镜正文。
- **硬约束**：每镜 ≤3 个重要角色；每 3 镜至少 1 个 reveal/reversal/callback；特写与全景交替；Dutch angle 只用于追逐/失衡/惊吓；分镜标记（面板线/箭头/时序注记）禁止出现在成片描述中；旁白时写 `narrator's lips remain completely closed`。
- **声音**：单条连续 BGM 贯穿（`non_diegetic_music` 一处定义），对话/关键音效处 ducking；禁逐镜换 BGM。

## 4. 定格纸艺科普

- **风格锚点**（照官方）：`handmade papercraft stop-motion, miniature paper diorama, layered cardboard cutouts with visible thickness, paper fibers and cut edges, real physical drop shadows, macro miniature photography with 2.5D parallax`。
- **运动语言**：stepped movement、tiny pauses、slight rebounds、hinged joints、pull-tabs、rotating discs——一切运动服从纸物理；转场用 page flip / pop-up unfold / pull-tab slide / 圆形纸遮罩，禁电子扫描线/霓虹/玻璃碎裂/液态变形。
- **负向提示块**（照官方，正文段末）：`no smooth plastic 3D, no glossy CG render, no live-action realism, no flat vector graphics, no melting or liquid morphing`。
- **节拍**：15s = 4 镜（每镜 2–7s）；每镜只讲一个知识节拍，观众理解机制前不切镜；标签卡出现时停顿。
- **声音**：纸质 SFX 写进 `overall_soundscape`（paper flip、scissor snip、cardboard slide、wooden click）；配乐按主题选乐器（中秋 = guzheng/pipa/bamboo flute；科普 = light marimba/xylophone/pizzicato strings），写进 `non_diegetic_music`，旁白处 ducking。

## 5. MV 歌词排版

- **锁定歌词为唯一来源**：说唱/演唱与画面文字都用锁定歌词逐词对齐；`<d>` 内逐字。
- **结构**：>15s 拆多段拼接；单段内 2–5s 一镜；切点落 1/4 或 1/8 节拍网格；**硬切 only**（正文写 hard cuts，禁 fade/dissolve）。
- **文字空间规则**：typography 是空间图形层（可被肩手遮挡），绝不遮眼睛与关键表情，演唱时避开嘴部；每镜仅一个主文字事件；文字动效转移动量——前镜文字在 bass hit 上 shatter/sweep out，后镜文字在 accent 上 smash in（写进各镜正文）。
- **统一质感**：全片同一 grain + LUT 写在风格段（如 `35mm grain, halftone dots, scan offsets`），藏多段色差。
- **声音**：全片一条 Master Audio 思路——配乐在 `non_diegetic_music` 一处定义；歌声是画面内事件进主描述字段。

## 6. 游戏开场（固定框架 + 动态填充）

- **公式**（照官方）：`Fixed video event framework + dynamic user-style fill + locked character identity + palette-linked UI system`。
- **固定框架**：UI 骨架与时间轴不变（如 0–2s 双人菜单 → 2–4s P1 特写 → …），`{visual_style}` 只换材质/色彩/渲染；调色 ≤5 色，UI/按钮/文字同色系。
- **身份锚**：参考图只捐身份锚（脸轮廓/发型/眼镜/比例），不捐写实质感（`attribute_transfer`）；P1 恒左、P2 恒右，禁身份交换/脸融合；UI 文字单行准确显示。
- **排除短语**：`no split screen, no hard cuts, no random letters, no misspelled usernames, no copied branded interface`。

## 7. 纸拼贴概念动画

- **风格签名**（照官方）：`flat bold color field, black-and-white halftone photographic cut-outs, selective colored cardstock accents, warm cream keylines, soft paper shadows, fine uncoated-paper grain, clean refined hand-torn paper edges`。
- **颜色语义表**：burnt orange=紧迫、mustard=工具/警告、ink green=认知/reset、deep purple=记忆/梦、teal=协作/系统、rose red=荒诞；禁钴蓝自动默认。
- **组装顺序**（即 `[Shot 1]` 内动作链）：干净色场 → 基底结构逐片 pop/slide → 主角元素 → 次级对象逐个 bounce/press-flat/pause/lock → 锁构图 → 结尾 hold。静态版式图作 `<Picture 1>`，**静帧 = 动画最终帧锚**——开场面颜色必须匹配静帧。
- **硬约束**：3–6 个可分离纸组；禁可读文字/假字母/数字/UI/LOGO；无镜头运动、无剪辑、no zoom、no morphing；结尾构图 = 静帧构图。

## 8. 实拍×手绘融合（15s 单镜）

- **开头句式**（照官方，可作 `[Shot 1]` 主题句）：`Live-action footage blended with hand-drawn glowing animation`；质感 = 蜡笔/粉笔/彩铅线条、轻微手抖、毛边、逐帧重画感。
- **五段节拍**（15s 版）：0–3s 实拍手与动画清晰接触（缠手指/落掌心/指尖诞生）→ 3–6s 第一次变形 → 6–10s 互动升级/恶作剧 → 10–13s 情感高点 → 13–15s 空间级变形（线条扩散到墙/地板）+ 余韵。
- **硬约束**：同一实体连续变形并保留前一形态痕迹；禁突然出现新角色；同一空间连续拍摄不剪辑；相机慢半拍跟随、不居中；拍摄者参与互动；禁 3DCG 质感、均匀矢量线、恐怖元素。
- **声音**：结尾段落固定写环境音（`overall_soundscape` 收在实拍环境声上）。

## 9. 对话短剧（通用，多镜）

- **镜头配比**：10–15s = 3–4 镜；每镜一个说话人一句台词；正反打（`the camera cuts to a close-up of…`）。
- **写法**：说话人首次出现带身份描述 + `(S1)/(S2)`；台词逐句 `<d>[语言]…</d>`；台词长度与镜时长匹配（一句 8–12 词 ≈ 3s）；语气写进说话人引导语（`in a playful tone and an easy conversational pace`）。
- **一致性**：双人物各建 `<Subject N>` 绑定参考图；retention 均 `fully_preserved`；跨镜服装发型特征在首次出现处写全。
- **参考骨架**：examples.md「萨摩耶咖啡店」（官方三镜情景剧案例）。

---

## 跨场景通用技法（官方 8 个风格 skill 横向提炼）

1. **锚定图机制**：参考图各授一职（角色卡/场景卡/字卡/产品锚定照），身份只捐锚点不捐质感；参考图 ≠ 分镜，禁把锚定图当镜头序列逐张拍。
2. **节拍表先于生成**：结构化节拍/镜头表是唯一执行源——时间段、视觉主导、主要动作、文案、转场、节奏意图逐列定清，再逐拍转 `[Shot N]`；无时间空隙，单次生成 ≤15s，超了拆段。
3. **空间锚 + 连贯链**：地标 + 屏幕方位定位；每镜写"向下一镜交接什么"；多段拼接用尾帧作首帧（`keyframe completion`）；时间跳跃显式注明。
4. **文字排版硬规则**：单行不换行、不遮脸眼嘴、同屏一个文字事件、颜色用具体色名、禁箭头/斜杠等符号字符；错字率高时减文字镜数而非禁动效。
5. **峰值-制动节奏**：见「节拍与镜数速查」；峰值 = 动作完成/文案落定/全员进场，制动 = 特写停顿/定格。
6. **原生音频默认 + 降级路径**：先在 prompt 里写清配乐与音效方向由 H3 原生生成；不满意才换独立配乐方案，禁双重声轨。
7. **通用失败阶梯**：重申锚点特征重试 → 缩短时长/减动作拆镜 → 换参数 → 降需求问用户；素材阶段的问题在素材阶段修，不带病进视频。
8. **自包含纪律**：正文必须逐字包含所有计划出现的台词与画面文字（缺文案不许交付）；语言随素材与受众，不随对话语言。
