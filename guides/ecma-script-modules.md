---
title: ECMAScript 模块
sort: 19
contributors:
  - sokra
related:
  - title: ECMAScript Modules in Node.js
    url: https://nodejs.org/api/esm.html
---

ECMAScript 模块（ESM）是在 Web 中使用模块的[规范](https://tc39.github.io/ecma262/#sec-modules)。
所有现代浏览器均支持此功能，同时也是在 Web 中编写模块化代码的推荐方式。

webpack 支持处理 ECMAScript 模块以优化它们。

## 导出 {#exporting}

关键字 `export` 允许将 ESM 中的内容暴露给其他模块:

```js
export const CONSTANT = 42;

export let variable = 42;
// 对外暴露的变量为只读
// 无法从外部修改

export function fun() {
  console.log('fun');
}

export class C extends Super {
  method() {
    console.log('method');
  }
}

let a, b, other;
export { a, b, other as c };

export default 1 + 2 + 3 + more();
```

## 导入 {#importing}

关键字 `import` 允许从其他模块获取引用到 ESM 中:

```js
import { CONSTANT, variable } from './module.js';
// 导入由其他模块导出的“绑定”
// 这些绑定是动态的. 这里并非获取到了值的副本
// 而是当将要访问“variable”时
// 再从导入的模块中获取当前值

import * as module from './module.js';
module.fun();
// 导入包含所有导出内容的“命名空间对象”

import theDefaultValue from './module.js';
// 导入 `default` 导出的快捷方式
```

## 将模块标记为ESM {#flagging-modules-as-esm}

默认情况下，webpack 将自动检测文件是 ESM 还是其他模块系统。

Node.js 通过设置 `package.json` 中的属性来显式设置文件模块类型。
在 package.json 中设置 `"type": "module"` 会强制 package.json 下的所有文件使用 ECMAScript 模块。
设置 `"type": "commonjs"` 将会强制使用 CommonJS 模块。

```json
{
  "type": "module"
}
```

除此之外，文件还可以通过使用 `.mjs` 或 `.cjs` 扩展名来设置模块类型。 `.mjs` 将它们强制置为 ESM，`.cjs` 将它们强制置为 CommonJs。

在使用 `text/javascript` 或 `application/javascript` mime type 的 DataURI 中，也将使用 ESM。

除了模块格式外，将模块标记为 ESM 还会影响解析逻辑，操作逻辑和模块中的可用符号。

导入模块在 ESM 中更为严格，导入相对路径的模块必须包含文件名和文件扩展名。

T> 依旧支持导入包，例如 `import "lodash"` .

non-ESM 仅能导入 `default` 导出的模块，不支持命名导出的模块。

CommonJs 语法不可用: `require`, `module`, `exports`, `__filename`, `__dirname`.

T> HMR 使用 `import.meta.webpackHot` 代替 `module.hot`.
