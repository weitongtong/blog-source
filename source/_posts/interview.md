---
title: interview
date: 2018-03-19 17:40:25
tags:
---
* Q: html中title属性和alt属性的区别？
```
<img src="#" alt="alt信息" title="title信息" />
// 当图片不输出信息的时候，会显示alt信息 鼠标放上去会出现title信息
// 当图片正常输出的时候，不会出现alt信息，鼠标放上去会出现title信息
```

* Q: ES5的继承和ES6的继承有什么区别？
ES5的继承实质上是先创建子类的实例对象，然后再将父类的方法添加到this上。
ES6的继承机制完全不同，实质上是先创建父类的实例对象this（所以必须先调用父类的super()方法），然后再用子类的构造函数修改this。

* Q: 垂直居中
https://www.cnblogs.com/zhouhuan/p/vertical_center.html

* router.push 和 location.href 的区别
前者改变的是 hash 或 history.pushState() 再根据对应的事件去触发
后者是直接页面跳转（页面刷新，hash的时候不刷新）

* 菜单下拉列表
  position: absoulte;
  transform: scaley(0) // 指定Y轴（垂直方向）的缩放
  transform-origin: 0 0 // X轴的动画起始位置 Y轴的动画起始位置
  transition: all 0.5s

  hover: transform: scaley(1)

require.js

按需加载


vue
created之前 将数据可响应式
mounted之前 编译

$nextTick

array 改下标不能监听到 原因

$set


css
实现三角形

Array.isArray

单例

vue不能检测数组
1. length 属性不能 Object.defineProperty
2. vue下索引也不允许更新，因为length = 5的数组，未必索引就有4，这个索引(属性)

defineProperty
  configurable: 可配置
    默认false, 为true时表示 该属性描述符才能被改变 和 该属性能被删除
  enumerable: 可枚举
    默认false, 为true时才可枚举
  value: 该属性对应的值
    默认undefined
  writable: 可写
    默认fasle，为true时才能被赋值
  get: getter方法
    默认undefined 该返回值用作属性值
  set: setter方法
    默认undefined
    
