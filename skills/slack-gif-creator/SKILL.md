---
name: slack-gif-creator
description: 面向 Slack 的动图 GIF 制作知识与工具集，包含尺寸/帧率/颜色等约束、校验工具与动画概念。用户提出“给 Slack 做一个 X 做 Y 的 GIF”之类需求时使用。
license: Complete terms in LICENSE.txt
---

# Slack GIF 制作器

用于制作适配 Slack 的动图 GIF 的工具包与知识集合。

## Slack 要求

**尺寸：**
- 表情 GIF：128x128（推荐）
- 消息内 GIF：480x480

**参数：**
- FPS：10-30（越低文件越小）
- 颜色数：48-128（越少文件越小）
- 时长：表情 GIF 尽量控制在 3 秒以内

## 核心工作流

```python
from core.gif_builder import GIFBuilder
from PIL import Image, ImageDraw

# 1. 创建 builder
builder = GIFBuilder(width=128, height=128, fps=10)

# 2. 生成帧
for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))
    draw = ImageDraw.Draw(frame)

    # 用 PIL 图元绘制动画
    #（圆、多边形、线段等）

    builder.add_frame(frame)

# 3. 以优化参数保存
builder.save('output.gif', num_colors=48, optimize_for_emoji=True)
```

## 绘制图形

### 处理用户上传的图片
如果用户上传了图片，先判断他们想要：
- **直接使用**（例如“把这张图动起来”“把它拆成帧”）
- **作为灵感参考**（例如“做一个类似风格的”）

Load and work with images using PIL:
```python
from PIL import Image

uploaded = Image.open('file.png')
# 直接使用，或仅作为配色/风格参考
```

### 从零绘制
从零绘制图形时使用 PIL 的 ImageDraw 图元：

```python
from PIL import ImageDraw

draw = ImageDraw.Draw(frame)

# 圆/椭圆
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# 星形、三角形、任意多边形
points = [(x1, y1), (x2, y2), (x3, y3), ...]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b), width=3)

# 线段
draw.line([(x1, y1), (x2, y2)], fill=(r, g, b), width=5)

# 矩形
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)
```

**不要使用：** Emoji 字体（跨平台不可靠），也不要假设此技能内置任何预置图形素材。

### 让画面更精致

画面应该精致且有创意，不要像占位图。可参考：

**加粗线条**：轮廓线与线段尽量使用 `width=2` 及以上。细线（width=1）容易锯齿、显得廉价。

**增加纵深与层次**：
- Use gradients for backgrounds (`create_gradient_background`)
- Layer multiple shapes for complexity (e.g., a star with a smaller star inside)

**让形状更有趣**：
- Don't just draw a plain circle - add highlights, rings, or patterns
- Stars can have glows (draw larger, semi-transparent versions behind)
- Combine multiple shapes (stars + sparkles, circles + rings)

**注意配色**：
- Use vibrant, complementary colors
- Add contrast (dark outlines on light shapes, light outlines on dark shapes)
- Consider the overall composition

**复杂形状**（心形、雪花等）：
- Use combinations of polygons and ellipses
- Calculate points carefully for symmetry
- Add details (a heart can have a highlight curve, snowflakes have intricate branches)

要大胆且细致。一个好的 Slack GIF 应该是精致的，而不是占位图。

## 可用工具

### GIFBuilder (`core.gif_builder`)
用于组装帧并针对 Slack 做优化：
```python
builder = GIFBuilder(width=128, height=128, fps=10)
    builder.add_frame(frame)  # 添加 PIL Image
    builder.add_frames(frames)  # 添加帧列表
builder.save('out.gif', num_colors=48, optimize_for_emoji=True, remove_duplicates=True)
```

### 校验器（`core.validators`）
检查 GIF 是否满足 Slack 要求：
```python
from core.validators import validate_gif, is_slack_ready

# 详细校验
passes, info = validate_gif('my.gif', is_emoji=True, verbose=True)

# 快速检查
if is_slack_ready('my.gif'):
    print("Ready!")
```

### 缓动函数（`core.easing`）
使用缓动让运动更顺滑，避免线性机械感：
```python
from core.easing import interpolate

# 进度从 0.0 到 1.0
t = i / (num_frames - 1)

# 应用缓动
y = interpolate(start=0, end=400, t=t, easing='ease_out')

# 可用：linear, ease_in, ease_out, ease_in_out,
#      bounce_out, elastic_out, back_out
```

### 帧辅助函数（`core.frame_composer`）
常见需求的便捷函数：
```python
from core.frame_composer import (
    create_blank_frame,         # 纯色背景
    create_gradient_background,  # 垂直渐变背景
    draw_circle,                # 画圆辅助
    draw_text,                  # 简单文本渲染
    draw_star                   # 五角星
)
```

## 动画概念

### 抖动/震动
用振荡对位置做偏移：
- Use `math.sin()` or `math.cos()` with frame index
- Add small random variations for natural feel
- Apply to x and/or y position

### 脉冲/心跳
让对象尺寸有节奏地变化：
- Use `math.sin(t * frequency * 2 * math.pi)` for smooth pulse
- For heartbeat: two quick pulses then pause (adjust sine wave)
- Scale between 0.8 and 1.2 of base size

### 弹跳
对象下落并回弹：
- Use `interpolate()` with `easing='bounce_out'` for landing
- Use `easing='ease_in'` for falling (accelerating)
- Apply gravity by increasing y velocity each frame

### 旋转
围绕中心旋转：
- PIL: `image.rotate(angle, resample=Image.BICUBIC)`
- For wobble: use sine wave for angle instead of linear

### 渐入/渐出
逐渐出现或消失：
- Create RGBA image, adjust alpha channel
- Or use `Image.blend(image1, image2, alpha)`
- Fade in: alpha from 0 to 1
- Fade out: alpha from 1 to 0

### 滑入
从屏幕外移动到目标位置：
- Start position: outside frame bounds
- End position: target location
- Use `interpolate()` with `easing='ease_out'` for smooth stop
- For overshoot: use `easing='back_out'`

### 缩放
通过缩放与位置配合实现镜头感：
- Zoom in: scale from 0.1 to 2.0, crop center
- Zoom out: scale from 2.0 to 1.0
- Can add motion blur for drama (PIL filter)

### 爆炸/粒子喷射
创建向外发散的粒子：
- Generate particles with random angles and velocities
- Update each particle: `x += vx`, `y += vy`
- Add gravity: `vy += gravity_constant`
- Fade out particles over time (reduce alpha)

## 优化策略

仅当用户要求减小体积时，才使用下列优化手段：

1. **Fewer frames** - Lower FPS (10 instead of 20) or shorter duration
2. **Fewer colors** - `num_colors=48` instead of 128
3. **Smaller dimensions** - 128x128 instead of 480x480
4. **Remove duplicates** - `remove_duplicates=True` in save()
5. **Emoji mode** - `optimize_for_emoji=True` auto-optimizes

```python
# Maximum optimization for emoji
builder.save(
    'emoji.gif',
    num_colors=48,
    optimize_for_emoji=True,
    remove_duplicates=True
)
```

## 理念

此技能提供：
- **知识**：Slack 的约束与动画概念
- **工具**：GIFBuilder、校验器、缓动函数
- **灵活性**：用 PIL 图元自定义动画逻辑

它不提供：
- 固定的动画模板或预制函数
- Emoji 字体渲染（跨平台不可靠）
- 内置的图形素材库

**关于用户上传**：此技能不内置预制图形，但如果用户上传了图片，可用 PIL 加载并处理——根据需求判断是“直接使用”还是“仅作灵感参考”。

尽情发挥：可以组合多种概念（弹跳+旋转、脉冲+滑入等），充分利用 PIL 能力。

## 依赖

```bash
pip install pillow imageio numpy
```
