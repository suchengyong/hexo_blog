---
title: 好用的时间控件
date: 2018-04-24 15:03:58
tags: js
categories: 前端开发 
thumbnail: http://pic1.win4000.com/wallpaper/2017-11-10/5a0506dd8bb70.jpg
---

### 好用的一款时间插件支持年，月，日，时，分，秒，各个阶段的查询

* 直接上代码

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>  
	<link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">  
	<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>  
	  
	<script src="https://cdn.bootcss.com/moment.js/2.18.1/moment-with-locales.min.js"></script>  
	<link href="https://cdn.bootcss.com/bootstrap-datetimepicker/4.17.47/css/bootstrap-datetimepicker.min.css" rel="stylesheet">  
	<script src="https://cdn.bootcss.com/bootstrap-datetimepicker/4.17.47/js/bootstrap-datetimepicker.min.js"></script>  
</head>
<body>
	<div class="row">  
    <div class="col-sm-6">  
        <div class="form-group">  
            <label for="">选择日期：</label>  
            <div class="input-group date" id='date1'>  
                <input type="text" class="form-control">  
                <span class="input-group-addon">  
                    <i class="glyphicon glyphicon-calendar"></i>  
                </span>  
            </div>  
        </div>  
    </div>  
    <script>
    	$(function () {  
		   var picker1=$('#date1').datetimepicker({  
		        format: 'YYYY-MM-DD', //这里换成YYYY-MM表示到月，YYYY表示到年，YYYY-MM-DD HH:mm:ss表示到时分
		        defaultDate: "2018-02-02",
		        locale: moment.locale('zh-cn')  
		    });  
		     picker1.on('dp.change', function (values) {//这里监听时间选择变化
                        var d = new Date(values.date._d);
                        var year = d.getFullYear();
                        var month = (d.getMonth() + 1)<10? '0'+(d.getMonth() + 1):(d.getMonth() + 1);
                        var day = d.getDate()<10? '0'+d.getDate():d.getDate();
                        var setter = year + '-' + month + '-'+ day;
                        // $scope.startDateTime = setter;
                        // sessionStorage.setItem("starttime", setter)
                    });
		});  
    </script>
</body>
</html>

```