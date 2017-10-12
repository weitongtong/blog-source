---
title: TypeScript入门
date: 2017-10-12 16:47:46
tags:
---
TypeScript 的定位是做静态类型语言，而 Flow 的定位是类型检查器。
TypeScript 是 JavaScript 的超集，就是在 JavaScript 上做了一层封装，封装出 TypeScript 的特性，当然最终代码可以编译为 JavaScript。

demo.ts
```ts
let num: number
num = 'linkFly' // 会报错

const fetch = function(url: string): Promise {

}
// fetch() 函数接收一个 string 类型的参数 url，返回一个 Promise。

```
```ts
export const fetch = function (url: string | object, params?: any, user?: User): Promise<object | Error> {
  // dosomething

  return http(options).then(data => {
    return data
  }).catch(err => {
    return err
  })
}
```
这个 TypeScript 包含了很多信息：

1. url 可能是 string 或 object 类型
2. params 是可以不传的，也可以传递任何类型
3. user 要求是 User 类型的，当然也是可以不传
4. 返回了一个 Promise，Promise 的求值结果可能是 object，也有可能是 Error

看到上面的信息后，我们大概知道可以这么调用并处理 fetch 的返回结果：
```ts
let result = await fetch('https://tasaid.com', { id: 1 })

// fetch 可能会返回 Error
if (result instanceof Error) {
  // 错误处理
}
```

这就是静态数据类型的意义。静态类型在越复杂的应用中，需求越强烈。
这是 react 对于数据类型的约束：
```ts
import PropTypes from 'prop-types'

component.propTypes = {
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  requiredFunc: PropTypes.func.isRequired,
}
```
这是 vue 对于数据类型的约束：
```ts
Vue.component('component', {
  props: {
    optionalArray: Array,
    optionalBool: Boolean,
    optionalFunc: Function,
    requiredFunc: {
      type: Function,
      required: true
    }
  }
})
```
而引入了 TypeScript 之后，就会感受到真正流畅的数据类型约束：
```ts
class Component {
  optionalArray?: Array<string> // string 类型的 数组
  optionalBool?: boolean // 写上 ? 号，就表示着这个属性可能为空
  optionalFunc?: (foo: string, bar: number) => boolean // 函数的参数，返回值都一目了然
  requiredFunc: () => void 
}
```

小tips：
* 使用 visual studio code 编辑器会体验到 TypeScript 强大的类型推导，毕竟两个都是微软亲儿子。
* 一些 JavaScript 编写的大型的第三方库，都提供了 TypeScript 的类型声明文件（`*.d.ts` 文件）, 一般都放在包目录的 types 文件夹中。或者在 `@didi/*` 仓库名下可以找到。
* babel 是将高级版本的 JavaScript 编译为目标版本的 JavaScript，TypeScript 是将 TypeScript 编译为目标版本的 JavaScript。它们的编译是重叠的，也就是说 TypeScript 可以不再依赖 babel 编译。


#### 基础类型
```ts
let a: number
let b = true // 有默认值的情况，甚至不需要声明类型，ts 会自动推导
let c: [string, number] // 元组
enum Color { Red, Green, Blue } // 枚举
let d: { name: String } = { name: 'Bob' }
```
#### 复杂类型
```ts
// array
let list_a: number[] = [1, 2, 3]
let list_b: Array<number> = [1, 2, 3] // number 类型的数组
let list_c: [string, number] = ['Bob', 0]

// any
let notSure: any = 4
notSure = true // any 类型可以自由赋值

// 函数类型
let fn: (id: string) => number = (id) => 1
// 这里使用了 ECMAScript 6 的箭头函数，与下面的等价
let fn: (id: string) => number = function(id) {
  return 1
}
```
#### 高级类型
```ts
// 联合类型，foo 是 string 或 number
let foo: string | number

// 类型断言，强制使用兼容类型中的某一类型
(foo as string)

// 类型保护（判断）
if (typeof foo === 'string') {
  // dosomething
}

// 类型保护（判断）
if (foo instanceof String) {
  // dosomething
}
```
#### Model
```ts
let user: { id: number, name: string } = { id: 1, name: 'Bob' }
```
#### Class类
所有类型的根本都是类。
> 注意，TS 中类型是核心，当你想把一个项目从 JavaScript 迁移到 TypeScript 的时候，需要为项目中补充大量的类型，而这些类型大部分都是基于 Class 构建的。

```ts
class User {
  id: number
  name: string
}
let user: User = { id: 1, name: 'Bob' }
```
更多细节：
```ts
class User {
  // 只读属性
  readonly id: number

  // 存取器，get/set
  private _name: string
  get name(): string {
    // dosomething
    return this._name
  }
  set name(name: string) {
    console.log('this is set method')
    // dosomething
    this._name = name
  }

  // 构造函数
  constructor(id: number, theName: string) {
    // 只读属性只能在构造函数里初始化
    this.id = id
    this._name = theName
  }

  // 实例方法
  say() {
    console.log(`name: ${this.name}`)
  }

  // 静态方法
  static print() {
    console.log('static method')
  }
}

let user = new User(1, 'Bob')
user.name = 'aa' // => 'this is set method'
user.say() // => 'name: aa'
User.print() // => 'static method'
```
#### 泛型
泛型是用来解决类型重用的问题。

以下只能传递 number 的参数并返回：
```ts
function identity(arg: number): number {
  // dosomething
  return arg
}
```
现在想传递一个 string 类型的参数，然后也返回它，这个时候就可以使用范型，使用范型可以接收任意类型并返回：
```ts
// 这个 T 就是泛型，也可以叫其他名字
function identity<T>(arg: T): T {
  // dosomething
  return arg
}
identity<string>('Bob')
identity('Bob') // 自动推导
identity(0)
identity(true)
```
我们可以轻松地使用泛型来实现数据包装：
```ts
function fetch<T>(url: string): Promise<T> {
  // 远程请求数据并返回结果
  return http(url).then(data => {
    return data as T
  })
}
class User {
  name: string
}
// 泛型使用
let user = fetch<User>('http://xxx/api/user')
```











[ts中文网](https://tslang.cn/docs/handbook/decorators.html)
[资料](http://tasaid.com/Blog/20171011232755.html)
