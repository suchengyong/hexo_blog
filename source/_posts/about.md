---
title: Hexo搭建记录
date: 2020-05-11 10:23:58
tags: hexo
categories: Hexo 
thumbnail: http://img0.imgtn.bdimg.com/it/u=3434045650,2211347790&fm=26&gp=0.jpg
---

Hexo 是一个快速、简洁且高效的博客框架。[Hexo](https://hexo.io/zh-cn/docs/) 使用 [Markdown](http://markdownpad.com/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成完美的静态网页。

## 基础安装

安装 Hexo 相当简单。然而在安装前，您必须检查电脑中是否已安装下列应用程序：
* [node](https://nodejs.org/zh-cn/)<br>
* [git](https://git-scm.com/downloads)
如果您的电脑中已经安装上述必备程序，那么恭喜您！接下来只需要使用 npm 或者 yarn 即可完成 Hexo 的安装。
``` bash
$ npm install -g hexo-cli
```

## 安装 Node.js
安装 Node.js 的最佳方式是使用 [nvm](https://github.com/creationix/nvm)。
cURL:
``` bash
$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh

```
Wget:

``` bash
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh

```

安装完成后，重启终端并执行下列命令即可安装 Node.js。

``` bash
	$ nvm install stable
```
## 安装 Hexo
所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。
``` bash
$ npm install -g hexo-cli
```
## hexo系列相关插件
* hexo脚手架
``` bash
$ npm install hexo-cli -g
```
* hexo-deployer-git (远程部署)
``` bash
$ npm install hexo-deployer-git --save
```
* hexo-asset-image (插入图片)
``` bash
$ npm install hexo-asset-image --save
```
* hexo-generator-json-content (用于全部文章获取)
``` bash
$  npm install hexo-generator-json-content --save
```
* yilia主题
``` bash
$  git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
## 建站

安装 Hexo 完成后，接下来就需要我们配置hexo,请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件.
``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
1.打开根目录_config.yml文件，设置网站内容:标题、描述等

2.配置deploy选项:

>type: 根据代码部署情况(我的是git)
>repo: 项目仓库地址
>branch: 项目所在分支

3.设置主题

>theme: yilia

4.主题内容设置:

>进入themes/yilia/\_config.yml,根据自己要求设置即可。
>可参考[Yilia](https://github.com/litten/hexo-theme-yilia)

## 写作
你可以执行下列命令来创建一篇新文章
``` bash 
$ hexo new [layout] <title>
```
## 本地启动测试
``` bash 
$ hexo server

```
## 发布

1.首先清除原来打包资源，首次不需要清除
``` bash 
$ hexo clean
```
2.生成静态资源
``` bash 
$  hexo generate
```
3.部署到远程服务器上
``` bash 
$  hexo deploy
```

