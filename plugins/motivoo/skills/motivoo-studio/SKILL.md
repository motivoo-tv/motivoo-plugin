---
name: motivoo-studio
description: 用 Motivoo 生成图片、视频、配音、音乐、音效或图像编辑（抠图/扩图/高清/消除/重绘）时使用。Use for any Motivoo image/video/audio generation request, checking Motivoo credits, or placing results on a Motivoo canvas.
---

# Motivoo Studio

Motivoo 通过 MCP 服务 `motivoo` 提供生成能力，结果按次消耗用户的 Motis 积分，并同步到用户的 Motivoo 画布。先阅读 MCP 连接里的 instructions，本 Skill 只补充工作纪律。

## 开始前

1. 调 `get_account`：确认连接可用，记下余额、今日剩余额度（`remaining_today`）与单次确认阈值（`confirm_threshold`）。阈值为 0 表示每次付费调用都要先问用户。
2. 连接失败或返回未授权：让用户在 MCP 管理里重新完成 Motivoo 账户授权，不要自己猜 token。
3. 确定画布：用户在继续之前的作品时用 `list_canvases` 找到对应画布并复用；新项目用 `create_canvas`；用户没要求就省略 `canvas_id`，结果进该连接的默认画布。

## 选模型与参数

- `list_models(type)` 返回每个模型真实支持的取值（比例、分辨率、时长、参考数量），只传其中出现的值；不确定就省略，用模型默认。
- 不要为了"更好"擅自换昂贵模型或提高分辨率；用户没指定时用推荐默认。

## 素材

- 本地文件：`create_upload` → 把返回的 curl 命令里的 `<LOCAL_FILE_PATH>` 换成本地文件路径后执行（file 字段必须放最后，成功是 HTTP 204）→ `finish_upload` 拿 `media_id`。素材是给某张画布用的，就在 `create_upload` 里带上 `canvas_id`，它会随画布一起管理。
- 网页上的图片/视频：先下载到本地，再按上面的方式上传；拿不到文件就请用户提供。生成工具不接受任意外部链接。
- 之前生成的结果可直接用其 `media_id` 作为参考。引用素材一律传 `media_id`；工具返回的 `url` 只用于下载和查看。
- 用参考图时先看一眼图片内容，把需要保留的主体特征写进提示词，不要只写“与参考图一致”。

## 费用纪律（必须遵守）

- 什么时候先 `estimate_cost`：视频、一次多张图、多镜头、用户问价，或 `confirm_threshold` 为 0 时。算出总价后连同计划一起告诉用户，得到同意再提交。单张图且用户已明确要生成时可以直接提交：超过阈值的调用不会被执行，只会返回 `needs_confirmation`，不扣费。
- `estimate_cost` 只报价，不会给 `confirm_token`。令牌只在生成工具返回 `needs_confirmation` 时出现。
- 今日剩余额度不足本次费用的 2 倍时，在征求同意时一并告诉用户（例如“生成后今日只剩 3 Motis”）。
- 工具返回 `needs_confirmation`：把金额如实告诉用户；用户同意后用**完全相同的参数和同一个 `idempotency_key`**，加上 `confirm_token` 重调。令牌 10 分钟内有效、只能用一次。不要替用户做决定，用户没明确同意就不要使用令牌。
- 返回 `budget_exceeded` 或余额不足：立即停下，告诉用户原因和处理入口（限额在 `settings_url`，充值在 `recharge_url`）。你无权修改限额。
- 超时不等于失败：用 `get_tasks` 继续等，**不要重复提交**；每个付费调用都必须带 `idempotency_key`：新请求用新的唯一值（如 UUID），重试同一请求（含带 `confirm_token` 再次调用）必须用同一个值和相同参数，这样不会重复扣费。
- 任务失败不自动付费重试：先分析原因（参数、提示词、素材），征得同意后再试。

## 交付

1. 任务完成后把产物下载到项目里：`curl -L -o ./motivoo-outputs/<filename> "<url>"`（`filename` 用工具返回的值）。
2. 汇报：生成了什么、文件路径、消耗多少 Motis、画布链接（`canvas_url`），以及剩余额度。
3. 用户要继续改：基于上一轮的 `media_id` 做参考或用 `edit_image`，并放在同一个画布。
