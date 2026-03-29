# seedance-expert

将用户的视频创意转化为 Seedance 2.0 可直接执行的多模态 prompt，覆盖从素材诊断到图片准备到视频生成的完整导演流程。

```
in  自然语言描述（中文或英文）+ 可选的已有素材信息
out 素材诊断 + 图片生成 prompt + 视频 prompt（可直接粘贴到即梦）+ 执行步骤

fail 用户上传写实真人脸素材       → 警告：平台自动拦截，建议改用动漫/3D/风格化面孔
fail 无图片直接生文字视频         → 引导：先走 Phase 1 生成角色/场景图，避免角色漂移
fail 多素材未标注角色             → 补全：要求用户明确每个 @ref 的用途
fail 视频延长时长填成总时长       → 纠正：duration = 新增部分时长，非原片+延长
fail 一镜到底描述中包含跳切       → 矛盾检测：提醒用户修正镜头语言
fail 音乐卡点无节拍描述           → 补全：要求按时间轴标注关键节拍点
fail 图片风格与视频风格不一致     → 警告：水彩角色图 + 写实视频会导致风格冲突
fail 素材超出平台限制             → 提醒：图片≤9张/视频≤3个/音频≤3个/总计≤12文件
```

Adapters: Claude Code Skill（对话自动触发，无需显式调用）

## 架构

```
用户想法（文字描述）
    │
    ▼
Phase 0: 素材诊断 ─── 用户已有什么？缺什么？
    │
    ▼
Phase 1: 图片准备 ─── 生成角色图/场景图/首帧图（即梦图片 or Seedream）
    │
    ▼
Phase 2: 视频 Prompt ─ 任务分类 → 素材分工 → 8 槽位构建 → 专项规则
    │
    ▼
Phase 3: 执行指引 ─── 去即梦网站的具体操作步骤
    │
    ▼
输出: 素材诊断 + 图片 prompt + 视频 prompt + 风险提醒 + 操作步骤
```

核心洞察：**纯文字 → 视频快但不稳定；图片先行 → 视频慢但可控性远高于纯文字。** 本 skill 始终引导用户走图片先行路线。

## 快速开始

```bash
# 克隆到 Claude Code skills 目录
git clone https://github.com/zinan92/seedance-expert.git ~/.claude/skills/seedance-expert

# 重启 Claude Code（自动发现 skill），然后直接对话触发
```

触发示例：

| 你说的话 | Claude 做的事 |
|---------|-------------|
| "帮我写个 Seedance 广告 prompt" | 完整流程：素材诊断 → 图片准备 → 视频 prompt → 执行步骤 |
| "我想用即梦把这段视频延长 5 秒" | Video Extension 流程，自动纠正时长填写方式 |
| "参考这个视频的运镜拍个新角色" | Camera Replication 流程，引导先准备角色图 |
| "做个音乐卡点视频" | Music Sync 流程，要求按节拍标注时间轴 |
| "一镜到底拍个谍战短片" | Long-Take 流程，检测跳切矛盾 |

## 功能一览

| 功能 | 说明 |
|------|------|
| 10 类任务自动分类 | 一致性、运镜复刻、模板复刻、剧情补全、视频延长、声音控制、一镜到底、视频编辑、音乐卡点、情绪演绎 |
| 素材诊断 | 判断用户已有素材，缺失部分自动引导至 Phase 1 |
| 图片 Prompt 生成 | 角色图/场景图/首帧图/产品图/表情参考，含风格、景别、比例建议 |
| 素材角色自动分配 | 图片→外观，视频→动作/运镜，音频→节奏/音色 |
| 8 槽位 Prompt 构建 | 主体/场景/动作/镜头/节奏/声音/时间轴/约束 |
| 6 个高频子模板 | 一致性、运镜复刻、视频延长、视频编辑、音乐卡点、情绪演绎 |
| 50+ 官方案例参考 | 从即梦官方手册提取的真实 prompt，按 10 类分组 |
| 8 类失败点预警 | 真人脸、风格冲突、时长误填、跳切矛盾等自动检测 |
| 执行步骤生成 | 从打开即梦到点击生成的完整操作指引 |

## 10 类任务速查

| # | 任务类型 | 触发关键词 | 核心控制 |
|---|---------|-----------|---------|
| 1 | 一致性 | "人物不要变"、"保持风格统一" | 图片参考角色/商品/场景 |
| 2 | 运镜复刻 | "按这个视频的镜头拍" | 视频参考运镜 + 图片参考主体 |
| 3 | 模板复刻 | "照着这个广告拍"、"复刻特效" | 视频作为结构模板 |
| 4 | 剧情补全 | "把这些图编成故事" | 多图 + 叙事文本 |
| 5 | 视频延长 | "接着拍 5 秒" | 源视频 + 延长描述 |
| 6 | 声音控制 | "用这个音色"、"加旁白" | 音频参考音色/BGM |
| 7 | 一镜到底 | "不要切镜头"、"连续镜头" | 多场景图 + "一镜到底" |
| 8 | 视频编辑 | "把角色换掉"、"改发型" | 源视频 + 编辑指令 |
| 9 | 音乐卡点 | "跟着节奏切"、"卡点视频" | 音频参考节奏 + 图片/视频 |
| 10 | 情绪演绎 | "情绪爆发"、"表情变化" | 视频参考表情 + 图片参考角色 |

## 平台限制速查

| 模态 | 格式 | 上限 | 大小 |
|------|------|------|------|
| 图片 | jpeg/png/webp/bmp/tiff/gif | 9 张 | < 30 MB/张 |
| 视频 | mp4/mov | 3 个，总时长 2-15s | < 50 MB |
| 音频 | mp3/wav | 3 个，总时长 ≤ 15s | < 15 MB |
| 混合 | — | 总计 12 个文件 | — |
| 输出 | — | 4-15 秒可选 | 自带音效 |

当前不支持上传写实真人脸素材（图片和视频均不行），系统会自动拦截。动漫/3D/风格化面孔可以。

## 技术栈

| 层级 | 技术 | 用途 |
|------|------|------|
| 运行时 | Claude Code | AI 编程助手，Skill 宿主 |
| 格式 | Markdown + YAML frontmatter | Skill 标准格式 |
| 数据源 | 即梦 Seedance 2.0 官方手册 | 50+ 案例和平台限制参数 |
| 平台 | [jimeng.jianying.com](https://jimeng.jianying.com/) | 字节跳动多模态视频生成 |

## 项目结构

```
seedance-expert/
├── SKILL.md                           # 核心 Skill 定义（4 阶段流程 + 模板 + 规则）
└── references/
    ├── prompt-examples.md             # 50+ 官方手册真实 prompt 案例（按 10 类分组）
    └── platform-constraints.md        # 输入输出规格、格式限制、平台约束
```

## For AI Agents

### Capability Contract

```yaml
name: seedance-expert
version: 0.2.0
type: claude-code-skill

capability: |
  将自然语言视频创意转化为 Seedance 2.0 可执行的多模态 prompt，
  覆盖素材诊断 → 图片准备 → 视频 prompt 构建 → 执行指引全流程。

input:
  format: natural language (Chinese or English)
  required: 视频创意描述
  optional: 已有素材清单（图片/视频/音频）

output:
  sections:
    - 素材诊断（有什么、缺什么）
    - 图片 prompt（如需生成）
    - 任务分类（10 类之一）
    - 素材分工（每个 @ref 的角色）
    - 视频 prompt（copy-paste ready）
    - 风险提醒
    - 执行步骤

task_types:
  - consistency
  - camera-motion-replication
  - creative-template-vfx
  - story-completion
  - video-extension
  - voice-sound
  - long-take-continuity
  - video-editing
  - music-sync
  - emotional-performance

platform_constraints:
  max_images: 9
  max_videos: 3
  max_audio: 3
  max_files_total: 12
  output_duration_seconds: 4-15
  realistic_human_faces: blocked

failure_modes:
  - condition: realistic human face upload
    behavior: warn and suggest stylized alternatives
  - condition: text-only without images
    behavior: redirect to Phase 1 image preparation
  - condition: multi-ref without role labels
    behavior: request explicit role assignment
  - condition: extension duration confusion
    behavior: correct to new-portion-only semantics
  - condition: long-take with jump cuts
    behavior: detect contradiction and prompt revision
  - condition: music sync without beat markers
    behavior: request timeline beat annotations
  - condition: style mismatch between image and video
    behavior: warn about inconsistency risk
  - condition: file count exceeds platform limit
    behavior: alert with specific limit numbers

install: git clone https://github.com/zinan92/seedance-expert.git ~/.claude/skills/seedance-expert

trigger_phrases:
  - "write a Seedance prompt"
  - "create a video with Seedance"
  - "generate Seedance 2.0 prompt"
  - "extend a video"
  - "edit a video with Seedance"
  - "music sync video"
  - "replicate camera movement"
  - "即梦"
  - "做个 AI 视频"
```

### Agent 调用示例

```python
# Claude Code skill 由对话自动触发，无需显式调用
# 以下展示 skill 的内部决策流程

user_request = "我有一张角色图和一段参考运镜视频，想拍个追逐戏"

# Phase 0: 素材诊断
diagnosis = {
    "has": ["角色图 x1", "运镜参考视频 x1"],
    "missing": ["场景图（追逐发生在哪里？）"],
    "next": "Phase 1 — 生成场景图"
}

# Phase 1: 图片准备
image_prompts = [
    "电影写实风格，广角镜头，夜晚的雨中小巷，两侧老旧建筑，地面积水反射霓虹灯光，雾气弥漫，暗调高对比度"
]

# Phase 2: 任务分类 → Camera & Motion Replication
task_type = "camera_motion_replication"
materials = {
    "@图片1": "角色外观参考",
    "@图片2": "场景环境参考",
    "@视频1": "运镜和动作参考"
}

# Phase 2: 生成 prompt
prompt = """
参考@图片1的角色形象，完全参考@视频1的运镜效果和动作节奏，
场景为@图片2的雨夜小巷，镜头跟随主角在巷中奔跑，
从背面跟拍到侧面环绕，积水飞溅...
"""

# Phase 2: 风险检查
warnings = ["确认角色图不含写实真人脸", "场景图风格需与角色图一致"]
```

## 相关项目

| 项目 | 说明 | 链接 |
|------|------|------|
| 即梦 Seedance 2.0 | 字节跳动多模态视频生成平台 | [jimeng.jianying.com](https://jimeng.jianying.com/) |
| Claude Code | Anthropic 官方 CLI 编程助手 | [claude.com/claude-code](https://claude.com/claude-code) |

## License

MIT
