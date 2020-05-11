---
title: 使用第三方库ts-node简化Ts编译流程
date: 2020-05-11 10:30:58
tags: Ts
categories: 前端开发 
thumbnail: http://img2.imgtn.bdimg.com/it/u=1099335495,2353408217&fm=26&gp=0.jpg
---

#### 1、ts-node: 将ts文件在内存中完成编译、同时完成运行

1、全局安装ts-node和typescript两个模块
```
npm i -g ts-node  typescript 

```

#### 使用： ts-node src/index.ts

2、nodemon: 用于检测文件变化
```
cnpm i -g nodemon
```
3、使用：
```
nodemon --exec ts-node src/index.ts
```
4、可以将检测文件变化添加到脚本、方便调用
在package.json中添加：
```
"scripts": {
    "dev": "nodemon --watch src -e ts --exec ts-node src/index.ts"
 }
 ```
 * --watch src: 只检测src文件夹
 * -e ts: 只检.ts文件

5、使用时直接：
```
npm run dev
```