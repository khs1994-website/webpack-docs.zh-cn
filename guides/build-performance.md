---
title: 构建性能
sort: 9
contributors:
  - sokra
  - tbroadley
  - byzyk
  - madhavarshney
  - wizardofhogwarts
---

本指南包含一些改进构建/编译性能的实用技巧。

---

## 通用环境

无论你是在 [开发环境](/guides/development) 还是在 [生产环境](/guides/production) 下运行构建脚本，以下最佳实践都应该有所帮助。


### 更新到最新版本

使用最新的 webpack 版本。我们会经常进行性能优化。webpack 的最新稳定版本是：

[![latest webpack version](https://img.shields.io/npm/v/webpack.svg?label=webpack&style=flat-square&maxAge=3600)](https://github.com/webpack/webpack/releases)

将 __Node.js__ 更新到最新版本，也有助于提高性能。除此之外，将你的 package 管理工具（例如 `npm` 或者 `yarn`）更新到最新版本，也有助于提高性能。较新的版本能够建立更高效的模块树以及提高解析速度。


### loader

对最少数量的必要模块使用 loader。不应该如下:

```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader'
      }
    ]
  }
};
```

而是使用 `include` 字段仅将 loader 应用在实际需要将其转换的模块所处路径：

```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        loader: 'babel-loader'
      }
    ]
  }
};
```


### 引导时间(bootstrap)

每个额外的 loader/plugin 都有其启动时间。尽量少使用工具。


### 解析

以下步骤可以提高解析速度:

- 减少 `resolve.modules`, `resolve.extensions`, `resolve.mainFiles`, `resolve.descriptionFiles` 中 items 数量，因为他们会增加文件系统调用的次数。
- 如果你不使用 symlinks（例如 `npm link` 或者 `yarn link`），可以设置 `resolve.symlinks: false`。
- 如果你使用自定义 resolve plugin 规则，并且没有指定 context 上下文，可以设置 `resolve.cacheWithContext: false`。


### dll

使用 `DllPlugin` 为更改不频繁的代码生成单独编译结果。这可以提高应用程序的编译速度，尽管它确实增加了构建过程的复杂度。


### 小即是快(smaller = faster)

减少编译结果的整体大小，以提高构建性能。尽量保持 chunk 体积小。

- 使用数量更少/体积更小的 library。
- 在多页面应用程序中使用 `SplitChunksPlugin`。
- 在多页面应用程序中使用 `SplitChunksPlugin `，并开启 `async` 模式。
- 移除未引用代码。
- 只编译你当前正在开发的那些代码。


### worker 池(worker pool)

`thread-loader` 可以将非常消耗资源的 loader 分流给一个 worker pool。

W> 不要使用太多的 worker，因为 Node.js 的 runtime 和 loader 都有启动开销。最小化 worker 和 main process(主进程) 之间的模块传输。进程间通讯(IPC, inter process communication)是非常消耗资源的。


### 持久化缓存

使用 `cache-loader` 启用持久化缓存。使用 `package.json` 中的 `"postinstall"` 清除缓存目录。


### 自定义 plugin/loader

这里不对它们的性能问题作过多赘述。

---


## 开发环境

以下步骤对于_开发环境_特别有帮助。


### 增量编译

使用 webpack 的 watch mode(监听模式)。而不使用其他工具来 watch 文件和调用 webpack 。内置的 watch mode 会记录时间戳并将此信息传递给 compilation 以使缓存失效。

在某些配置环境中，watch mode 会回退到 poll mode(轮询模式)。监听许多文件会导致 CPU 大量负载。在这些情况下，可以使用 `watchOptions.poll` 来增加轮询的间隔。


### 在内存中编译

下面几个工具通过在内存中（而不是写入磁盘）编译和 serve 资源来提高性能：

- `webpack-dev-server`
- `webpack-hot-middleware`
- `webpack-dev-middleware`

### stats.toJson 加速

webpack 4 默认使用 `stats.toJson()` 输出大量数据。除非在增量步骤中做必要的统计，否则请避免获取 `stats` 对象的部分内容。`webpack-dev-server` 在 v3.1.3 以后的版本，包含一个重要的性能修复，即最小化每个增量构建步骤中，从 `stats` 对象获取的数据量。

### devtool

需要注意的是不同的 `devtool` 设置，会导致性能差异。

- `"eval"` 具有最好的性能，但并不能帮助你转译代码。
- 如果你能接受稍差一些的 map 质量，可以使用 `cheap-source-map` 变体配置来提高性能
- 使用 `eval-source-map` 变体配置进行增量编译。

=> 在大多数情况下，最佳选择是 `cheap-module-eval-source-map`。


### 避免在生产环境下才会用到的工具

某些 utility, plugin 和 loader 都只用于生产环境。例如，在开发环境下使用 `TerserPlugin` 来 minify(压缩) 和 mangle(混淆破坏) 代码是没有意义的。通常在开发环境下，应该排除以下这些工具：

- `TerserPlugin`
- `ExtractTextPlugin`
- `[hash]`/`[chunkhash]`
- `AggressiveSplittingPlugin`
- `AggressiveMergingPlugin`
- `ModuleConcatenationPlugin`


### 最小化 entry chunk

webpack 只会在文件系统中生成已经更新的 chunk。某些配置选项（HMR, `output.chunkFilename` 的 `[name]`/`[chunkhash]`, `[hash]`）来说，除了对更新的 chunk 无效之外，对于 entry chunk 也不会生效。

确保在生成 entry chunk 时，尽量减少其体积以提高性能。下面的代码块将只提取包含 runtime 的 chunk，_其他 chunk 都作为其子 chunk_:

```js
new CommonsChunkPlugin({
  name: 'manifest',
  minChunks: Infinity
});
```

### 避免额外的优化步骤

webpack 通过执行额外的算法任务，来优化输出结果的体积和加载性能。这些优化适用于小型代码库，但是在大型代码库中却非常耗费性能：

```js
module.exports = {
  // ...
  optimization: {
    removeAvailableModules: false,
    removeEmptyChunks: false,
    splitChunks: false,
  }
};
```

### 输出结果不携带路径信息

webpack 会在输出的 bundle 中生成路径信息。然而，在打包数千个模块的项目中，这会导致造成垃圾回收性能压力。在 `options.output.pathinfo` 设置中关闭：

```js
module.exports = {
  // ...
  output: {
    pathinfo: false
  }
};
```

### Node.js 版本 8.9.10-9.11.1


 Node.js v8.9.10 - v9.11.1 中的 ES2015 `Map` 和 `Set` 实现，存在 [性能回退](https://github.com/nodejs/node/issues/19769)。webpack 大量地使用这些数据结构，因此这次回退也会影响编译时间。

之前和之后的 Node.js 版本不受影响。


### TypeScript loader

现在，`ts-loader` 已经开始使用 TypeScript 内置 watch mode API，可以明显减少每次迭代时重新构建的模块数量。`experimentalWatchApi` 与普通 TypeScript watch mode 共享同样的逻辑，并且在开发环境使用时非常稳定。此外开启 `transpileOnly`，用于真正快速增量构建。

```js
module.exports = {
  // ...
  test: /\.tsx?$/,
  use: [
    {
      loader: 'ts-loader',
      options: {
        transpileOnly: true,
        experimentalWatchApi: true,
      },
    },
  ],
};
```

注意：`ts-loader` 文档建议使用 `cache-loader`，但是这实际上会由于使用硬盘写入而减缓增量构建速度。

为了重新获得类型检查，请使用 [`ForkTsCheckerWebpackPlugin`](https://www.npmjs.com/package/fork-ts-checker-webpack-plugin)。

ts-loader 的 github 仓库中有一个 [完整示例](https://github.com/TypeStrong/ts-loader/tree/master/examples/fork-ts-checker-webpack-plugin)。

---


## 生产环境

以下步骤对于_生产环境_特别有帮助。

W> __不要为了很小的性能收益，牺牲应用程序的质量！__注意，在大多数情况下，优化代码质量比构建性能更重要。


### 多个 compilation(编译时)

在进行多个 compilation 时，以下工具可以帮助到你：

- [`parallel-webpack`](https://github.com/trivago/parallel-webpack)：它允许在 worker 池中运行 compilation。
- `cache-loader`：可以在多个 compilation 之间共享缓存。


### source map

source map 相当消耗资源。你真的需要它们？

---


## 工具相关问题

下列工具存在某些可能会降低构建性能的问题：


### Babel

- 最小化项目中的 preset/plugins 数量。


### TypeScript

- 在单独的进程中使用 `fork-ts-checker-webpack-plugin` 进行类型检查。
- 配置 loader 跳过类型检查。
- 使用 `ts-loader` 时，设置 `happyPackMode: true` / `transpileOnly: true`。


### Sass

- `node-sass` 中有个来自 Node.js 线程池的阻塞线程的 bug。 当使用 `thread-loader` 时，需要设置 `workerParallelJobs: 2`。
