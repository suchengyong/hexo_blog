---
title: 利用box-shadow制作loading图
date: 2018-02-12 10:03:58
tags: css3,loading
categories: 前端开发 
thumbnail: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1518407881002&di=f965533c99cf3f67d2b4790351043774&imgtype=0&src=http%3A%2F%2Fp1.yokacdn.com%2Fpic%2Fpeople%2Fspotlight%2F2014%2FU424P41T8D368844F256DT20140904175727.jpg
---

我们见过很多利用css3做的loading图，像下面这种应该是很常见的。通常制作这种loading，我们会一个标签对应一个圆，八个圆就要八个标签。但是这种做法很浪费资源。我们可以只用一个标签，然后利用box—shadow来制作一个loading图。

* 首先，需要一个标签：

``` bash
<div class="loading"></div>

```

* 然后是css：

``` bash
$fontSize: 30px;
$radius: 2em;
.loading {
font-size: $fontSize;
width: 1em;
height: 1em;
border-radius: 50%;
margin: 100px auto;
box-shadow: 0 -2em rgba(255, 0, 0, 1), 
1.414em -1.414em rgba(255, 0, 0, 0.875), 
2em 0 rgba(255, 0, 0, 0.75), 
1.414em 1.414em rgba(255, 0, 0, 0.625), 
0 2em rgba(255, 0, 0, 0.5), 
-1.414em 1.414em rgba(255, 0, 0, 0.375), 
-2em 0 rgba(255, 0, 0, 0.25), 
-1.414em -1.414em rgba(255, 0, 0, 0.125);
}

```

* 单位用的是em，这样如果要修改圆的大小，就只需要修改font-size就可以了，如果用px的话，一旦需要修改大小，那么涉及单位的就都要修改了。

``` bash
	box-shadow: 0 -2em rgba(255, 0, 0, 1), 
	1.414em -1.414em rgba(255, 0, 0, 0.875), 
	2em 0 rgba(255, 0, 0, 0.75), 
	1.414em 1.414em rgba(255, 0, 0, 0.625), 
	0 2em rgba(255, 0, 0, 0.5), 
	-1.414em 1.414em rgba(255, 0, 0, 0.375), 
	-2em 0 rgba(255, 0, 0, 0.25), 
	-1.414em -1.414em rgba(255, 0, 0, 0.125);
```

* 需要用到多重阴影，每一个阴影就是一个圆了。设置它们的坐标需要用到三角函数。先要设定一个半径(如2em)，然后360度平均分成8份，每份是45度，利用三角函数就可以计算阴影的偏移位置了。还有就是圆的颜色渐变，就是设置它们的不同透明度。

* 上面做出来的是静态的，还需要让它动起来:

``` bash
$fontSize: 30px;
$radius: 2em;

.loading {
  font-size: $fontSize;
  width: 1em;
  height: 1em;
  border-radius: 50%;
  margin: 100px auto;
  box-shadow: 0 -2em rgba(255, 0, 0, 1), 
  1.414em -1.414em rgba(255, 0, 0, 0.875), 
  2em 0 rgba(255, 0, 0, 0.75), 
  1.414em 1.414em rgba(255, 0, 0, 0.625),
   0 2em rgba(255, 0, 0, 0.5), 
   -1.414em 1.414em rgba(255, 0, 0, 0.375), 
   -2em 0 rgba(255, 0, 0, 0.25), 
   -1.414em -1.414em rgba(255, 0, 0, 0.125);

  animation: rotate 1s infinite forwards steps(8, end);
}

@keyframes rotate {
   100% {
       transform: rotate(360deg);
   }
}
```

* steps()这个函数可以让动画分步进行，而不是连贯性的。

* 好了，上面就是全部的代码了，很少。上面的box-shadow其实我是手算的，本来我想利用sass的for循环计算的。