# MCP Workflow Templates

预定义的工作流模板，展示如何组合多个 MCP 完成复杂任务。

## 1. 短视频制作流水线

**场景**：从文字创意到发布一条短视频（抖音/小红书/B站）

```yaml
name: short_video_pipeline
steps:
  - tool: mcp-image-gen.generate_image
    params: { prompt: "产品/场景描述", model: "imagen-4.0-generate-001" }
    output: base_images[]

  - tool: mcp-media-toolkit.remove_background
    params: { image_path: "${base_images[0]}" }
    output: clean_image
    optional: true

  - tool: mcp-image-gen.upscale_image
    params: { image_path: "${clean_image}", upscale_factor: "x2" }
    output: hd_image

  - tool: mcp-video-gen.generate_video
    params: { prompt: "动态描述", image_url: "${hd_image}", provider: "veo" }
    output: task_id

  - tool: mcp-video-gen.query_video_status
    params: { task_id: "${task_id}", provider: "veo" }
    output: video_path
    poll: true

  - tool: mcp-video-gen.generate_speech
    params: { text: "旁白文本", provider: "minimax" }
    output: voiceover_path

  - tool: mcp-video-gen.generate_music
    params: { prompt: "BGM风格描述", provider: "google-lyria" }
    output: bgm_path

  - tool: mcp-ffmpeg.concatenate_videos
    params: { video_paths: ["${video_path}"], output: "final.mp4" }
    note: "同时混入配音和BGM"

  - tool: mcp-subtitle.transcribe_to_srt
    params: { audio_path: "${voiceover_path}" }
    output: srt_path

  - tool: mcp-ffmpeg.add_subtitles
    params: { video_path: "final.mp4", subtitle_path: "${srt_path}" }
    output: final_with_subs

  - tool: mcp-content-styles.convert_content
    params: { content: "标题+描述", platform: "xiaohongshu" }
    output: formatted_copy

  - tool: mcp-social-publisher.publish
    params: { platform: "xiaohongshu", title: "${formatted_copy.title}", content: "${formatted_copy.content}" }
```

---

## 2. 多语言配音翻译

**场景**：将中文视频翻译为英文版本

```yaml
name: multilang_dubbing
steps:
  - tool: mcp-video-gen.transcribe_audio  # or mcp-subtitle
    params: { audio_path: "original.mp4", language_code: "cmn-CN" }
    output: transcript

  - tool: mcp-subtitle.translate_srt
    params: { srt_path: "${transcript.srt}", target_language: "en" }
    output: en_srt

  - tool: mcp-voice-clone.speak
    params: { text: "${en_transcript}", voice_id: "cloned_voice_id", provider: "elevenlabs" }
    output: en_voiceover

  - tool: mcp-subtitle.create_bilingual_srt
    params: { srt_path: "${transcript.srt}", target_language: "en" }
    output: bilingual_srt

  - tool: mcp-ffmpeg.add_subtitles
    params: { video_path: "original.mp4", subtitle_path: "${bilingual_srt}" }
    note: "替换音轨为英文配音，添加双语字幕"
```

---

## 3. AI 讲师课程制作

**场景**：从讲稿生成完整的教学视频

```yaml
name: ai_teacher
steps:
  - tool: mcp-image-gen.generate_image
    params: { prompt: "专业讲师半身照，白色背景" }
    output: teacher_photo

  - tool: mcp-presentation.create_slides
    params: { title: "课程标题", slides: [...], template: "corporate" }
    output: pptx_path

  - tool: mcp-avatar.generate_talking_head
    params: { image_path: "${teacher_photo}", text: "讲稿文本" }
    output: talking_head_task_id

  - tool: mcp-avatar.query_avatar_status
    params: { task_id: "${talking_head_task_id}" }
    output: teacher_video
    poll: true

  - tool: mcp-subtitle.transcribe_to_srt
    params: { audio_path: "${teacher_video}" }
    output: srt

  - tool: mcp-ffmpeg.add_subtitles
    params: { video_path: "${teacher_video}", subtitle_path: "${srt}" }
    output: final_lesson
```

---

## 4. 社交媒体内容矩阵

**场景**：一个核心创意，适配多平台发布

```yaml
name: content_matrix
steps:
  - tool: mcp-image-gen.generate_image
    params: { prompt: "核心视觉创意" }
    output: hero_image

  - tool: mcp-media-toolkit.resize_image
    params: { image_path: "${hero_image}", width: 1080, height: 1080, mode: "fill" }
    output: square_image  # 小红书

  - tool: mcp-media-toolkit.resize_image
    params: { image_path: "${hero_image}", width: 1080, height: 1920, mode: "fill" }
    output: portrait_image  # 抖音

  - tool: mcp-content-styles.convert_content
    params: { content: "核心文案", platform: "xiaohongshu" }
    output: xhs_copy

  - tool: mcp-content-styles.convert_content
    params: { content: "核心文案", platform: "weibo" }
    output: weibo_copy

  - tool: mcp-social-publisher.preview_content
    params: { platform: "xiaohongshu", title: "${xhs_copy.title}", content: "${xhs_copy.content}" }
    output: xhs_preview

  - tool: mcp-social-publisher.preview_content
    params: { platform: "weibo", title: "${weibo_copy.title}", content: "${weibo_copy.content}" }
    output: weibo_preview
```

---

## 5. 产品 3D 展示

**场景**：从产品描述生成 3D 模型 + 展示视频

```yaml
name: product_3d_showcase
steps:
  - tool: mcp-image-gen.generate_image
    params: { prompt: "产品概念图，白色背景，多角度" }
    output: product_image

  - tool: mcp-3d-gen.generate_3d
    params: { prompt: "产品3D模型描述", image_url: "${product_image}" }
    output: model_task_id

  - tool: mcp-3d-gen.query_3d_status
    params: { task_id: "${model_task_id}" }
    output: model_path
    poll: true

  - tool: mcp-video-gen.generate_video
    params: { prompt: "产品360度旋转展示", image_url: "${product_image}", provider: "veo" }
    output: showcase_video
```
