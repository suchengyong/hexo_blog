---
title: 工作实用总结
date: 2018-03-08 15:03:58
tags: js
categories: 前端开发 
thumbnail: http://pic26.photophoto.cn/20130120/0036036864029026_b.jpg
---

### 1.生成随机字符串

```
function randomStr() {
    return Math.random().toString(36).substring(2);
}

```

### 2.判断一个实例是否为 数组的几种方法

```
	1.console.log(arr instanceof Array)
	2.console.log(Array.isArray(arr))
	3.console.log(arr.constructor === Array)
	4.console.log(Object.prototype.toString.call(arr) === '[Object Array]')
	5.console.log(Object.prototype.toString.call(function(){}))
	
```

### 判断滚动条是向上还是向下

```
$(document).ready(function(){
    var position=0,top=0;

    $(window).scroll(function(e){
            position = $(this).scrollTop();

            if(top<=position){//下滚
                .......
            }

            else{//上滚
                .......
            }
            setTimeout(function(){top = position;},0);
    });
});

```

### 利用padding制作响应式正方形

* 有些情况下、我们要做响应式的布局、来应对多设备、或者多分辨率

* 来个简单的需求

* 需求：每行三个正方形、依次排开、做列表、流式

* 最简单的需求出来了、那我们直接开搞

* 首先、我们要了解到、padding的百分比是基于谁的

* 这里敲了几下黑白、这道送分题、相信很多人心中已经知道了答案

* 那就是 基于父元素的宽度

* 注意、是宽度、也就是说、利用这个特性、我们父子元素保持宽度一致、

* 子元素的padding是100%、就可以达到效果


```
<style>
  *{
    padding: 0;
    margin: 0;
  }
  .list{
    width: 100%;
    padding: 5px 16px;
    box-sizing: border-box;
  }
  /*用padding的方式*/
  .square-box{
    margin: 5px auto;
    padding: 0 5px;
    box-sizing: border-box;
    float:left;
    width:calc(100%/3)
  }
  .square{
    width: calc(100%);
    padding-bottom: 100%;
    background: red;
    box-sizing: border-box;
  }
  /*用calc的方式*/
  /*.square-box{
    margin: 5px auto;
    box-sizing: border-box;
    float:left;
    width:calc(100%/3)
  }
  .square{
    margin: 0 auto;
    width: calc(100% - 16px * 2);
    padding-bottom: calc(100% - 16px * 2);
    background: red;
    box-sizing: border-box;
  }*/
</style>

<div class="list">
  <div class="square-box">
    <div class="square"></div>
  </div>
  <div class="square-box">
    <div class="square"></div>
  </div>
  <div class="square-box">
    <div class="square"></div>
  </div>
  <div class="square-box">
    <div class="square"></div>
  </div>
  <div class="square-box">
    <div class="square"></div>
  </div>
</div>
<div style="clear:both"></div>
<div class="text-center">试着放大缩小窗口</div>

```

### 制作两端对齐响应式布局

* 制作这个布局主要用到了text-align: justify

* 大家应该知道、这是文字两端对齐的css样式

* 但是注意了（敲敲黑板） 没有换行的文本不会生效

* 那么这里有一个投机的办法、就是用:after的content属性为它创造换行的环境

* 用到这个、我还想起了一个和他很像的属性text-align-last: justify

* 但是text-align-last 只有IE和Firefox支持

* 那么就不考虑、喜欢尝试的同学可以下去自己试一下

* 那么只需要把元素设为inline-block并且加上after伪类就可以了


```
<style>
  .test{
    text-align: justify;
  }
  .test .box{
    display: inline-block;
    width: 100px;
    height: 100px;
    background: red;
  }
  .test:after {width: 100%;height: 0;margin: 0;display: inline-block;overflow: hidden;content: '';}
</style>

<div class="test">
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
</div>
<div class="text-center">试着放大缩小窗口</div>

```

### 判断浏览器为PC还是移动端

* 判断userAgent、根据正则、找出对应的设备或者浏览器

```
<script type="text/javascript">
    function browserRedirect() {
        var sUserAgent = navigator.userAgent.toLowerCase();
        var bIsIpad = sUserAgent.match(/ipad/i) == "ipad";
        var bIsIphoneOs = sUserAgent.match(/iphone os/i) == "iphone os";
        var bIsMidp = sUserAgent.match(/midp/i) == "midp";
        var bIsUc7 = sUserAgent.match(/rv:1.2.3.4/i) == "rv:1.2.3.4";
        var bIsUc = sUserAgent.match(/ucweb/i) == "ucweb";
        var bIsAndroid = sUserAgent.match(/android/i) == "android";
        var bIsCE = sUserAgent.match(/windows ce/i) == "windows ce";
        var bIsWM = sUserAgent.match(/windows mobile/i) == "windows mobile";
        document.writeln("您的浏览设备为：");
        if (bIsIpad || bIsIphoneOs || bIsMidp || bIsUc7 || bIsUc || bIsAndroid || bIsCE || bIsWM) {
            document.writeln("phone");
        } else {
            document.writeln("pc");
        }
    }

    browserRedirect();
</script>

```

### Js 将字符串对象转换成对象的三种方法

* 如果某天从后端返回的对象莫名其妙的变成了字符串、需要前端处理成对象、再去取值的话,那就需要用到、字符串强转对象了

* 首先我们要转换的东西可能是这个样子：

> '{"name":"test"}'

* JSON的标准写法应该是KEY和VALUE都带引号的

* 我们要将这样的字符串转为对象、有三种方法可以参考使用

* 第一种可能是大家用的非常之多的、用JSON对象下面的方法.parse()

* 写起来可能是这样的：

```
var jsonData = '{"name":"test"}';
var obj = JSON.parse(jsonData)
console.log(obj.name);//"test"
JSON.parse()和JSON.stringify() 是我用的比较多的方法

```
* 第二种是利用JS的eval()函数

```
var jsonData = '{"name":"test"}';
var obj= eval('('+ jsonData +')');
console.log(obj.name);//"test"

```

* 这里要解释一下、为什么要再eval里面头尾加入圆括号、因为eval()是以函数语句解析的、那么其中的{}大括号会用作标记为作用域、或者方法体类似的概念、我们想要把他解析成对象就不会成功了

> eval('{}')
> eval('({})')//返回的是一个obj
> 第三种是用Function对象

```
var jsonData = '{"name":"test"}';
var obj = new Function('return ' + jsonData)();
console.log(obj.name);//"test"

```

* 原理比较类似第二种、将return 拼接字符串对象、放入function

* 执行的时候、由于将字符串解析成了语句、所以return出去的是一个对象

* 以上就是今天所要说的三种方法、欢迎补充！


### 水平出现滚动条布局

```
  <style>
    ul{
      width: 50%;
      height: 100px;
      border: 1px solid #ddd;
      padding: 10px;
      box-sizing: border-box;
      white-space: nowrap;
      overflow: hidden;
      overflow-x: scroll; /* 1 */
      -webkit-backface-visibility: hidden;
      -webkit-perspective: 1000;
      -webkit-overflow-scrolling: touch; /* 2 */
      text-align: justify; /* 3 */
      &::-webkit-scrollbar {
          display: none;
      }
  }
  ul li{
    display: inline-block;
    width: 100px;
    background-color: red;
    margin-right: 20px;
    height: 80px;
  }
  
  </style>
 <ul>
      <li>1</li>
      <li>2</li>
      <li>3</li>
      <li>4</li>
      <li>5</li>
      <li>6</li>
      <li>7</li>
      <li>8</li>
      <li>9</li>
      <li>10</li>
      <li>11</li>
      <li>12</li>
      <li>13</li>
      <li>14</li>
      <li>15</li>
      <li>16</li>
      <li>17</li>
      <li>18</li>
    </ul>

```

### 百度语音阅读接口

> http://fanyi.baidu.com/gettts?lan=zh&text="哈哈哈"&spd=5&source=web

### gitHub创建一个仓库时选择：

>1.勾选Initialize this repository with a README
>2.Add.gitignore:选择node
>3.Add a license:选择MIT License

### 导航添加鼠标移动添加下划线效果

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<title>Examples</title>
<meta name="description" content="">
<meta name="keywords" content="">
<style>
  ul{
  
    width: 800px;
    margin: 0 auto;
    height: 50px;
    font-size: 0;
  }
  li{
    list-style: none;
    position: relative;
    display: inline-block;
    line-height: 50px;
    padding: 0 10px;
    box-sizing: border-box;
    font-size: 18px;
    height: 50px;
    border-bottom:0px solid #000;
  }
  li::before{
    content: '';
    position: absolute;
    left: 100%;
    top: 0;
    width: 0;
    height: 100%;
    border-bottom:3px solid red;
    transition: 0.2s all linear;
  }
  li:hover::before{
    width: 100%;
    left:0;
  }
  li:hover ~ li:before{
    left:0;
  }
</style>
</head>
<body>
    <ul>
      <li>哈哈哈</li>
      <li>行搜索</li>
      <li>很好</li>
      <li>不好吗密码</li>
      <li>很棒部</li>
    </ul>
</body>
</html>

```