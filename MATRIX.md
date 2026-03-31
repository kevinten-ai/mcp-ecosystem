# MCP Ecosystem Matrix

## Server Dependency Map

哪些 MCP 之间有数据流动关系：

```
mcp-image-gen ────────┬──► mcp-video-gen (image_url → img2vid)
    │                 │
    │ edit/upscale    ├──► mcp-media-toolkit (remove_bg → 去背景后的图)
    │                 │
    │                 ├──► mcp-avatar (image_path → 数字人)
    │                 │
    │                 └──► mcp-presentation (image_path → PPT配图)
    │
mcp-video-gen ────────┬──► mcp-ffmpeg (视频片段 → 合成/裁切)
    │                 │
    │ TTS/music/STT   ├──► mcp-subtitle (audio → 字幕)
    │                 │
    │                 └──► mcp-ffmpeg (音频 → 混合到视频)
    │
mcp-voice-clone ──────┬──► mcp-video-gen (克隆语音 → 配音)
    │                 │
    │                 └──► mcp-avatar (克隆语音 → 数字人配音)
    │
mcp-subtitle ─────────┬──► mcp-ffmpeg (SRT/ASS → 烧字幕到视频)
    │                 │
    │                 └──► mcp-content-styles (字幕文案 → 平台风格)
    │
mcp-content-styles ───┬──► mcp-social-publisher (风格化文案 → 发布)
    │                 │
    │                 └──► mcp-presentation (风格化内容 → PPT)
    │
mcp-3d-gen ───────────┬──► mcp-video-gen (3D预览图 → img2vid)
                      │
                      └──► mcp-image-gen (3D渲染图 → 编辑/超分)
```

## Shared Credentials

同一个 API Key 可以跨多个 MCP 使用：

| 凭据 | 使用的 MCP |
|---|---|
| `GEMINI_API_KEY` (GCP) | mcp-image-gen, mcp-video-gen (Veo + Lyria), mcp-subtitle (STT + 翻译) |
| `MINIMAX_API_KEY` | mcp-video-gen (视频 + TTS + 音乐) |
| `FISH_AUDIO_API_KEY` | mcp-voice-clone |
| `ELEVENLABS_API_KEY` | mcp-voice-clone (TTS + 音效) |
| `PEXELS_API_KEY` | mcp-media-toolkit (素材搜索) |
| `GCP_PROJECT_ID` | mcp-image-gen, mcp-video-gen, mcp-subtitle |

## Tool Count by MCP

| MCP | 工具数 | 工具列表 |
|---|---|---|
| mcp-image-gen | 3 | generate_image, edit_image, upscale_image |
| mcp-video-gen | 7 | generate_video, query_video_status, list_providers, generate_speech, generate_music, transcribe_audio, list_providers |
| mcp-3d-gen | 3 | generate_3d, query_3d_status, list_providers |
| mcp-avatar | 3 | generate_talking_head, query_avatar_status, list_providers |
| mcp-voice-clone | 5 | clone_voice, speak, list_voices, generate_sfx, list_providers |
| mcp-ffmpeg | 20+ | trim, concat, convert, subtitles, overlay, speed, aspect_ratio, ... |
| mcp-media-toolkit | 5 | remove_background, search_stock_media, resize_image, convert_format, create_collage |
| mcp-subtitle | 4 | transcribe_to_srt, translate_srt, create_bilingual_srt, srt_to_ass |
| mcp-presentation | 4 | create_slides, add_slide, export_to_pdf, create_thumbnail |
| mcp-content-styles | 4 | convert_content, get_platform_prompt, list_platforms, get_skill_content |
| mcp-social-publisher | 3 | publish, preview_content, list_platforms |
| **总计** | **~61** | |

## Installation Priority

按使用频率和依赖关系推荐的安装顺序：

### Tier 1: Core（必装）
1. **mcp-image-gen** — 图片是一切的起点
2. **mcp-video-gen** — 视频是最终产出
3. **mcp-ffmpeg** — 后期处理必备

### Tier 2: Enhancement（增强体验）
4. **mcp-media-toolkit** — 素材处理（免费）
5. **mcp-subtitle** — 字幕生成（GCP 赠金）
6. **mcp-content-styles** — 内容风格化（免费）

### Tier 3: Advanced（高级能力）
7. **mcp-voice-clone** — 声音克隆
8. **mcp-avatar** — 数字人
9. **mcp-presentation** — PPT 生成

### Tier 4: Distribution（分发）
10. **mcp-social-publisher** — 社交媒体发布
11. **mcp-3d-gen** — 3D 模型（特定场景）
