---
name: motivoo-product-shots
description: 用产品照片生成电商主图、场景图、海报和社媒素材：抠图、换场景、扩图。Use when the user wants product photos, e-commerce images, posters or marketing visuals from a product picture with Motivoo.
---

# Motivoo 产品图流程

依赖 `motivoo-studio` 的费用纪律与交付约定。

## 1. 准备

1. 拿到产品图：本地文件走 `create_upload` → curl → `finish_upload`；网页图片先下载到本地再同样上传。
2. 确认用途与尺寸：电商主图常用 1:1，海报 3:4 或 9:16，横幅 16:9。
3. 新建或复用画布（`create_canvas` / `list_canvases`）。

## 2. 产品锁定

- 背景杂乱时先 `edit_image(tool=bg_remove)` 得到干净的产品图，后续都用它的 `media_id` 作为 `reference_images`。
- 提示词里明确"保持产品外观、logo、颜色与比例不变"，只描述场景、光线、道具与构图。

## 3. 出图

- 先 `count=2` 试 1–2 个方向给用户挑，确认方向后再按需要的数量批量生成。
- 需要更大画幅：`edit_image(tool=outpaint)` 扩边。
- 局部问题（多余物体、瑕疵）：准备白色标记区域的蒙版图上传后用 `erase` 或 `inpaint`。

## 4. 交付

- 下载到 `./motivoo-outputs/<产品名>/`，文件名体现用途（如 `main-1x1.png`）。
- 汇报每张图的用途、消耗与画布链接。
