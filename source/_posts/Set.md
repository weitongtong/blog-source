---
title: Set
date: 2017-09-25 15:36:22
tags:
---

类似于数组，但成员的值都是唯一的。
```js
const s = new Set([1, 2, 2, 3])

s.add(2)
s.size()

for(let i of s) {
  console.log(i)
}
// 1 2 3
```
> Set中的重复判断 类似于`===`，但`NaN`除外

属性：
* `Set.prototype.constructor`: 构造函数
* `Set.prototype.size`: 返回`Set`实例的成员总数

方法：
* `add(value)`: 添加某个值，返回`Set`结构本身
* `delete(value)`: 删除某个值，返回一个布尔值，表示删除是否成功
* `has(value)`: 返回一个布尔值，标识该值是否为`Set`成员
* `clear()`: 清除所有成员，没有返回值

`Array.form`方法可以将`Set`结构转为数组：
```js
const items = new Set([1, 2, 3])
const arr = Array.from(items)
```

遍历：
* `keys()`: 返回键名的遍历器
* `values()`: 返回键值的遍历器
* `entries()`: 返回键值对的遍历器
* `forEach()`: 遍历回调

> `Set`的遍历顺序就是插入顺序。
> 由于`Set`结构没有键名，只有键值，所以`keys`方法和`values`方法的行为完全一致。

`Set`结构的实例默认可遍历，它的默认遍历器生成函数就是它的`values`方法
```js
Set.prototype[Symbol.iterator] === Set.prototype.values
// true
```

扩展运算符`...`内部使用`for...of`循环，所以也可以用于`Set`结构
```js
let set = new Set(['red', 'green'])
let arr = [...set]
// ['red', 'green']
```

目前没有直接的办法可以改变原来的`Set`结构
```js
// 方法一
let set = new Set([1, 2, 3])
set = new Set([...set].map(val => val * 2))

// 方法二
let set = new Set([1, 2, 3])
set = new Set(Array.from(set, val => val * 2))
```