---
title: raw-loader
source: https://raw.githubusercontent.com/webpack-contrib/raw-loader/master/README.md
edit: https://github.com/webpack-contrib/raw-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/raw-loader
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]



可以将文件作为字符串导入的 webpack loader。

## 起步

你需要预先安装 `raw-loader`：

```console
$ npm install raw-loader --save-dev
```

在然后 `webpack` 配置中添加 loader：

**file.js**

```js
import txt from './file.txt';
```

**webpack.config.js**

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.txt$/i,
        use: 'raw-loader',
      },
    ],
  },
};
```

或者，通过命令行使用：

```console
$ webpack --module-bind 'txt=raw-loader'
```

然后，运行 `webpack`。

## 示例

### Inline

```js
import txt from 'raw-loader!./file.txt';
```

Beware, if you already define loader(s) for extension(s) in `webpack.config.js` you should use:

```js
import css from '!!raw-loader!./file.css'; // Adding `!!` to a request will disable all loaders specified in the configuration
```

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.

[CONTRIBUTING](https://raw.githubusercontent.com/webpack-contrib/raw-loader/master/.github/CONTRIBUTING.md)

## License

[MIT](https://raw.githubusercontent.com/webpack-contrib/raw-loader/master/LICENSE)

[npm]: https://img.shields.io/npm/v/raw-loader.svg
[npm-url]: https://npmjs.com/package/raw-loader
[node]: https://img.shields.io/node/v/raw-loader.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/raw-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/raw-loader
[tests]: https://img.shields.io/circleci/project/github/webpack-contrib/raw-loader.svg
[tests-url]: https://circleci.com/gh/webpack-contrib/raw-loader
[cover]: https://codecov.io/gh/webpack-contrib/raw-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/raw-loader
[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=raw-loader
[size-url]: https://packagephobia.now.sh/result?p=raw-loader
