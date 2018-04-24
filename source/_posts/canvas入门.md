---
title: canvas入门
date: 2017-10-28 14:31:06
tags:
---

#### 线条的属性
* lineWidth: 设置线条的宽度
* lineCap: 设置线条两端帽子的样式
    'butt'(default)
    'round': 圆形
    'square': 方形
* lineJoin: 设置线条连接的样式
    'miter'(default): 尖角
        miterLimit: 设置尖角距离的最大值
    'bevel': 衔接
    'round': 圆角

#### 图形变换
* 位移 translate(x, y) 
* 旋转 rotate(deg)
* 缩放 scale(sx, sy) : 在 x 轴方向进行 sx 的缩放，在 y 轴方向进行 sy 的缩放

因为变换是级联的（下一次的变换会在上一次的基础上变换）:
context.save() // 保存状态
context.restore() // 回到之前的保存状态

变换矩阵:
a c e
b d f
0 0 1

a,d: 水平，垂直缩放
b,c: 水平，垂直倾斜
e,f: 水平，垂直位移

transform(a, b, c, d, e, f)
> transform(1, 0, 0, 1, 0, 0) // 单位矩阵

setTransform(a, b, c, d, e, f) // 先重置，再变换

#### 填充样式
fillStyle:

Linear Gradient:
```js
// step1:
var grd = context.createLinearGradient(xStart, yStart, xEnd, yEnd) // 渐变线

// step2:
grd.addColorStop(stop, color)
// 渐变线上的关键色
// stop:位置 [0.0 - 1.0]
// color: 颜色
```

Radial Gradient(径向渐变)(圆环)
```js
// step1: 
var grd = context.createRadialGradient(x0, y0, r0, x1, y1, r1)
// x0 y0 r0 : 定义第一个圆的 x y 坐标 和 半径
// x1 y1 r1 : 定义第二个圆的 x y 坐标 和 半径

// step2: 
grd.addColorStop(stop, color)
```

createPattern(img, repeat-style):
  img: new Image()
  repeat-style: no-repeat
                repeat-x
                repeat-y
                repeat
