# 词库：运镜、切镜、风格、光影、情绪、模糊词替代

运镜与切镜部分为官方指南原文（docs/h3-prompt-guide-base-en.txt），照官方三维体系使用；其余为跨平台通用技法按 H3 英文正文改写。

## 官方运镜三维表（类型 + 幅度 + 速度）

一个完整的运镜表达有三个维度：**类型**定义镜头怎么动，**幅度**定义构图变化范围，**速度**定义变化节奏。幅度与速度只在有意义时写——中等幅度与正常速度通常省略。

| 维度 | 表达 | 说明 |
|-|-|-|
| 类型 | `Zoom In / Zoom Out` | 焦距变化，机身不动 |
| 类型 | `Push In / Pull Out` | 镜头向前 / 向后移动 |
| 类型 | `Pan Left / Pan Right` | 机位不动，镜头水平摇 |
| 类型 | `Truck Left / Truck Right` | 镜头水平平移 |
| 类型 | `Tilt Up / Tilt Down` | 机位不动，镜头垂直俯仰 |
| 类型 | `Pedestal Up / Pedestal Down` | 整机上升 / 下降 |
| 类型 | `Arc Shot` | 环绕主体弧形移动 |
| 类型 | `Tracking Shot` | 跟随运动主体 |
| 类型 | `Static Shot` | 机位与焦距都不动 |
| 类型 | `Shake Slightly / Shake Strongly` | 轻微 / 强烈镜头抖动 |
| 类型 | `POV` | 主体第一人称视角 |
| 类型 | `Roll Clockwise / Roll Counterclockwise` | 绕镜头光轴顺/逆时针滚转 |
| 幅度 | `with small amplitude` | 小范围变化 |
| 幅度 | `with large amplitude` | 大范围变化 |
| 速度 | `at slow speed` | 慢速 |
| 速度 | `at fast speed` | 快速 |

**运镜写成镜头内的自然英文动作句，不堆标签。**官方示例（照抄可用）：

```text
The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.
The camera pans right with large amplitude at fast speed, revealing the open doorway.
The camera holds a static shot as the runner exits the frame.
```

- 一镜一个运镜句，与主体动作分开成句。
- 求稳加 `at slow speed` / `with small amplitude`；无意义运动改 `Static Shot`。
- API 文档另提过 `[pan]` `[zoom]` `[static]` 方括号简写指令——结构化正文以自然英文句为准（指南明确要求），简写仅适合极简短场景。

## 切镜句式（官方）

普通切镜：`the camera cuts to` / `the shot cuts to` / `the shot transitions to` / `the shot changes to` / `the shot switches to`。

用户明确要求时才可用：cross-dissolve、fade、wipe。

**切镜原则**：切镜引入新信息（主体/空间/状态/视角/时间）；只是距离或轻微角度变化用运镜句。每个切镜时间写 `At MM:SS.mmm`（毫秒三位）。

## 转场写成事件，不写转场名词

命名特效词（morph / glitch transition）容易被渲染成字面特效；描述转场的**物理事件**更稳：

```text
Cut at peak motion blur, then settle and snap back into focus.
The frame whites out in the flash, and the next shot begins in the aftermath.
She steps through the doorway; the cut lands as the door closes behind her.
```

## 镜头语言补充词（拿不准就用这些实测顺手的表达）

| 中文意图 | 英文写法 |
|---|---|
| 变焦跟焦点 | `rack focus from the foreground bottle to her face` |
| 甩镜转场 | `a whip pan carries us into the next room` |
| 手持跟随 | `a handheld follow shot stays half a step behind him` |
| 三脚架固定 | `a static tripod shot holds the frame` |
| 升降镜头 | `a slow crane up reveals the whole square` |
| 广角畸变 | `a wide angle lens with strong perspective distortion` |
| 胶片质感 | `fine grain, soft highlight halation, gentle exposure breathing` |
| 暗部噪点 | `coarse noise in the shadows, like pushed film stock` |

## 负向排除短语库（实测负向指令在本模型上异常有效，值得具体写）

写法：写在对应段落末尾，**点名具体易犯项**，比"no glitches"式泛泛排除有用得多；每处只点该场景真正易犯的两三项。分类取用：

**风格保护**（防风格漂移到邻近类型）：

```text
No soft dissolves or fluid morphs.
No photorealistic live-action; no flat 2D anime; no plastic toy skin.
No glass cards, no floating HUD panels, no decorative text walls.
```

**文字与字符保护**：

```text
Do not introduce Chinese text, garbled characters, or misspellings.
No random letters, no watermark, no captions in the lower third.
```

**画面与动作保护**：

```text
No tearing, no black frames, no obvious VFX or compositing seams.
No muzzle flash, no recoil; nobody enters the frame at any edge.
Do not show giant eyes, split mouths, fangs, or threatening behavior.
No split screen, no hard cuts unless written, no jump scares.
```

**一致性保护**（配合 `<Subject N>`）：

```text
Her face keeps the same proportions in every shot; no identity drift.
The logo appears exactly as in <Picture 1>; no redrawing or approximation.
```

## 风格词库（官方常见风格词）

`Cinematic`、`live-action`、`2D-animated`、`3D CG`、`claymation`（黏土定格）、`watercolor`（水彩）、`vintage film`（复古胶片）。

- 写法：`[Shot 1] Live-action, cinematic, a medium-wide shot frames…`——风格词放 `[Shot 1]` 句首；Ref2VA 在 `[Shot 1]` 前单独成段（一或两句）。
- 风格锚点贯穿：`[Shot 1]` 定风格，结尾镜回扣一次，防中途漂移。
- 负向风格保护（官方风格 skill 实践）：风格易跑偏时在段末用 `no/without` 短语点名，如 `no photorealistic live-action` / `no flat 2D anime`（3D 动画片）；克制使用。

## 光影词库（写光位，性价比最高的维度）

| 中文意图 | 英文写法 |
|---|---|
| 侧逆光发丝光 | `backlit from the side, rim light tracing her hair` |
| 顶光压迫 | `harsh overhead light casting deep shadows under his brows` |
| 丁达尔效应 | `volumetric light beams cutting through the dust` |
| 冷暖对比 | `warm lamp glow against the cold blue window light` |
| 黄金时刻 | `golden-hour sunlight raking across the field` |
| 霓虹夜色 | `wet asphalt reflecting red and cyan neon signs` |

## 情绪外化对照表（"她很伤心" ❌ → 物理细节 ✅）

| 内在情绪 | 英文外化写法 |
|---|---|
| 伤心 | `her eyes redden, lashes trembling as tears slide past the bridge of her nose` |
| 紧张 | `his fingers drum the table; he re-grips the cup twice in three seconds` |
| 惊讶 | `her brows shoot up, lips parting as she takes a half step back` |
| 压抑愤怒 | `his jaw tightens, knuckles whitening around the pen` |
| 喜悦 | `she laughs from the belly, eyes crinkling into crescents` |
| 疲惫 | `his eyelids droop; shoulders sink as he exhales through loose lips` |
| 期待 | `she leans forward, fingertips curled over the table edge` |

## 质感与物理保真

- 写实类加物理细节锚：`real skin pores and fine texture preserved`、`physical motion follows real-world physics`。
- 材质写可见证据：`condensation beading on the cold glass`、`loose threads at the frayed cuff`、`dust motes drifting in the projector beam`。
- 动作写动作链（先…再…最后…），不写结果：`she lifts the cup, sniffs once, and sets it back on the saucer` ✅；`she enjoys her coffee` ❌。

## 模糊词替代表（英文正文版）

| 模糊词 | 替代写法 |
|---|---|
| high quality / beautiful / stunning | 删掉，改写具体的画质光影（光位、质感、构图） |
| fast / quickly（易运动模糊） | 改物理过程：`her skirt sweeps past the lens, stirring a gust` |
| sad music / emotional BGM | `sparse piano notes at a slow tempo, joined by sustained low strings`（只写配器/速度/强弱） |
| tense atmosphere | 物理证据：`the clock ticks louder; nobody touches their food` |
| cinematic feel | 具体化：`shallow depth of field, 35mm grain, motivated practical lighting` |
| dramatically | 具体动作：`slams the door hard enough to rattle the window` |
| suddenly | 换可观察的转折点：`without warning, the lights cut out`（或直接写切镜时间） |
| a lot of people | `a crowd of forty or fifty people, packed shoulder to shoulder` |
