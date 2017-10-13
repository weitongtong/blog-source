---
title: vue-cli相关
date: 2017-10-13 16:49:29
tags:
---
wepack build之后会产生3个js文件：
* app.js 是入口js,具体业务代码（我们的src下的）
* vendor.js 通过提取公共模块插件来提取的代码块（node_modules）
* manifest.js 在vendor的基础上，再抽取出要经常变动的部分。

> 至于manifest的话，主要是一些异步加载的实现方法（通过建立script方式动态引入js），内容上包含异步js的文件名和路径。
> 主要是js的改动会改变异步加载里面的js文件名，频繁的变动，而相对来说vue库之类的代码，实际上只要编译打包一次就够了，如果只是打包成一个vendor的话，经常变动js会导致vendor重复编译，有点浪费，所以把会重复跟随变动的部分抽离出来作为manifest文件。

以下是webpack.prod.js的配置：
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module, count) {
    // any required modules inside node_modules are extracted to vendor
    return (
      module.resource &&
      /\.js$/.test(module.resource) &&
      module.resource.indexOf(
        path.join(__dirname, '../node_modules')
      ) === 0
    )
  }
}),
// extract webpack runtime and module manifest to its own file in order to
// prevent vendor hash from being updated whenever app bundle is updated
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  chunks: ['vendor']
})
```

打包工具要解决的问题:
* 文件依赖管理 梳理文件之间的依赖关系。
* 资源加载管理 处理文件的加载顺序（先后时机）和文件的加载数量（合并、嵌入、拆分）。
* 效率与优化管理 提高开发效率，完成页面优化。

webpack是一个现代Javascript应用的打包工具。它采用tool+plugins的结构。tool提供基础能力，即文件依赖管理和资源加载管理；在此基础上通过一系列的plugins来丰富打包工具的功能。

在webpack里，所有的文件都是模块。但是webpack只认识js模块，所以要通过一些loader插件把css、图片等文件转化成webpack认识的模块。

在webpack打包的文件中，模块是以模块函数来表示的。通过把文件转化成模块函数就可以控制模块的运行时机。即加载完成后不会立即执行，等到调用模块函数的时候才会执行。

webpack的工作步骤如下：
1. 从入口文件开始递归地建立一个依赖关系图。
2. 把所有文件都转化成模块函数。
3. 根据依赖关系，按照配置文件把模块函数分组打包成若干个bundle。
4. 通过script标签把打包的bundle注入到html中，通过manifest文件来管理bundle文件的运行和加载。

打包的规则为：
一个入口文件对应一个bundle。该bundle包括入口文件模块和其依赖的模块。
按需加载的模块或需单独加载的模块则分开打包成其他的bundle。

除了这些bundle外，还有一个特别重要的bundle，就是manifest.bundle.js文件，即webpackBootstrap。这个manifest文件是最先加载的，负责解析webpack打包的其他bundle文件，使其按要求进行加载和执行。

#### 打包代码解析:
首先分析一下manifest文件:

(1) 三个主要变量，`modules`、`installedModules` 和 `installedChunks`。

modules对象保存的是所有的模块函数。模块函数是webpack处理的基本单位，对应打包前的一个文件，形式为`function(module, webpack_exports, webpack_require) {…}`。所有的模块函数的索引值是连续编码的，如果第一个bundle里的模块函数的索引是0-7，第二个bundle里的模块函数的索引就从8开始，从而保证索引和模块函数一一对应。

installedModules对象保存的是模块对象。模块对象是运行模块函数得到的对象，是标准的Commonjs对象，其属性主要有模块id和exports对象。webpack的运行就是指执行模块函数得到模块对象的过程。

installedChunks保存的是异步加载对象的promise信息，结构为[resolve, reject, promise]。主要是用来标记异步加载模块。用promise便于异步加载模块的全局管理，如果加载超时就可以抛出js异常。

(2) 三个主要函数`webpackJsonpCallback`，`webpack_require` 和 `webpack_require.e`

`webpackJsonpCallback(chunkIds, moreModules, executeModules){…}`是bundle文件的包裹函数。bundle文件被加载后就会运行这个函数。函数的三个参数分别对应三种模块。
* chunkIds指的是需要单独加载的模块的id，对应installedChunks；
* moreModules包括该bundle打包的所有模块函数；
* executeModules指的是需要立即执行的模块函数的id，对应modules，一般是入口文件对应的模块函数的id；
webpackJsonpCallback先把模块函数存到modules对象中；然后处理chunkIds，调用resolve来改变promise的状态；最后处理executeModules，把对应的模块函数转化成模块对象。

webpack_require(moduleId)通过运行modules里的模块函数来得到模块对象，并保存到installedModules对象中。

webpack_require.e(chunkId)通过建立promise对象来跟踪按需加载模块的加载状态，并设置超时阙值，如果加载超时就抛出js异常。如果不需要处理加载超时异常的话，就不需要这个函数和installedChunks对象，可以把按需加载模块当作普通模块来处理。

manifest.js:
```js
/******/ (function(modules) { // webpackBootstrap
        // modules存储的是模块函数
/******/ 	// install a JSONP callback for chunk loading
/******/ 	var parentJsonpFunction = window["webpackJsonp"];
/******/ 	window["webpackJsonp"] = function webpackJsonpCallback(chunkIds, moreModules, executeModules) {
/******/ 		// add "moreModules" to the modules object,
/******/ 		// then flag all "chunkIds" as loaded and fire callback
/******/ 		var moduleId, chunkId, i = 0, resolves = [], result;
            // 遍历chunkIds，如果对应的模块是按需加载的模块，就把其resolve函数存起来。
/******/ 		for(;i < chunkIds.length; i++) {
/******/ 			chunkId = chunkIds[i];
/******/ 			if(installedChunks[chunkId]) {
              // 是按需加载的模块，取出其resolve函数
/******/ 				resolves.push(installedChunks[chunkId][0]);
/******/ 			}
              // 该chunk已经被处理了
/******/ 			installedChunks[chunkId] = 0;
/******/ 		}
            // 遍历moreModules把模块函数存到modules中
/******/ 		for(moduleId in moreModules) {
/******/ 			if(Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
/******/ 				modules[moduleId] = moreModules[moduleId];
/******/ 			}
/******/ 		}
/******/ 		if(parentJsonpFunction) parentJsonpFunction(chunkIds, moreModules, executeModules);
            // 执行resolve函数，一般是__webpack_require__函数
/******/ 		while(resolves.length) {
/******/ 			resolves.shift()();
/******/ 		}
            // 遍历moreModules把模块函数转化成模块对象
/******/ 		if(executeModules) {
/******/ 			for(i=0; i < executeModules.length; i++) {
/******/ 				result = __webpack_require__(__webpack_require__.s = executeModules[i]);
/******/ 			}
/******/ 		}
/******/ 		return result;
/******/ 	};
/******/
/******/ 	// The module cache
          // 存储的是模块对象
/******/ 	var installedModules = {};
/******/
/******/ 	// objects to store loaded and loading chunks
          // 按需加载的模块的promise
/******/ 	var installedChunks = {
/******/ 		2: 0
/******/ 	};
/******/
/******/ 	// The require function
          // require的功能是把modules对象里的模块函数转化成模块对象，
          // 即运行模块函数，模块函数会把模块的export赋值给模块对象，供其他模块调用。
/******/ 	function __webpack_require__(moduleId) {
/******/
/******/ 		// Check if module is in cache
/******/ 		if(installedModules[moduleId]) {
/******/ 			return installedModules[moduleId].exports;
/******/ 		}
            // 下面开始把一个模块的代码转化成一个模块对象
/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = installedModules[moduleId] = {
/******/ 			i: moduleId,
/******/ 			l: false, // 是否已经加载完成
/******/ 			exports: {} // 模块输出，几乎代表模块本身
/******/ 		};
/******/
/******/ 		// Execute the module function
            // 即运行模块函数，打包后的每个模块都是一个函数
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
/******/
/******/ 		// Flag the module as loaded
/******/ 		module.l = true;
/******/
/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}
/******/
/******/ 	// This file contains only the entry chunk.
/******/ 	// The chunk loading function for additional chunks
/******/ 	__webpack_require__.e = function requireEnsure(chunkId) {
/******/ 		var installedChunkData = installedChunks[chunkId];
            // 模块已经被处理过（加载了模块函数并转换成了模块对象），就返回promise，调用resolve
/******/ 		if(installedChunkData === 0) {
/******/ 			return new Promise(function(resolve) { resolve(); });
/******/ 		}
/******/
/******/ 		// a Promise means "currently loading".
            // 模块正在被加载，返回原来的promise
            // 加载完后会运行模块函数，模块函数会调用resolve改变promise的状态
/******/ 		if(installedChunkData) {
/******/ 			return installedChunkData[2];
/******/ 		}
/******/
/******/ 		// setup Promise in chunk cache
            // 新建promise，并把resolve，reject函数和promise都赋值给installedChunks[chunkId]，以便全局访问
/******/ 		var promise = new Promise(function(resolve, reject) {
/******/ 			installedChunkData = installedChunks[chunkId] = [resolve, reject];
/******/ 		});
/******/ 		installedChunkData[2] = promise;
/******/
/******/ 		// start chunk loading
/******/ 		var head = document.getElementsByTagName('head')[0];
/******/ 		var script = document.createElement('script');
/******/ 		script.type = 'text/javascript';
/******/ 		script.charset = 'utf-8';
/******/ 		script.async = true;
/******/ 		script.timeout = 120000;
/******/
/******/ 		if (__webpack_require__.nc) {
/******/ 			script.setAttribute("nonce", __webpack_require__.nc);
/******/ 		}
/******/ 		script.src = __webpack_require__.p + "static/js/" + chunkId + "." + {"0":"f88d1711e34db78d2921","1":"4fd48cb8f466e537b9fe"}[chunkId] + ".js";
/******/ 		var timeout = setTimeout(onScriptComplete, 120000);
/******/ 		script.onerror = script.onload = onScriptComplete;
/******/ 		function onScriptComplete() {
/******/ 			// avoid mem leaks in IE.
/******/ 			script.onerror = script.onload = null;
/******/ 			clearTimeout(timeout);
/******/ 			var chunk = installedChunks[chunkId];
/******/ 			if(chunk !== 0) { // 没有被处理
/******/ 				if(chunk) { // 是按需加载模块，即请求超时了
/******/ 					chunk[1](new Error('Loading chunk ' + chunkId + ' failed.'));
/******/ 				}
/******/ 				installedChunks[chunkId] = undefined;
/******/ 			}
/******/ 		};
/******/ 		head.appendChild(script);
/******/
/******/ 		return promise;
/******/ 	};
/******/
/******/ 	// expose the modules object (__webpack_modules__)
/******/ 	__webpack_require__.m = modules;
/******/
/******/ 	// expose the module cache
/******/ 	__webpack_require__.c = installedModules;
/******/
/******/ 	// define getter function for harmony exports
/******/ 	__webpack_require__.d = function(exports, name, getter) {
/******/ 		if(!__webpack_require__.o(exports, name)) {
/******/ 			Object.defineProperty(exports, name, {
/******/ 				configurable: false,
/******/ 				enumerable: true,
/******/ 				get: getter
/******/ 			});
/******/ 		}
/******/ 	};
/******/
/******/ 	// getDefaultExport function for compatibility with non-harmony modules
/******/ 	__webpack_require__.n = function(module) {
/******/ 		var getter = module && module.__esModule ?
/******/ 			function getDefault() { return module['default']; } :
/******/ 			function getModuleExports() { return module; };
/******/ 		__webpack_require__.d(getter, 'a', getter);
/******/ 		return getter;
/******/ 	};
/******/
/******/ 	// Object.prototype.hasOwnProperty.call
/******/ 	__webpack_require__.o = function(object, property) { return Object.prototype.hasOwnProperty.call(object, property); };
/******/
/******/ 	// __webpack_public_path__
/******/ 	__webpack_require__.p = "/";
/******/
/******/ 	// on error function for async loading
/******/ 	__webpack_require__.oe = function(err) { console.error(err); throw err; };
/******/ })
/************************************************************************/
/******/ ([]);
//# sourceMappingURL=manifest.439b74d2200fbac023c2.js.map
```

然后分析普通的bundle文件:

所有的代码都包裹在webpackJsonp函数中。

函数的第二个参数是一个数组，包含了打包的所有文件对应的模块函数。
模块函数的标准形式为`function(module, webpack_exports, webpack_require) {…}`。
模块函数里的代码是打包前的文件的代码做了相应的替换得到的。
比如:替换`require`为`webpack_require`，替换以前的路径为新的路径，替换`import`为`webpack_require.e().then()`等。模块函数通过`webpack_exports['a'] = printMe`之类的语句输出模块的`exports`。通过模块函数，把`amd`、`cmd`、`commjs`模块都统一为`webpack`的模块。
```js
webpackJsonp([0],[
  // ...
  (function(module, __webpack_exports__, __webpack_require__) {
    "use strict";
    Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
    // 加载依赖模块
    var __WEBPACK_IMPORTED_MODULE_0__style_css__ = __webpack_require__(2);
    // ...
    var __WEBPACK_IMPORTED_MODULE_1__katong_jpg___default = __webpack_require__.n(__WEBPACK_IMPORTED_MODULE_1__katong_jpg__);
    // ...
    var __WEBPACK_IMPORTED_MODULE_2__say_js__ = __webpack_require__(7);
    // welcome.js文件里的代码
    var elDiv1 = document.createElement('div');
    // ...
    // 把原来的url替换成了上面加载的图片
    elImg.src = __WEBPACK_IMPORTED_MODULE_1__katong_jpg___default.a;
    // ...
    // 把import替换成用promise实现的__webpack_require__.e()来完成异步按需加载
    btn.onclick = function() {
        __webpack_require__.e(0).then(__webpack_require__.bind(null, 8)).then(m => {
          m.default();
        });
    };
    // 把原来的say()函数替换成上面加载的函数
    Object(__WEBPACK_IMPORTED_MODULE_2__say_js__["a"])();
  }),
],[1]);
```