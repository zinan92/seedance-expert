---
name: seedance-expert
description: This skill should be used when the user asks to "write a Seedance prompt", "create a video with Seedance", "generate Seedance 2.0 prompt", "use Seedance for video generation", "extend a video", "edit a video with Seedance", "do music sync video", "replicate camera movement", or mentions Seedance, 即梦, video generation prompts, multi-modal video creation, or AI video editing. Provides expert guidance on Seedance 2.0 multi-modal video prompt construction, task classification, and best practices.
version: 0.1.0
---

# Seedance 2.0 Expert — Multi-Modal Video Prompt Architect

Seedance 2.0 is a multi-modal AI video generation system by ByteDance (即梦). It accepts images, videos, audio, and text as inputs, and generates controllable video output. The core philosophy: treat every piece of reference material as a controllable signal — images control appearance, videos control motion/camera, audio controls rhythm/voice, text orchestrates everything.

## Core Workflow

### Step 1: Classify the Task

Before writing any prompt, identify which task type the user needs. Map the request to one of 10 categories:

| # | Task Type | Trigger Signals | Primary Controls |
|---|-----------|----------------|-----------------|
| 1 | **Consistency** | "keep character same", "uniform style" | Image refs for character/product/scene |
| 2 | **Camera & Motion Replication** | "copy this camera move", "replicate action" | Video ref for motion + image ref for subject |
| 3 | **Creative Template / VFX** | "use this as template", "copy this ad style" | Video ref as structural template |
| 4 | **Story Completion** | "turn these images into a story" | Multiple image refs + narrative text |
| 5 | **Video Extension** | "continue this video", "extend 5 seconds" | Source video + extension description |
| 6 | **Voice & Sound** | "use this voice", "add narration" | Audio ref for voice/music + video/image refs |
| 7 | **Long-Take Continuity** | "one continuous shot", "no cuts" | Multiple scene image refs + explicit 一镜到底 |
| 8 | **Video Editing** | "change the character", "modify this video" | Source video + edit instructions |
| 9 | **Music Sync** | "beat-matched", "cut on the beat" | Audio ref for rhythm + image/video refs |
| 10 | **Emotional Performance** | "show emotion change", "dramatic reaction" | Video ref for expression + image ref for character |

When multiple types overlap, pick the dominant one and incorporate secondary elements.

### Step 2: Assign Material Roles

Every uploaded reference must have a clear role. Use @ notation:

- **Images** → control appearance: character look (@图片1), costume, product detail, scene/composition, first frame
- **Videos** → control motion: camera movement (@视频1), character action, transition style, VFX template, source for extension/editing
- **Audio** → control sound: voice timbre (@音频1), narration style, BGM mood, rhythm for beat-matching
- **Text** → orchestrate: describe subject, action, camera, scene, timing, constraints, relationships between refs

### Step 3: Construct the Prompt

Fill these 8 slots (skip irrelevant ones):

1. **Subject** — Who/what is the main character, appearance source
2. **Scene** — Where, what visual style, environment refs
3. **Action** — What the subject does, which video ref to follow
4. **Camera** — Push/pull/pan/tilt/follow/orbit, POV, reference source
5. **Rhythm** — Steady progression, rapid cuts, or beat-synced
6. **Sound** — Who speaks, what voice, BGM, sound effects
7. **Timeline** — For complex shots, use 0-3s / 3-6s / 6-10s format
8. **Constraints** — Consistency requirements, "no cuts", brand text legibility

### Step 4: Apply Task-Specific Rules

**Extension tasks:** Write "将@视频1延长 Xs". Generation duration = new portion length only. Emphasize continuity with the original ending.

**Editing tasks:** Explicitly state what to preserve and what to change: preserve original camera structure and action rhythm, only modify specified elements.

**Music sync tasks:** Write per-beat descriptions with timestamps. Key actions must land on rhythm beats.

**Long-take tasks:** Explicitly write 一镜到底 and 全程不要切镜头. Describe smooth transitions between scenes, avoid contradictory jump cuts.

**Multi-reference tasks:** When using 3+ refs, explicitly state each ref's role in the prompt. Never leave a ref unassigned.

## Output Format

When generating a response, always produce these 4 sections:

1. **Task Classification** — Which of the 10 types this belongs to
2. **Material Assignment** — What each @ref controls
3. **Recommended Prompt** — The actual prompt text ready for Seedance
4. **Risk Warnings** — Potential failure points, constraint violations

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

**Consistency:** 使用 @图片1 作为角色参考，保持人物长相、服装、配饰完全一致。视频中角色在不同镜头下依然保持统一形象，场景风格统一，画面细节稳定。

**Camera Replication:** 使用 @视频1 作为运镜与动作参考，学习其镜头移动方式、节奏和表演调度，但将主体替换为 @图片1 所示角色。

**Video Extension:** 将 @视频1 向后延长 5 秒，保持原有镜头语言、动作惯性、角色状态和情绪连续，自然承接原片结尾。

**Video Editing:** 基于 @视频1 进行编辑，保持原视频镜头结构和动作节奏不变，将主角替换为______，并加入______元素。

**Music Sync:** 使用 @音频1 作为节奏参考，所有关键动作、切景和造型变化对齐音乐重拍和节奏点。

**Emotional Performance:** 角色情绪从______逐步发展到______，表情和肢体变化明显，情绪转折自然有层次，镜头重点捕捉面部和动作细节。

## Common Failure Points

- Uploading realistic human face photos (blocked by platform)
- Assigning multiple refs without specifying each one's role
- Saying "reference this video" without clarifying: motion? camera? rhythm? VFX?
- Complex shots without timeline breakdown leading to incoherent output
- Requesting "one continuous shot" while describing jump cuts
- Confusing extension duration with total duration
- Music sync without explicit beat-point descriptions

## Additional Resources

### Reference Files

For detailed prompt examples from the official manual:
- **references/prompt-examples.md** — 50+ real Seedance 2.0 prompts organized by task type
- **references/platform-constraints.md** — Complete input/output specs, format limits, platform restrictions
