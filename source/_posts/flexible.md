---
title: rem自适应布局-移动端自适应必备:flexible.js
date: 2018-02-26 15:03:58
tags: flexible
categories: 移动端自适应
thumbnail: https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=960958117,1709091495&fm=27&gp=0.jpg
---

* 由于移动端特殊性，本文讲的是如何使用rem实现自适应，或叫rem响应式布局，通过使用一个脚本就可以rem自适应，不用再为各种设备宽度不同而烦恼如何实现自适应的问题。

* rem是相对于根元素<html>，这样就意味着，我们只需要在根元素确定一个px字号，则可以来算出元素的宽高。1rem=16px(浏览器html的像素，可以设定这个基准值），假如浏览器的html设为64px，则下面的元素则1rem=64px来运算。

* 阿里团队开源的一个库。使用flexible.js轻松搞定各种不同的移动端设备兼容自适应问题。

### 实现方法：

* 通过js来调整html的字体大小，而在页面中的制作稿则统一使用rem这个单位来制作。关键代码如下：

```
;(function(win, lib) {
    var doc = win.document;
    var docEl = doc.documentElement;
    var metaEl = doc.querySelector('meta[name="viewport"]');
    var flexibleEl = doc.querySelector('meta[name="flexible"]');
    var dpr = 0;
    var scale = 0;
    var tid;
    var flexible = lib.flexible || (lib.flexible = {});
    
    if (metaEl) {
        console.warn('将根据已有的meta标签来设置缩放比例');
        var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
        if (match) {
            scale = parseFloat(match[1]);
            dpr = parseInt(1 / scale);
        }
    } else if (flexibleEl) {
        var content = flexibleEl.getAttribute('content');
        if (content) {
            var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
            var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);
            if (initialDpr) {
                dpr = parseFloat(initialDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));    
            }
            if (maximumDpr) {
                dpr = parseFloat(maximumDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));    
            }
        }
    }

    if (!dpr && !scale) {
        var isAndroid = win.navigator.appVersion.match(/android/gi);
        var isIPhone = win.navigator.appVersion.match(/iphone/gi);
        var devicePixelRatio = win.devicePixelRatio;
        if (isIPhone) {
            // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
            if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {                
                dpr = 3;
            } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
                dpr = 2;
            } else {
                dpr = 1;
            }
        } else {
            // 其他设备下，仍旧使用1倍的方案
            dpr = 1;
        }
        scale = 1 / dpr;
    }

    docEl.setAttribute('data-dpr', dpr);
    if (!metaEl) {
        metaEl = doc.createElement('meta');
        metaEl.setAttribute('name', 'viewport');
        metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
        if (docEl.firstElementChild) {
            docEl.firstElementChild.appendChild(metaEl);
        } else {
            var wrap = doc.createElement('div');
            wrap.appendChild(metaEl);
            doc.write(wrap.innerHTML);
        }
    }

    function refreshRem(){
        var width = docEl.getBoundingClientRect().width;
        if (width / dpr > 540) {
            width = 540 * dpr;
        }
        var rem = width / 10;
        docEl.style.fontSize = rem + 'px';
        flexible.rem = win.rem = rem;
    }

    win.addEventListener('resize', function() {
        clearTimeout(tid);
        tid = setTimeout(refreshRem, 300);
    }, false);
    win.addEventListener('pageshow', function(e) {
        if (e.persisted) {
            clearTimeout(tid);
            tid = setTimeout(refreshRem, 300);
        }
    }, false);

    if (doc.readyState === 'complete') {
        doc.body.style.fontSize = 12 * dpr + 'px';
    } else {
        doc.addEventListener('DOMContentLoaded', function(e) {
            doc.body.style.fontSize = 12 * dpr + 'px';
        }, false);
    }
    

    refreshRem();

    flexible.dpr = win.dpr = dpr;
    flexible.refreshRem = refreshRem;
    flexible.rem2px = function(d) {
        var val = parseFloat(d) * this.rem;
        if (typeof d === 'string' && d.match(/rem$/)) {
            val += 'px';
        }
        return val;
    }
    flexible.px2rem = function(d) {
        var val = parseFloat(d) / this.rem;
        if (typeof d === 'string' && d.match(/px$/)) {
            val += 'rem';
        }
        return val;
    }

})(window, window['lib'] || (window['lib'] = {}));

```

* 从上面的代码，主要是改变了dpx和document的font-size大小。大小为docEl.getBoundingClientRect().width / 10 + 'px';


** 假设我们的设计稿宽是640的，则html的字体大小则被设为64px.则相当于1rem=64px。 **


* 假如一个元素的宽是160px,在平时，我们可以采用百分比可以做到自适应，假如使用响应式的话，可能需要设置多个，比如在320px，输出80px，而在640px输出160px等。

* 而采用以上rem的方法，则只需要输出2.5rem就能实现统一，如下表格：

| 设备宽度 | 320px | 360px | 414px | 640px |
| -------- | -----: | ----: |----: | :----: |
| <font color=#ff0000 >Html字体大小</font> | <font color=#ff0000 >32px</font> | <font color=#ff0000 >36px</font> | <font color=#ff0000 >41.4</font> | <font color=#ff0000 >64px</font> |
| 实际输出 | 1rem | 1rem | 1rem | 1rem |
| 设计稿缩放大小 | 80px | 90px | 103.5px | 160px |
| <font color=#ff0000 >实际输出</font> | <font color=#ff0000 >2.5rem</font> | <font color=#ff0000 >2.5rem</font> | <font color=#ff0000 >2.5rem</font> | <font color=#ff0000 >2.5rem</font> |

* 以上的2.5rem是怎么得出的呢？

* 160/64（1rem的基数为64px）=2.5rem;按照官方的说法（640px举例）

* Flexible会将视觉稿分成100份（主要为了以后能更好的兼容vh和vw），而每一份被称为一个单位a。同时1rem单位被认定为10a。针对我们这份视觉稿可以计算出：(设计稿为750px为例）

>1a   = 7.5px
>1rem = 75px 

* 那么我们这个示例的稿子就分成了10a，也就是整个宽度为10rem，<html>对应的font-size为75px