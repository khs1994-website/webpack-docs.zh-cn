---
title: 创建 library
sort: 7
contributors:
  - pksjce
  - johnstew
  - simon04
  - 5angel
  - marioacc
  - byzyk
  - EugeneHlushko
  - AnayaDesign
  - chenxsan
  - wizardofhogwarts
---

除了打包应用程序，webpack 还可以用于打包 JavaScript library。以下指南适用于希望简化打包策略的 library 作者。


## 创建一个 library

假设你正在编写一个名为 `webpack-numbers` 的小的 library，可以将数字 1 到 5 转换为文本表示，反之亦然，例如将 2 转换为 'two'。

基本的项目结构可能如下所示：

__project__

``` diff
+  |- webpack.config.js
+  |- package.json
+  |- /src
+    |- index.js
+    |- ref.json
```

初始化 npm，安装 webpack 和 lodash：

``` bash
npm init -y
npm install --save-dev webpack lodash
```

__src/ref.json__

```json
[
  {
    "num": 1,
    "word": "One"
  },
  {
    "num": 2,
    "word": "Two"
  },
  {
    "num": 3,
    "word": "Three"
  },
  {
    "num": 4,
    "word": "Four"
  },
  {
    "num": 5,
    "word": "Five"
  },
  {
    "num": 0,
    "word": "Zero"
  }
]
```

__src/index.js__

``` js
import _ from 'lodash';
import numRef from './ref.json';

export function numToWord(num) {
  return _.reduce(numRef, (accum, ref) => {
    return ref.num === num ? ref.word : accum;
  }, '');
}

export function wordToNum(word) {
  return _.reduce(numRef, (accum, ref) => {
    return ref.word === word && word.toLowerCase() ? ref.num : accum;
  }, -1);
}
```

这个 library 的调用规范如下：

- __ES2015 module import:__

``` js
import * as webpackNumbers from 'webpack-numbers';
// ...
webpackNumbers.wordToNum('Two');
```

- __CommonJS module require:__

``` js
const webpackNumbers = require('webpack-numbers');
// ...
webpackNumbers.wordToNum('Two');
```

- __AMD module require:__

``` js
require(['webpackNumbers'], function (webpackNumbers) {
  // ...
  webpackNumbers.wordToNum('Two');
});
```

consumer(使用者) 还可以通过一个 script 标签来加载和使用此 library：

``` html
<!doctype html>
<html>
  ...
  <script src="https://unpkg.com/webpack-numbers"></script>
  <script>
    // ...
    // 全局变量
    webpackNumbers.wordToNum('Five')
    // window 对象中的属性
    window.webpackNumbers.wordToNum('Five')
    // ...
  </script>
</html>
```

注意，我们还可以通过以下配置方式，将 library 暴露为：

- global 对象中的属性，用于 Node.js。
- `this` 对象中的属性。

完整的 library 配置和代码，请查看 [webpack-library-example](https://github.com/kalcifer/webpack-library-example)。


## 基本配置

现在，让我们以某种方式打包这个 library，能够实现以下几个目标：

- 使用 `externals` 选项，避免将 `lodash` 打包到应用程序，而使用者会去加载它。
- 将 library 的名称设置为 `webpack-numbers`。
- 将 library 暴露为一个名为 `webpackNumbers` 的变量。
- 能够访问其他 Node.js 中的 library。

此外，consumer(使用者) 应该能够通过以下方式访问 library：

- ES2015 模块。例如 `import webpackNumbers from 'webpack-numbers'`。
- CommonJS 模块。例如 `require('webpack-numbers')`.
- 全局变量，在通过 `script` 标签引入时

我们可以从如下 webpack 基本配置开始：

__webpack.config.js__

``` js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'webpack-numbers.js'
  }
};
```


## 外部化 lodash(externalize lodash)

现在，如果执行 `webpack`，你会发现创建了一个体积相当大的文件。如果你查看这个文件，会看到 lodash 也被打包到代码中。在这种场景中，我们更倾向于把 `lodash` 当作 `peerDependency`。也就是说，consumer(使用者) 应该已经安装过 `lodash` 。因此，你就可以放弃控制此外部 library ，而是将控制权让给使用 library 的 consumer。

这可以使用 `externals` 配置来完成：

__webpack.config.js__

``` diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'webpack-numbers.js'
-   }
+   },
+   externals: {
+     lodash: {
+       commonjs: 'lodash',
+       commonjs2: 'lodash',
+       amd: 'lodash',
+       root: '_'
+     }
+   }
  };
```

这意味着你的 library 需要一个名为 `lodash` 的依赖，这个依赖在 consumer 环境中必须存在且可用。

T> 注意，如果你仅计划将 library 用作另一个 webpack bundle 中的依赖模块，则可以直接将 `externals` 指定为一个数组。


## 外部化限制(external limitations)

对于想要实现从一个依赖中调用多个文件的那些 library：

``` js
import A from 'library/one';
import B from 'library/two';

// ...
```

无法通过在 externals 中指定整个 `library` 的方式，将它们从 bundle 中排除。而是需要逐个或者使用一个正则表达式，来排除它们。

``` js
module.exports = {
  //...
  externals: [
    'library/one',
    'library/two',
    // 匹配以 "library/" 开始的所有依赖
    /^library\/.+$/
  ]
};
```


## 暴露 library

对于用法广泛的 library，我们希望它能够兼容不同的环境，例如 CommonJS，AMD，Node.js 或者作为一个全局变量。为了让你的 library 能够在各种使用环境中可用，需要在 `output` 中添加 `library` 属性：

__webpack.config.js__

``` diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
-     filename: 'webpack-numbers.js'
+     filename: 'webpack-numbers.js',
+     library: 'webpackNumbers'
    },
    externals: {
      lodash: {
        commonjs: 'lodash',
        commonjs2: 'lodash',
        amd: 'lodash',
        root: '_'
      }
    }
  };
```

T> 注意，`library` 设置绑定到 `entry` 配置。对于大多数 library，指定一个入口起点就足够了。虽然 [一次打包暴露多个库](https://github.com/webpack/webpack/tree/master/examples/multi-part-library) 也是也可以的，然而，通过 [index script(索引脚本)（仅用于访问一个入口起点）](https://stackoverflow.com/questions/34072598/es6-exporting-importing-in-index-file) 暴露部分导出则更为简单。我们__不推荐__使用`数组`作为 library 的 `entry`。

这会将你的 library bundle 暴露为名为 `webpackNumbers` 的全局变量，consumer 通过此名称来 import。为了让 library 和其他环境兼容，则需要在配置中添加 `libraryTarget` 属性。这个选项可以控制以多种形式暴露 library。

__webpack.config.js__

``` diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'webpack-numbers.js',
-     library: 'webpackNumbers'
+     library: 'webpackNumbers',
+     libraryTarget: 'umd'
    },
    externals: {
      lodash: {
        commonjs: 'lodash',
        commonjs2: 'lodash',
        amd: 'lodash',
        root: '_'
      }
    }
  };
```

有以下几种方式暴露 library：

- 变量：作为一个全局变量，通过 `script` 标签来访问（`libraryTarget:'var'`）。
- this：通过 `this` 对象访问（`libraryTarget:'this'`）。
- window：在浏览器中通过 `window` 对象访问（`libraryTarget:'window'`）。
- UMD：在 AMD 或 CommonJS `require` 之后可访问（`libraryTarget:'umd'`）。

如果设置了 `library` 但没有设置 `libraryTarget`，则 `libraryTarget` 默认指定为 `var`，详细说明请查看 [output ](/configuration/output) 文档。查看 [`output.libraryTarget`](/configuration/output#outputlibrarytarget) 文档，以获取所有可用选项的详细列表。

W> 在 webpack v3.5.5 中，使用 `libraryTarget: { root:'_' }` 将无法正常工作（参考 [issue 4824](https://github.com/webpack/webpack/issues/4824)) 所述）。然而，可以设置 `libraryTarget: { var: '_' }` 来将 library 作为全局变量。


### 最终步骤

遵循 [生产环境](/guides/production) 指南中提到的步骤，来优化生产环境下的输出结果。那么，我们还需要将生成 bundle 的文件路径，添加到 `package.json` 中的 `main` 字段中。

__package.json__

``` json
{
  ...
  "main": "dist/webpack-numbers.js",
  ...
}
```

或者，按照这个 [指南](https://github.com/dherman/defense-of-dot-js/blob/master/proposal.md#typical-usage)，将其添加为标准模块：

``` json
{
  ...
  "module": "src/index.js",
  ...
}
```

这里的 key(键) `main` 是参照 [`package.json` 标准](https://docs.npmjs.com/files/package.json#main)，而 `module` 是参照 [一个](https://github.com/dherman/defense-of-dot-js/blob/master/proposal.md)[提案](https://github.com/rollup/rollup/wiki/pkg.module)，此提案允许 JavaScript 生态系统升级使用 ES2015 模块，而不会破坏向后兼容性。

W> `module` 属性应指向一个使用 ES2015 模块语法（而不是其他浏览器或 Node.js 尚不支持的模块语法）的脚本。这使得 webpack 本身就可以解析模块语法，如果用户只用到 library 的某些部分，可以通过 [tree shaking](https://webpack.js.org/guides/tree-shaking/) 打包更轻量的包。

现在，你可以 [将其发布为一个 npm package](https://docs.npmjs.com/getting-started/publishing-npm-packages)，并且在 [unpkg.com](https://unpkg.com/#/) 找到它，并分发给你的用户。

T> 为了暴露和 library 关联着的样式表，你应该使用 [`MiniCssExtractPlugin`](/plugins/extract-text-webpack-plugin)。然后，用户可以像使用其他样式表一样使用和加载这些样式表。
