---
title: Vue使用Canvas绘制图片、矩形、线条、文字，下载图片
date: 2020-05-11 10:30:58
tags: vue,canvas
categories: 前端开发 
thumbnail: http://img4.imgtn.bdimg.com/it/u=2846291658,2120653646&fm=26&gp=0.jpg
---
## 1、 前言

#### 1.1 、业务场景
图片储存在后台中，根据图片的地址，在vue页面中，查看图片，并根据坐标标注指定区域。
由于浏览器的机制，使用window.location.href下载图片时，并不会保存到本地，会在浏览器打开。
### 2、 实现原理

#### 2.1、 绘制画布
```
<el-dialog
    title="查看图片"
    :visible.sync="dialogJPG"
    append-to-body>
    <canvas id="mycanvas" width="940" height="570"></canvas>
</el-dialog>
```
这里为了交互体验，使用了element-ui的弹窗方式。将canvas画布放到了弹窗中。
为了突出画布效果可以在css中设置一个边框。
```
#mycanvas {
    border: 1px solid rgb(199, 198, 198);
}
```
#### 2.2 绘制图片
```
//  imageUrl为后台提供图片地址
doDraw(imageUrl){
    //  获取canvas
    var canvas = document.getElementById("mycanvas")
    //  由于弹窗，确保已获取到
    var a = setInterval(() =>{
        //  重复获取
        canvas = document.getElementById("mycanvas")
        if(!canvas){
          return false
        } else {
            clearInterval(a)
            //  可以理解为一个画笔，可画路径、矩形、文字、图像
            var context = canvas.getContext('2d')
            var img = new Image()
            img.src = imageUrl
            //  加载图片
            img.onload = function(){
                if(img.complete){
                    //  根据图像重新设定了canvas的长宽
                    canvas.setAttribute("width",img.width)
                    canvas.setAttribute("height",img.height)
                    //  绘制图片
                    context.drawImage(img,0,0,img.width,img.height)
                }
            }
        }
    },1)
},
```
context.drawImage()方法的参数介绍，可参照 W3school
#### 2.3 、绘制矩形
```
context.strokeStyle = "red"
context.lineWidth = 3;
context.strokeRect(x, y, width, height)
```
context 同上面的定义
strokeStyle 矩形颜色
lineWidth 矩形边框宽度
x,y,width,height 矩形位置加长宽
#### 2.4 、绘制线条
```
context.moveTo(x1,y1)
context.lineTo(x2,y2)
context.strokeStyle = "red"
context.lineWidth = 3;
context.stroke()
```
(x1,y1) (x2,y2) 线条的起点和终点坐标
strokeStyle lineWidth 线条的样式
#### 2.5 、绘制文字
```
context.font = "26px Arial bolder"
context.fillStyle = 'red'
context.fillText(text,x,y)
```
font fillStyle 文字样式
text 文字内容
x,y 文字显示坐标
#### 2.6 、下载图片
```
// 图片地址和图片名称
downIamge (imgsrc, name) {
    let image = new Image()
    image.setAttribute('crossOrigin', 'anonymous')
    image.onload = function () {
        let canvas = document.createElement('canvas')
        canvas.width = image.width
        canvas.height = image.height
        let context = canvas.getContext('2d')
        context.drawImage(image, 0, 0, image.width, image.height)
        let url = canvas.toDataURL('image/jpg')
        let a = document.createElement('a')
        let event = new MouseEvent('click')
        a.download = name
        a.href = url
        a.dispatchEvent(event)
    }
    image.src = imgsrc
},
```