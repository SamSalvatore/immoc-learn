第5章 Linux

## 5-1 Linux的体系结构
* 内核态
* 用户态

## 5-2 查找特定文件
find
> 语法: find path [options] param

作用: 在指定目录下查找文件


面试里常用的方式
```shell
# 精确查找文件
find ~ -name "target3.java"

# 模糊查找文件
find ~ -name "target*"

# 不区分文件名大小写去查找文件
find ~ -iname "target*"

# 递归列出当前目录下所有文件以及目录
find ~ 
# 文档
man find
```

![-w1087](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15871932427548.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

## 5-3 检索文件内容
### grep
> 语法: grep [options] pattern file

* 全称： Global Regular Expression Print
* 作用：查找文件里符合条件的字符串

### 管道操作符 |
* 可将指令连接起来，前一个指令的输出作为后一个指令的输入

![-w1017](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/04/18/15871936454620.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

使用管道注意的要点
* 只处理前一个命令正确输出，不处理错误输出
* 右边命令必须能够接受标准输入流，否则传递过程中数据会被抛弃
* sed,awk,grep,cut,head,top,less,more,wc,join,sort,spliy等

面试里常用的方式
* grep 'partial\[true\]' xxx.log
* grep -o 'engile\[0-9a-z]*\]'
* grep -v 'grep'

##  5-4 对日志内容做统计
### awk
> 语法: awk [options] 'cmd' file

* 一次读取一行文本，按输入分隔符进行切片，切成多个组成部分
* 将切片直接保存在内建的变量中，`$1`,`$2`... ($0表示行的全部)
* 支持对单个切片的判断，支持循环判断，默认分隔符为空格


##  5-5 批量替换文件内容及本章小结
面试里常用的方式
* sed -i 's/^Str/String/' replace.java
* sed -i 's/\.$/\;/' replace.java
* sed -i 's/jack/me/g' replace.java

##  5-6 彩蛋之容易忽略的细节
* 面试要偷偷摸摸的进行
* 面试时间不要一味将就对方
* 提离职要谨慎
* 好聚好散
* 跳槽时间衔接： 一般15号之后离职，下个月15号前社保不会变
