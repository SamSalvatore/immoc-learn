[TOC]
# 第二章 Nginx初体验【善疏则通，能导必安】
##  2-1 总诀式：课程结构概述

##  2-2 破剑式：Nginx概述
nginx定义
>Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。

>其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布。

>Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。


高性能的WEB服务器
* 高性能的**静态**WEB服务器
* 反向代理

nginx 只能处理静态资源。无法处理动态资源。对于动态资源，nginx需要通过反向代理实现
![-w1099](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15872162413117.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

  
##  2-3 破刀式：Nginx缘起历史【时代的召唤】
nginx为什么出现并如此流行
* 互联网数据的快速增长
* Apache处理请求的低效性

![-w1039](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15872170421511.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

##  2-4 破枪式：Nginx主流企业场景【只学有用的】
一个Http请求的全流程剖析
![-w1258](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15872171880711.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

![-w2178](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15872177461034.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

三个常见的应用场景
* 静态资源服务(Web Server)
* 反向代理服务
* API服务

##  2-5 破箭式：Nginx优势【核心竞争力】
优势特点
* 高并发、高性能
* 扩展性好
* 异步非阻塞的事件驱动模型
* 高可靠性
* 开源

##  2-6 破气式：安装第一个rpm包Nginx
下面这些是在我的macos系统下测试的

安装nginx
```shell
brew install nginx
```

查看nginx安装路径

```shell
➜  ~ which nginx
/usr/local/bin/nginx
```

nginx日志文件位置
```
/usr/local/var/log/nginx
```
启动nginx

```shell
sudo nginx

# 按照配置文件启动
sudo nginx -c nginx配置文件地址   
```
停止nginx

```shell
sudo nginx -s stop
```
重启nginx

```shell
sudo nginx -s reload
```

nginx启动参数
``` shell
➜  ~ nginx -h
nginx version: nginx/1.17.6
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  
  # -v: 展示nginx版本号并且退出
  -v            : show version and exit 
  
 # -V: 展示nginx版本号和配置并且退出
  -V            : show version and configure options then exit
  
  # -t: 测试nginx配置并且退出
  -t            : test configuration and exit
  
  # -T: 测试nginx配置，dump it 并且退出
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  
  # -s singnal: 给master进程发送信号: stop或者是quit或者是reopen或者是reload
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/Cellar/nginx/1.17.6/)
  
  # -c: 设置配置文件(默认是/usr/local/etc/nginx/nginx.conf)
  -c filename   : set configuration file (default: /usr/local/etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
```