---
title: xshell 连接阿里云服务器centos7.3系统指南与安装mongodb数据库
date: 2018-04-25 10:03:58
tags: linux
categories: js 
thumbnail: http://image.uczzd.cn/6207851735369211778.jpeg
---

### 服务器系统为centos 使用如下命令安装各种包

```
1.sudo yum install vim openssl build-essential libssl-dev wget curl git安装vim 和openssl  和build-essential和 libssl-dev和wget和curl和git

```

* 2.输入：nvm查看它信息，
* 2.1可以通过curl或者wget进行安装nvm:
* curl方式:

```
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash 
```
wget方式:

```
    wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```

```
2.1.sudo yum install git安装git

```

* 2.2.连接服务器成功后输入：yum install -y lrzsz安装上上传和下载命令，rz为上传sz为下载

* 3.1.安装东西使用yum install -y 包名   即可

* 3.2 nvm ls-remote列出全部可以安装的版本号

* 4.nvm use 版本号 切换指定版本，切换效果是全局的

* 5.nvm current 查看当前使用的版本

* 6.nvm ls 查看该系统已经安装的版本，这个命令也能看到当前使用的是哪个版本

* 7.nvm install v9.11.1 安装指定版本

* 8.nvm use v9.11.1 使用指定版本

* 9.nvm alias default v9.11.1 指定默认版本

* 10.node -v 查看node版本号;输出v9.11.1说明安装成功

```
npm --registry=https://registry.npm.taobao.org install -g npm 将npm 指定到淘宝镜像来加快包下载速度

```

```
  echo fs.inotify.max_user_watches = 524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p

```

```
npm --registry=https://registry.npm.taobao.org install -g cnpm 将npm 指定到淘宝镜像cnpm来加快包下载速度以后可以使用cnpm install 来下载波包；

```

* 然后使用cnpm install webpack pm2 gulp -g 来安装这些包；

* sudo yum install nginx 来安装nginx 然后nginx -v 查看安装版本号

* 输入 cd /etc/nginx/conf.d 回车，然后vim nginx_8081.conf进行文件编辑内容如下：

```
upstream suchengyong {
    server 127.0.0.1:8081;
}
server {
    listen 80;
    server_name  112.74.54.23;

    location / {
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
        proxy_pass http://suchengyong;
    }

}

```

* 然后按Esc键在按：wq回车保存成功然后输入nginx -t 测试是否成功，返回success表示成功：

* Centos系统 nginx启动：service nginx  start或者重启：service nginx  restart或者/etc/init.d/nginx stop关闭nginx或者/etc/init.d/nginx start启动nginx

## mongodb数据库安装

###  一、打开官网下载对应版本

<img src="http://www.zixuephp.net/uploads/image/20171208/1512724483131413.png" alt="">

###  二、解压安装包，复制到对应目录中

```
#下载安装包
wget -c https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.6.0.tgz
#下载完后进行解压包
tar -xzvf mongodb-linux-x86_64-rhel70-3.6.0.tgz
#创建mogodb目录
mkdir /usr/local/mongodb
#复制安装包到mongodb目录中
mv mongodb-linux-x86_64-rhel70-3.6.0 /usr/local/mongodb
cd /usr/local/mongodb

```

### 三、守护进程启动mongodb

* 启动方式和参数：./mongod --dbpath=/usr/local/mongodb/data/db --logpath=/usr/local/mongodb/data/log/logs.log --logappend --fork

```
#/usr/local/mongodb/创建数据库目录和日志目录
mkdir data
#进入data目录
cd data
#创建两个文件夹
mkdir db
mkdir log
cd log
#建立日志文件
touch logs.log
 
#进入/usr/local/mongodb/bin/目录中启动mongodb
 
[root@localhost bin]# ./mongod --dbpath=/usr/local/mongodb/data/db --logpath=/usr/local/mongodb/data/log/logs.log --logappend --fork
#运行结果
about to fork child process, waiting until server is ready for connections.
forked process: 1874
child process started successfully, parent exiting

```

### 四、使用客户端进行mongodb数据库的操作

* 启动方式（/usr/local/mongodb/bin/目录）：./mongo

```
[root@localhost bin]# ./mongo
MongoDB shell version v3.6.0
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.0
Server has startup warnings: 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] ** WARNING: You are running this process as the root user, which is not recommended.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server. 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          Start the server with --bind_ip <address> to specify which IP 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2017-12-08T16:00:38.798+0800 I CONTROL  [initandlisten] 
>
#查看数据库
> show databases;
admin   0.000GB
config  0.000GB
local   0.000GB
#选择数据库
> use admin
switched to db admin
#查看数据表
> show tables;
system.users
system.version

```

### 五、mongodb3.6开机启动脚本设置

*  mongodb3.6开机启动shell脚本，当每次系统重启后mongodb进程并不会随着系统去启动，必须手动去启动它，让费时间，所以需要借助shell脚本让mongodb自动启动。

### 六、mongodb开机启动脚本

* 1.创建脚本文件：vim /etc/init.d/mongodb

```
#!/bin/bash
# chkconfig: - 85 15
#author:zuoping
name=mongod
path_bin=/usr/local/mongodb/bin/
path=/usr/local/mongodb/
case "$1" in
  start)
    ${path_bin}${name} --dbpath=${path}data/db --logpath=${path}data/log/logs.log --logappend --fork
    if [ $? -eq 0 ];then
      echo "${name}启动成功..."
    else
      echo "${name}启动失败..."
    fi
  ;;
  stop)
    if [ $(ps -ef|grep "mongod" |grep "fork"|awk {'print $2'}) -gt 0 ];then
      kill `ps -ef|grep "mongod" |grep "fork"|awk {'print $2'}`
      if [ $? -eq 0 ];then
        echo "${name}停止成功"
      else
        echo "${name}停止失败"
      fi
    else
      echo "${name}进程已经停止"
    fi
  ;;
  restart)
    if [ $(ps -ef|grep "mongod" |grep "fork"|awk {'print $2'}) -gt 0 ];then
      kill `ps -ef|grep "mongod" |grep "fork"|awk {'print $2'}`
      if [ $? -eq 0 ];then
        echo "${name}停止成功"
      else
        echo "${name}停止失败"
      fi
    else
      echo "${name}进程已经停止"
    fi
    echo "${name}启动中..."
    sleep 3s
    ${path_bin}${name} --dbpath=${path}data/db --logpath=${path}data/log/logs.log --logappend --fork
    if [ $? -eq 0 ];then
      echo "${name}重启成功"
    else
      echo "${name}重启失败"
    fi
   ;;
   *)
    echo "${name}start|stop|restart"
   ;;
esac

```

* 3.加入系统服务和开机自启

```
cd /etc/init.d/
#设置执行权限
chmod a+x mongodb
#加入系统服务
chkconfig --add mongodb
#开机服务自启
chkconfig mongodb on
#重启系统生效
shutdown -r

```

* 4.mongodb开启、停止、重启操作

```
#开启
service mongodb start
#停止
service mongodb stop
#重启
service mongodb restart

```
