---
title: 插件(plugin)
sort: 4
contributors:
  - TheLarkInn
  - jhnns
  - rouzbeh84
  - johnstew
  - MisterDev
  - byzyk
---

**插件**是 webpack 的 [支柱](https://github.com/webpack/tapable) 功能。webpack 自身也是构建于你在 webpack 配置中用到的**相同的插件系统**之上！

插件目的在于解决 [loader](/concepts/loaders) 无法实现的**其他事**。

## 剖析

webpack **插件**是一个具有 [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法的 JavaScript 对象。`apply` 方法会被 webpack compiler 调用，并且 compiler 对象可在**整个**编译生命周期访问。

**ConsoleLogOnBuildWebpackPlugin.js**

```javascript
const pluginName = "ConsoleLogOnBuildWebpackPlugin";

class ConsoleLogOnBuildWebpackPlugin {
  apply(compiler) {
    compiler.hooks.run.tap(pluginName, (compilation) => {
      console.log("webpack 构建过程开始！");
    });
  }
}
```

compiler hook 的 tap 方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有 hook 中复用。

## 用法

由于**插件**可以携带参数/选项，你必须在 webpack 配置中，向 `plugins` 属性传入 `new` 实例。

取决于你的 webpack 用法，对应有多种使用插件的方式。

### 配置

**webpack.config.js**

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin"); //通过 npm 安装
const webpack = require("webpack"); //访问内置的插件
const path = require("path");

module.exports = {
  entry: "./path/to/my/entry/file.js",
  output: {
    filename: "my-first-webpack.bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: "babel-loader",
      },
    ],
  },
  plugins: [
    new webpack.ProgressPlugin(),
    new HtmlWebpackPlugin({ template: "./src/index.html" }),
  ],
};
```

### Node API

在使用 Node API 时，还可以通过配置中的 `plugins` 属性传入插件。

**some-node-script.js**

```javascript
const webpack = require("webpack"); //访问 webpack 运行时(runtime)
const configuration = require("./webpack.config.js");

let compiler = webpack(configuration);

new webpack.ProgressPlugin().apply(compiler);

compiler.run(function (err, stats) {
  // ...
});
```

T> 你知道吗：以上看到的示例和 [webpack 自身运行时(runtime)](https://github.com/webpack/webpack/blob/e7087ffeda7fa37dfe2ca70b5593c6e899629a2c/bin/webpack.js#L290-L292) 极其类似。[wepback 源码](https://github.com/webpack/webpack)中隐藏有大量使用示例，你可以用在自己的配置和脚本中。
