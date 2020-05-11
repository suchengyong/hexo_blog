---
title: vue项目使用flexible+rem实现移动端自适应
date: 2018-02-26 16:03:58
tags: css,js
categories: 前端开发 
thumbnail: http://www.ruanyifeng.com/images_pub/pub_179.jpg
---

>项目安装flexible px2rem
>npm i lib-flexible --save
>npm i px2rem-loader --save-dev

* 入口文件引入flexible
* main.js文件 import 'lib-flexible'

* webpack生成的vue-cli项目更改配置
* 文件 build/utils.js
* 修改

```
const cssLoader = {
    loader: 'css-loader',
    options: {
      sourceMap: options.sourceMap,
      importLoaders: 2
    }
  }

```

* 增加

```
const px2remLoader = {
    loader: 'px2rem-loader',
    options: {
      remUnit: 75
    }
  }

```

* remUnit的值根据设计稿来，这里是750的设计稿

* 修改
* const loaders = options.usePostCSS ? [cssLoader, postcssLoader,px2remLoader] : [cssLoader,px2remLoader]
