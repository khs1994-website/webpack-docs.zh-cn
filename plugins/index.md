---
title: plugin
contributors:
  - simon04
  - gonzoyumo
  - rouzbeh84
  - aretecode
  - eko3alpha
  - refactorized
  - byzyk
---

webpack 有着丰富的插件接口(rich plugin interface)。webpack 自身的多数功能都使用这个插件接口。这个插件接口使 webpack 变得**极其灵活**。

Name                                                     | Description
-------------------------------------------------------- | -----------
[`BabelMinifyWebpackPlugin`](/plugins/babel-minify-webpack-plugin) | 使用 [babel-minify](https://github.com/babel/minify)进行压缩
[`BannerPlugin`](/plugins/banner-plugin)                 | 在每个生成的 chunk 顶部添加 banner
[`CommonsChunkPlugin`](/plugins/commons-chunk-plugin)    | 提取 chunks 之间共享的通用模块
[`CompressionWebpackPlugin`](/plugins/compression-webpack-plugin) | 预先准备的资源压缩版本，使用 Content-Encoding 提供访问服务
[`ContextReplacementPlugin`](/plugins/context-replacement-plugin) | 重写 `require` 表达式的推断上下文
[`CopyWebpackPlugin`](/plugins/copy-webpack-plugin) | 将单个文件或整个目录复制到构建目录
[`DefinePlugin`](/plugins/define-plugin)           | 允许在编译时(compile time)配置的全局常量
[`DllPlugin`](/plugins/dll-plugin)                 | 为了极大减少构建时间，进行分离打包
[`EnvironmentPlugin`](/plugins/environment-plugin) | [`DefinePlugin`](./define-plugin) 中 `process.env` 键的简写方式。
[`ExtractTextWebpackPlugin`](/plugins/extract-text-webpack-plugin) | 从 bundle 中提取文本（CSS）到单独的文件
[`HotModuleReplacementPlugin`](/plugins/hot-module-replacement-plugin) | 启用模块热替换(Enable Hot Module Replacement - HMR)
[`HtmlWebpackPlugin`](/plugins/html-webpack-plugin)          | 简单创建 HTML 文件来服务打包文件
[`I18nWebpackPlugin`](/plugins/i18n-webpack-plugin)          | 为 bundle 增加国际化支持
[`IgnorePlugin`](/plugins/ignore-plugin)  | 从 bundle 中排除某些模块
[`LimitChunkCountPlugin`](/plugins/limit-chunk-count-plugin) | 设置 chunk 的最小/最大限制，以微调和控制 chunk
[`LoaderOptionsPlugin`](/plugins/loader-options-plugin)      | 用于从 webpack 1 迁移到 webpack 2
[`MinChunkSizePlugin`](/plugins/min-chunk-size-plugin)       | 确保 chunk 大小超过指定限制
[`MiniCssExtractPlugin`](/plugins/mini-css-extract-plugin)       | 为每个引入 CSS 的 JS 文件创建一个 CSS 文件
[`NoEmitOnErrorsPlugin`](/configuration/optimization/#optimization-noemitonerrors)  | 在输出阶段时，遇到编译错误跳过
[`NormalModuleReplacementPlugin`](/plugins/normal-module-replacement-plugin) | 替换与正则表达式匹配的资源
[`NpmInstallWebpackPlugin`](/plugins/npm-install-webpack-plugin) | 在开发环境下自动安装缺少的依赖
[`ProgressPlugin`](/plugins/progress-plugin) | 报告编译进度
[`ProvidePlugin`](/plugins/provide-plugin) | 不必通过 import/require 使用模块
[`SourceMapDevToolPlugin`](/plugins/source-map-dev-tool-plugin)  | 对 source map 进行更细粒度的控制
[`EvalSourceMapDevToolPlugin`](/plugins/eval-source-map-dev-tool-plugin)  | 对 eval source map 进行更细粒度的控制
[`UglifyjsWebpackPlugin`](/plugins/uglifyjs-webpack-plugin)      | 可以控制项目中 UglifyJS 的版本
[`TerserPlugin`](/plugins/terser-webpack-plugin) | 允许控制项目中 Terser 的版本
[`ZopfliWebpackPlugin`](/plugins/zopfli-webpack-plugin) | 通过 node-zopfli 将资源预先压缩的版本

更多第三方插件，请查看 [awesome-webpack](https://github.com/webpack-contrib/awesome-webpack#webpack-plugins) 列表。

![Awesome](../assets/awesome-badge.svg)
