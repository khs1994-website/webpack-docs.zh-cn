---
title: coffee-loader
source: https://raw.githubusercontent.com/webpack-contrib/coffee-loader/master/README.md
edit: https://github.com/webpack-contrib/coffee-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/coffee-loader
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]



Compile [CoffeeScript](https://coffeescript.org/) to JavaScript.

## 起步

安装 `coffeescript` 和 `coffee-loader`：

```console
npm install --save-dev coffeescript coffee-loader
```

然后添加 plugin 到 `webpack` 配置文件. 例：

**file.coffee**

```coffee
# 任务：
number   = 42
opposite = true

# 条件：
number = -42 if opposite

# 函数：
square = (x) -> x * x

# 数组：
list = [1, 2, 3, 4, 5]

# 对象：
math =
  root:   Math.sqrt
  square: square
  cube:   (x) -> x * square x

# Splats：
race = (winner, runners...) ->
  print winner, runners

# 存在性：
alert "I knew it!" if elvis?

# 数组推导（comprehensions）：
cubes = (math.cube num for num in list)
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        loader: 'coffee-loader',
      },
    ],
  },
};
```

替代方案：

```js
import coffee from 'coffee-loader!./file.coffee';
```

然后按偏好运行 `webpack`

## 选项

类型：`Object`
默认：`{ bare: true }`

所有 coffeescript 选项的文档 [点击查看](https://coffeescript.org/#nodejs-usage).

`transpile` 选项的文档 [点击查看](https://coffeescript.org/#transpilation).

> ℹ️ `sourceMap` 选项从 `compiler.devtool` 中选取一个值作为默认值。

> ℹ️ `filename` 选项从 webpack loader API 中选取一个值。 选项值将被忽略。

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        loader: 'coffee-loader',
        options: {
          bare: false,
          transpile: {
            presets: ['@babel/env'],
          },
        },
      },
    ],
  },
};
```

## 示例

### CoffeeScript 与 Babel

来自 CoffeeScript 2 的文档：

> CoffeeScript 2 使用最新的句法生成 JavaScript。
> 代码运行所在的运行时或浏览器有可能无法支持全部相关句法。
> 这种情况下，新的 JavaScript 句法将被转换为旧的 JavaScript 句法，以便在较低版本 Node 环境或浏览器中运行这些代码。比如将 { a } = obj 转换为 a = obj.a.
> 这个转换的过程是由一些诸如 Babel, Bublé or Traceur Compiler 等转换工具完成的。

安装 `@babel/core` 和 `@babel/preset-env`  然后创建配置文件：

```console
npm install --save-dev @babel/core @babel/preset-env
echo '{ "presets": ["@babel/env"] }' > .babelrc
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        loader: 'coffee-loader',
        options: {
          transpile: {
            presets: ['@babel/env'],
          },
        },
      },
    ],
  },
};
```

### Literate CoffeeScript

开启 Literate CoffeeScript 时需要设置：

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        loader: 'coffee-loader',
        options: {
          literate: true,
        },
      },
    ],
  },
};
```

## 贡献

如果您尚未了解，建议您阅读以下贡献指引。

[CONTRIBUTING](https://github.com/webpack-contrib/coffee-loader/blob/master/.github/CONTRIBUTING.md)

## 许可

[MIT](https://github.com/webpack-contrib/coffee-loader/blob/master/LICENSE)

[npm]: https://img.shields.io/npm/v/coffee-loader.svg
[npm-url]: https://npmjs.com/package/coffee-loader
[node]: https://img.shields.io/node/v/coffee-loader.svg
[node-url]: https://nodejs.org/
[deps]: https://david-dm.org/webpack-contrib/coffee-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/coffee-loader
[tests]: https://github.com/webpack-contrib/coffee-loader/workflows/coffee-loader/badge.svg
[tests-url]: https://github.com/webpack-contrib/coffee-loader/actions
[cover]: https://codecov.io/gh/webpack-contrib/coffee-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/coffee-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=coffee-loader
[size-url]: https://packagephobia.now.sh/result?p=coffee-loader
