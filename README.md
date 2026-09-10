# minimax-prompt

MiniMax H3 AI 视频提示词工程 Skill。基于 MiniMax 官方《Video Prompt Writing Guide》（T2VA/I2VA/FL2VA/L2VA）、《Full-Reference Mode Rewrite Output Format Guide》（Ref2VA）与平台文档（原文存档于 docs/）提炼，吸收官方 8 个风格 skill 的场景方法论，并经跨平台社区实测经验（含中文社区）校准。姊妹项目 [seedance-prompt](../seedance-prompt)（即梦）、[kling-prompt](../kling-prompt)（可灵）——三个平台底层提示词范式不同，本 skill 按 H3 范式独立构建，不与其他平台公式混用。目标：用户说出视频需求 → 产出贴合预期、可直接粘贴到海螺 AI（hailuoai.com / hailuoai.video）、MiniMax Hub（hub.minimax.io）或 API 使用的提示词。

## 三套范式的核心差异（为什么需要三个 skill）

| 维度 | 即梦 Seedance（时间戳流派） | 可灵 3.0（镜头/主体流派） | MiniMax H3（结构化字段流派） |
|---|---|---|---|
| 提示词形态 | 秒级时间戳剧本（"0-5s：…"） | 逐镜写"镜头N，Xs，景别，内容" | **固定字段**：三字段式 / Ref2VA 六段式，字段名与顺序官方规定 |
| 正文语言 | 中文 | 中文 | **英文**（台词/歌词/画面文字保留原语言） |
| 叙事单位 | 时间片 | 镜头 | `[Shot N]` + 毫秒级切镜时间（`At 00:03.500`） |
| 台词 | `角色说到：'…'` | `角色（语气）：台词` | **说话人 ID + 标签**：`(S1) says: <d>[English] …</d>` |
| 声音 | 时间片内写音效，禁止项禁 BGM | 音画同步开关 + Audio 随镜 | **三线分离**：台词进主描述、环境音进 `overall_soundscape`、配乐进 `non_diegetic_music` |
| 一致性 | 8 维描述 + @图绑定 | 主体库 @主体名 | **引用标签**：`<Subject N>`/`<Picture N>`/`<Video N>`/`<Audio N>` + 保真标记 |
| 时长 | 4–30s（超长 30–180s） | 3–15s | **4–15s 整数封顶**，长片靠多段拼接 |
| 编辑 | 智能编辑（A→B + 时间戳） | 指令变换固定句式 | Ref2VA `[video editing]` 任务 + retention 标记 |

## 结构

```
minimax-prompt/
├── SKILL.md                  # 主入口：平台分流 → 范式速览 → 选变体 → 模式路由 → 素材角色确认 → 需求访谈 → 方向对齐 → 写词 → 交付 → 自检 → 失败诊断
├── docs/                     # 官方资料原文存档（维护用）
│   ├── h3-prompt-guide-base-en.txt   # 官方提示词指南：T2VA/I2VA/FL2VA/L2VA
│   ├── h3-prompt-guide-ref-en.txt    # 官方指南：Ref2VA 全参考模式
│   ├── h3-official-skill.md          # 官方 h3-prompt-writing skill 原文
│   └── h3-platform-doc.md            # 平台文档存档（模型/参数/功能模式）
└── references/
    ├── formulas.md           # 公式库：五模式公式、Ref2VA 六段式、任务类型前缀表、保真标记表、编辑/续拍、长片拼接
    ├── dialogue.md           # 台词与声音：说话人 ID、<d> 标签、旁白、跨镜台词、画面文字、双声音字段、语种边界
    ├── video-analysis.md     # 参考视频解析：缩略图墙协议、证据分层、切镜时间提取、三线声音预分离、用途映射
    ├── vocabulary.md         # 词库：官方运镜三维表、切镜句式、转场事件写法、负向短语库、风格/光影/情绪外化、模糊词替代
    ├── templates.md          # 垂直模板：极简产品广告/品牌片/3D动画短片/定格科普/MV歌词/游戏开场/纸拼贴/实拍×手绘/对话短剧
    ├── examples.md           # 官方案例精读（5 完整案例含结构注解 + 4 极简示例）
    └── model-notes.md        # 模型速查：H3 vs H3-Max、参数边界、Context-IR、2K 重生成、试错策略、留白
```

## 工作流

1. **选变体**：要参考生成/首尾帧 → H3（Max 仅文生/图生）；犹豫默认 H3
2. **路由**：按需求映射五模式（T2VA/I2VA/FL2VA/L2VA/Ref2VA）；垂直题材先查 templates.md
3. **素材角色确认**（自适应）：上传视频先分流——样片复刻（不上传，产出自包含提示词 + 改动核对单）/ 素材引用（上传，Ref2VA 标签体系）/ 编辑 / 仅解析；上传图片另判角色——帧锚（`keyframe completion`）/ 参考定义 / 分镜锚
4. **参考视频解析**：需要用到视频内容的先按 video-analysis.md 完成结构化解析（切镜时间提取、主体特征清单、三线声音预分离），区分画面事实与推断
5. **需求访谈**：8 维清单（主体/风格/镜头结构/台词与说话人/声音设计/时长/画幅分辨率/素材），缺失才问，上限 3 个
6. **写词**：按官方字段结构写英文正文；台词进 `<d>`、声音三线分离、运镜查官方三维表、Ref2VA 先定标签与任务类型
7. **标准交付**：正文（英文）+ 中文对照说明 + 平台设置行 + 素材上传清单（上传顺序 = 标签编号；复刻场景附改动核对单）+ 可调项 + 假设说明
8. **自检**：字段结构、切镜时间递增、说话人 ID 一致、标签无悬空引用、素材上限、时长边界等清单核对
9. **失败诊断**：14 条症状 → 修改策略速查（一次只改一个变量）

## 安装（ZCode / Claude Code 类 Agent）

源文件维护在本仓库，通过链接接入 skill 目录（Windows）：

```powershell
New-Item -ItemType Junction -Path "$env:USERPROFILE\.zcode\skills\minimax-prompt" -Target "F:\project\code\minimax-prompt"
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\minimax-prompt" -Target "F:\project\code\minimax-prompt"
```

macOS / Linux：

```bash
ln -s /path/to/minimax-prompt ~/.agents/skills/minimax-prompt
```

> 另有 MiniMax 官方原版 skill 可直接安装：`npx skills add https://github.com/MiniMax-AI/MiniMax-H3 --skill h3-prompt-writing`——它是英文规则原文（本 skill docs/ 已存档）；本 skill 在其之上补中文工作流、需求访谈、样片复刻机制、标准交付格式与失败诊断。

## 维护说明

- 官方指南与平台文档原文存档于 `docs/`，H3 模型迭代时以此为校对基准更新公式与参数边界
- **只收录影响提示词写法的信息**（参数边界、字段结构、写法规则）；计费、API 集成等与写词无关的内容不进本 skill
- 参数边界（时长 4–15s、素材上限、分辨率档）与 H3 / H3-Max 强绑定，模型升级后需同步修订
- 官方指南与社区实测均未覆盖的领域（稳定性甜点区量化、多说话人上限的精确口径）留白待实测，不编造（见 model-notes.md「预期管理与留白」）；社区实测经验（能力画像、负向短语、台词节拍、编辑工作流、中文兼容写法）已内化进正文，不标注来源
- 官方风格 skill 原文未整体存档（体积大且绑 MiniMax Hub 工作流），其提示词写法层已提炼入 templates.md
