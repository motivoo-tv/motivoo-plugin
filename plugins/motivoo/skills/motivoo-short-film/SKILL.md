---
name: motivoo-short-film
description: 把一个想法、剧本或分镜做成多镜头短片：先出风格统一的关键帧，再逐镜转视频，可加配音与配乐。Use when the user wants a short film, ad, trailer or multi-shot video made with Motivoo.
---

# Motivoo 短片流程

依赖 `motivoo-studio` 的费用纪律与交付约定。

## 1. 计划（不花钱）

1. 和用户确认：时长、画幅（如 16:9 / 9:16）、风格、是否需要旁白和音乐。
2. 拆成 3–8 个镜头，每镜写清：画面内容、主体、动作、镜头运动、时长。
3. `create_canvas` 新建一个以作品命名的画布，之后所有调用都带这个 `canvas_id`。
4. `list_models(type=image)` 与 `list_models(type=video)` 选模型；确认视频模型支持需要的时长、画幅和首帧参考（`video_modes` 含 first_last_frame 或 `reference_inputs` 含 image）。
5. 对每一步用 `estimate_cost` 估价并汇总（关键帧 + 视频 + 音频），把镜头表和总价给用户确认。

## 2. 角色与风格锚点

- 有角色或产品时，先生成 1 张定妆图（或用用户上传的图），之后每个关键帧都把它的 `media_id` 放进 `reference_images`，保证一致性。
- 提示词里固定风格描述（光线、色调、画风），每镜复用同一段。

## 3. 关键帧

- 每镜 `generate_image` 一张（`count=1`），画幅与视频一致。
- 把关键帧给用户看；不满意的镜头单独重做，不要整批重来。

## 4. 转视频

- 每镜 `generate_video`：`first_frame` = 该镜关键帧，`prompt` 只写动作与镜头运动，时长按镜头表。
- 视频需要几分钟：全部提交后统一用 `get_tasks(task_ids, wait_seconds=55)` 轮询，不要重复提交。

## 5. 声音（可选）

- 旁白：`list_voices` 选音色 → `text_to_speech`。
- 配乐：`generate_music`（`instrumental=true` 避免与旁白冲突，时长约等于成片总长）。

## 6. 交付

- 按镜头顺序下载到 `./motivoo-outputs/<作品名>/shot-01.mp4 …`，音频同目录。
- 如果本地有 ffmpeg 且用户需要，可按顺序拼接并混入配乐；否则告诉用户可在画布里继续剪辑（`canvas_url`）。
- 汇报每镜结果、总消耗与剩余额度。
