---
title: 判断浏览器为PC还是移动端
date: 2018-02-26 15:03:58
tags: css,js
categories: 前端开发
thumbnail: http://img2.imgtn.bdimg.com/it/u=3313838802,2768404782&fm=26&gp=0.jpg
---

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

### 判断滚动条是向上还是向下

* 在写wap中遇到了这个需求、把代码记录下来吧

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

### 制作两端对齐响应式布局

>制作这个布局主要用到了text-align: justify

>大家应该知道、这是文字两端对齐的css样式

>但是注意了（敲敲黑板） 没有换行的文本不会生效

>那么这里有一个投机的办法、就是用:after的content属性为它创造换行的环境

>用到这个、我还想起了一个和他很像的属性text-align-last: justify

>但是text-align-last 只有IE和Firefox支持

>那么就不考虑、喜欢尝试的同学可以下去自己试一下

>那么只需要把元素设为inline-block并且加上after伪类就可以了

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