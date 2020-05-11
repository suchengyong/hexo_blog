---
title: rem是如何实现自适应布局的
date: 2018-02-26 10:03:58
tags: H5,rem
categories: 移动端自适应 
thumbnail: http://pic1.win4000.com/wallpaper/1/5937d6f62c251.jpg
---

* 摘要：rem是相对于根元素html，这样就意味着，我们只需要在根元素确定一个px字号，则可以来算出元素的宽高。本文讲的是如何使用rem实现自适应。

* rem这是个低调的css单位，近一两年开始崭露头角，有许多同学对rem的评价不一，有的在尝试使用，有的在使用过程中遇到坑就弃用了。但是我对rem综合评价是用来做web app它绝对是最合适的人选之一。

### rem是什么？
 * rem（font size of the root element）是指相对于根元素的字体大小的单位。简单的说它就是一个相对单位。看到rem大家一定会想起em单位，em（font size of the element）是指相对于父元素的字体大小的单位。它们之间其实很相似，只不过一个计算的规则是依赖根元素一个是依赖父元素计算。

### rem能等比例适配所有屏幕

* 接下来讲下rem是如何工作的。上面说过rem是通过根元素进行适配的，网页中的根元素指的是html我们通过设置html的字体大小就可以控制rem的大小。举个例子：

``` bash

html{
    font-size:20px;
}
.btn {
    width: 6rem;
    height: 3rem;
    line-height: 3rem;
    font-size: 1.2rem;
    display: inline-block;
    background: #06c;
    color: #fff;
    border-radius: .5rem;
    text-decoration: none;
    text-align: center;    
}

```

* Demo 上面代码结果按钮大小如下图：

![图片](http://img.caibaojian.com/uploads/2015/12/1418899506.jpeg)

* 我把html设置成10px是为了方便我们计算，为什么6rem等于60px。如果这个时候我们的.btn的样式不变，我们再改变html的font-size的值，看看按钮发生上面变化:

``` 
html{
    font-size:40px;
}

```

* 结果按钮的大小发生了变化，大小如下：

![图片](http://img.caibaojian.com/uploads/2015/12/1418898055.jpeg)

* 上面的width，height变成了上面结果的两倍，我们只改变了html的font-size，但.btn样式的width,height的rem设置的属性不变的情况下就改变了按钮在web中的大小。

* 其实从上面两个案例中我们就可以计算出1px多少rem:

* 第一个例子：

* 120px = 6rem * 20px(根元素设置大值)

* 第二个例子：

* 240px = 6rem * 40px(根元素设置大值)

* 推算出：

* 10px  = 1rem 在根元素（font-size = 10px的时候）；

* 20px  = 1rem 在根元素（font-size = 20px的时候）；

* 40px  = 1rem 在根元素（font-size = 40px的时候）；

* 我是通过JS去动态计算根元素的font-size，这样的好处是所有设备分辨率都能兼容适配，淘宝首页目前就是用的JS计算。但其实不用JS我们也可以做适配，一般我们在做webapp都会先统计自己网站有哪些主流的屏幕设备，然后去针对那些设备去做media query设置也可以实现适配，例如下面这样：

```
html {
    font-size : 20px;
}
@media only screen and (min-width: 401px){
    html {
        font-size: 25px !important;
    }
}
@media only screen and (min-width: 428px){
    html {
        font-size: 26.75px !important;
    }
}
@media only screen and (min-width: 481px){
    html {
        font-size: 30px !important; 
    }
}
@media only screen and (min-width: 569px){
    html {
        font-size: 35px !important; 
    }
}
@media only screen and (min-width: 641px){
    html {
        font-size: 40px !important; 
    }
}

```

### REM自适应JS

```
//designWidth:设计稿的实际宽度值，需要根据实际设置
//maxWidth:制作稿的最大宽度值，需要根据实际设置
//这段js的最后面有两个参数记得要设置，一个为设计稿实际宽度，一个为制作稿最大宽度，例如设计稿为750，最大宽度为750，则为(750,750)
;(function(designWidth, maxWidth) {
	var doc = document,
	win = window,
	docEl = doc.documentElement,
	remStyle = document.createElement("style"),
	tid;

	function refreshRem() {
		var width = docEl.getBoundingClientRect().width;
		maxWidth = maxWidth || 540;
		width>maxWidth && (width=maxWidth);
		var rem = width * 100 / designWidth;
		remStyle.innerHTML = 'html{font-size:' + rem + 'px;}';
	}

	if (docEl.firstElementChild) {
		docEl.firstElementChild.appendChild(remStyle);
	} else {
		var wrap = doc.createElement("div");
		wrap.appendChild(remStyle);
		doc.write(wrap.innerHTML);
		wrap = null;
	}
	//要等 wiewport 设置好后才能执行 refreshRem，不然 refreshRem 会执行2次；
	refreshRem();

	win.addEventListener("resize", function() {
		clearTimeout(tid); //防止执行两次
		tid = setTimeout(refreshRem, 300);
	}, false);

	win.addEventListener("pageshow", function(e) {
		if (e.persisted) { // 浏览器后退的时候重新计算
			clearTimeout(tid);
			tid = setTimeout(refreshRem, 300);
		}
	}, false);

	if (doc.readyState === "complete") {
		doc.body.style.fontSize = "16px";
	} else {
		doc.addEventListener("DOMContentLoaded", function(e) {
			doc.body.style.fontSize = "16px";
		}, false);
	}
})(750, 750);

```