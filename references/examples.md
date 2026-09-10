# 官方示范提示词精读（5 完整案例 + 4 极简示例）

正文均为官方指南原文（docs/h3-prompt-guide-base-en.txt、ref-en.txt），注解为本 skill 添加。用法：写词时找同模式案例对照结构，不照抄内容。

## 案例 1：T2VA——面包师清晨营业

```text
integrated_multimodal_description: [Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: <d>[English] First batch of the morning.</d> [Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape: Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music: A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
```

**结构注解**：
- `[Shot 1]` 一句话装下：风格（Live-action, cinematic）+ 景别构图（medium-wide shot frames…）+ 主体（外观隐含、动作明确）+ 运镜三维句 + 说话人身份描述 + `(S1)` + 台词 `<d>`。
- `[Shot 2]` 切镜只引入新信息（特写蒸汽 = 新视角）；台词跨镜用 `carry over from the previous shot`（连续性表述），不重复台词内容。
- `overall_soundscape` 四个物理声源对应画面四个动作（开窗/托盘/门铃/切面包）；无台词无配乐混入。
- `non_diegetic_music` 三句：主奏乐器 + 加入乐器 + 结尾动态，无情绪词。

## 案例 2：I2VA——火车窗边读信

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

**结构注解**：
- 首行官方指令句精确照抄（0.00 秒 + `fully referenced` + `(from [Shot 1])`），后接空行再进字段。
- `[Shot 1]` 前半是**首帧锚定**：`remains beside…, preserving her appearance, clothing, seat position, and the carriage layout`——逐项锁定图内事实；后半才启动新动作（lifts her gaze）。
- 结构 = 首帧锚 → 动作启动 → 连续发展（倒影移动）→ 台词 → 收尾小动作（折信）。

## 案例 3：FL2VA——雨中骑车人开伞（8 秒单镜）

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape: Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music: N/A
```

**结构注解**：
- 指令句对齐两个时间点（0.00 与 8.00——有效时长两位小数）；两图同属 Shot 1（单镜）。
- 正文不重复两张图的静态描述，写的是**连接路径**：首帧状态（begins in the position…established by Picture 1）→ 动作链（release → raise → press → canopy opens）→ 中间物理细节（water rolls）→ 末帧收敛（settles into the pose, spacing, and composition established by Picture 2 at the end of the shot）。
- 无配乐：`non_diegetic_music: N/A`（官方用例）。

## 案例 4：L2VA——玻璃杯坠地碎裂（6 秒单镜）

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape: Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music: A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
```

**结构注解**：
- 末帧是碎裂结果 → 开场反推合理前序状态（intact glass + same hand and sleeve visible in <Picture 1>——从图中元素反推前置状态）。
- 收敛句逐项落地：`the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>`——物理结果、手位、机位、光影、构图五项全部对齐末帧。
- 配乐动态与画面事件同步（ends immediately after the glass breaks）。

## 案例 5：Ref2VA 六段式——萨摩耶咖啡店（三镜情景剧）

```text
subject_definitions:
<Subject 1> is the coffee-shop environment in <Picture 1>, featuring an exposed brick wall, an orange tufted sofa with patterned pillows, a neon sign, and a wooden coffee table.
<Subject 2> is the fluffy white Samoyed in <Picture 2>, <Picture 3>, and <Picture 4>, with thick white fur, pointed ears, a dark nose, and a curved tail.
<Subject 3> is the young blonde woman in <Video 1>, with long blonde hair and a light-pink button-down shirt with rolled-up sleeves.
<Subject 4> is the young man in <Video 2>, with short wavy brown hair and a dark-grey hoodie with drawstrings.
<Audio 1> is the voice-timbre reference for <Subject 3> (S1), containing a spoken English vocal layer.

summary:
[reference generation + audio reference] The target video shows <Subject 3> eating a cookie in <Subject 1>. <Subject 4> enters with <Subject 2>, which lunges toward the cookie. The three-shot exchange uses <Audio 1> as the voice-timbre reference for <Subject 3> and ends with a canned audience laugh.

retention_analysis:
<Subject 1> (appears in [Shot 1], [Shot 2], [Shot 3]): fully_preserved - the exposed brick wall, orange tufted sofa, patterned pillows, neon sign, and wooden coffee table are retained.
<Subject 2> (appears in [Shot 1], [Shot 2]): fully_preserved - the Samoyed's thick white fur, pointed ears, dark nose, and curved tail are retained.
<Subject 3> (appears in [Shot 1], [Shot 2], [Shot 3]): fully_preserved - the blonde woman's identity, long hair, and light-pink shirt are retained.
<Subject 4> (appears in [Shot 1], [Shot 2]): fully_preserved - the young man's short wavy brown hair and dark-grey hoodie are retained.
<Audio 1>: reference - its vocal timbre guides the dialogue delivery of <Subject 3> without copying the original signal.

detailed_description:
The target video uses a realistic multi-camera sitcom style with warm indoor lighting.
[Shot 1] A medium shot establishes <Subject 1>, the coffee shop with its exposed brick wall, orange tufted sofa, patterned pillows, neon sign, and wooden coffee table. <Subject 3> (S1), the young woman with long blonde hair and a light-pink button-down shirt with rolled-up sleeves, sits on the sofa holding a chocolate-chip cookie. From the left, <Subject 4>, the young man with short wavy brown hair and a dark-grey hoodie with drawstrings, enters holding the leash of <Subject 2>, the thick-furred white Samoyed with pointed ears, a dark nose, and a curved tail. The dog lunges toward the cookie and pulls the leash taut. <Subject 3> (S1) jerks her hand back and, using the clear youthful voice timbre referenced from <Audio 1>, exclaims with light annoyance, <d>[English] Hey! Watch your dog!</d> She closes her lips and guards the cookie while <Subject 4> pulls the dog back.
[Shot 2] At 00:03.000, the shot cuts to a close-up of <Subject 4> (S2), the young man in the dark-grey hoodie from Shot 1, sitting beside <Subject 3> on the sofa and holding <Subject 2> securely in his arms. <Subject 4> (S2) says in a casual young male voice with a playful tone and an easy conversational pace, <d>[English] He just likes cookies more than me.</d> He closes his mouth into an apologetic smile and strokes the dog's thick white fur.
[Shot 3] At 00:05.000, the shot cuts to a close-up of <Subject 3> (S1), the blonde woman in the light-pink shirt from Shot 1. Her annoyance softens as she looks toward the Samoyed. <Subject 3> (S1) replies in the same clear youthful voice referenced from <Audio 1> with an amused cadence, <d>[English] Well, he has good taste at least.</d> She smiles and raises the cookie in a small toast-like gesture. A classic canned audience laugh begins immediately after the line and continues through the final frame.

overall_soundscape:
Soft indoor coffee-shop room tone continues throughout the scene.

non_diegetic_music:
N/A
```

**结构注解**：
- 六段顺序固定；四张图、两段视频、一段音频 → 4 个 `<Subject N>`（环境/狗/女/男）+ 1 个 `<Audio N>`；图只作"定义来源"，不独立建 `<Picture N>` 条目（无帧锚用途）。
- summary 任务类型 `[reference generation + audio reference]` 与各素材实际角色严格对应；音色参考不是复制，用 `audio reference` 不用 `audio reuse`。
- retention 逐标签写明保留的特征清单；音频用 `reference` 并写明"音色引导台词、不复制信号"。
- detailed_description：风格开场一句独立于 `[Shot 1]` 前；每个 `<Subject N>` 首次出现处重述参考特征（定义不等于正文，正文要重述）；音色引用两次显式回提 `<Audio 1>`（`referenced from <Audio 1>`）；每镜一个说话人一句台词，时长与切镜（3s/5s 两切）匹配。
- 画面内事件（罐头笑声）写在正文对应时间点，不进两个声音字段。

## 附：平台文档极简示例（API 演示格式用）

- 文生视频：`A tiktok dancer is dancing on a drone, doing flips and tricks.`
- 图生视频：`Contemporary dance, the people in the picture are performing contemporary dance.`
- 首尾帧：`A little girl grows up.`
- 参考生成：`The character performs street dance, following the motion in reference video 1; the character's appearance follows reference images 1 and 2.`

> 这些是 API 文档演示请求格式的极简示例，展示"参考素材用途要说清"的写法。正式交付按上方五案例的完整结构写，不按极简风格写。
