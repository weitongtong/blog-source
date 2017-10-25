---
title: css变量
date: 2017-10-04 13:36:41
tags:
---

[Adobe Color CC](https://color.adobe.com/zh/create/color-wheel/)

基本用法：
声明一个变量：
```css
element{
  --main-bg-color: brown;
}
```
使用变量：
```css
element{
  background-color: var(--main-bg-color);
}
```

<!-- more -->

什么是`css`变量：
CSS 变量当前有两种形式：

变量，就是拥有合法标识符和合法的值。可以被使用在任意的地方。可以使用var()函数使用变量。例如：var(--example-variable)会返回--example-variable所对应的值。
自定义属性。这些属性使用--*where*的特殊格式作为名字。例如--example-variable: 20px;即使一个css声明语句。意思是将20px赋值给--example-varibale变量。

<div class="to-be-continued"></div>
