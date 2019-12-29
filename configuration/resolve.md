---
title: 解析(resolve)
sort: 7
contributors:
  - sokra
  - skipjack
  - SpaceK33z
  - pksjce
  - sebastiandeutsch
  - tbroadley
  - byzyk
  - numb86
  - jgravois
  - EugeneHlushko
---

这些选项能设置模块如何被解析。webpack 提供合理的默认值，但是还是可能会修改一些解析的细节。关于 resolver 具体如何工作的更多解释说明，请查看[模块解析](/concepts/module-resolution)。


## `resolve`

`object`

配置模块如何解析。例如，当在 ES2015 中调用 `import 'lodash'`，`resolve` 选项能够对 webpack 查找 `'lodash'` 的方式去做修改（查看[`模块`](#resolve-modules)）。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    // configuration options
  }
};
```


### `resolve.alias`

`object`

创建 `import` 或 `require` 的别名，来确保模块引入变得更简单。例如，一些位于 `src/` 文件夹下的常用模块：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    alias: {
      Utilities: path.resolve(__dirname, 'src/utilities/'),
      Templates: path.resolve(__dirname, 'src/templates/')
    }
  }
};
```

现在，替换「在导入时使用相对路径」这种方式，就像这样：

```js
import Utility from '../../utilities/utility';
```

你可以这样使用别名：

```js
import Utility from 'Utilities/utility';
```

也可以在给定对象的键后的末尾添加 `$`，以表示精准匹配：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    alias: {
      xyz$: path.resolve(__dirname, 'path/to/file.js')
    }
  }
};
```

这将产生以下结果：

```js
import Test1 from 'xyz'; // 精确匹配，所以 path/to/file.js 被解析和导入
import Test2 from 'xyz/file.js'; // 非精确匹配，触发普通解析
```

下面的表格展示了一些其他情况：

| `别名：`                            | `import 'xyz'`                        | `import 'xyz/file.js'`              |
| ----------------------------------- | ------------------------------------- | ----------------------------------- |
| `{}`                                | `/abc/node_modules/xyz/index.js`      | `/abc/node_modules/xyz/file.js`     |
| `{ xyz: '/abs/path/to/file.js' }`   | `/abs/path/to/file.js`                | error                               |
| `{ xyz$: '/abs/path/to/file.js' }`  | `/abs/path/to/file.js`                | `/abc/node_modules/xyz/file.js`     |
| `{ xyz: './dir/file.js' }`          | `/abc/dir/file.js`                    | error                               |
| `{ xyz$: './dir/file.js' }`         | `/abc/dir/file.js`                    | `/abc/node_modules/xyz/file.js`     |
| `{ xyz: '/some/dir' }`              | `/some/dir/index.js`                  | `/some/dir/file.js`                 |
| `{ xyz$: '/some/dir' }`             | `/some/dir/index.js`                  | `/abc/node_modules/xyz/file.js`     |
| `{ xyz: './dir' }`                  | `/abc/dir/index.js`                   | `/abc/dir/file.js`                  |
| `{ xyz: 'modu' }`                   | `/abc/node_modules/modu/index.js`     | `/abc/node_modules/modu/file.js`    |
| `{ xyz$: 'modu' }`                  | `/abc/node_modules/modu/index.js`     | `/abc/node_modules/xyz/file.js`     |
| `{ xyz: 'modu/some/file.js' }`      | `/abc/node_modules/modu/some/file.js` | error                               |
| `{ xyz: 'modu/dir' }`               | `/abc/node_modules/modu/dir/index.js` | `/abc/node_modules/dir/file.js`     |
| `{ xyz: 'xyz/dir' }`                | `/abc/node_modules/xyz/dir/index.js`  | `/abc/node_modules/xyz/dir/file.js` |
| `{ xyz$: 'xyz/dir' }`               | `/abc/node_modules/xyz/dir/index.js`  | `/abc/node_modules/xyz/file.js`     |

如果在 `package.json` 中定义，`index.js` 可能会被解析为另一个文件。

`/abc/node_modules` 也可能在 `/node_modules` 中解析。


### `resolve.aliasFields`

`[string]: ['browser']`

指定一个字段，例如 `browser`，根据[此规范](https://github.com/defunctzombie/package-browser-field-spec)进行解析。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    aliasFields: ['browser']
  }
};
```


### `resolve.cacheWithContext`

`boolean`（从 webpack 3.1.0 开始）

如果启用了不安全缓存，请在缓存键(cache key)中引入 `request.context`。这个选项被 [`enhanced-resolve`](https://github.com/webpack/enhanced-resolve/) 模块考虑在内。从 webpack 3.1.0 开始，在配置了 resolve 或 resolveLoader 插件时，解析缓存(resolve caching)中的上下文(context)会被忽略。这解决了性能衰退的问题。


### `resolve.descriptionFiles`

`[string]: ['package.json']`

用于描述的 JSON 文件。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    descriptionFiles: ['package.json']
  }
};
```


### `resolve.enforceExtension`

`boolean: false`

如果是 `true`，将不允许无扩展名(extension-less)文件。默认如果 `./foo` 有 `.js` 扩展，`require('./foo')` 可以正常运行。但如果启用此选项，只有 `require('./foo.js')` 能够正常工作。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    enforceExtension: false
  }
};
```


### `resolve.enforceModuleExtension`

`boolean: false`

对模块是否需要使用的扩展（例如 loader）。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    enforceModuleExtension: false
  }
};
```


### `resolve.extensions`

`[string]: ['.wasm', '.mjs', '.js', '.json']`

自动解析确定的扩展。默认值为：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    extensions: ['.wasm', '.mjs', '.js', '.json']
  }
};
```

能够使用户在引入模块时不带扩展：

```js
import File from '../path/to/file';
```

W> 使用此选项，会__覆盖默认数组__，这就意味着 webpack 将不再尝试使用默认扩展来解析模块。对于使用其扩展导入的模块，例如，`import SomeFile from "./somefile.ext"`，要想正确的解析，一个包含“\*”的字符串必须包含在数组中。


### `resolve.mainFields`

`[string]`

当从 npm 包中导入模块时（例如，`import * as D3 from 'd3'`），此选项将决定在 `package.json` 中使用哪个字段导入模块。根据 webpack 配置中指定的 [`target`](/concepts/targets) 不同，默认值也会有所不同。

当 `target` 属性设置为 `webworker`, `web` 或者没有指定，默认值为：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    mainFields: ['browser', 'module', 'main']
  }
};
```

对于其他任意的 target（包括 `node`），默认值为：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    mainFields: ['module', 'main']
  }
};
```

例如，考虑任意一个名为 `upstream` 的 library，其 `package.json` 包含以下字段：

```json
{
  "browser": "build/upstream.js",
  "module": "index"
}
```

在我们 `import * as Upstream from 'upstream'` 时，这实际上会从 `browser` 属性解析文件。在这里 `browser` 属性是最优先选择的，因为它是 `mainFields` 的第一项。同时，由 webpack 打包的 Node.js 应用程序首先会尝试从 `module` 字段中解析文件。


### `resolve.mainFiles`

`[string]: ['index']`

解析目录时要使用的文件名。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    mainFiles: ['index']
  }
};
```


### `resolve.modules`

`[string]: ['node_modules']`

告诉 webpack 解析模块时应该搜索的目录。

绝对路径和相对路径都能使用，但是要知道它们之间有一点差异。

通过查看当前目录以及祖先路径（即 `./node_modules`, `../node_modules` 等等），相对路径将类似于 Node 查找 'node_modules' 的方式进行查找。

使用绝对路径，将只在给定目录中搜索。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    modules: ['node_modules']
  }
};
```

如果你想要添加一个目录到模块搜索目录，此目录优先于 `node_modules/` 搜索：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    modules: [path.resolve(__dirname, 'src'), 'node_modules']
  }
};
```


### `resolve.unsafeCache`

`regex` `array` `boolean: true`

启用，会主动缓存模块，但并__不安全__。传递 `true` 将缓存一切。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    unsafeCache: true
  }
};
```

正则表达式，或正则表达式数组，可以用于匹配文件路径或只缓存某些模块。例如，只缓存 utilities 模块：

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    unsafeCache: /src\/utilities/
  }
};
```

W> 修改缓存路径可能在极少数情况下导致失败。


### `resolve.plugins`

[`[Plugin]`](/plugins/)

应该使用的额外的解析插件列表。它允许插件，如 [`DirectoryNamedWebpackPlugin`](https://www.npmjs.com/package/directory-named-webpack-plugin)。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    plugins: [
      new DirectoryNamedWebpackPlugin()
    ]
  }
};
```


### `resolve.symlinks`

`boolean: true`

是否将符号链接(symlink)解析到它们的符号链接位置(symlink location)。默认：

启用时，符号链接(symlink)的资源，将解析为其_真实_路径，而不是其符号链接(symlink)位置。注意，当使用符号链接 package 包工具时（如 `npm link`），可能会导致模块解析失败。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    symlinks: true
  }
};
```


### `resolve.cachePredicate`

`function: function (module) { return true; }`

决定请求是否应该被缓存的函数。函数传入一个带有 `path` 和 `request` 属性的对象。必须返回一个 boolean 值。

__webpack.config.js__

```js
module.exports = {
  //...
  resolve: {
    cachePredicate: (module) => {
      // additional logic
      return true;
    }
  }
};
```


## `resolveLoader`

`object`

这组选项与上面的 `resolve` 对象的属性集合相同，但仅用于解析 webpack 的 [loader](/concepts/loaders) 包。默认：

__webpack.config.js__

```js
module.exports = {
  //...
  resolveLoader: {
    modules: ['node_modules'],
    extensions: ['.js', '.json'],
    mainFields: ['loader', 'main']
  }
};
```

T> 注意，这里你可以使用别名，并且其他特性类似于 resolve 对象。例如，`{ txt: 'raw-loader' }` 会使用 `raw-loader` 去 shim(填充) `txt!templates/demo.txt`。


## `resolveLoader.moduleExtensions`

`[string]`

解析 loader 时，用到扩展名(extensions)/后缀(suffixes)。从 webpack 2 开始，我们 [强烈建议](/migrate/3/#automatic-loader-module-name-extension-removed) 使用全名，例如 `example-loader`，以尽可能清晰。然而，如果你确实想省略 `-loader`，也就是说只使用 `example`，则可以使用此选项来实现：

__webpack.config.js__

```js
module.exports = {
  //...
  resolveLoader: {
    moduleExtensions: ['-loader']
  }
};
```
