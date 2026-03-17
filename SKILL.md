---
name: seedance-expert
description: This skill should be used when the user asks to "write a Seedance prompt", "create a video with Seedance", "generate Seedance 2.0 prompt", "use Seedance for video generation", "extend a video", "edit a video with Seedance", "do music sync video", "replicate camera movement", "make an AI video", or mentions Seedance, 即梦, video generation prompts, multi-modal video creation, or AI video editing. Provides end-to-end guidance from image preparation through video prompt construction.
version: 0.2.0
---

# Seedance 2.0 Expert — End-to-End Video Creation Pipeline

Seedance 2.0 is a multi-modal AI video generation system by ByteDance (即梦). It accepts images, videos, audio, and text as inputs. The core insight: **text-only → video is fast but unstable; image-first → video is slower but far more controllable.** This skill covers the full pipeline.

## Pipeline Overview

```
用户想法（文字描述）
    │
    ▼
Phase 0: 素材诊断 — 用户已有什么素材？缺什么？
    │
    ▼
Phase 1: 图片准备 — 生成角色图/场景图/首帧图（即梦图片 or Seedream）
    │
    ▼
Phase 2: 视频 Prompt — 任务分类 → 素材分工 → 8 槽位构建
    │
    ▼
Phase 3: 执行指引 — 去即梦网站的具体操作步骤
```

## Phase 0: Material Diagnosis

Before anything else, ask the user what they already have:

| User has... | Next step |
|------------|-----------|
| Nothing (only an idea) | → Phase 1: generate key images first |
| Character images but no scene | → Phase 1: generate scene/environment images |
| All images ready | → Skip to Phase 2 |
| Existing video to extend/edit | → Skip to Phase 2 (Extension/Editing task) |
| Reference video for camera/motion | → Phase 1 if character images still needed, else Phase 2 |

**Key rule:** If the user has no images and the task requires character consistency, product detail, or specific composition — always recommend generating images first. Text-only video generation leads to unstable results.

## Phase 1: Image Preparation (Seedream / 即梦图片生成)

Determine which images are needed and generate prompts for each:

### Image Types to Prepare

| Image Type | When Needed | Prompt Focus |
|-----------|-------------|-------------|
| **Character portrait** | Character consistency tasks | Face, hair, clothing, accessories, pose, style |
| **Scene/environment** | Specific location needed | Architecture, lighting, atmosphere, color palette |
| **First frame** | Precise opening composition | Exact layout, camera angle, subject position |
| **Product shot** | Commercial/ad tasks | Product details, texture, branding, angles |
| **Costume/outfit** | Fashion or outfit-switching | Fabric, color, cut, accessories |
| **Expression reference** | Emotional performance | Specific facial expression, body language |

### Image Prompt Construction Rules

Write image prompts that maximize downstream video compatibility:

1. **Specify style explicitly** — "3D动画风格" / "电影写实风格" / "水墨风格" / "赛博朋克风格". The video will inherit this style.
2. **Describe the character thoroughly** — Age, gender, hair (color/length/style), clothing (specific items + colors), accessories. The more detail, the better consistency in video.
3. **Choose the right angle** — If this will be a first frame, match the camera angle to your planned video opening (正面半身 / 侧面全身 / 俯拍特写).
4. **Keep background simple for character refs** — Solid or blurred backgrounds make character extraction cleaner.
5. **Match resolution intent** — Vertical (9:16) for portrait/short video, horizontal (16:9) for cinematic, square (1:1) for product.

### Image Prompt Template

```
[风格]，[镜头/景别]，[人物描述：性别、年龄、发型、发色、服装、配饰、表情、姿势]，[场景/背景]，[光线/氛围]，[画面质量关键词]
```

**Example — Character portrait:**
```
3D动画风格，半身正面特写，一个25岁左右的亚洲女性，黑色长直发，穿着白色高领毛衣和深蓝色围巾，微笑表情，简洁的浅灰色背景，柔和的侧光，高质量，细节丰富
```

**Example — Scene/environment:**
```
电影写实风格，广角镜头，19世纪伦敦街头，鹅卵石路面，两侧维多利亚风格建筑，雾气弥漫，路灯昏黄光线，行人和马车的剪影，暗调电影色彩
```

**Example — First frame (for video opening):**
```
电影写实风格，中景正面镜头，一个穿红色风衣的女特工站在雨中的十字路口，手持黑色公文包，表情冷峻，背景是模糊的霓虹灯和行人，暗调高对比度，电影级构图
```

### Output for Phase 1

Produce a numbered list of images to generate:

```
📷 需要生成的图片清单：

1. 角色主图 — [prompt]
   用途：视频中作为 @图片1 角色外观参考
   建议比例：1:1

2. 场景图 — [prompt]
   用途：视频中作为 @图片2 环境参考
   建议比例：16:9

3. 首帧图 — [prompt]
   用途：视频首帧画面，决定开场构图
   建议比例：与视频输出比例一致
```

## Phase 2: Video Prompt Construction

### Step 1: Classify the Task

Map to one of 10 categories:

| # | Task Type | Trigger | Primary Controls |
|---|-----------|---------|-----------------|
| 1 | **Consistency** | "keep character same" | Image refs for character/product/scene |
| 2 | **Camera & Motion** | "copy this camera move" | Video ref for motion + image ref for subject |
| 3 | **Creative Template** | "use this as template" | Video ref as structural template |
| 4 | **Story Completion** | "turn images into story" | Multiple image refs + narrative text |
| 5 | **Video Extension** | "continue this video" | Source video + extension description |
| 6 | **Voice & Sound** | "use this voice" | Audio ref for voice/music |
| 7 | **Long-Take** | "one continuous shot" | Multiple scene images + "一镜到底" |
| 8 | **Video Editing** | "change the character" | Source video + edit instructions |
| 9 | **Music Sync** | "cut on the beat" | Audio ref for rhythm |
| 10 | **Emotional Performance** | "show emotion change" | Video ref for expression |

### Step 2: Assign Material Roles

- **Images** → appearance: character (@图片1), costume, product, scene, first frame
- **Videos** → motion: camera movement (@视频1), action, transitions, VFX, source for extension/editing
- **Audio** → sound: voice timbre (@音频1), BGM mood, rhythm for beat-matching
- **Text** → orchestration: subject, action, camera, timing, constraints, ref relationships

### Step 3: Construct 8-Slot Prompt

1. **Subject** — Who/what, appearance source
2. **Scene** — Where, visual style, environment refs
3. **Action** — What happens, which video ref to follow
4. **Camera** — Push/pull/pan/tilt/follow/orbit, POV
5. **Rhythm** — Steady / rapid cuts / beat-synced
6. **Sound** — Voice, BGM, sound effects
7. **Timeline** — Complex shots: 0-3s / 3-6s / 6-10s format
8. **Constraints** — Consistency, "no cuts", brand text legibility

### Step 4: Task-Specific Rules

- **Extension:** "将@视频1延长 Xs". Duration = new portion only. Emphasize continuity.
- **Editing:** State what to preserve and what to change.
- **Music sync:** Per-beat descriptions with timestamps. Actions on beats.
- **Long-take:** Write "一镜到底" + "全程不要切镜头". No contradictory jump cuts.
- **Multi-ref:** 3+ refs → each must have explicit role.

## Phase 3: Execution Guide

After generating prompts, provide actionable next steps:

```
📋 执行步骤：

1. 打开 jimeng.jianying.com → 图片生成
2. 依次生成上面的 X 张图片，保存到本地
3. 切换到「视频生成」→ 选择「全能参考」模式
4. 上传图片，按 @图片1/@图片2 顺序对应
5. 粘贴视频 prompt
6. 选择时长：X 秒，比例：X:X
7. 点击生成
```

For extension/editing tasks, remind: duration setting = new portion length, not total.

## Output Format

Always produce these sections:

1. **素材诊断** — What the user has, what's missing
2. **图片准备**（if needed）— Numbered image prompts with usage labels
3. **任务分类** — Which of the 10 types
4. **素材分工** — What each @ref controls
5. **视频 Prompt** — Copy-paste ready
6. **风险提醒** — Failure points, constraint violations
7. **执行步骤** — Step-by-step guide for jimeng.jianying.com

## Prompt Skeleton (Universal Template)

```
使用 @图片1 作为主角外观参考，保持人物长相、发型、服装和整体风格一致。
使用 @视频1 作为动作和运镜参考，复刻其中的镜头推进方式、人物动作节奏和转场感受。
使用 @音频1 作为声音/配乐参考，保持相近的情绪和节奏。
场景设定为______，整体风格为______。

0-3秒：______
3-6秒：______
6-10秒：______

要求：
1. 主角全程保持一致性，不漂移、不换脸、不变服装。
2. 动作连贯自然，镜头切换平滑。
3. 如果有对白，语气为______，情绪为______。
4. 如果有音乐卡点，关键动作落在节奏点上。
5. 成片风格统一，画面细节稳定。
```

## 6 High-Frequency Sub-Templates

**Consistency:** 使用 @图片1 作为角色参考，保持人物长相、服装、配饰完全一致。视频中角色在不同镜头下依然保持统一形象。

**Camera Replication:** 使用 @视频1 作为运镜与动作参考，学习其镜头移动方式、节奏和表演调度，但将主体替换为 @图片1 所示角色。

**Video Extension:** 将 @视频1 向后延长 5 秒，保持原有镜头语言、动作惯性、角色状态和情绪连续，自然承接原片结尾。

**Video Editing:** 基于 @视频1 进行编辑，保持原视频镜头结构和动作节奏不变，将主角替换为______。

**Music Sync:** 使用 @音频1 作为节奏参考，所有关键动作、切景和造型变化对齐音乐重拍和节奏点。

**Emotional Performance:** 角色情绪从______逐步发展到______，表情和肢体变化明显，情绪转折自然有层次。

## Common Failure Points

- Skipping image preparation → text-only video has inconsistent characters
- Uploading realistic human face photos (blocked by platform)
- Multiple refs without specifying each one's role
- "Reference this video" without clarifying: motion? camera? rhythm? VFX?
- Complex shots without timeline breakdown
- "One continuous shot" while describing jump cuts
- Confusing extension duration with total duration
- Music sync without explicit beat-point descriptions
- Image style mismatch → character portrait in 水彩 but video prompt says 写实

## Additional Resources

### Reference Files

- **references/prompt-examples.md** — 50+ real Seedance 2.0 prompts organized by task type
- **references/platform-constraints.md** — Input/output specs, format limits, platform restrictions
