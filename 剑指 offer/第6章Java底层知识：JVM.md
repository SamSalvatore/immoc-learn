[TOC]
# 第6章 Java底层知识：JVM
## 6-1 谈谈你对Java的理解
* 平台无关性
    * 一次编译，到处运行
* GC
* 语言特性
* 面向对象
* 类库
* 异常处理

##  6-2 平台无关性如何实现
![-w1311](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15850286489549.jpg)

Java 源码首先被编译成字节码，再由不同平台的JVM进行解析，Java语言在不同的平台上运行时不需要进行重新编译，Java虚拟机在执行字节码的时候，把字节码转换成具体平台上的机器指令


为什么JVM不直接将源码解析成机器码去执行
* 准备工作: 每次执行都需要各种检查
* 兼容性: 也可以将别的语言解析成字节码

##  6-3 JVM如何加载class文件

![-w1166](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15850290956659.jpg)


* Class Loader： 依据特定格式，加载class文件到内存
* Execution Engine： 对命令进行解析
* Native Interface： 融合不同开发语言的原生库为Java所用
* Runtime Data Area: JVM内存空间结构模型

##  6-4 什么是反射
Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。

反射的例子

```java
public class Robot {
    private String name;
    public void sayHi(String helloSentence){
        System.out.println(helloSentence + " " + name);
    }
    private String throwHello(String tag){
        return "Hello " + tag;
    }
    static {
        System.out.println("Hello Robot");
    }
}
```


```
public class ReflectSample {
    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException, NoSuchFieldException {
        Class rc = Class.forName("com.interview.javabasic.reflect.Robot");
        Robot r = (Robot) rc.newInstance();
        System.out.println("Class name is " + rc.getName());
        Method getHello = rc.getDeclaredMethod("throwHello", String.class);
        getHello.setAccessible(true);
        Object str = getHello.invoke(r, "Bob");
        System.out.println("getHello result is " + str);
        Method sayHi = rc.getMethod("sayHi", String.class);
        sayHi.invoke(r, "Welcome");
        Field name = rc.getDeclaredField("name");
        name.setAccessible(true);
        name.set(r, "Alice");
        sayHi.invoke(r, "Welcome");
        System.out.println(System.getProperty("java.ext.dirs"));
        System.out.println(System.getProperty("java.class.path"));

    }
}
```

##  6-5 谈谈ClassLoader
类从编译到执行的过程
* 编译器将Robot.java 源文件编译为Robot.class字节码文件
* ClassLoader将字节码转换为JVM中的`Class<Robot>`对象
    * `Class rc = Class.forName("com.interview.javabasic.reflect.Robot");`
* JVM利用`Class<Robot>`对象实例化为Robot对象
    * ` Robot r = (Robot) rc.newInstance();
`

谈谈ClassLoader

* ClassLoader在Java中有着非常重要的作用，它主要工作在Class装载的加载阶段，其主要作用是从系统外部获得Class二进制数据流。

* 它是Java的核心组件，所有的Class都是由ClassLoader进行加载的，ClassLoader负责通过将Class文件里的二进制数据流装载进系统，然后交给Java虚拟机进行连接、初始化等操作

ClassLoader的种类
* BootStrapClassLoader: C++编写，加载核心库 Java.* 
* ExtClassLoader: Java编写，加载扩展库javax.*
* AppClassLoader: Java编写，加载程序所在目录
    * 就是我们平时自己写的代码的目录
* 自定义ClassLoader: Java编写，定制化加载

自定义ClassLoader的实现


```java
public class MyClassLoader extends ClassLoader {
    private String path;
    private String classLoaderName;

    public MyClassLoader(String path, String classLoaderName) {
        this.path = path;
        this.classLoaderName = classLoaderName;
    }

    //用于寻找类文件
    @Override
    public Class findClass(String name) {
        byte[] b = loadClassData(name);
        return defineClass(name, b, 0, b.length);
    }

    //用于加载类文件
    private byte[] loadClassData(String name) {
        name = path + name + ".class";
        InputStream in = null;
        ByteArrayOutputStream out = null;
        try {
            in = new FileInputStream(new File(name));
            out = new ByteArrayOutputStream();
            int i = 0;
            while ((i = in.read()) != -1) {
                out.write(i);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                out.close();
                in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return out.toByteArray();
    }
}
```


##  6-6 ClassLoader的双亲委派机制

>某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父类加载器，依次递归，如果父类加载器可以完成类加载任务，就成功返回；只有父类加载器无法完成此加载任务时，才自己去加载。

* 自底向上查看类是否已经被加载
* 自上到下尝试加载类

![-w922](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15853801117071.jpg)


### 委托机制的意义 — 防止内存中出现多份同样的字节码
比如两个类A和类B都要加载System类：
* 如果不用委托而是自己加载自己的，那么类A就会加载一份System字节码，然后类B又会加载一份System字节码，这样内存中就出现了两份System字节码。
* 如果使用委托机制，会递归的向父类查找，也就是首选用Bootstrap尝试加载，如果找不到再向下。这里的System就能在Bootstrap中找到然后加载，如果此时类B也要加载System，也从Bootstrap开始，此时Bootstrap发现已经加载过了System那么直接返回内存中的System即可而不需要重新加载，这样内存中就只有一份System的字节码了。

##  6-7 loadClass和forName的区别
类的加载方式
* 隐式加载: `new` 
* 显式加载: `loadClass`、`forName`等
    * 先获取Class对象，在通过Class对象的new Instance（）方法生成对象的实例


类的装载过程
![-w1169](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15853852459068.jpg)


loadClass和forName的区别
* Class.forName得到的class是已经初始化完成的
* ClassLoader.loadClass得到的class 是还没有链接的

```java
public class Robot {
    private String name;
    public void sayHi(String helloSentence){
        System.out.println(helloSentence + " " + name);
    }
    private String throwHello(String tag){
        return "Hello " + tag;
    }
    static {
        System.out.println("Hello Robot");
    }
}
```

```java
public class LoadDifference {
    public static void main(String[] args) throws ClassNotFoundException {
        
        // 不会执行Robot的static静态代码块
        ClassLoader cl = Robot.class.getClassLoader();
        
        
        //会执行Robot的static静态代码块
        Class r = Class.forName("com.interview.javabasic.reflect.Robot");
    }
}
```

##  6-8 Java内存模型之线程独占部分-1

### 内存简介
![-w867](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15853888309832.jpg)

* 32位处理器: 2^32 的可寻址范围
* 64位处理器: 2^64 的可寻址范围

地址空间的划分
* 内核空间
* 用户空间

### 程序计数器
* 当前线程所执行的字节码行号指示器(逻辑)
* 不会发生内存泄漏

### java虚拟机栈（Stack）
* Java方法执行的内存模型
* 包含多个栈帧

### 局部变量表和操作数栈
* 局部变量表: 包含方法执行过程中所有变量
* 操作数栈: 入栈、出栈、复制、交换、产生消费变量


##  6-9 Java内存模型之线程独占部分-2

![-w1405](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15853899306407.jpg)


递归为什么会引发java.lang.StackOverflowError异常
* 递归过深，栈帧数超过虚拟栈深度

![-w1143](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15853887366960.jpg)


### 本地方法栈
* 与虚拟机栈类似，主要作用于标注了native的方法

##  6-10 Java内存模型之线程共享部分
元空间（MetaSpace）与永久代(PermGen)的区别
* 元空间使用本地内存，而永久代使用的是JVM的内存


MetaSpace相比PerGen的优势
* 字符串常量池存在永久代中，容易出现性能问题和内存溢出
* 类和方法的信息大小难以确定，给永久代的大小指定带来困难
* 永久代会为GC带来不必要的复杂性
* 方便HotSpot与其他JVM如Jrockit的集成


Java堆(Heap)
* 对象实例的分配区域
* GC 管理的主要区域


##  6-11 Java内存模型之 常考题解析-1
JVM 三大性能调优参数 `-Xms` ,`-Xmx` ,`-Xss`的含义
* `-Xss`: 规定了每个线程虚拟机栈(堆栈)的大小
* `-Xms`:堆的初始值
* `-Xmx`:堆能达到的最大值

一般都将-Xms配置和-Xmx一样大



### Java内存模型中堆和栈的区别 - 内存分配策略
* 静态存储: 编译时确定每个数据目标在运行时的存储空间需求
* 栈式存储: 数据区需求在编译时未知，运行时模块入口前确定
* 堆式存储：编译时或运行时模块入口都无法确定，动态分配

联系：引用对象、数组时，栈里定义变量保存堆中目标的首地址


* 管理方式: 栈自动释放，堆需要GC
* 空间大小: 栈比堆小
* 碎片相关: 栈产生的碎片远小于堆
* 分配方式: 栈支持静态和动态分配，而堆仅支持动态分配
* 效率: 栈的效率比堆高



### 元空间、堆、线程独占部分间的联系 -- 内存角度
![-w735](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15854033611321.jpg)

![-w1047](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15854034343277.jpg)


### 不同JDK版本之间的`intern（）`方法的区别 - JDK6 VS JDK6+

![-w1117](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15854075172269.jpg)


```java
String s = new String("a");
a.intern()
```

##  6-12 Java内存模型之常考题解析-2

JDK6 输出 false 、 false
JDK8 输出 false、true

前置知识点：
**“a” 双引号扩起来的字符串都是放置在字符串常量池中的**
`s.intern()` 可以理解为将字符串对象拷贝一份副本到字符串常量池中

![-w1357](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15854080403366.jpg)



  ![-w1403](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/28/15854080227277.jpg)


##  6-13 彩蛋之找工作的最佳时期
### 为什么会出现金三银四的现象
* 年终奖已发放，调薪情况已经确定
* 卧薪尝胆迎来阶段性成果
* 公司增加员工名额，弥补劳动力缺口


### 相对容易找到工作的时期: 临近年末的时候
* 大多数人不愿意在这个时候跳槽，导致远低于求
* 门槛变低，通过率变高
* 拿到offer的时间会相对变短

### 年末跳槽的好与坏
真的会失去年终奖吗？ 那到未必
* 通过薪资等形式补偿
* 谈薪方式一: 直接和猎头表达
* 谈薪方式二: 说出顾虑
