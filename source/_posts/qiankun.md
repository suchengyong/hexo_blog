---
title: 前端微服务框架qiankun项目实践
date: 2020-05-11 10:30:58
tags: js
categories: 前端开发 
thumbnail: http://img2.imgtn.bdimg.com/it/u=2337344105,3018349735&fm=26&gp=0.jpg
---
## 导语
最近在做微前端的项目 , 过程中真是踩了不少坑 , 在有限的资料中不断试错 , 默默无语两行泪 哈哈.  在此次将踩坑部分都记录下来, 让更多的人少走点弯路 ,   此项目使用 蚂蚁金服qiankun 为基础作为开发 . 话不多说 开讲 !!!

### 什么是qiankun（乾坤）
qiankun 是一个基于 single-spa 的微前端实现库，旨在帮助大家能更简单、无痛的构建一个生产可用微前端架构系统。

### 什么是微前端
微前端架构具备以下几个核心价值：
* 技术栈无关

主框架不限制接入应用的技术栈，微应用具备完全自主权
* 独立开发、独立部署

微应用仓库独立，前后端可独立开发，部署完成后主框架自动完成同步更新

* 增量升级

在面对各种复杂场景时，我们通常很难对一个已经存在的系统做全量的技术栈升级或重构，而微前端是一种非常好的实施渐进式重构的手段和策略

* 独立运行时

每个微应用之间状态隔离，运行时状态不共享

### 主应用配置

此次项目 主应用与 子应用均为 vue ,
#### 下载 qiankun
```
npm install qiankun
```
在主应用中注册微应用
```
// 导入乾坤函数
import {
  registerMicroApps,
  setDefaultMountApp,
  start
} from "qiankun";
```

#### 封装 render 方法

此方法在main.js 中要初始调用一次, 主要用来挂载主应用 , 之后子应用分别依次调用 ,所以故作判断. 传入的参数分别为 子应用 的 HTML 和 加载状态 content 字段 我们用 vuex 存储 起来,方便使用
```
let app = null;

function render({ appContent, loading }) {
  if (!app) {
    app = new Vue({
      router,
      store,
      render: h => h(App),
    }).$mount('#app');
    
  } else {
    store.commit('microApp/changeCenter', appContent);
    store.commit('microApp/changeLoading', loading);
  }

}
```
#### 微应用注册

下文中的apps 可以为获取后数据 , 注册微应用 本文案例比较简单,方便大家理解 ,
在注册自应用的参数 ** container 与 render** 踩坑比较多,下边会着重讲解.
```
function genActiveRule(routerPrefix) {
  return location => location.pathname.startsWith(routerPrefix);
}

//传递给子应用的数据
let msg = {
![](https://user-gold-cdn.xitu.io/2020/4/27/171bbc5de042ec98?w=1811&h=959&f=gif&s=4951066)
  data:'修炼爱情的辛酸,学会放好以前的渴望'
}

let apps = [
  {
    name: 'linjunjie', 
    entry: '//localhost:215',  // 改成自己子应用的端口号
    container:'#subView', //节点 id   //  沙盒模式 
    // render:render,  // 普通模式   
    activeRule: genActiveRule('/star'),
    props:msg
  }
]
   //注册的子应用 参数为数组
registerMicroApps(apps,{
  beforeLoad: [
    app => {
      console.log(app)
      console.log('[LifeCycle] before load %c%s', 'color: green;', app.name);
    },
  ],
  beforeMount: [
    app => {
      console.log('[LifeCycle] before mount %c%s', 'color: green;', app.name);
    },
  ],
  afterUnmount: [
    app => {
      console.log('[LifeCycle] after unmount %c%s', 'color: green;', app.name);
    },
  ],
});


setDefaultMountApp('/star/linjunjie')

//开启沙盒模式
start({ 
   sandbox :{strictStyleIsolation: true}
})
```
当微应用信息注册完之后，一旦浏览器的 url 发生变化，便会自动触发 qiankun 的匹配逻辑，所有 activeRule 规则匹配上的微应用就会被插入到指定的 container 中，同时依次调用微应用暴露出的生命周期钩子。

#### 主应用为子应用准备的 展示元素
```
<template>
  <div id="app">
    <div id="nav">
      <!--//主应用 为子应用的跳转dom-->
      <div @click="onChangePage('/star/linjunjie')" >林俊杰</div>
      <div @click="onChangePage('/star/zhangyixin')" >张艺兴</div>
   
    </div>
<!--//用来展子应用的 内容区-->
     <div id="subView" class="sub-content-wrap" v-html="content"></div>
  </div>
</template>

<script>

  import { mapState } from 'vuex';
  export default{
    data(){
      return {
     
      }
    },
    computed:{
    //获取子应用HTML 数据
       ...mapState('microApp', ['content']),
       ...mapState('microApp', ['mircoAppLoading']),
    },

    methods:{
    
      //定义跳转方法
      onChangePage(url){
        console.log(url)
        
        this.routerGo(url, '我喜爱的男明星')
      },
  
      routerGo(href = '/', title = null, stateObj = {}) {
        window.history.pushState(stateObj, title, href); 
      },
    }
 }
</script>

```
### 子应用配置
关于子应用的配置相对较简单 , 不需要额外下载qiankun 主要将生命钩子 导出即可

#### 导出响应的生命钩子
导出 bootstrap、mount、unmount 三个生命周期钩子，以供主应用在适当的时机调用。注意，实例化路由时，判断当运行在qiankun环境时，路由要添加前缀，前缀与主应用注册子应用函数genActiveRule("/subdemo")内的参数一致

'star' 值需要与主应用的值对应 genActiveRule("/star") 中的值需要商定好 主应用与微应用都要使用

如果 new VueRouter 不在main.js  中 配置 ,请将此配置移动到 main.js  方便管理

```
import routes from './router' //将路由信息导出方便使用 

let router = null;
let instance = null;

function render(props = {}) {
  const { container } = props;
  router = new VueRouter({
    base: window.__POWERED_BY_QIANKUN__ ? '/star' : '/',  
    mode: 'history',
    routes,
  });

  instance = new Vue({
    router,
    store,
    render: h => h(App),
  }).$mount(container ? container.querySelector('#app') : '#app');
}

if (!window.__POWERED_BY_QIANKUN__) {
  render();
}


export async function bootstrap() {
  console.log('[vue] vue app bootstraped');
}

export async function mount(props) {
 //props 包含主应用传递的参数  也包括为子应用 创建的节点信息
  console.log(props)
  render(props);
}

export async function unmount() {
  instance.$destroy();
  instance = null;
  router = null;
}

```
#### 配置微应用的打包工具
除了代码中暴露出相应的生命周期钩子之外，为了让主应用能正确识别微应用暴露出来的一些信息，微应用的打包工具需要在vue.config.js 中 增加如下配置:
```
const packageName = require('./package.json').name;

module.exports = {
  output: {
    library: `${packageName}-[name]`,
    libraryTarget: 'umd',
    jsonpFunction: `webpackJsonp_${packageName}`,
  },
};
```
#### 子应用判断
子应用中新建 publicPath.js  在main.js 引入
```
if (window.__POWERED_BY_QIANKUN__) { 
//处理资源
 __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__; 
}
```
#### 处理 资源加载问题

配置 vue.config.js
```
module.exports = {
  publicPath:`//localhost:${port}`,
}
```
#### vue.config.js 完整配置

```
const path = require('path');
const packageName = require('./package').name;

function resolve(dir) {
  return path.join(__dirname, dir);
}

const port = 7101; // dev port
module.exports = {

  publicPath:`//localhost:${port}`,
  outputDir: 'dist',
  assetsDir: 'static',
  filenameHashing: true,
 
  devServer: {
    // host: '0.0.0.0',
    hot: true,
    historyApiFallback: true,//添加 重点
    port,
    overlay: {
      warnings: false,
      errors: true,
    },
    headers: {
      'Access-Control-Allow-Origin': '*',
    },
  },

  configureWebpack: {
    resolve: {
      alias: {
        '@': resolve('src'),
      },
    },
    output: {
      library: `${packageName}-[name]`,
      libraryTarget: 'umd',
      jsonpFunction: `webpackJsonp_${packageName}`,
    },
  },
};
```
### 踩坑记录
#### 当前页面为子应用时, 刷新页面404

以下方式均为主应用配置

* 方式一 删除 mode 配置项
```
mode: 'history', //   将此配置代码删除
```
* 方式二 配置404  页面

如果没有注释掉mode: 'history'  此参数 将404 页面重新导向  home首页
```
{
    path: '*',
    name: 'indexNotFound',
    component: resolve => require(['@/components/home'], resolve),
    children: HomeChild,
},
```
#### 子应用 样式隔离 开始沙箱模式 遇到的问题
* 主应用配置sandbox :{strictStyleIsolation: true}渲染模式由 render 模式 改为 containercontainer:'#subView', 此时 子应用的 挂载 dom  为 <div id="subView"> </div>   谨记主 container :#+id
* 子应用配置 上文有提到  主要代码 截取
```
 instance = new Vue({
    router,
    store,
    render: h => h(App),
  }).$mount(container ? container.querySelector('#app') : '#app'); //重点
```
遇到的问题: 开启沙箱模式,如果是 采用 render 模式会报错 ,故选择container 模式

写到这里,项目已经构建完成了 

这里是完整代码 方便大家学习 代码github地址:https://github.com/suchengyong/qiankun-vue

### 项目问题

为啥我项目启动后看不到子应用的效果
将master 主应用 main.js 中 注册的 子应用的端口号 改成自己项目的端口号即可

### 结语

开发中还有其他坑 忘记记录了, 千万记得项目部署子应用资源跨域的问题 , 需要Nginx配置跨域问题
