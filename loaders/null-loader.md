---
title: null-loader
source: https://raw.githubusercontent.com/webpack-contrib/null-loader/master/README.md
edit: https://github.com/webpack-contrib/null-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/null-loader
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![chat][chat]][chat-url]



返回一个空模块的 webpack loader。

此 loader 的一个用途是，使依赖项导入的模块静音。
例如，项目依赖于一个 ES6 库，会导入不需要的 polyfill，
因此删除它将不会导致功能损失。

## 要求

此模块需要 Node v6.9.0+ 和 webpack v4.0.0+。

## 起步

你需要预先安装 `null-loader`：

```console
$ npm install null-loader --save-dev
```

然后，在 `webpack` 配置中添加 loader。例如：

```js
// webpack.config.js
const path = require('path');

module.exports = {
  module: {
    rules: [
      {
        // 匹配一个 polyfill（或任何文件），
        // 然后在 bundle 中不会引入这个 polyfill
        test: path.resolve(__dirname, 'node_modules/library/polyfill.js'),
        use: 'null-loader'
      }
    ]
  }
}
```

然后，通过你偏爱的方式去运行 `webpack`。

## 贡献人员

如果你从未阅读过我们的贡献指南，请在上面花点时间。

#### [贡献指南](https://raw.githubusercontent.com/webpack-contrib/null-loader/master/.github/CONTRIBUTING)

## License

#### [MIT](https://raw.githubusercontent.com/webpack-contrib/null-loader/master/LICENSE)

[npm]: https://img.shields.io/npm/v/null-loader.svg
[npm-url]: https://npmjs.com/package/null-loader

[node]: https://img.shields.io/node/v/null-loader.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/webpack-contrib/null-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/null-loader

[tests]: 	https://img.shields.io/circleci/project/github/webpack-contrib/null-loader.svg
[tests-url]: https://circleci.com/gh/webpack-contrib/null-loader

[cover]: https://codecov.io/gh/webpack-contrib/null-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/null-loader

[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
