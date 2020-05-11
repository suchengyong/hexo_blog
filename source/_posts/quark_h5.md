---
title: Vue + Koa从零打造一个H5页面可视化编辑器——Quark-h5
date: 2020-05-11 10:30:58
tags: js
categories: 前端开发 
thumbnail: http://img2.imgtn.bdimg.com/it/u=1262566837,2339302645&fm=26&gp=0.jpg
---
## 前言
想必你一定使用过易企秀或百度H5等微场景生成工具制作过炫酷的h5页面，除了感叹其神奇之处有没有想过其实现方式呢？本文从零开始实现一个H5编辑器项目完整设计思路和主要实现步骤，并开源前后端代码。有需要的小伙伴可以按照该教程从零实现自己的H5编辑器。（实现起来并不复杂，该教程只是提供思路，并非最佳实践）

原文章地址：https://mp.weixin.qq.com/s/SG9ZYIivbpbXOOTlal9Akw

Github: https://github.com/huangwei9527/quark-h5

演示地址：http://47.104.247.183:4000/

### 技术栈
前端：vue: 模块化开发少不了angular，react，vue三选一，这里选择了vue。vuex: 状态管理sass: css预编译器。element-ui：不造轮子，有现成的优秀的vue组件库当然要用起来。没有的自己再封装一些就可以了。loadsh：工具类

服务端：koa：后端语言采用nodejs，koa文档和学习资料也比较多，express原班人马打造，这个正合适。mongodb：一个基于分布式文件存储的数据库，比较灵活。

### 工程搭建
#### 基于vue-cli3环境搭建

* 如何规划好我们项目的目录结构？首先我们需要有一个目录作为前端项目，一个目录作为后端项目。所以我们要对vue-cli 生成的项目结构做一下改造：
```
···
·
|-- client// 原 src 目录，改成 client 用作前端项目目录
|-- server// 新增 server 用于服务端项目目录
|-- engine-template// 新增 engine-template 用于页面模板库目录
|-- docs// 新增 docs 预留编写项目文档目录
·
···
```
* 这样的话 我们需要再把我们webpack配置文件稍作一下调整，首先是把原先的编译指向src的目录改成client，其次为了 npm run build 能正常编译 client 我们也需要为 babel-loader 再增加一个编译目录：


* 根目录新增vue.config.js,目的是为了改造项目入口,改为:client/main.js
```
module.exports = {
  pages: {
    index: {
      entry: "client/main.js"
    }
  }
}
```
* babel-loader能正常编译 client, engine-template目录， 在vue.config.js新增如下配置

```
// 扩展 webpack 配置
chainWebpack: config => {
    config.module
    .rule('js')
    .include.add(/engine-template/).end()
    .include.add(/client/).end()
    .use('babel')
    .loader('babel-loader')
    .tap(options => {
    // 修改它的选项...
    return options
    })
}
```
这样我们搭建起来一个简易的项目目录结构。

###  工程目录结构
```
|-- client--------前端项目界面代码
        |--common--------前端界面对应静态资源
        |--components--------组件
        |--config--------配置文件
        |--eventBus--------eventBus
        |--filter--------过滤器
        |--mixins--------混入
        |--pages--------页面
        |--router--------路由配置
        |--store--------vuex状态管理
        |--service--------axios封装
        |--App.vue--------App
        |--main.js--------入口文件
        |--permission.js--------权限控制
|-- server--------服务器端项目代码
        |--confog--------数据库链接相关
        |--middleware--------中间件
        |--models--------Schema和Model
        |--routes--------路由
        |--views--------ejs页面模板
        |--public--------静态资源
        |--utils--------工具方法
        |--app.js--------服务端入口
|-- common--------前后端公用代码模块（如加解密）
|-- engine-template--------页面模板引擎，使用webpack打包成js提供页面引用
|-- docs--------预留编写项目文档目录
|-- config.json--------配置文件
```
### 前端编辑器实现
编辑器的实现思路是：编辑器生成页面JSON数据，服务端负责存取JSON数据，渲染时从服务端取数据JSON交给前端模板处理。

### 数据结构
确认了实现逻辑，数据结构也是非常重要的，把一个页面定义成一个JSON数据，数据结构大致是这样的：

##### 页面工程数据接口
```
 {
    title: '', // 标题
    description: '', //描述
    coverImage: '', // 封面
    auther: '', // 作者
    script: '', // 页面插入脚本
    width: 375, // 高
    height: 644, // 宽
    pages: [], // 多页页面
    shareConfig: {}, // 微信分享配置
    pageMode: 0, // 渲染模式，用于扩展多种模式渲染，翻页h5/长页/PC页面等等
}
```
#### 多页页面pages其中一页数据结构：
```
{
    name: '',
    elements: [], // 页面元素
    commonStyle: {
    backgroundColor: '',
    backgroundImage: '',
    backgroundSize: 'cover'
    },
    config: {}
}
```
#### 元素数据结构：
```
{
    elName: '', // 组件名
    animations: [], // 图层的动画,可以支持多个动画
    commonStyle: {}, // 公共样式，默认样式
    events: [], // 事件配置数据，每个图层可以添加多个事件
    propsValue: {}, // 属性参数
    value: '', // 绑定值
    valueType: 'String', // 值类型
    isForm: false// 是否是表单控件，用于表单提交时获取表单数据
}
```
### 核心代码
编辑器核心代码，基于 Vue 动态组件特性实现：
```
<div 
   v-for="item in activePage.element"
   :key="item.uuid"
   :style="getCommonStyle(item.commonStyle)"
   :active="item.uuid === activeElementUUID">
   <component :is="item.elName" v-bind="item" />
</div>
```
### 画板元素渲染
编辑画板只需要循环遍历pages[i].elements数组，将里面的元素组件JSON数据取出，通过动态组件渲染出各个组件，支持拖拽改变位置尺寸.

### 元素组件管理
在client目录新建plugins来管理组件库。也可以将该组件库发到npm上工程中通过npm管理

### 组件库
编写组件，考虑的是组件库，所以我们竟可能让我们的组件支持全局引入和按需引入，如果全局引入，那么所有的组件需要要注册到Vue component 上，并导出：

#### client/plugins下新建index.js入口文件
```
/**
 * 组件库入口
 * */
    import Text from'./text'
    // 所有组件列表
    const components = [
        Text
    ]
    // 定义 install 方法，接收 Vue 作为参数
    const install = function (Vue) {
    // 判断是否安装，安装过就不继续往下执行
    if (install.installed) return
        install.installed = true
        // 遍历注册所有组件
        components.map(component => Vue.component(component.name, component))
    }
    
    // 检测到 Vue 才执行，毕竟我们是基于 Vue 的
        if (typeofwindow !== 'undefined' && window.Vue) {
        install(window.Vue)
    }
    
    exportdefault {
        install,
        // 所有组件，必须具有 install，才能使用     Vue.use()
        Text
    }
```
### 组件开发
示例：text文本组件

client/plugins下新建text组件目录
```
 |-- text                --------text组件
        |--src              --------资源
        |--index.vue    --------组件
        |--index.js         --------入口
```
text/index.js
```
// 为组件提供 install 方法，供组件对外按需引入
    import Component from'./src/index'
    Component.install = Vue => {
    Vue.component(Component.name, Component)
    }
    exportdefault Component
```
text/src/index.vue
```
    <!--text.vue-->
    <template>
      <div class="qk-text">
        {{text}}
      </div>
    </template>
    
    <script>
    export default {
    name: 'QkText', // 这个名字很重要，它就是未来的标签名<qk-text></qk-text>
    props: {
    text: {
    type: String,
    default: '这是一段文字'
          }
    }
    }
    </script>
    
    <style lang="scss" scoped>
    </style>
```
#### 编辑器里使用组件库：
```
    // 引入组件库
    import QKUI from'client/plugins/index'
    // 注册组件库
    Vue.use(QKUI)
    
    // 使用：
    <qk-text text="这是一段文字"></qk-text>
```
#### 配置文件
Quark-h5编辑器左侧选择组件区域可以通过一个配置文件定义可选组件新建一个ele-config.js配置文件：
```
exportdefault [
    {
    title: '基础组件',
    components: [
    {
    elName: 'qk-text', // 组件名，与组件库名称一致
    title: '文字',
    icon: 'iconfont iconwenben',
    // 给每个组件配置默认显示样式
    defaultStyle: {
    height: 40
    }
    }
    ]
    },
    {
    title: '表单组件',
    components: []
    },
    {
    title: '功能组件',
    components: []
    },
    {
    title: '业务组件',
    components: []
    }
    ]
```
公共方法中提供一个function 通过组件名和默认样式获取元素组件JSON，getElementConfigJson(elName, defaultStyle)方法

### 封装一个动画执行方法
```
    /**
     * 动画方法， 将动画css加入到元素上，返回promise提供执行后续操作（将动画重置）
     * @param $el 当前被执行动画的元素
     * @param animationList 动画列表
     * @param isDebugger 动画列表
     * @returns {Promise<void>}
     */
    exportdefaultasyncfunction runAnimation($el, animationList = [], isDebug , callback){
    let playFn = function (animation) {
    returnnewPromise(resolve => {
    $el.style.animationName =  animation.type
    $el.style.animationDuration =  `${animation.duration}s`
    // 如果是循环播放就将循环次数置为1，这样有效避免编辑时因为预览循环播放组件播放动画无法触发animationend来暂停组件动画
    $el.style.animationIterationCount =  animation.infinite ? (isDebug ? 1 : 'infinite') : animation.interationCount
    $el.style.animationDelay =  `${animation.delay}s`
    $el.style.animationFillMode =  'both'let resolveFn = function(){
    $el.removeEventListener('animationend', resolveFn, false);
    $el.addEventListener('animationcancel', resolveFn, false);
    resolve()
    }
    $el.addEventListener('animationend', resolveFn, false)
    $el.addEventListener('animationcancel', resolveFn, false);
    })
    }
    for(let i = 0, len = animationList.length; i < len; i++){
    await playFn(animationList[i])
    }
    if(callback){
    callback()
    }
    }
```
animationIterationCount 如果是编辑模式的化动画只执行一次，不然无法监听到动画结束animationend事件

执行动画前先将元素样式style缓存起来，当动画执行完再将原样式赋值给元素
```
   let cssText = this.$el.style.cssText;
    runAnimations(this.$el, animations, true, () => {
       this.$el.style.cssText = cssText
    })
```
### psd设计图导入生成h5页面
将psd每个设计图中的每个图层导出成图片保存到静态资源服务器中，

服务端安装psd依赖
```
    cnpm install psd --save
```
#### 加入psd.js依赖，并且提供接口来处理数据
```
var PSD = require('psd');
    router.post('/psdPpload',async ctx=>{
    const file = ctx.request.files.file; // 获取上传文件
    let psd = await PSD.open(file.path)
    var timeStr = + newDate();
    let descendantsList = psd.tree().descendants();
    descendantsList.reverse();
    let psdSourceList = []
    let currentPathDir = `public/upload_static/psd_image/${timeStr}`
    for (var i = 0; i < descendantsList.length; i++){
    if (descendantsList[i].isGroup()) continue;
    if (!descendantsList[i].visible) continue;
    try{
    await descendantsList[i].saveAsPng(path.join(ctx.state.SERVER_PATH, currentPathDir + `/${i}.png`))
    psdSourceList.push({
    ...descendantsList[i].export(),
    type: 'picture',
    imageSrc: ctx.state.BASE_URL + `/upload_static/psd_image/${timeStr}/${i}.png`,
    })
    }catch (e) {
    // 转换不出来的图层先忽略
    continue;
    }
    }
    ctx.body = {
        elements: psdSourceList,
        document: psd.tree().export().document
    };
})
```
最后把获取的数据转义并返回给前端，前端获取到数据后使用系统统一方法，遍历添加统一图片组件

## 后端服务

### 初始化项目
工程目录上文已给出，也可以使用 koa-generator 脚手架工具生成

##### ejs-template 模板引擎配置
app.js
```
//配置ejs-template 模板引擎
render(app, {
    root: path.join(__dirname, 'views'),
    layout: false,
    viewExt: 'html',
    cache: false,
    debug: false
});
```
### koa-static静态资源服务
因为html2canvas需要图片允许跨域，所以在静态资源服务中所有资源请求设置'Access-Control-Allow-Origin':'*'

app.js
```
//配置静态web
app.use(koaStatic(__dirname + '/public'), { gzip: true, setHeaders: function(res){
    res.header( 'Access-Control-Allow-Origin', '*')
}});

```
#### 修改路由的注册方式，通过遍历routes文件夹读取文件
app.js
```
const fs =  require('fs')
fs.readdirSync('./routes').forEach(route=> {
    let api = require(`./routes/${route}`)
    app.use(api.routes(), api.allowedMethods())
})
```

#### 添加jwt认证，同时过滤不需要认证的路由，如获取token
app.js
```
const jwt = require('koa-jwt')
app.use(jwt({ secret: 'yourstr' }).unless({
    path: [
        /^\/$/, /\/token/, /\/wechat/,
        { url: /\/papers/, methods: ['GET'] }
    ]
}));
```
#### 中间件实现统一接口返回数据格式，全局错误捕获并响应
middleware/formatresponse.js
```
module.exports = async (ctx, next) => {
    await next().then(() => {
    if (ctx.status === 200) {
        ctx.body = {
            message: '成功',
            code: 200,
            body: ctx.body,
            status: true
        }
    } elseif (ctx.status === 201) { // 201处理模板引擎渲染
    
    } else {
        ctx.body = {
            message: ctx.body || '接口异常，请重试',
            code: ctx.status,
            body: '接口请求失败',
            status: false
        }
    }
    }).catch((err) => {
    if (err.status === 401) {
        ctx.status = 401;
        ctx.body = {
        code: 401,
        status: false,
        message: '登录过期，请重新登录'
    }
    } else {
        throw err
    }
    })
    }
```
### koa2-cors跨域处理
当接口发布到线上，前端通过ajax请求时，会报跨域的错误。koa2使用koa2-cors这个库非常方便的实现了跨域配置，使用起来也很简单
```
const cors = require('koa2-cors');
    app.use(cors());
```
### 连接数据库
我们使用mongodb数据库，在koa2中使用mongoose这个库来管理整个数据库的操作。

创建配置文件
根目录下新建config文件夹，新建mongo.js
```
   //config/mongo.js
    const mongoose = require('mongoose').set('debug', true);
    const options = {
        autoReconnect: true
    }
    
    // username 数据库用户名
    // password 数据库密码
    // localhost 数据库ip
    // dbname 数据库名称
    const url = 'mongodb://username:password@localhost:27017/dbname'
    
    module.exports = {
        connect: ()=> {
            mongoose.connect(url,options)
            let db = mongoose.connection
            db.on('error', console.error.bind(console, '连接错误:'));
            db.once('open', ()=> {
                console.log('mongodb connect suucess');
            })
        }
    }
```
把mongodb配置信息放到config.json中统一管理

然后在app.js中引入
```
const mongoConf = require('./config/mongo');
    mongoConf.connect();
```
## 启动运行
#### 启动前端
```
    npm run dev-client
```
#### 启动服务端
```
    npm run dev-server
```
注意：如果没有生成过引擎模板js文件的，需要先编辑引擎模板，否则预览页面加载页面引擎.js 404报错

#### 编译engine.js模板引擎
```
    npm run lib:h5-swiper

```
