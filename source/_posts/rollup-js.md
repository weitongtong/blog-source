---
title: rollup.js
date: 2017-09-16 15:23:22
tags:
---

# 初探-Rollup.js

## 一、为什么模块化

- 更好的代码组织方式
- 更好的依赖处理
- 性能优化
- ...

## 二、为什么要用Rollup打包工具

先做个简单的测试，我们准备了一个 module.js 文件和 entry.js 文件，分别用 Rollup 和 Webpack 打包。
[demo地址](https://www.baidu.com)

package.json 如下：
![image](http://oifogbmox.bkt.clouddn.com/ro1.png)

module.js 如下：
![image](http://oifogbmox.bkt.clouddn.com/ro2.png)

entry.js 如下：
![image](http://oifogbmox.bkt.clouddn.com/ro3.png)

rollup.config.js 如下：
![image](http://oifogbmox.bkt.clouddn.com/ro4.png)

webpack.config.js 如下
![image](http://oifogbmox.bkt.clouddn.com/ro5.png)


1. 对比打包之后的文件，webpack打包出来的约3k，而rollup的不到1k
![image](http://oifogbmox.bkt.clouddn.com/ro6.png)

2. Tree Shaking
Rollup是第一个提出Tree Shaking的打包工具。简单来说，Rollup会静态分析你所引入的模块，去掉没有真正用到的部分，只引入你需要的部分，减少体积。
rollup:
![image](http://oifogbmox.bkt.clouddn.com/ro7.png)
webpack:
![image](http://oifogbmox.bkt.clouddn.com/ro8.png)

  > Webpack2.0 已经支持 Tree Shaking, 但配置较为复杂 具体见官网

3. Rollup 是基于ES6实现的代码模块化
Rollup 对代码模块完全使用新的标准化格式，这些标准都包含在 JavaScript 的 ES6 版本中，而不是以前的特殊解决方案，如 CommonJS 和 AMD。

  ES6的部分功能
  - 语法更好
  - 模块导出（export）的是绑定，而不是值
    ES6:
    ![image](http://oifogbmox.bkt.clouddn.com/ro9.png)output: 0 1
    Commonjs:
    ![image](http://oifogbmox.bkt.clouddn.com/ro10.png)output: 0 0
  - 支持循环依赖

## 三、如何使用
  1. 安装
    ```bash
    sudo npm i rollup -g
    ```
  2. 使用方法（cli / api / other plugin）
    Rollup 提供了多种打包方式，通过 format 属性可以设置你想要打包成的代码类型：
    
    amd - 输出成AMD模块规则，RequireJS可以用
    cjs - CommonJS规则，适合Node，Browserify，Webpack 等
    es - 默认值，不改变代码
    iife - 输出自执行函数，最适合导入html中的script标签，且代码更小
    umd - 通用模式，amd, cjs, iife都能用

  3. 使用配置文件
    ```javascript
      // rollup.config.js
      export default {
        input: 'src/main.js',
        output: {
          file: 'bundle.js',
          format: 'cjs'
        }
      }
    ```
    ```bash
      // 默认 rollup.config.js
      rollup -c
      // 指定配置文件
      rollup --config rollup.config.dev.js
      rollup --config rollup.config.prod.js
    ```
  4. 使用插件
    通常我们需要导入从npm下载的模块(importing modules installed with npm) 比如：通过label编译模块，使用json等。For that, we use plugins, which change the behaviour of Rollup at key points in the bundling process. A list of available plugins is maintained on the Rollup wiki.

    以 **rollup-plugin-json** 为例 它能够使rollup导入json文件里的数据
    ```bash
      npm install --save-dev rollup-plugin-json
    ```
    > 我们使用--save-dev而不是--save是因为我的代码在运行时不是真正的以来这个插件——只是在我们编译bundle时依赖而已。

    ```javascript
    // rollup.config.js
    import json from 'rollup-plugin-json'

    export default {
      entry: './static/js/entry.js',
      output: {
        file: 'rollup_bundle_iife.js',
        format: 'iife',
      },
      plugins: [
        json()
      ]
    }
    ```
  5. 结合npm库使用rollup
    在某种情况下，你的项目需要下载npm的第三方模块到你的node_module文件夹中。跟其他的Webpack Browserfy 不同，Rollup不知道'out of box' 怎么处理这些依赖，我们需要添加一些设置。
    以 **the-answer** 为例：
    ```bash
    npm i the-answer -S
    ```
    ```javascript
    import answer from 'the-answer';

    export default function () {
      console.log('the answer is ' + answer);
    }
    ```
    编译报警告
    >(!) Unresolved dependencies
      https://github.com/rollup/rollup/wiki/Troubleshooting#treating-module-as-external-dependency
      the-answer (imported by main.js)

    导出来的bundle.js仍然能够在Node.js下运行，因为import声明被转化成CommonJS风格的require语句，但是the-answer没有放到bundle中，因此我们需要一个插件。

    **rollup-plugin-node-resolve 插件**

    ```bash
      npm install --save-dev rollup-plugin-node-resolve
    ```
    ```javascript
      // rollup.config.js
      import resolve from 'rollup-plugin-node-resolve';

      export default {
        input: 'src/main.js',
        output: {
          file: 'bundle.js'
          format: 'cjs'
        },
        plugins: [ resolve() ]
      }
    ```

    **rollup-plugin-commonjs插件**

    有些库导出的是es6模块，所以你可import——the-answer就是这种。然而npm的大多数第三方库是CommonJS风格的模块。在其发生改变之前，我们需要转换CommonJS为ES2015的模块，然后再用rollup处理。
    这正是rollup-plugin-commonjs的功能所在。

  6. 同级依赖peer dependencies
    ```javascript
    // rollup.config.js
    import resolve from 'rollup-plugin-node-resolve';

    export default {
      input: 'src/main.js',
      output: {
        file: 'bundle.js',
        format: 'cjs'
      },
      plugins: [resolve({
        // pass custom options to the resolve plugin（自定义）
        customResolveOptions: {
          moduleDirectory: 'node_modules'
        }
      })],
      // indicate which modules should be treated as external (这里把lodash作为同级依赖 不打包进来)
      external: ['lodash']
    };
    ```
  7. 结合Babel使用rollup
    很多开发者会在他们的项目中使用Babel，因此他们可以使用一些超前的es6特性，这样能够在浏览器和node环境中使用。
    同时使用rollup和Babel的最简单的方法是使用rollup-plugin-babel插件。安装：
```bash
  npm i -D rollup-plugin-babel
```
    rollup.config.js:
```javascript
// rollup.config.js
import resolve from 'rollup-plugin-node-resolve';
import babel from 'rollup-plugin-babel';

export default {
  entry: 'src/main.js',
  format: 'cjs',
  plugins: [
    resolve(),
    babel({
      exclude: 'node_modules/**' // only transpile our source code
    })
  ],
  dest: 'bundle.js'
};
  ```
    在babel能够真正的编译源码之前，你需要一些设置，创建一个新文件src/.babelrc：
```
{
  "presets": [
    ["latest", {
      "es2015": {
        "modules": false
      }
    }]
  ],
  "plugins": ["external-helpers"]
}
```
    在这一步有一些东西跟往常不太一样。首先我们设置modules:false，否则Babel将会在rollup转化前转化我们的模块为CommonJS风格，这样就无法实现rollup的目的（tree shaking）。

    其次，我们使用了external-helpers插件，它使rollup在bundle的头部添加一次'helper'，而不是在每个使用模块的地方包含他们（这是默认行为)。

    第三我们把.babelrc放在了src文件夹里，而不是项目的跟目录，如果我们售后需要它，这个允许我们有不同的.babelrc文件对应不同的需求，例如测试。（针对不同的需求设置不同的配置是个好的方法）

    现在，在运行rollup之前，我们需要安装latest预设以及external-helers插件。
  ```
    npm i -D babel-preset-latest babel-plugin-external-helpers
  ```
    现在运行rollup生成不打了，此时可以使用es2015特性了。首先更新下src/main.js的内容。

  ```javascript
  import answer from 'the-answer';

  export default () => {
    console.log(`the answer is ${answer}`);
  }
  使用npm run build运行rollup，然后查看bundle。

  'use strict';

  var index = 42;

  var main = (function () {
    console.log('the answer is ' + index);
  });
  ```

## 四、Big list of options

  1. globals -g / --globals
  use for umd / iife bundles. For example, in a case like this...
  ``` javascript
  import Vue from 'vue'
  ```
  ...we want to tell Rollup that the vue module ID equates to the global Vue variable:
  ``` javascript
  // rollup.config.js
  export default {
    ...,
    format: 'iife',
    moduleName: 'myBundle',
    globals: {
      vue: 'Vue', // vue 模块在 全局中对应的变量名称是'Vue'
    }
  }
  ```



  [官网](https://rollupjs.org/)
  [中文文档](https://segmentfault.com/a/1190000009910959#articleHeader7)
  [参考资料](https://zhuanlan.zhihu.com/p/27832148/)