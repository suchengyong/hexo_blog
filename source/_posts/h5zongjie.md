---
title: h5 总结及踩坑记录
date: 2020-05-11 10:20:59
tags: js,htnl5
categories: 前端开发 
thumbnail: http://img5.imgtn.bdimg.com/it/u=2837837654,3789113455&fm=26&gp=0.jpg
---
### 适配方案

这个项目里用的是 rem 适配方案，通过计算 设备宽度/设计稿宽度 的比例，来设置 html 的 font-size 属性，达到适配的目的，代码如下：
```
function setSize() {
  // 设备宽度
  let deviceWidth = window.screen.width;
  // 设计稿宽度
  const baseValue = 750;
  // html的字体大小 = (设备宽度 / 设计稿宽度) * 100
  document.documentElement.style.fontSize = (deviceWidth / baseValue) * 100 + 'px';
}

// DOM树加载完执行
window.addEventListener("DOMContentLoaded", function () {
  setSize();
})

// 屏幕变化就执行
window.addEventListener("resize", function () {
  setSize();
})

setSize();
```
### 资源的预加载
资源的预加载用的是 preloadjs
中文官网：www.createjs.cc/preloadjs
用法也是极其简单：
```
var queue = new window.createjs.LoadQueue(true);
  queue.on("complete", this.allLoadComplete);  // 所有文件加载完成时触发
  queue.on("fileload", this.aloneLoadComplete);  // 单个文件加载完成时触发
  queue.on("progress", this.fileProgress);  // 加载进度
  queue.loadManifest(allImg);  // 需要加载的资源数组
  queue.load()
```
### 如何解决需要引入很多图片的问题
这里用到了 webpack 的 api: require.context，当项目需要引入很多资源时，这项技术是必须要掌握的
可以阅读 使用require.context自动导入ES模块 - yeyan1996 这位大佬的文章，本文不做深入探讨

### html2canvas 截取页面时图片模糊
如果截取的区域里有涉及到图片，不要用 background 设置图片，全部替换成 img 标签
这样可以大大提升图片的清晰度

### ios 键盘会把页面顶上去 不会自动下来
问题描述：移动端 ios 键盘弹起后，会把页面顶上去，输入完成后页面不会自动下来
解决办法：
```
document.body.addEventListener('focusout', function () {
    window.scrollTo(0,0);
});
```
当监听到 body 里有元素失去焦点时，就把页面滚上去
>focusout: 当元素即将失去焦点时，focusout 事件被触发。focusout 事件和 blur 事件之间的主要区别在于后者不会冒泡
—— MDN
### 滑动加载以及获取body实际高度的坑
滑动加载的关键就是如果页面滑动到了底部，就进行数据的请求，要事先和后端沟通好数据怎么返回
滑动到页面底部的条件：滚动条离顶部的距离（document.body.scrollTop）+ 窗口的文档显示区的高度（window.innerHeight）>= 文档实际高度（document.body.scrollHeight）
这里有个坑，关于获取文档实际高度的：
document.body.scrollHeight 可以在手机上获取到实际文档高度，但在 chrome 里获取到的高度是 0
document.documentElement.scrollHeight 在 chrome 里可以获取到正常的，但在手机上获取的高度是 0
这样的话，还要判断当前设备是手机还是 pc 然后再去获取吗，No No No，有个优雅的写法：
```
// 获取文档实际高度/移动端
var bodyHeight = Math.max(
  document.documentElement.scrollHeight,
  document.body.scrollHeight
);
```

### 去除 iphone x 的小尾巴
大家管这玩意叫胡子，我比较喜欢叫小尾巴~，就是 iphone x 下面那一根玩意
想要了解更多请点击：imweb.io/topic/5baa3…
我在这里就说说我是怎么用的
在 meta 标签里加上 viewport-fit=cover
```
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover" />
```
然后在底部的样式里加上样式：
```
bottom: env(safe-area-inset-bottom);
bottom: 0;  // 这一句也要加上
```
没有小尾巴的机型需要用到这个样式
这里的 safe-area-inset-bottom 的意思是：在 Viewport底部的安全区域内设置量（CSS像素），更多方向看下图
### ios 最新版系统 微信浏览器 html2canvas 生成图片失败
这个项目里有个生成图片的功能，需要保存用户操作过的一些东西，其他设备都正常，唯独 ios 最新版的系统有问题
debug 了一波后发现，这他瞄的根本没有执行这个函数，找一半天也没找到为什么，后来看 issues 发现有人说把版本换成 rc.4 的就可以了
issues：github.com/niklasvh/ht…

### emoji 表情转码
项目测试的时候，发现 textarea 标签里可以输入表情，紧接着，接口就报错了，一查看，虽然支持输入表情，但是不会自动对表情转码，所以提交接口的时候报错了，下面分享个 emoji 表情转字符的方法：
```
// 表情转字符
utf16toEntities(str) {
  var patt = /[\ud800-\udbff][\udc00-\udfff]/g // 检测utf16字符正则
  str = str.replace(patt, function(char) {
    var H, L, code
    if (char.length === 2) {
      H = char.charCodeAt(0) // 取出高位
      L = char.charCodeAt(1) // 取出低位
      code = (H - 0xd800) * 0x400 + 0x10000 + L - 0xdc00 // 转换算法
      return '&#' + code + ';'
    } else {
      return char
    }
  })
  return str
}
```

### iOS 的 webview 中 滑动不流畅

#### 解决方案

1.在滚动容器上增加滚动 touch 方法

将-webkit-overflow-scrolling 值设置为 touch
```
.wrapper {
    -webkit-overflow-scrolling: touch;
}
```
设置滚动条隐藏： .container
```
::-webkit-scrollbar {display: none;}
```
可能会导致使用position:fixed; 固定定位的元素，随着页面一起滚动

2.设置 overflow

设置外部 overflow 为 hidden,设置内容元素 overflow 为 auto。内部元素超出 body 即产生滚动，超出的部分 body 隐藏。
```
body {
    overflow-y: hidden;
}
.wrapper {
    overflow-y: auto;
}
```
> 两者结合使用更佳！

### iOS 上拉边界下拉出现白色空白

#### 表现

手指按住屏幕下拉，屏幕顶部会多出一块白色区域。手指按住屏幕上拉，底部多出一块白色区域。

#### 产生原因

在 iOS 中，手指按住屏幕上下拖动，会触发 touchmove 事件。这个事件触发的对象是整个 webview 容器，容器自然会被拖动，剩下的部分会成空白。

#### 解决方案

1. 监听事件禁止滑动

移动端触摸事件有三个，分别定义为
```
1. touchstart ：手指放在一个DOM元素上。
2. touchmove ：手指拖曳一个DOM元素。
3. touchend ：手指从一个DOM元素上移开。
```

touchmove 事件的速度是可以实现定义的，取决于硬件性能和其他实现细节

preventDefault 方法，阻止同一触点上所有默认行为，比如滚动。

由此我们找到解决方案，通过监听 touchmove，让需要滑动的地方滑动，不需要滑动的地方禁止滑动。

值得注意的是我们要过滤掉具有滚动容器的元素
##### 实现如下：
```
document.body.addEventListener('touchmove', function(e) {
    if(e._isScroller) return;
    // 阻止默认事件
    e.preventDefault();
}, {
    passive: false
})

```
### click 点击事件延时与穿透

#### 表现

监听元素 click 事件，点击元素触发时间延迟约 300ms。

点击蒙层，蒙层消失后，下层元素点击触发。

#### 产生原因

为什么会产生 click 延时？

iOS 中的 safari，为了实现双击缩放操作，在单击 300ms 之后，如果未进行第二次点击，则执行 click 单击操作。也就是说来判断用户行为是否为双击产生的。但是，在 App 中，无论是否需要双击缩放这种行为，click 单击都会产生 300ms 延迟。

#### 为什么会产生 click 点击穿透？

双层元素叠加时，在上层元素上绑定 touch 事件，下层元素绑定 click 事件。由于 click 发生在 touch 之后，点击上层元素，元素消失，下层元素会触发 click 事件，由此产生了点击穿透的效果。

#### 原理与解决方案

解决方案一：使用 touchstart 替换 click

前面已经介绍了，移动设备不仅支持点击，还支持几个触摸事件。那么我们现在基本思路就是用 touch 事件代替click 事件。

将 click 替换成 touchstart 不仅解决了 click 事件都延时问题，还解决了穿透问题。因为穿透问题是在 touch 和 click 混用时产生。

##### 在原生中使用
```
el.addEventListener("touchstart", () => { console.log("ok"); }, false);
```
##### 在 vue 中使用
```
<button @touchstart="handleTouchstart()">点击</button>
```
开源解决方案中，也是既提供了 click 事件，又提供了touchstart 事件。如 vant 中的 button 组件


那么，是否可以将 click 事件全部替换成 touchstart 呢？为什么开源框架还会给出 click 事件呢？

我们想象一种情景，同时需要点击和滑动的场景下。如果将 click 替换成 touchstart 会怎样？

事件触发顺序: touchstart, touchmove, touchend, click。
很容易想象，在我需要touchmove滑动时候，优先触发了touchstart的点击事件，是不是已经产生了冲突呢？

所以呢，在具有滚动的情况下，还是建议使用 click 处理。

在接下来的fastclick开源库中也做了如下处理。针对 touchstart 和 touchend，截取了部分源码。
```
scrollParent = targetElement.fastClickScrollParent;
if (scrollParent && scrollParent.fastClickLastScrollTop !== scrollParent.scrollTop) {
return true;
}
```
主要目的就是，在使用 touchstart 合成 click 事件时，保证其不在滚动的父元素之下。

#### 解决方案二：使用 fastclick 库

使用 npm/yarn 安装后使用
```
import FastClick from 'fastclick';

FastClick.attach(document.body, options);
```
同样，使用fastclick库后，click 延时和穿透问题都没了

按照我的惯例，只要涉及开源库，那么我们一定要去了解它实现的原理。主要是将现有的原生事件集合封装合成一个兼容性较强的事件集合。

fastclick源码 核心代码不长， 1000 行不到。有兴趣可以了解一下!

### 软键盘将页面顶起来、收起未回落问题

#### 表现
Android 手机中，点击 input 框时，键盘弹出，将页面顶起来，导致页面样式错乱。

移开焦点时，键盘收起，键盘区域空白，未回落。

#### 产生原因

我们在app 布局中会有个固定的底部。安卓一些版本中，输入弹窗出来，会将解压 absolute 和 fixed 定位的元素。导致可视区域变小，布局错乱。

#### 原理与解决方案

软键盘将页面顶起来的解决方案，主要是通过监听页面高度变化，强制恢复成弹出前的高度。
```
// 记录原有的视口高度
const originalHeight = document.body.clientHeight || document.documentElement.clientHeight;

window.onresize = function(){
  var resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
  if(resizeHeight < originalHeight ){
    // 恢复内容区域高度
    // const container = document.getElementById("container")
    // 例如 container.style.height = originalHeight;
  }
}
```
键盘不能回落问题出现在 iOS 12+ 和 wechat 6.7.4+ 中，而在微信 H5 开发中是比较常见的 Bug。

#### 兼容原理，1.判断版本类型 2.更改滚动的可视区域
```
const isWechat = window.navigator.userAgent.match(/MicroMessenger\/([\d\.]+)/i);
if (!isWechat) return;
const wechatVersion = wechatInfo[1];
const version = (navigator.appVersion).match(/OS (\d+)_(\d+)_?(\d+)?/);
 
 // 如果设备类型为iOS 12+ 和wechat 6.7.4+，恢复成原来的视口
if (+wechatVersion.replace(/\./g, '') >= 674 && +version[1] >= 12) {
  window.scrollTo(0, Math.max(document.body.clientHeight, document.documentElement.clientHeight));
}
```
> window.scrollTo(x-coord, y-coord)，其中window.scrollTo(0, clientHeight)恢复成原来的视口

### iPhone X系列安全区域适配问题

#### 表现
头部刘海两侧区域或者底部区域，出现刘海遮挡文字，或者呈现黑底或白底空白区域。

#### 产生原因

iPhone X 以及它以上的系列，都采用刘海屏设计和全面屏手势。头部、底部、侧边都需要做特殊处理。才能适配 iPhone X 的特殊情况。

#### 解决方案

设置安全区域，填充危险区域，危险区域不做操作和内容展示。

>危险区域指头部不规则区域，底部横条区域，左右触发区域。
具体操作为：viewport-fit meta 标签设置为 cover，获取所有区域填充。判断设备是否属于 iPhone X，给头部底部增加适配层

#### viewport-fit 有 3 个值分别为：
* auto：此值不影响初始布局视图端口，并且整个web页面都是可查看的。
* contain：视图端口按比例缩放，以适合显示内嵌的最大矩形。
* cover：视图端口被缩放以填充设备显示。强烈建议使用 safe area inset 变量，以确保重要内容不会出现在显示之外

#### 设置 viewport-fit 为 cover
```
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
```
增加适配层

使用 safe area inset 变量
```
/* 适配 iPhone X 顶部填充*/
@supports (top: env(safe-area-inset-top)){
  body,
  .header{
      padding-top: constant(safe-area-inset-top, 40px);
      padding-top: env(safe-area-inset-top, 40px);
      padding-top: var(safe-area-inset-top, 40px);
  }
}
/* 判断iPhoneX 将 footer 的 padding-bottom 填充到最底部 */
@supports (bottom: env(safe-area-inset-bottom)){
    body,
    .footer{
        padding-bottom: constant(safe-area-inset-bottom, 20px);
        padding-bottom: env(safe-area-inset-bottom, 20px);
        padding-top: var(safe-area-inset-bottom, 20px);
    }
}
```

>safe-area-inset-top, safe-area-inset-right, safe-area-inset-bottom, safe-area-inset-left safe-area-inset-*由四个定义了视口边缘内矩形的 top, right, bottom 和 left 的环境变量组成，这样可以安全地放入内容，而不会有被非矩形的显示切断的风险。对于矩形视口，例如普通的笔记本电脑显示器，其值等于零。对于非矩形显示器（如圆形表盘，iPhoneX 屏幕），在用户代理设置的四个值形成的矩形内，所有内容均可见。

其中 env() 用法为 env( <custom-ident> , <declaration-value>? )，第一个参数为自定义的区域，第二个为备用值。

其中 var() 用法为 var( <custom-property-name> , <declaration-value>? )，作用是在 env() 不生效的情况下，给出一个备用值。

constant（） 被 css 2017-2018 年为草稿阶段，是否已被标准化未知。而其他iOS 浏览器版本中是否有此函数未知，作为兼容处理而添加进去。

### 页面生成为图片和二维码问题
#### 表现
在工作中有需要将页面生成图片或者二维码的需求。可能我们第一想到的，交给后端来生成更简单。但是这样我们需要把页面代码全部传给后端，网络性能消耗太大。

#### 解决方案

#### 生成二维码

使用 QRCode 生成二维码
```
import QRCode from 'qrcode';
// 使用 async 生成图片
const options = {};
const url = window.location.href;
async url => {
  try {
    console.log(await QRCode.toDataURL(url, options))
  } catch (err) {
    console.error(err);
  }
}
```
将 await QRCode.toDataURL(url, options) 赋值给 图片 url 即可
#### 生成图片

主要是使用 htmlToCanvas 生成 canvas 画布
```
import html2canvas from 'html2canvas';

html2canvas(document.body).then(function(canvas) {
    document.body.appendChild(canvas);
});
```
但是不单单在此处就完了，由于是 canvas 的原因。移动端生成出来的图片比较模糊。

我们使用一个新的 canvas 方法多倍生成，放入一倍容器里面，达到更加清晰的效果，通过超链接下载图片 下载文件简单实现，更完整的实现方式之后更新
```
const scaleSize = 2;
const newCanvas = document.createElement("canvas");
const target = document.querySelector('div');
const width = parseInt(window.getComputedStyle(target).width);
const height = parseInt(window.getComputedStyle(target).height);
newCanvas.width = width * scaleSize;
newCanvas.height = widthh * scaleSize;
newCanvas.style.width = width + "px";
newCanvas.style.height =width + "px";
const context = newCanvas.getContext("2d");
context.scale(scaleSize, scaleSize);
html2canvas(document.querySelector('.demo'), { canvas: newCanvas }).then(function(canvas) {
  // 简单的通过超链接设置下载功能
  document.querySelector(".btn").setAttribute('href', canvas.toDataURL());
}
```
>根据需要设置 scaleSize 大小

### H5 调试相关方案策略
#### 表现
调试代码一般就是为了查看数据和定位 bug。分为两种场景，一种是开发和测试时调试，一种是生产环境上调试。

>为什么有生产环境上调试呢？有些时候测试环境上没法复现这个 bug，测试环境和生产环境不一致，此时就需要紧急生产调试。

在 PC 端开发时，我们可以直接掉出控制台，使用浏览器提供的工具操作devtools或者查看日志。但是在 App 内部我们怎么做呢？

#### 原理与解决方案

#### 1. vconsole 控制台插件

使用方法也很简单
```
import Vconsole from 'vconsole'

new Vconsole()
```
有兴趣看看它实现的基本原理，我们关注的点应该在 vsconsole 如何打印出我们所有 log 的 腾讯开源vconsole

上述方法仅用于开发和测试。生产环境中不允许出现，所以，使用时需要对环境进行判断。
```
import Vconsole from 'vconsole'
if (process.env.NODE_ENV !== 'production') {
    new Vconsole()
}
```
#### 2. 代理 + spy-debugger

操作稍微有点麻烦，不过我会详细写出，大致分为 4 个步骤

#### 安装插件(全局安装)
```
sudo npm install spy-debugger -g
```
#### 手机与电脑置于同一 wifi 下，手机设置代理
设置手机的 HTTP 代理，代理 IP 地址设置为 PC 的 IP 地址，端口为spy-debugger的启动端口

> spy-debugger 默认端口：9888

> Android ：设置 - WLAN - 长按选中网络 - 修改网络 - 高级 - 代理设置 - 手动

> IOS ：设置 - Wi-Fi - 选中网络, 点击感叹号, HTTP 代理手动

#### 手机打开浏览器或者 app 中 H5 页面
#### 打开桌面日志网站进行调试，点击 npm 控制台监听地址。查看抓包和 H5 页面结构
这种方式可以调试生成环境的页面，不需要修改代码，可以应付大多数调试需求

