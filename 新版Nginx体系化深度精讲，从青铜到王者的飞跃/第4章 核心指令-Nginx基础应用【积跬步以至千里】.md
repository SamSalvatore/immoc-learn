[TOC]
# 第4章 核心指令-Nginx基础应用【积跬步以至千里】
##  4-1 配置文件main段核心参数用法-上
##  4-2 配置文件main段核心参数用法-下
##  4-3 配置文件events段核心参数用法

##  4-4 server_name指令用法
### 语法结构

```
语法: server_name name1 name2 name3...;
示例:server_name www.nginxx.com;
示例2: server_name *.nginx.org;
示例3: server_name www.nginxx.com *.nginx.org;
```

### 四种写法

```
server_name www.imooc.com
server_name *imooc.com
server_name www.imooc.*
server_name ~^www\.imooc\.*$
```

##  4-5 server_name指令用法优先级
### 多域名如何匹配
![-w654](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909018982748.jpg)

### 匹配优先级
![-w1672](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909017991719.jpg)

##  4-6 root和alias的区别
### 语法结构
![-w1867](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909033817246.jpg)

### 共同点与区别
相同点: URI到磁盘文件的映射
区别: root会将定于路径与URI叠加;alias则只取定义路径

### 区别示例
![-w2093](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909035756373.jpg)

![-w1914](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909036466415.jpg)


个人感觉类比于阿里云Oss：

URI就好比对象存储的文件夹。root就好比主机名(domain). 这样的话用户请求的url实际上对应访问的是 domain + objectName。 也就是我们nginx中的 `root + URI`

alias 就好比阿里云oss的全路径

### 注意事项
* alias末尾必须加 `/`
* alias 只能位于location中

##  4-7 location的基础用法
![-w2400](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909040898114.jpg)

##  4-8 location指令中匹配规则的优先级
### 优先级
![-w1135](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/05/31/15909043631762.jpg)







##  4-9 深入理解location中URL结尾的反斜线
### URL写法区别
#### 不带`/`
```
location /test {
...
}
```
nginx在处理的时候，会首先将test作为目录来处理。
如果找不到，会将test作为文件来处理。

#### 带`/`
```
location /test/ {
...
}
```
nginx在处理的时候，只会将test作为目录来处理


##  4-10 stub_status模块用法
