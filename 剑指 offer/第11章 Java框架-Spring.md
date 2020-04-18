[TOC]
# 第11章 Java框架-Spring

## 11-1 Spring家族的介绍
* Spring
* SpringBoot
* Spring Cloud


##  11-2 IOC原理
IOC(Inversion of Control): 控制反转
* Spring Core 最核心的部分
* 需要先了解依赖注入（Dependency Inversion）

DI举例: 设计行李箱
![-w907](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836397254143.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


![-w1072](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836397506801.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

当底层实现更改时， 比如PM要求Tire类支持动态传入参数。那么所有的上层类都需要跟着改:
![-w1059](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836398296063.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


### 依赖注入
含义: 把底层类作为参数传递给上层类，实现上层对下层的“控制”
![-w914](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836398908791.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

![-w1051](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836399073666.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

![-w937](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836400365681.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

依赖注入的方式:
* Setter
* Interface
* Constructor
* Annotation

### 依赖倒置原则、IOC、DI、IOC 容器的关系
![-w644](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836401035842.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

IOC 容器的优势
* 避免在各处使用new来创建类，并且可以做到统一维护
* 创建实例的时候不需要了解其中的细节

![-w1091](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836402510286.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

![-w2550](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/08/15836403771363.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


##  11-3 SpringIOC的应用
BeanFactory与ApplicationContext的比较
* BeanFactory是Spring框架的基础设施，面向Spring
* ApplicationContext面向使用Spring框架的开发者


##  11-4 SpringIOC的refresh源码解析
![-w1185](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/14/15841878653726.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


##  11-5 SpringIOC的getBean方法的解析
getBean方法的代码逻辑
* 转换beanName
* 从缓存中加载实例
* 实例化Bean
* 检测parentBeanFactory
* 初始化依赖的Bean
* 创建Bean

`Spring Bean`的作用域
* `singleton`:`Spring`的默认作用域，容器里拥有唯一的Bean实例
* `prototype`: 针对每个getBean请求，容器都会创建一个Bean实例
* `request`: 会为每个Http请求创建一个Bean实例
* `session`:会为每个session创建一个Bean实例
* `globalSession`:会为每个全局Http Session创建一个Bean实例，该作用域仅对Portlet有效

##  11-6 AOP的介绍和使用
### 关注点分离：不同的问题交给不同的部分去解决
* 面向切面编程AOP正是此种技术的体现
* 通用化功能代码的实现，对应的就是所谓的切面（Aspect）
* 业务功能代码和切面代码分开后，架构将变得高内聚低耦合
* 确保功能的完整性: 切面最终需要被合并到业务中(Weave)

### AOP的三种织入方式
* 编译时织入: 需要特殊的Java编译器，如AspectJ
* 类加载时织入:需要特殊的Java编译器，如AspectJ和AspectWerkz
* 运行时织入: Spring采用的方式，通过动态代理的方式，实现简单



### AOP的主要名词概念
* `Aspect`: 通用功能的代码实现 
    * `@Aspect`
* `Target`: 被织入Aspect的对象
* `Joint Point`：可以作为切入点的机会，所有方法都可以做为切入点
* `Pointcut`: Aspect实际被应用在的Joint Point，支持正则
* `Advice`:类里的方法以及这个方法如何织入到目标方法的方式
* `Weaving`：Aop的实现过程

### Advice的种类
* 前置通知(Before)
* 后置通知(AfterReturning)
* 异常通知(AfterThrowing)
* 最终通知（After）
* 环绕通知(Around)

##  11-7 SpringAOP的原理
AOP的实现: JdkProxy和Cglib
* 默认策略如果目标类是接口，则由JDKProxy来实现，否则是后者

##  11-8 本章小结