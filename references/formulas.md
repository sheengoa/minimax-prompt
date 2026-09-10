# 公式库：五模式完整公式

依据 docs/h3-prompt-guide-base-en.txt（基础模式）与 docs/h3-prompt-guide-ref-en.txt（全参考模式）提炼。所有公式产出的正文为英文；台词/歌词/画面文字保留原语言。字段名、顺序、指令句式严格照官方，不自创。

## 通用骨架：镜头与切镜（五模式共用）

- `[Shot 1]` 是开场镜，**无时间戳**；其后每镜 `[Shot N] At MM:SS.mmm, …` 开头，切镜时间严格递增且落在视频时长内（毫秒三位，如 `00:03.500`）。
- 切镜句式：`the camera cuts to` / `the shot cuts to` / `the shot transitions to` / `the shot changes to` / `the shot switches to`；用户明确要求时才可用 cross-dissolve、fade、wipe。
- **切镜必须引入新信息**：主体、空间、状态、视角或时间。只是距离或轻微角度变化 → 用运镜句，不切镜。
- `[Shot 1]` 开头先定风格与初始构图（风格词库：`Cinematic` / `live-action` / `2D-animated` / `3D CG` / `claymation` / `watercolor` / `vintage film`；关键帧任务从参考图取风格，T2VA 从用户文本取）。写法示例：`[Shot 1] Live-action, cinematic, a medium-wide shot frames…`
- 每个细节都要落在可见或可闻的事物上：视觉风格、初始构图、主体外观与位置、场景与关键道具、动作与反应、切镜、台词、同步的画面内声音。不写剧情梗概。

## T2VA（文生视频）

无参考素材，从文本直接构建完整视听时间轴。可在不偏离用户意图的前提下补足场景、人物、动作、声音细节。

结构：`integrated_multimodal_description` + `overall_soundscape` + `non_diegetic_music`，首行无指令句直接进字段。骨架见 SKILL.md「基础公式」，完整官方案例见 examples.md「面包师清晨营业」。

写作顺序建议：风格构图句 → 主体外观位置 → 动作与反应 → 运镜句 → 台词（如有）→ 下一镜切镜时间。

## I2VA（首帧图生视频）

**首行指令句（官方精确句式，不可改写）：**

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
```

指令行后接一个空行，再进三字段。`<Picture 1>` 是 0.00 秒处的真实首帧，属于 `[Shot 1]`。

**发展结构：首帧锚定 → 动作启动 → 连续发展 → 结果或反应。**

- `[Shot 1]` 开头先确立图中的风格、主体、构图与场景锚点，再写下一个动作；人物身份、服装、颜色、关键物件、空间关系保持一致（"remains/preserving…"）。
- 官方案例（火车窗边读信女子的完整提示词）见 examples.md。

## FL2VA（首尾帧）

**首行指令句（官方精确句式；N 为实际最后一镜号，S.SS 为有效时长、恰好两位小数）：**

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
```

**发展结构：首帧状态 → 可观察的中间变化 → 差异逐收窄 → 末帧状态。**

- 重点写主体如何移动、姿势如何变化、物件如何被操作、构图如何演进、场景与光影如何过渡；**不要把两张静态图的描述各写一遍再拼起来**，要写连接它们的运动路径。
- **默认单镜**（`[Shot 1]`），让模型从首帧连续插值到末帧；用户明确要求多镜才用多镜。
- 末帧必须在最后一个 `[Shot N]` 的结尾、视频结束时到达（"settles into the pose, spacing, and composition established by Picture 2 at the end of the shot"）。
- 官方案例（雨中骑车人开伞，8 秒单镜）见 examples.md。

## L2VA（尾帧图生视频）

**首行指令句（官方精确句式）：**

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
```

注意：`<Picture 1>` 是**最后一帧**，属于最后一个 `[Shot N]`，并不天然属于 Shot 1——句式中 `N` 取实际最后一镜的镜号（官方示例是单镜视频，故写作 `(from [Shot 1])`）。

**发展结构：合理的前置状态 → 明确的动作与过渡路径 → 最后一镜逐步收敛 → 落到末帧。**

- 从用户意图与末帧反推一个合理的前序状态，再描述人物、物件、镜头、场景如何逐步接近参考图。
- 官方案例（玻璃杯坠地碎裂，6 秒单镜）见 examples.md。

## Ref2VA（全参考模式，六段式）

适用：多图/视频/音频做参考——人物一致、风格迁移、运镜迁移、音色参考、分镜锚，以及整片编辑与续拍。镜头、运镜、说话人、台词的基础格式与 T2VA 共用（见上文），本节只讲全参考特有的部分。

### 六段结构与顺序（固定）

| 段 | 作用 |
|---|---|
| `subject_definitions` | 定义每条参考内容及其标签 |
| `summary` | 概括任务类型、目标视频与主要参考关系 |
| `retention_analysis` | 逐标签说明参考内容如何被保留/迁移/复用 |
| `detailed_description` | 按播放顺序逐镜描述画面、动作、声音、台词（主体） |
| `overall_soundscape` | 概括全片环境音与物理音 |
| `non_diegetic_music` | 描述只有观众能听到的配乐 |

六段全部用英文写；标签一旦分配，跨段含义不变。

### subject_definitions：四类标签

| 标签 | 用法 |
|---|---|
| `<Subject N>` | 可复用的可见内容单元：人/动物/物件、场景/环境、服装/道具/界面/特效、风格/动作/表情/姿势。定义其标签、参考角色与要跟随的主要特征，需要时可注明来源素材。一个 Subject 可由多个素材合成，一个素材也可供出多个 Subject |
| `<Picture N>` | 参考图本身作为具体帧锚（首帧/尾帧/关键帧/编辑帧/构图锚）或分镜规划锚时才独立建条目；仅用于定义人物/场景/风格时，不建条目，在对应 `<Subject N>` 定义里注明来源 |
| `<Video N>` | 只用于整片级关系：编辑原片、从原片结尾续拍、参考整片的运镜/切镜/节奏/时间结构。原片里的可见内容被复用时仍归 `<Subject N>` |
| `<Audio N>` | 独立音频信号：复制音轨、参考配乐风格、参考音色与语气、复用台词歌词、参考节拍。与目标说话人绑定时写 `<Subject N> (Sx)` 或稳定音色描述 + `(Sx)` |

写法示例（照官方）：

```text
<Subject 1> is the young woman in <Picture 1>, with long dark hair, a blue cardigan, and a thin silver necklace.
<Subject 1> is the woman whose appearance comes from <Picture 1> and whose walking motion comes from <Video 1>.
<Picture 2> is the first frame of [Shot 1], showing a woman seated beside a café window.
<Picture 3> is a storyboard reference for [Shot 1] and [Shot 2], defining their viewpoint, subject placement, and shot order.
<Video 1> is the source video for the target video edit.
<Audio 1> is the voice-timbre reference for <Subject 1> (S1).
```

- `<Video N>` 与 `<Audio N>` **独立编号**，编号不表配对关系；同源视频的声画可以一个是 `<Video 1>`、一个是 `<Audio 2>`。普通参考视频不因其文件含声就自动生成 `<Audio N>`，只有该音轨被明确复用/参考时才建。
- 一个音频素材兼任多角色时，用一句自然的话写清多角色，不拆多条。

### summary：任务类型前缀

以方括号任务类型开头，随后用已定义的标签概括目标视频与参考关系（本段不引入新标签）：

```text
[reference generation] …
[video editing + reference generation + audio reuse] …
```

| 任务类型 | 何时使用 |
|---|---|
| `keyframe completion` | 某图作为目标视频的首帧/尾帧/关键帧/编辑帧/其他具体帧锚 |
| `reference generation` | 图/视频/音频为人物、场景、风格、动作、运镜、分镜等提供生成指引，但不作为具体帧、也不是被编辑或续拍的源片 |
| `video editing` | 直接修改已有源视频（编辑图片或关键帧间生成不算） |
| `video continuation` | 新内容从已有源视频继续/延伸/衔接 |
| `audio reuse` | 同一音频信号被全部或部分复用 |
| `audio reference` | 音频不被直接复制，只参考音乐风格/音色/台词歌词内容/音效质感/节拍/连续性 |

- 满足多重关系用 ` + ` 组合，不重复同类：续拍源视频 + 一图作尾帧 → `[video continuation + keyframe completion]`；编辑源视频且保留原声 → `[video editing + audio reuse]`。
- **有视频/音频素材不自动产生对应任务类型**：参考视频只提供运镜/切镜/节奏 → 仍是 `reference generation`；只有直接被编辑或续拍才用 `video editing` / `video continuation`。
- 编辑任务的 summary 固定开头：`The target video is an edited version of <Video 1>.`

### retention_analysis：保真标记

逐标签一行，格式 `<标签> (出现镜号): 标记 - 说明`。

可见内容（`<Subject N>` / `<Picture N>` / `<Video N>`）：

| 标记 | 含义 |
|---|---|
| `fully_preserved` | 定义的角色被完整保留 |
| `partially_preserved` | 仍被使用，但部分定义特征被改变或只部分保留 |
| `attribute_transfer` | 参考特征被迁移到另一个可识别的目标主体 |
| `weak_reference` | 只保留风格/类别/构图/氛围上的大致相似 |

音频（`<Audio N>`）：

| 标记 | 含义 |
|---|---|
| `fully_copy` | 完整源音频作为目标视频的完整最终音轨 |
| `partially_copy` | 只复制部分时间轴或部分声音层，或复制后增删替换了其他声音 |
| `reference` | 信号不被直接复制，只参考音色/节奏/音乐风格/台词内容/音效质感 |
| `weak_reference` | 只保留类别或氛围的大致相似 |

写法示例（照官方）：

```text
<Subject 1> (appears in [Shot 1], [Shot 3]): fully_preserved - …
<Picture 2> ([Shot 1] first frame): fully_preserved - …
<Video 1> (cut and pacing structure): weak_reference - …
<Audio 1>: fully_copy - <Audio 1> is reused 1:1 as the target video's complete final audio track.
<Audio 2>: reference - the target speaker follows <Audio 2>'s voice timbre and measured delivery without copying the original signal.
```

标记只能在该标签于 `subject_definitions` 中已定义的参考角色范围内选择；目标视频里新增的动作/背景/情节不算保真度损失。

### detailed_description：主体描述

- 与 T2VA 的差异：主字段名不同（`detailed_description`）；**风格开场写在 `[Shot 1]` 之前**（一或两句英文，如 `The target video uses a realistic multi-camera sitcom style with warm indoor lighting.`）；在素材首次清晰出现处插入四类标签并说明其参考特征、画面位置与当前动作；音频关系在对应镜或语义阶段引用 `<Audio N>` 并说明复制还是参考。
- 帧锚自然措辞：`the shot begins from <Picture 1>` / `the shot's keyframe corresponds to <Picture 2>` / `the shot ends on <Picture 3>`。
- 主体说话时保留视觉标签 + 说话人 ID 双标记：`<Subject 2> (S1) turns toward the woman and says, <d>[English] …</d>`；同一主体画外说话保持同形并标 `off-screen`。
- 长度：生成任务 350–500 英文词；对白密集内容优先装下完整台词时间轴；编辑任务随源片复杂度伸缩。单镜不必然短——按信息量分配。
- 只复用参考音频中的台词/歌词时逐字保真（规则见 dialogue.md）；只参考音色/节奏/语气时，**不得**把参考音频的台词带进目标视频。
- `(Sx)` 按目标视频实际发声事件顺序一次性分配、全片复用；`retention_analysis` 中不写 `(Sx)`。BGM/完整音轨内的口头内容用 `<Audio N>` 作声源、不另造说话人；由具体人/角色/旁白发出的声音才分配 `(Sx)`。

完整官方案例（萨摩耶咖啡店，三镜情景剧）见 examples.md。

## 编辑任务与续拍任务（Ref2VA 特例）

- **编辑**：`<Video 1>` 是被编辑的源片；summary 用 `[video editing (+ …)]`，开头 `The target video is an edited version of <Video 1>.`；`retention_analysis` 逐项写明哪些保留（`fully_preserved`）哪些改动（`partially_preserved` + 改动说明）；`detailed_description` 描述的是**编辑后的成片**，在改动处自然引用标签与 `<Video 1>` 的源状态。
- **多处局部编辑可单次完成**（实测：恢复旧照片、清理场景等六处改动一条提示词能全部执行），但每处改动都要**与保持约束成对出现**——`Replace the potted plant with a floor lamp, keeping the sofa, lighting, and camera angle exactly as in <Video 1>.` 多处编辑逐条列出，末尾统一加一句其余元素保持不变；改动之间互相影响（换背景 + 换光照）才拆分任务。
- **身份保护镜头策略**：参考图人物的一致性随脸部出现时长与角度增加而下降——远景/背影镜头减少脸部渲染压力，关键辨识特征只放在特写镜里充分描述；正文可直接指定 `The face is shown clearly only in the final close-up; wide shots keep her seen from behind or in profile.`
- **续拍**：summary 用 `[video continuation (+ keyframe completion / audio reference)]`；`detailed_description` 前部承接源片结尾状态（人物位置、光影、机位），再展开新内容；音轨只延续可闻特征时用 `audio reference`。
- 接力生成的多段（长片拼接）每段都是一次独立生成任务，段间用尾帧作下一段首帧素材（`keyframe completion`）保持连贯。

## 长片拼接（>15s）

H3 单次封顶 15s，长叙事按镜头边界切段，每段 4–15s：

1. **切段**：在场景或情节的自然切点分段，段尾定格在可作静帧的构图上。
2. **段间衔接两法**：上一段尾帧图作下一段首帧素材（FL2VA/I2VA，`keyframe completion`）；或以源片段作 `<Video N>` 续拍（`video continuation`）。
3. **每段交付物**独立成套：提示词正文 + 平台设置 + 素材清单；主体跨段一致靠参考图（同一组 `<Subject N>` 特征描述 + 同一批参考图）。
4. **验收**逐段进行：上一段通过再生成下一段；衔接处重点检查主体外观、光影方向、空间连续性。
