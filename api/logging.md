---
title: Logger 接口
sort: 6
contributors:
  - EugeneHlushko
  - wizardofhogwarts
  - chenxsan
---

T> 本 API 从 v4.39.0 开始可用

使用 Logger 输出消息是一种向用户展示信息的有效方式。

Webpack Logger 可以用在 [loader](/loaders/) 和 [plugin](/api/plugins/#logging)。生成的 Logger 将作为 [统计信息](/api/stats/) 的一部分进行输出，并且用户可以在 [webpack 配置文件](/configuration/) 中对 Logger 进行配置。

在 Webpack 中使用自定义 Logger API 的优点：

- 可以 [配置](/configuration/stats/#statslogging) 日志的展示级别
- 日志内容可作为 `stats.json` 的一部分输出
- 统计预设会影响日志的输出
- 使用 plugin 可以影响日志的捕获与展示级别
- 当使用多个 plugin 和 loader 时，提供更加通用的日志记录解决方案
- 基于 Webpack 开发的 CLI、UI 工具等可能会选择不同的方式来展示日志
- Webpack 核心可以触发日志输出，例如：timing data

通过引入 Webpack Logger API，我们希望统一 Webpack plugins 和 loaders 生成日志的方式，并提供更好的方法来检查构建问题。 集成的 Logging 解决方案可以帮助 plugins 和 loader 的开发人员提升他们的开发经验。同时为非 CLI 形式的 Webpack 解决方案构建铺平了道路，例如 dashboard 或其他 UI。

W> __避免在日志中输出无效信息！__请记住，多个 plugin 和 loader 经常一起使用。loader 通常处理多个文件，并且每个文件都会调用，所以尽可能选择较低的日志级别以保证 log 的信息量。

## Examples of how to get and use webpack logger in loaders and plugins

__my-webpack-plugin.js__

```js
const PLUGIN_NAME = 'my-webpack-plugin';
export class MyWebpackPlugin {
  apply(compiler) {
    // you can access Logger from compiler
    const logger = compiler.getInfrastructureLogger(PLUGIN_NAME);
    logger.log('log from compiler');

    compiler.hooks.compilation.tap(PLUGIN_NAME, compilation => {
      // you can also access Logger from compilation
      const logger = compilation.getLogger(PLUGIN_NAME);
      logger.info('log from compilation');
    });
  }
}
```

__my-webpack-loader.js__

```js
module.exports = function (source) {
  // you can get Logger with `this.getLogger` in your webpack loaders
  const logger = this.getLogger('my-webpack-loader');
  logger.info('hello Logger');
  return source;
};
```

## Logger methods

- `logger.error(...)`：用于输出错误信息
- `logger.warn(...)`：用于输出警告信息
- `logger.info(...)`：用于输出__重要__信息。默认情况下会显示这些信息，所以仅用于输出用户真正需要查看的消息
- `logger.log(...)`：用于输出__不重要__的信息。只有当用户选择查看时，才会显示
- `logger.debug(...)`：用于输出调试信息。只有当用户选择查看特定模块的调试日志时，才会显示
- `logger.trace()`：显示堆栈跟踪信息，展示形式类似于 `logger.debug`
- `logger.group(...)`：将消息进行分组，展示形式类似于 `logger.log`
- `logger.groupEnd()`：结束消息分组
- `logger.groupCollapsed(...)`：将消息进行分组。默认显示为折叠 `logger.log` 日志，当日志记录级别设置为 `'verbose'` 或 `'debug'` 时，显示展开的日志
- `logger.status`：写入一条临时消息，并且设置新状态，覆盖上一个状态
- `logger.clear()`：打印水平线。展示形式类似于 `logger.log`
- `logger.profile(...)`，`logger.profileEnd(...)`：捕获配置文件。当支持 `console.profile` API 时，使用其进行输出

## Runtime Logger API

Runtime logger API 仅应该用作开发工具，不应该包含在 [生产模式](/configuration/mode/#mode-production)中。

- `const logging = require('webpack/lib/logging/runtime')`：直接从 Webpack 中引入即可使用 logger API
- `logging.getLogger('name')`：根据名称获取一个 logger 的实例
- `logging.configureDefaultLogger(...)`：重写 logger 的默认配置

```javascript
const logging = require('webpack/lib/logging/runtime');
logging.configureDefaultLogger({
  level: 'log',
  debug: /something/
});
```

- `logging.hooks.log`：向 logger 中添加 Plugins
