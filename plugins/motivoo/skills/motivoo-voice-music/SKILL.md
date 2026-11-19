---
name: motivoo-voice-music
description: 用 Motivoo 生成配音旁白、背景音乐、歌曲和音效。Use when the user needs narration/voice-over, text-to-speech, a song, background music or sound effects.
---

# Motivoo 声音流程

依赖 `motivoo-studio` 的费用纪律与交付约定。

## 配音 / 旁白

1. `list_voices(locale)` 列出音色，按语言、性别、年龄给用户推荐 2–3 个（可附 `preview_url` 试听）。
2. 长文本按段落拆分，每段一次 `text_to_speech`，方便单段重录。
3. 语速用 `speed`（0.5–2.0），不要改写用户的原文。

## 音乐

- `list_models(type=audio, audio_mode=music)` 看模型是否支持时长、人声性别等参数。
- 纯配乐：`instrumental=true`；歌曲：提供 `lyrics`，或省略让模型作词。
- `prompt` 写风格、情绪、乐器、速度，不写歌词。

## 音效

- `generate_sfx`：描述声音本身（材质、距离、环境），需要循环时 `loop=true`。
- 按模型的时长区间传 `duration_sec`。

## 交付

- 下载到 `./motivoo-outputs/audio/`，文件名标明用途（如 `narration-01.mp3`、`bgm.mp3`）。
- 汇报时长、消耗与画布链接。
