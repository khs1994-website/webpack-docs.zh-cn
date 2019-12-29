---
title: loader
sort: 0
contributors:
  - simon04
  - bajras
  - rhys-vdw
  - EugeneHlushko
  - hemal7735
---

webpack 可以使用 [loader](/concepts/loaders) 来预处理文件。这允许你打包除 JavaScript 之外的任何静态资源。你可以使用 Node.js 来很简单地编写自己的 loader。

loader 通过在 `require()` 语句中使用 `loadername!` 前缀来激活，或者通过 webpack 配置中的正则表达式来自动应用 - 查看[配置](/concepts/loaders#configuration)。


## 文件

- [`raw-loader`](/loaders/raw-loader) 加载文件原始内容（utf-8）
- [`val-loader`](/loaders/val-loader) 将代码作为模块执行，并将 exports 转为 JS 代码
- [`url-loader`](/loaders/url-loader) 像 file loader 一样工作，但如果文件小于限制，可以返回 [data URL](https://tools.ietf.org/html/rfc2397)
- [`file-loader`](/loaders/file-loader) 将文件发送到输出文件夹，并返回（相对）URL
- [`ref-loader`](https://www.npmjs.com/package/ref-loader) 手动创建所有文件之间的依赖关系


## JSON

- [`json-loader`](/loaders/json-loader) 加载 [JSON](http://json.org/) 文件（默认包含）
- [`json5-loader`](/loaders/json5-loader) 加载和转译 [JSON 5](https://json5.org/) 文件
- `cson-loader` 加载和转译 [CSON](https://github.com/awnist/cson-loader) 文件


## 转译(transpiling)

- [`script-loader`](/loaders/script-loader) 在全局上下文中执行一次 JavaScript 文件（如在 script 标签），不需要解析
- [`babel-loader`](/loaders/babel-loader) 加载 ES2015+ 代码，然后使用 [Babel](https://babel.docschina.org/) 转译为 ES5
- [`buble-loader`](https://github.com/sairion/buble-loader) 使用 [Bublé](https://buble.surge.sh/guide/) 加载 ES2015+ 代码，并且将代码转译为 ES5
- [`traceur-loader`](https://github.com/jupl/traceur-loader) 加载 ES2015+ 代码，然后使用 [Traceur](https://github.com/google/traceur-compiler#readme) 转译为 ES5
- [`ts-loader`](https://github.com/TypeStrong/ts-loader) 或 [`awesome-typescript-loader`](https://github.com/s-panferov/awesome-typescript-loader) 像 JavaScript 一样加载 [TypeScript](https://www.typescriptlang.org/) 2.0+
- [`coffee-loader`](/loaders/coffee-loader) 像 JavaScript 一样加载 [CoffeeScript](http://coffeescript.org/)
- [`fengari-loader`](https://github.com/fengari-lua/fengari-loader/) 使用 [fengari](https://fengari.io/) 加载 Lua 代码


## 模板(templating)

- [`html-loader`](/loaders/html-loader) 导出 HTML 为字符串，需要引用静态资源
- [`pug-loader`](https://github.com/pugjs/pug-loader) 加载 Pug 模板并返回一个函数
- [`markdown-loader`](https://github.com/peerigon/markdown-loader) 将 Markdown 转译为 HTML
- [`react-markdown-loader`](https://github.com/javiercf/react-markdown-loader) 使用 markdown-parse parser(解析器) 将 Markdown 编译为 React 组件
- [`posthtml-loader`](https://github.com/posthtml/posthtml-loader) 使用 [PostHTML](https://github.com/posthtml/posthtml) 加载并转换 HTML 文件
- [`handlebars-loader`](https://github.com/pcardune/handlebars-loader) 将 Handlebars 转移为 HTML
- [`markup-inline-loader`](https://github.com/asnowwolf/markup-inline-loader) 将内联的 SVG/MathML 文件转换为 HTML。在应用于图标字体，或将 CSS 动画应用于 SVG 时非常有用。
- [`twig-loader`](https://github.com/zimmo-be/twig-loader) 编译 Twig 模板，然后返回一个函数

## 样式

- [`style-loader`](/loaders/style-loader) 将模块的导出作为样式添加到 DOM 中
- [`css-loader`](/loaders/css-loader) 解析 CSS 文件后，使用 import 加载，并且返回 CSS 代码
- [`less-loader`](/loaders/less-loader) 加载和转译 LESS 文件
- [`sass-loader`](/loaders/sass-loader) 加载和转译 SASS/SCSS 文件
- [`postcss-loader`](/loaders/postcss-loader) 使用 [PostCSS](http://postcss.org) 加载和转译 CSS/SSS 文件
- [`stylus-loader`](https://github.com/shama/stylus-loader) 加载和转译 Stylus 文件


## 代码检查和测试(linting && testing)

- [`mocha-loader`](/loaders/mocha-loader) 使用 [mocha](https://mochajs.org/) 测试（浏览器/NodeJS）
- [`eslint-loader`](https://github.com/webpack-contrib/eslint-loader) PreLoader，使用 [ESLint](https://eslint.org/) 清理代码
- [`jshint-loader`](/loaders/jshint-loader) PreLoader，使用 [JSHint](http://jshint.com/about/) 清理代码
- [`jscs-loader`](https://github.com/unindented/jscs-loader) PreLoader，使用 [JSCS](http://jscs.info/) 检查代码样式
- [`coverjs-loader`](/loaders/coverjs-loader) PreLoader，使用 [CoverJS](https://github.com/arian/CoverJS) 确定测试覆盖率


## 框架(frameworks)

- [`vue-loader`](https://github.com/vuejs/vue-loader) 加载和转译 [Vue 组件](https://vuejs.org/v2/guide/components.html)
- [`polymer-loader`](https://github.com/webpack-contrib/polymer-webpack-loader) 使用选择预处理器(preprocessor)处理，并且 `require()` 类似一等模块(first-class)的 Web 组件
- [`angular2-template-loader`](https://github.com/TheLarkInn/angular2-template-loader) 加载和转译 [Angular](https://angular.io/) 组件

## Awesome

更多第三方 loader，查看 [awesome-webpack](https://github.com/webpack-contrib/awesome-webpack#loaders) 列表。
