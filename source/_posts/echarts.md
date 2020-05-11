---
title: echarst记录
date: 2018-02-07 10:03:58
tags: echarst
categories: 前端开发 
thumbnail: http://img3.imgtn.bdimg.com/it/u=3843804769,4166221848&fm=26&gp=0.jpg
---

* 记录花了一天时间开发的一个时间联动的折线垂直图表

```
option = {
    timeline: {
        data: [
            '青盒子', '红盒子', '瞄金', '雪涛'
        ],
        axisType: 'category',
        show: true,
        autoPlay: false,
        playInterval: 1000
    },
    
    options: [
    {
        title: {
            text: '青盒子现世后的价格走势',
        },
        grid: {
            top: 80,
            bottom: 100
        },
        tooltip: {
            trigger: 'axis',
            showContent:false,
            formatter: function (params) {
                 console.log(params)
                 var html = '';
                 for(var i=0;i<params.length;i++){
                     html += '出力: ' + params[i].data[0].toFixed(2) + '<br>报价: ' + params[i].data[1].toFixed(2)+'<br>';
                     
                 }
                return html
            }
        },
        xAxis: {
            type: 'value',
        },
        yAxis: {
            type: 'value',
            name: '单位：元'
        },
        series: [{
            id:'a',
            type: 'line',
            symbolSize: 10,
            
            label: {
                normal: {
                    show: false,
                    position: 'top',
                }
            },
            data: [[800,500], [950,500], [950,600], [1200,600]]
        }]
    },
    {
        title: {
            text: '青盒子现世后的价格走势',
        },
        grid: {
            top: 80,
            bottom: 100
        },
        tooltip: {
            trigger: 'axis',
             showContent:false,
            formatter: function (params) {
                 console.log(params)
                 var html = '';
                 for(var i=0;i<params.length;i++){
                     html += '出力: ' + params[i].data[0].toFixed(2) + '<br>报价: ' + params[i].data[1].toFixed(2)+'<br>';
                     
                 }
                return html
            }
        },
        xAxis: {
            type: 'value',
        },
        yAxis: {
            type: 'value',
            name: '单位：元'
        },
        series: [{
            id:'a',
            type: 'line',
            symbolSize: 10,

            data: [[750,900], [850,900], [850,600], [1000,600]]
        }]
    },
    {
        title: {
            text: '青盒子现世后的价格走势',
        },
        grid: {
            top: 80,
            bottom: 100
        },
        tooltip: {
            trigger: 'axis',
             showContent:false,
            formatter: function (params) {
                 console.log(params)
                 var html = '';
                 for(var i=0;i<params.length;i++){
                     html += '出力: ' + params[i].data[0].toFixed(2) + '<br>报价: ' + params[i].data[1].toFixed(2)+'<br>';
                     
                 }
                return html
            }
        },
        xAxis: {
            type: 'value',
        },
        yAxis: {
            type: 'value',
            name: '单位：元'
        },
        series: [{
            id:'a',
            type: 'line',
            symbolSize: 10,
            label: {
                normal: {
                    show: true,
                    position: 'top',
                }
            },
            data: [[850,800], [1050,800], [1050,600], [1300,600]]
        }]
    },
    {
        title: {
            text: '青盒子现世后的价格走势',
        },
        grid: {
            top: 80,
            bottom: 100
        },
        tooltip: {
            trigger: 'axis',
             showContent:false,
            formatter: function (params) {
                 console.log(params)
                 var html = '';
                 for(var i=0;i<params.length;i++){
                     html += '出力: ' + params[i].data[0].toFixed(2) + '<br>报价: ' + params[i].data[1].toFixed(2)+'<br>';
                     
                 }
                return html
            }
        },
        xAxis: {
            type: 'value',
        },
        yAxis: {
            type: 'value',
            name: '单位：元'
        },
        series: [{
            id:'a',
            type: 'line',
            symbolSize: 10,
            label: {
                normal: {
                    show: true,
                    position: 'top',
                }
            },
            data: [[950,1000], [1200,1000], [1200,600], [1300,600]]
        }]
    }
    
    ]

};

```