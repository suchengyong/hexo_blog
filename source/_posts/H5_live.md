---
title: H5 直播的疯狂点赞动画是如何实现的
date: 2020-05-11 10:30:58
tags: htnl5
categories: 前端开发 
thumbnail: http://img4.imgtn.bdimg.com/it/u=151388545,4107558105&fm=26&gp=0.jpg
---
#### 直播有一个很重要的互动：点赞。

为了烘托直播间的氛围，直播相对于普通视频或者文本内容，点赞通常有两个特殊需求：

点赞动作无限次，引导用户疯狂点赞
直播间的所有疯狂点赞，都需要在所有用户界面都动画展现出来(广播用户使用websocket消息)
文章链接 https://mp.weixin.qq.com/s/E8MXKfNPn3nkVjGGXNESAA

两种方式渲染点赞动画都已经完成，完整源码，源码戳这里 https://github.com/antiter/praise-animation 。

这里还可以体验线上点赞动画，戳这里： https://wqs.jd.com/pglive/index.html