---
title: flex
date: 2017-09-26 20:05:55
tags:
---
flex: 弹性布局
display: flex | inline-flex

#### 容器属性：

(1) flex-direction
决定主轴的方向(即项目的排列方向)。

* row(默认值): 主轴为水平方向，起点在左端。
* row-reverse: 主轴为水平方向，起点在右端。
* column: 主轴在垂直方向，起点在上沿。
* column-reverse: 主轴在垂直方向，起点在下沿。

(2) flex-wrap
默认情况下，项目都排在一条轴线上，如果一条轴线上显示不下，如何排列。

* nowrap(默认): 不换行(所有的压缩挤在一行)。
* wrap: 换行，第一行在上方。
* wrap-reverse: 换行，第一行在下方。

(3) flex-flow
为 flex-direction 和 flex-wrap 的简写形式，没什么好说的。

(4) **justify-content**
项目在**主轴**上的对齐形式。
* flex-start(默认值): 左对齐(靠左)。
* flex-end: 右对齐(靠右)。
* center: 居中。
* space-between: 两端对齐，项目之间的间距都相等。
* space-around: 每个项目两侧的间距都相等。所以项目之间的间距是项目和边框的间距的两倍。
<img src="http://oifogbmox.bkt.clouddn.com/170926-1.png" style="height: 350px" />

(5) **align-items**
项目在**交叉轴**上的对齐形式。
* flex-start: 交叉轴的起点对齐。
* flex-end: 交叉轴的终点对齐。
* center: 交叉轴的中点对齐。
* stretch(默认值)(伸展): 如果项目未设置高度或为auto，将占满整个容器的高度。
* baseline: 文字的基线对齐。
<img src="http://oifogbmox.bkt.clouddn.com/170926-2.png" style="height: 350px" />

(6) align-context
定义了多根轴线的对齐方式，如果只有一根，则不起作用。
* flex-start: 与交叉轴的起点对齐。
* flex-end: 与交叉轴的终点对齐。
* center: 与交叉轴的中点对齐。
* space-between: 与交叉轴两端对齐，轴线之间的间隔平均分布。
* space-around: 每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
* stretch（默认值): 轴线占满整个交叉轴。
<img src="http://oifogbmox.bkt.clouddn.com/170926-3.png" style="height: 350px" />

#### 项目属性：
(1) order
定义项目的排列顺序，数值越小，排列越靠前，默认为0。

(2) flex-grow
定义项目的放大比例，默认为0，即存在剩余空间，也不放大。

如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。
如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

(3) flex-shrink
定义了项目的缩小比例，默认为1，即如果空间不足，该项目缩小。
为0时，不缩小。

(4) flex-basis
定义了项目占据的主轴的空间，默认值为auto，即项目的本来大小。（如100px）

(5) flex
为flex-grow, flex-shrink 和 flex-basis的缩写。

(6) align-self
具体某个项目的 align-items(交叉轴上的对齐方式)。
