---
title: 很棒的Webpack插件
date: 2020-05-11 10:03:12
tags: webpack
categories: 前端开发 
thumbnail: http://a0.att.hudong.com/56/12/01300000164151121576126282411.jpg
---
# 提高工作效率的很棒的Webpack插件

### 1、Webpack Bundle Analyzer

通过交互式的、可缩放的树状图来可视化webpack输出文件的大小。
该插件将帮助你您执行以下操作：

* 了解你的包里真正有什么
* 找出哪些模块构成了捆绑软件的最大组成部分
* 查找错误到达的模块
* 优化你的Webpack捆绑包
#### 安装
```
# NPM
npm install --save-dev webpack-bundle-analyzer
# Yarn
yarn add -D webpack-bundle-analyzer
```
#### 使用
```
const BundleAnalyzerPlugin = require('webpack-bundle analyzer');
module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
}
```
### 2、offline-plugin
offline-plugin 旨在为webpack项目提供离线体验。

该插件使用 ServiceWorker 和 AppCache 作为后台引擎。只需将这个插件包含在 webpack.config 中，并将随附的运行时包含在客户端脚本中，通过缓存所有（或一些）webpack输出资产，你的项目将变为离线状态。

#### 安装
```
npm install offline-plugin [--save-dev]
```
#### 使用

首先，在您的webpack.config中实例化插件：
```
// webpack.config.js example
var OfflinePlugin = require('offline-plugin');
module.exports = {
  // ...
  plugins: [
    // ... other plugins
    // 如果OfflinePlugin是最后添加的插件，那就更好了
    new OfflinePlugin()
  ]
  // ...
}
```
并且，您可以选择使用选项进行配置。然后，将运行时间添加到entry文件（通常是entry目）中：
```
require('offline-plugin/runtime').install();
```
#### ES6/Babel/TypeScript
```
import * as OfflinePluginRuntime from 'offline-plugin/runtime';
OfflinePluginRuntime.install();
```
有关 TypeScript 用法的更多详细信息，请参见此处。

### 3、webpack-pwa-manifest
webpack-pwa-manifest 将自己描述为“用于Webpack的渐进式Web App清单生成器，具有自动图标大小调整和指纹识别支持。”


webpack-pwa-manifest 是一个webpack插件，可为您的渐进式Web应用程序生成 manifest.json。

如果您在配置上使用注入，请确保 HtmlWebpackPlugin 出现在plugins 数组中的 WebpackPwaManifest 之前。

#### 特征

* 自动图标大小调整
* 图标指纹
* 清单指纹
* HTML上的自动清单注入
* 热重载支持
#### 安装
```
npm install --save-dev webpack-pwa-manifest
```
### 使用

在你的 webpack.config.js 中：

```
import WebpackPwaManifest from 'webpack-pwa-manifest'
...
plugins: [
  new WebpackPwaManifest({
    name: 'My Progressive Web App',
    short_name: 'MyPWA',
    description: 'My awesome Progressive Web App!',
    background_color: '#ffffff',
    crossorigin: 'use-credentials', // 可以是null、use-credentials还是anonymous
    icons: [
      {
        src: path.resolve('src/assets/icon.png'),
        sizes: [96, 128, 192, 256, 384, 512] // 多个大小
      },
      {
        src: path.resolve('src/assets/large-icon.png'),
        size: '1024x1024' // 你还可以使用specification模式
      }
    ]
  })
]
```
### 4、imagemin-webpack-plugin
imagemin-webpack-plugin 是一个webpack插件，用于使用imagemin压缩图像。

#### 安装
```
npm install imagemin-webpack-plugin
```
#### 使用
```
import ImageminPlugin from 'imagemin-webpack-plugin'
module.exports = {
  plugins: [
    // 确保插件位于添加图像的插件之后
    new ImageminPlugin({
      disable: process.env.NODE_ENV !== 'production', // 在开发过程中禁用
      pngquant: {
        quality: '95-100'
      }
    })
  ]
}
```
### 5、prerender-spa-plugin
prerender-spa-plugin 将静态HTML预渲染到单页应用程序中

该插件的目的是提供一个简单的预渲染解决方案，该解决方案可轻松扩展并适用于任何使用webpack构建的网站或单页应用。

#### 什么是预渲染？

最近，服务器端渲染（SSR）席卷了JavaScript前端世界。事实上，你现在可以先在服务器上渲染你的网站和应用程序，然后再发送给你的客户，这绝对是一个革命性的想法（在JS客户端应用程序流行起来之前，每个人都在做的事情，这完全不是在JS客户端应用程序流行起来之前的事情）。

然而，过去对PHP、ASP、JSP（等等）网站的批评，现在对服务器端渲染也是一样的。它的速度很慢，相当容易崩溃，而且难以正确实现。

问题是，不管别人怎么说，你可能不需要SSR。你可以通过使用预分发来获得它几乎所有的优点(没有缺点)。预渲染基本上是启动一个无头浏览器，加载应用程序的路由，并将结果保存到一个静态HTML文件。然后，你可以将其与以前使用的任何静态文件服务解决方案一起使用。它仅适用于HTML5导航等。无需更改代码或添加服务器端渲染解决方法。

#### 安装
```
yarn add -D prerender-spa-plugin
```
#### 基本使用
```
const path = require('path')
const PrerenderSPAPlugin = require('prerender-spa-plugin')
module.exports = {
  plugins: [
    ...
    new PrerenderSPAPlugin({
      // 必需的——指向webpack输出应用程序的prerender路径。
      staticDir: path.join(__dirname, 'dist'),
      // 必需的 - Routes to render.
      routes: [ '/', '/about', '/some/deep/nested/route' ],
    })
  ]
}
```
### 6、duplicate-package-checker-webpack-plugin
这是一个webpack插件，当你的捆绑包包含同一软件包的多个版本时，会发出警告。

#### 为什么？

由于不同的软件包版本，单个软件包可能多次包含在webpack捆绑软件中。这种情况可能会在没有任何警告的情况下发生，从而导致捆绑软件中出现额外的膨胀，并可能导致难以发现的错误。

该插件会在这种情况下警告你，以最大程度地减少捆绑包的大小，并避免由于意外的重复包装而导致的错误。

#### 安装
```
npm install duplicate-package-checker-webpack-plugin --save-dev
```
#### 使用

将插件添加到你的webpack配置中：
```
const DuplicatePackageCheckerPlugin = require("duplicate-package-checker-webpack-plugin");
module.exports = {
  plugins: [new DuplicatePackageCheckerPlugin()]
};
```