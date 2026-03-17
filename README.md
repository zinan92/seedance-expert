<div align="center">

# seedance-expert

**让 Claude 变成你的 Seedance 2.0 视频导演 — 自动分类任务、编排素材、生成可直接投喂的 prompt**

[![Claude Code Skill](https://img.shields.io/badge/Claude_Code-Skill-blueviolet.svg)](https://claude.com/claude-code)
[![Seedance 2.0](https://img.shields.io/badge/Seedance-2.0-ff6b35.svg)](https://jimeng.jianying.com/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

</div>

---

## 痛点

Seedance 2.0 支持图片、视频、音频、文本四种模态输入，能力很强但上手门槛也高：素材该怎么分工？`@图片1` 管什么、`@视频1` 管什么？复杂片段要不要拆时间轴？延长视频的时长填"新增"还是"总时长"？一不留神就踩坑，出片全靠运气。

## 解决方案

安装这个 Claude Code Skill 后，只要你说"帮我写个 Seedance prompt"，Claude 就会自动走完完整的导演流程：判断任务类型 → 分配素材角色 → 填充 8 槽位 prompt → 提醒平台限制 → 输出可直接复制到即梦的成品 prompt。内置 50+ 官方手册真实案例作为参考。

## 架构

```
用户请求
   │
   ▼
┌──────────────────┐
│  Step 1: 任务分类  │  ← 10 类任务自动识别
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Step 2: 素材分工  │  ← 图片=外观 / 视频=动作 / 音频=节奏
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Step 3: Prompt 构建│  ← 8 槽位 + 时间轴 + 约束
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Step 4: 专项规则  │  ← 延长/编辑/卡点/一镜到底
└────────┬─────────┘
         ▼
┌──────────────────────────────────────────┐
│  输出: 任务判断 + 素材分工 + Prompt + 风险提醒 │
└──────────────────────────────────────────┘
```

## 快速开始

```bash
# 1. 克隆到 Claude Code skills 目录
git clone https://github.com/zinan92/seedance-expert.git ~/.claude/skills/seedance-expert

# 2. 重启 Claude Code（自动发现 skill）

# 3. 开始使用 — 以下任何说法都会触发
```

**触发示例：**

| 你说的话 | Claude 做的事 |
|---------|-------------|
| "帮我写个 Seedance 广告 prompt" | 走完完整流程，输出可复制 prompt |
| "我想用即梦把这段视频延长 5 秒" | 自动走 Video Extension 流程 |
| "参考这个视频的运镜拍个新角色" | 自动走 Camera Replication 流程 |
| "做个音乐卡点视频" | 自动走 Music Sync 流程 |

## 功能一览

| 功能 | 说明 | 状态 |
|------|------|------|
| 10 类任务自动分类 | 一致性、运镜复刻、模板复刻、剧情补全、视频延长、声音控制、一镜到底、视频编辑、音乐卡点、情绪演绎 | ✅ |
| 素材角色自动分配 | 图片→外观，视频→动作/运镜，音频→节奏/音色 | ✅ |
| 8 槽位 Prompt 构建 | 主体/场景/动作/镜头/节奏/声音/时间轴/约束 | ✅ |
| 6 个高频子模板 | 一致性、运镜复刻、视频延长、视频编辑、音乐卡点、情绪演绎 | ✅ |
| 50+ 官方案例参考 | 从即梦官方手册提取的真实 prompt | ✅ |
| 平台限制自动提醒 | 真人脸限制、文件数量/格式/大小限制 | ✅ |
| 通用 Prompt 骨架 | 含时间轴写法的万能模板 | ✅ |
| 常见失败点预警 | 7 类高频踩坑点自动检测 | ✅ |

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

> ⚠️ **当前不支持上传写实真人脸素材**（图片和视频均不行），系统会自动拦截。动漫/3D/风格化面孔可以。

## 项目结构

```
seedance-expert/
├── SKILL.md                              # 核心流程（任务分类→素材分工→Prompt构建）
└── references/
    ├── prompt-examples.md                # 50+ 官方手册真实 prompt 案例（按 10 类分组）
    └── platform-constraints.md           # 输入输出规格、格式限制、平台限制
```

## 技术栈

| 层级 | 技术 | 用途 |
|------|------|------|
| 运行时 | Claude Code | AI 编程助手 |
| 格式 | Markdown + YAML frontmatter | Skill 标准格式 |
| 数据源 | 即梦 Seedance 2.0 官方手册 | 案例和限制参数 |
| 平台 | [jimeng.jianying.com](https://jimeng.jianying.com/) | 视频生成 |

## For AI Agents

本节面向需要将此 skill 集成到工作流中的 AI Agent。

### 结构化元数据

```yaml
name: seedance-expert
description: Multi-modal AI video prompt architect for Seedance 2.0 (即梦)
version: 0.1.0
type: claude-code-skill
trigger_phrases:
  - "write a Seedance prompt"
  - "create a video with Seedance"
  - "generate Seedance 2.0 prompt"
  - "extend a video"
  - "edit a video with Seedance"
  - "music sync video"
  - "replicate camera movement"
  - "即梦"
capabilities:
  - classify video generation tasks into 10 categories
  - assign material roles (image=appearance, video=motion, audio=rhythm)
  - construct 8-slot prompts with timeline notation
  - apply task-specific rules (extension, editing, music sync, long-take)
  - warn about platform constraints (face restriction, file limits)
  - provide 50+ real prompt examples from official manual
input_format: natural language (Chinese or English)
output_format: |
  1. Task Classification
  2. Material Assignment
  3. Recommended Prompt (copy-paste ready for Seedance)
  4. Risk Warnings
install_command: git clone https://github.com/zinan92/seedance-expert.git ~/.claude/skills/seedance-expert
supported_task_types:
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
  output_duration: 4-15s
  realistic_human_faces: blocked
```

### Agent 调用示例

```python
# Claude Code skill 由对话自动触发，无需显式调用
# 以下展示 skill 的内部决策流程

user_request = "我有一张角色图和一段参考运镜视频，想拍个追逐戏"

# Step 1: 任务分类 → Camera & Motion Replication
task_type = "camera_motion_replication"

# Step 2: 素材分配
materials = {
    "@图片1": "角色外观参考",
    "@视频1": "运镜和动作参考"
}

# Step 3: 生成 prompt
prompt = """
参考@图1的角色形象，完全参考@视频1的运镜效果和动作节奏，
镜头跟随主角在街道中奔跑，从背面跟拍到侧面环绕...
"""

# Step 4: 风险检查
warnings = ["确认角色图不含写实真人脸"]
```

## 相关项目

| 项目 | 说明 | 链接 |
|------|------|------|
| 即梦 Seedance 2.0 | 字节跳动多模态视频生成平台 | [jimeng.jianying.com](https://jimeng.jianying.com/) |
| Claude Code | Anthropic 官方 CLI 编程助手 | [claude.com/claude-code](https://claude.com/claude-code) |

## License

MIT
