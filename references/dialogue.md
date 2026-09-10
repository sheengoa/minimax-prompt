# 台词与声音：说话人、台词标签、双声音字段

依据 docs/h3-prompt-guide-base-en.txt 与 ref-en.txt 提炼。H3 原生立体声，台词、音效、配乐一次生成——写词时把三类声音分开装进正确的位置，是音画质量的关键。

## 三线分工（先记这张表）

| 声音 | 写在哪 | 规则 |
|---|---|---|
| 台词、歌声 | 主描述字段（`integrated_multimodal_description` / `detailed_description`）内，说话人句 + `<d>` 标签 | 逐字保真，不翻译 |
| 画面内可闻的音乐（角色能听到：radio/电视/手机/现场演奏） | 主描述字段，作为画面内事件 | 写明声源载体 |
| 环境音、物理动作音、非语言人声（呼吸/笑声/喘息） | `overall_soundscape`（1–4 句，连续一段） | 不重复台词/配乐 |
| 只有观众能听到的配乐 | `non_diegetic_music`（1–3 句） | 不写情绪功能 |
| 用户明确要求全片静音 | `overall_soundscape: N/A` | 仅此情形可用 N/A |
| 无配乐 | `non_diegetic_music: N/A` | — |

## 说话人 ID

- 发声主体（说话/唱歌/画外人声）用稳定 ID：`(S1)`、`(S2)`；多名已编号说话人齐声用复合 ID `(S1,S2)`。同一人跨镜不换号；从不发声的角色不给 ID。
- **首次出现必给身份信息**，从视听上下文足以锁定身份：角色类型、年龄、性别、是否在画面内、音高、音色、语速、口音。示例：`The young woman with a quiet, breathy voice (S1)`。
- 说话人的身份短语、ID、动作与语气都写在 `<d>` 外；`<d>` 内只放语言标签 + 逐字原话。
- `(Sx)` 按目标视频实际发声事件顺序一次性分配、全片复用；`retention_analysis` 不写 `(Sx)`。
- 参考素材中的主体说话：视觉标签 + 说话人 ID 双标记 `<Subject 2> (S1) …`；画外保持同形并标 `off-screen`；说话人不对应已定义主体时，用稳定音色描述 + `(Sx)`。
- BGM/完整音轨内的口头内容：声源是 `<Audio N>`，不另造 `(Sx)`；由具体人/角色/旁白发出的才分配 `(Sx)`。

## 台词 `<d>` 标签

格式：`<d>[语言] 逐字原话</d>`，语言标签如 `[English]` `[Chinese]`。

- **逐字保真**：保留每个原词与标点，不翻译、不改写、不概括。复用参考音频台词时，听不清写 `[unclear]`，不猜写；标点规范化为基本书写符号（`,` `.` `?` `!`），删波浪号/表情/项目符号等装饰符号；完整陈述/疑问/感叹句在 `</d>` 前以 `.` `?` `!` 收尾。
- 完整示范台词句式：

```text
The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>
The two children (S1,S2) shout together, <d>[English] Wait for us!</d>
```

- 引号选择注意：正文用英文双引号包画面文字（见下节），`<d>` 标签本身不加引号。

## 旁白（voiceover）

- 用官方精确短语 `says in an off-screen voiceover`。
- **每个旁白 `<d>` 块之后立即写明对应画面人物的唇部状态**（保持闭合）：

```text
The man (S1) says in an off-screen voiceover: <d>[English] I still remember that road.</d> while his lips remain completely closed.
```

## 跨镜台词与截断

- 同一句台词/歌词跨切镜：在两部分的连接点都写 `<scenetrans>`，并明确写音频跨镜延续。可用表述：`continues seamlessly across the cut` / `continues uninterrupted into the next shot` / `carries over from the previous shot` / `remains audible across the transition`。
- 台词被视频结尾截断：用 `<cutoff>`。
- 台词长度与镜时长匹配：一句 10 个词的台词塞进 1.5 秒的镜必然丢句；对白密集时优先保完整台词时间轴（官方口径），镜数与切镜时间围绕台词排布。

## 画面可见文字

画面中实际可见的文字（横幅、招牌、标签、字幕、霓虹字）→ 放英文双引号内，**保留原文与标点，不翻译**：

```text
A red neon sign reading "营业中" glows above the doorway.
```

写明载体、位置与出现时机；同屏只留一处文字事件，错字率高时优先减文字镜数。

## overall_soundscape 写法

- 1–4 句英文，连续一段，概括全片：环境音（风/雨/交通）、物理动作音（脚步/织物/碰撞）、非语言人声（呼吸/笑声/喘息）。
- 台词、歌声、画面内配乐已在主描述字段，**不在本段重复**。
- 官方示例：

```text
overall_soundscape: Steady rain taps against the café windows while low room ambience continues underneath. The entrance bell rings once, followed by wet footsteps and the soft scrape of a chair.
```

## non_diegetic_music 写法

- 1–3 句英文，描述只有观众能听到的配乐：写乐器、速度、节奏、强弱变化；**不写抽象情绪词，不解释配乐的情感功能**（"悲伤的钢琴"❌ → "sparse piano notes at a slow tempo"✅）。
- 角色能听到的音乐（歌声、乐器、radio/电视/手机播放）是画面内事件，写进主描述字段。
- 官方示例：

```text
non_diegetic_music: Sparse piano notes at a slow tempo, joined by sustained low strings that gradually increase in volume before fading out.
```

- 参考音频同时含环境层与配乐层时，分别写进对应字段并注明复制/参考关系：

```text
overall_soundscape: The copied ambience layer from <Audio 1> continues throughout the target video.
non_diegetic_music: <Audio 2> is directly reused as the complete audience-only score.
```

## 语种边界

- 11 种稳定支持：Arabic、Chinese、English、French、German、Italian、Japanese、Korean、Portuguese、Russian、Spanish。其他语种不同程度支持，交付时注明稳定性有限。
- `<d>` 内语言标签与实际台词语言一致；台词原话是什么语言就写什么语言，正文其余部分始终英文。
- 需要角色唱歌词时：歌词同样进 `<d>`（用 sing 类动词引导），逐字保真规则与台词相同；只有观众能听到的歌曲人声属画面内事件还是配乐，按"角色能否听到"判定归属。
