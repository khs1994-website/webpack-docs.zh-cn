---
title: Comparison
sort: 1
contributors:
  - pksjce
  - bebraw
  - chrisVillanueva
  - tashian
  - simon04
  - byzyk
related:
  - title: JSPM vs. webpack
    url: https://ilikekillnerds.com/2015/07/jspm-vs-webpack/
  - title: webpack vs. Browserify vs. SystemJS
    url: https://engineering.velocityapp.com/webpack-vs-browersify-vs-systemjs-for-spas-95b349a41fa0
---

webpack 并非唯一的模块打包工具。当您面临选择 webpack 或者是其他模块打包工具时，请参见以下 webpack 与其他竞品的功能对比表格。

| 特性 | webpack/webpack | jrburke/requirejs | substack/node-browserify | jspm/jspm-cli | rollup/rollup | brunch/brunch |
|---------|-----------------|-------------------|--------------------------|---------------|---------------|---------------|
| 按需加载额外 chunks | __yes__ | __yes__ | no | [System.import](https://github.com/systemjs/systemjs/blob/master/docs/system-api.md#systemimportmodulename--normalizedparentname---promisemodule) | no | no |
| AMD `define` | __yes__ | __yes__ | [deamdify](https://github.com/jaredhanson/deamdify) | yes | [rollup-plugin-amd](https://github.com/piuccio/rollup-plugin-amd) | yes |
| AMD `require` | __yes__ | __yes__ | no | yes | no | yes |
| AMD `require` 按需加载 | __yes__ | 手动配置 | no | yes | no | no |
| CommonJS `exports` | __yes__ | 仅包裹在 `define` 中 | __yes__ | yes | [commonjs-plugin](https://github.com/rollup/rollup-plugin-commonjs) | yes |
| CommonJS `require` | __yes__ | 仅包裹在 `define` 中  | __yes__ | yes | [commonjs-plugin](https://github.com/rollup/rollup-plugin-commonjs) | yes |
| CommonJS `require.resolve` | __yes__ | no | no | no | no | |
| 在 require 中使用拼接 `require("./fi" + "le")` | __yes__ | no♦ | no | no | no | |
| 调试支持 | __SourceUrl, SourceMaps__ | 未引入 | SourceMaps | __SourceUrl, SourceMaps__ | __SourceUrl, SourceMaps__ | SourceMaps |
| 依赖列表（Dependencies） | 19MB / 127 个 package | 11MB / 118 个 package | __1.2MB / 1 package__ | 26MB / 131 个 package | ?MB / 3 个 package | |
| ES2015 `import`/`export` | __yes__ (webpack 2) | no | no | __yes__ | __yes__ | yes，通过 [es6 module 转译器](https://github.com/gcollazo/es6-module-transpiler-brunch)
| require 中使用表达式 (guided) `require("./templates/" + template)` | __yes（包含所有匹配到的文件）__ | no♦ | no | no | no | no |
| require 中使用表达式 (free) `require(moduleName)` | 手动配置 | no♦ | no | no | no | |
| 生成单文件 bundle | __yes__ | yes♦ | yes | yes | yes | yes |
| 间接 require `var r = require; r("./file")` | __yes__ | no♦ | no | no | no | |
| 单独加载某个文件 | no | yes | no | yes | no | no |
| 混淆路径名称 | __yes__ | no | 部分 | yes | 未引入（路径名称未包含在 bundle 中） | no |
| 压缩 | [Terser](https://github.com/fabiosantoscode/terser) | uglify, closure compiler | [uglifyify](https://github.com/hughsk/uglifyify) | yes | [uglify-plugin](https://github.com/TrySound/rollup-plugin-uglify) | [UglifyJS-brunch](https://github.com/brunch/uglify-js-brunch)
| 使用 common bundle 构建多页应用 | 手动配置 | __yes__ | 手动配置 | 使用 bundle 算法 | no | no|
| 多个 bundle | __yes__ | 手动配置 | 手动配置 | yes | no | yes |
| Node.js 内置库 `require("path")` | __yes__ | no | __yes__ | __yes__ | [node-resolve-plugin](https://github.com/rollup/rollup-plugin-node-resolve) | |
| 其他 Node.js 相关内容 | process，__dir/filename，global | - | process，__dir/filename，global | process，__dir/filename，global for cjs | global ([commonjs-plugin](https://github.com/rollup/rollup-plugin-commonjs)) | |
| Plugin | __yes__ | yes | __yes__ | yes | yes | yes |
| 预处理 | __loaders，[transforms](https://github.com/webpack-contrib/transform-loader)__ | loaders | transforms | plugin translate | plugin transforms | compilers，optimizers |
| 浏览器替换项 | `web_modules`，`.web.js`，package.json 字段，别名（alias）配置项 | 别名配置项 | package.json 字段，别名配置项 | package.json，别名配置项 | no | |
| 读取文件 | 文件系统 | __web__ | 文件系统 | 使用插件 | 文件系统或者使用插件 | 文件系统 |
| 运行时开销 | __每个模块 243B + 20B + 每个依赖 4B__ | 每个模块 14.7kB + 0B + 每个依赖 (3B + X) | 每个模块 415B + 25B + 每个依赖 (6B + 2X) | 自运行 bundle 5.5kB，所有 loader 和 polyfill 38kB，普通模块 0，293B CJS，ES2015 System.register 压缩 (gzip) 前 139B | __ES2015 module 无开销__ （其他格式可能会有）| |
| 监听模式 | yes | 未引入 | [watchify](https://github.com/browserify/watchify) | dev 中未引入 | [rollup-watch](https://github.com/rollup/rollup-watch) | yes |

♦ 为生成模式 (开发模式取反值)

X 为路径字符串的长度


## 打包与加载

了解 _loading_ 和 _bundling_ 模块的一些主要区别是很有必要的。比如像 [SystemJS](https://github.com/systemjs/systemjs) 这类的工具，本质上是基于 [JSPM](https://github.com/jspm/jspm-cli), 常应用于在浏览器运行时加载和转译模块。这就与 webpack 有很大的差异，因为在 webpack 中模块是在浏览器加载之前，就已经被转译过（通过 loaders 转译）并打包的。

每种方法都有其长，也有其短。如果是包括较多模块的大型网站和应用程序，在运行时加载和转译模块，会使其增加许多开销。故而，SystemJS 更加适用于模块较少的小型项目。不过，在判定哪些文件可以从服务端转到客户端时，[HTTP/2](https://http2.github.io/) 倒是能提升一些速度。请注意，HTTP/2 并不会改变 _transpiling_ 模块，所以在客户端会增加时间开销。
