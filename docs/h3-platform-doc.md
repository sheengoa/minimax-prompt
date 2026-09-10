# MiniMax 平台文档：视频生成（网页创作台 + API 通用规则）原文存档

> 来源：https://platform.minimax.io/docs/guides/video-generation
> 抓取日期：2026-09-10（经网页读取，markdown 原文链接 `…/video-generation.md` 直连超时，正文按抓取内容转录，代码示例有删节；以官方文档站为校对基准）
> 用途：维护用原文存档。只收录影响提示词写法的信息（参数边界、功能模式、提示词建议）；计费与集成细节以官方文档站为准。

---

MiniMax 的视频模型（MiniMax H3、MiniMax H3 Max）支持高效的视频内容创作。

## 1. 支持的模型

| 模型 | 说明 |
|---|---|
| **MiniMax-H3** | 开放的通用多模态视频模型，统一理解文本、图像、视频、音频输入；支持视频生成、参考生成、视频编辑 |
| **MiniMax-H3-Max** | MiniMax 与 fal.ai 联合发布，由 fal.ai 在 H3 基础上后训练并针对高速生成优化；仅支持 T2V/I2V，参考生成"coming soon" |

两者均需选用 Pay-as-you-go（按量付费）API。

## 2. 功能模式（四种）

1. **文生视频 T2VA**：仅 prompt
2. **图生视频 I2VA**：prompt + 首帧图和/或尾帧图（`role=first_frame` / `last_frame`）
3. **参考生成 Ref2VA**：prompt + 参考图/视频/音频任意组合（`role=reference_image` / `reference_video`）
4. **首尾帧**：属于图生视频的特例（首帧+尾帧，`FL2VA`）

H3 全部支持；Max 仅前两种。

## 3. 基本参数

| 项目 | MiniMax-H3 | MiniMax-H3-Max |
|---|---|---|
| 分辨率 | 768P / 2K | 480P / 768P |
| 时长 | 4–15 秒（仅整数） | 5–15 秒（仅整数） |
| 画幅 | 常见比例或 adaptive | 同左 |

- 首尾帧图：0/1/2 张；宽高在 [256, 5760]；宽高比 2:5–5:2；无图时退化为文生视频
- 参考素材：图 ≤ 9 张；视频 ≤ 3 段（每段 2–15s，总长 ≤ 15s）；音频 ≤ 3 段（同上）；混合输入总计 ≤ 12 个文件
- 格式：视频 H.264/AVC、H.265/HEVC（内嵌音频 AAC/MP3）；图 JPG/JPEG/PNG/WEBP/HEIC/HEIF；音频 WAV/MP3
- 大小：视频 ≤ 50 MB/个、图 ≤ 30 MB/个、音频 ≤ 15 MB/个；请求体 ≤ 64 MB（大素材建议用 URL）
- **提示词长度上限：≤ 7000 字符**
- **ratio 规则**：文生视频（t2va）ratio 必填且不能为 "adaptive"；图生视频（i2v）ratio 恒为 "adaptive"（由输入图决定）
- 帧率：24 FPS（模型规格，见仓库 README）；输出音频 32 kHz 立体声

## 4. 提示词建议（官方）

- 文生视频时，可在关键描述后直接加镜头运动指令，如 `[pan]`、`[zoom]`、`[static]`
- 参考生成的写法：需在 prompt 中说明各参考素材的用途，如 "The character performs street dance, following the motion in reference video 1"
- 提示词增强工具 **H3-Context-IR**：可先创建该任务（异步，`task_type=h3_context_ir`），从 `content.prompt` 取得增强后的提示词再做视频生成；它深度解析多模态上下文、产出结构化表达并尽量保留原始意图，但该端点只返回提示词，不生成视频。官方强烈建议在生成管线中接入 Context-IR 或按 Prompting Guidance 自建上下文处理系统

## 5. 使用步骤（API，异步三步）

端点：`POST /v2/video_generation` 创建任务；`GET /v2/query/video_generation/{task_id}` 轮询（建议 10 秒间隔）；成功后从 `content.url` 下载。

- `model`：`MiniMax-H3` / `MiniMax-H3-Max`
- `content[]`：多模态条目（`type=text|image|video|audio` + `role`），文本条目即提示词
- 视频重生成（2K）：将符合 768P 规范的源视频升为 2K，须复现原 `content` 并恰好加一个 `role=base_video` 的 `video_url` 项
- 任务通过 `task_type`（`regeneration` / `h3_context_ir`）区分；任务列表可查最近 7 天

## 6. 官方示例提示词

- 文生视频："A tiktok dancer is dancing on a drone, doing flips and tricks."
- 图生视频："Contemporary dance, the people in the picture are performing contemporary dance."
- 首尾帧："A little girl grows up."
- 参考生成："The character performs street dance, following the motion in reference video 1; the character's appearance follows reference images 1 and 2."

> 注：以上是 API 文档的极简示例（演示请求格式用）。提示词结构化写作以 docs/h3-prompt-guide-base-en.txt 与 docs/h3-prompt-guide-ref-en.txt 两份官方指南为准。
