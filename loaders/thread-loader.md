---
title: thread-loader
source: https://raw.githubusercontent.com/webpack-contrib/thread-loader/master/README.md
edit: https://github.com/webpack-contrib/thread-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/thread-loader
translators:
  - weiyuan0609
  - QC-L
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]



运行以下 loader 在 worker pool 中。

## 快速开始 {#getting-started}

```bash
npm install --save-dev thread-loader
```

使用时，需将此 loader 放置在其他 loader 之前。放置在此 loader 之后的 loader 会在一个独立的 worker 池中运行。

在 worker 池中运行的 loader 是受到限制的。例如：

* 这些 loader 不能生成新的文件。
* 这些 loader 不能使用自定义的 loader API（也就是说，不能通过插件来自定义）。
* 这些 loader 无法获取 webpack 的配置。

每个 worker 都是一个独立的 node.js 进程，其开销大约为 600ms 左右。同时会限制跨进程的数据交换。

请仅在耗时的操作中使用此 loader！

### 示例 {#examples}

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve('src'),
        use: [
          "thread-loader",
          // 耗时的 loader （例如 babel-loader）
        ],
      },
    ],
  },
};
```

**with options**

```js
use: [
  {
    loader: "thread-loader",
    // 有同样配置的 loader 会共享一个 worker 池
    options: {
      // 产生的 worker 的数量，默认是 (cpu 核心数 - 1)，或者，
      // 在 require('os').cpus() 是 undefined 时回退至 1
      workers: 2,

      // 一个 worker 进程中并行执行工作的数量
      // 默认为 20
      workerParallelJobs: 50,

      // 额外的 node.js 参数
      workerNodeArgs: ['--max-old-space-size=1024'],

      // 允许重新生成一个僵死的 work 池
      // 这个过程会降低整体编译速度
      // 并且开发环境应该设置为 false
      poolRespawn: false,

      // 闲置时定时删除 worker 进程
      // 默认为 500（ms）
      // 可以设置为无穷大，这样在监视模式(--watch)下可以保持 worker 持续存在
      poolTimeout: 2000,

      // 池分配给 worker 的工作数量
      // 默认为 200
      // 降低这个数值会降低总体的效率，但是会提升工作分布更均一
      poolParallelJobs: 50,

      // 池的名称
      // 可以修改名称来创建其余选项都一样的池
      name: "my-pool"
    },
  },
  // 耗时的 loader（例如 babel-loader）
];
```

**预警**

可以通过预警 worker 池来防止启动 worker 时的高延时。

这会启动池内最大数量的 worker 并把指定的模块加载到 node.js 的模块缓存中。

```js
const threadLoader = require('thread-loader');

threadLoader.warmup(
  {
    // 池选项，例如传递给 loader 选项
    // 必须匹配 loader 选项才能启动正确的池
  },
  [
    // 加载模块
    // 可以是任意模块，例如
    'babel-loader',
    'babel-preset-es2015',
    'sass-loader',
  ]
);
```

## 贡献 {#contributing}

如果还未阅读贡献指南，请抽时间进行阅读。

[CONTRIBUTING](https://github.com/webpack-contrib/thread-loader/blob/master/.github/CONTRIBUTING.md)

## License {#license}

[MIT](https://github.com/webpack-contrib/thread-loader/blob/master/LICENSE)

[npm]: https://img.shields.io/npm/v/thread-loader.svg
[npm-url]: https://npmjs.com/package/thread-loader
[node]: https://img.shields.io/node/v/thread-loader.svg
[node-url]: https://nodejs.org/
[deps]: https://david-dm.org/webpack-contrib/thread-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/thread-loader
[tests]: https://github.com/webpack-contrib/thread-loader/workflows/thread-loader/badge.svg
[tests-url]: https://github.com/webpack-contrib/thread-loader/actions
[cover]: https://codecov.io/gh/webpack-contrib/thread-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/thread-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=thread-loader
[size-url]: https://packagephobia.now.sh/result?p=thread-loader
