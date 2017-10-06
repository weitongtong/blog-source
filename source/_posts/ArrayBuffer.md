---
title: ArrayBuffer
date: 2017-10-06 08:40:52
tags:
---
二进制数组由三类对象组成：
(1) `ArrayBuffer`对象：代表内存之中的一段二进制数据，可以通过“视图”进行操作。“视图”部署了数组接口，这意味着，可以用数组的方法操作内存。
(2) `TypedArray`视图：

数据类型 | 字节长度 | 含义 | 对应的C语言类型
---|---|---|---
Int8 | 1 | 8位带符号整数 | signed char
Uint8 | 1 | 8位不带符号整数 | unsigned char
Uint8C | 1 | 8位不带符号整数<br />（自动过滤溢出） | unsigned char
Int16 | 2 | 16位带符号整数 | short
Uint16 | 2 | 16位不带符号整数 | unsigned short
Int32 | 4 | 32位带符号整数 | int
Uint32 | 4 | 32位不带符号的整数 | unsigned int
Float32 | 4 | 32位浮点数 | float
Float64 | 8 | 64位浮点数 | double

> 二进制数组并不是真正的数组，而是类似数组的对象

`ArrayBuffer`对象代表储存二进制数据的一段内存，它不能直接读写，只能通过视图(`TypedArray`视图和`DataView`视图)来读写，视图的作用是指定格式解读二进制数据。

`ArrayBuffer`也是一个构造函数，可以分配一段可以存放数据的连续内存区域。
```js
const buf = new ArrayBuffer(32) // 生成一段32字节的内存区域，每个字节的默认都是0。

const dataView = new DataView(buf) // 创建视图
dataView.getUint8(0) // 以不带符号的8位整数格式，读取第一个元素，结果为0
```
