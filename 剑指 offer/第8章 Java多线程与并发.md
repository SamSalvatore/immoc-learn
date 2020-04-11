[TOC]
# 第8章 Java多线程与并发

##  8-1 进程和线程的区别
### 进程和线程的由来
![-w922](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/16/15842565561494.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


### 进程和线程的区别
* 进程是资源分配的最小单位，线程是CPU调度的最小单位
    * 所有与进程相关的资源，都被记录在PCB中
    * 进程是抢占处理机的调度单位；线程属于某个进程，共享其资源
    * 线程只由堆栈寄存器、程序计数器和TCB组成

### 总结
* 线程不能看作独立应用，而进程可看作独立应用
* 进程有独立的地址空间，相互不影响，线程只是进程的不同执行路径
* 线程没有独立的地址空间，多进程的程序比多线程程序健壮
    * 一个线程挂掉了，整个进程就挂掉了
* 进程的切换比线程的切换开销大

### Java进程和线程的关系
* Java对操作系统提供的功能进行封装，包括进程和线程
* 运行一个程序会产生一个进程，进程包含至少一个线程
* 每个进程对应一个JVM实例，多个线程共享JVM里的堆
* Java采用单线程编程模型，程序会自动创建主线程

##  8-2 线程的start和run方法的区别
![-w911](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/16/15842879571516.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

* 调用`start()`方法会创建一个新的子线程并启动
* `run()`方法只是Thread的一个普通方法的调用


### start()方法源码简介
`start()`方法内部会调用`start0()`方法. 而start0是`native`方法

```java
private native void start0();
```

`start0` 最终会调用`JVM_StartThread()`方法:
![-w727](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/16/15842893482210.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)


而查看`JVM_StartThread()`方法的源码发现其最终会创建一个线程:

```java
native_thread = new JavaThread(&thread_entry, sz);
```

thread_entry会将run方法教给虚拟机去调用
![-w882](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/16/15842895478368.jpg?x-oss-process=image/auto-orient,1/quality,q_90/watermark,text_bXdlYi10ZXN0,color_f5eded,size_40,x_10,y_10)

##  8-3 Thread和Runnable的关系
* Thread是类，Runnable是接口。Thread实现了Runnable接口
* Thread是实现了Runnable接口的类，使得run支持多线程
* 因累的单一继承原则，推荐多使用Runnable接口


```java
public class Thread implements Runnable
```

##  8-4 如何实现处理线程的返回值

### 如何给`run()`方法传参
* 构造函数传参
* 成员变量传参
* 回调函数传参

### 如何实现处理线程的返回值
* 主线程等待法
* 使用`Thread`类的`join()`阻塞当前线程以等待子线程处理完毕
* 通过Callable接口实现: 通过FutureTask Or 线程池获取






主线程等待法
```java
public class CycleWait implements Runnable {

    private String value;

    @Override
    public void run() {
        try {
            Thread.currentThread().sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        value = "we have data now";
    }

    public static void main(String[] args) throws InterruptedException {
        CycleWait cycleWait = new CycleWait();
        Thread t = new Thread(cycleWait);
        t.start();

        //主线程等待法。 需要每次循环等待多久是不确定的
        while (cycleWait.value == null) {
            Thread.currentThread().sleep(100);
        }
        System.out.println("value :" + cycleWait.value);
    }
}
```

Thread.join()
```java
public class CycleWait implements Runnable {

    private String value;

    @Override
    public void run() {
        try {
            Thread.currentThread().sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        value = "we have data now";
    }

    public static void main(String[] args) throws InterruptedException {
        CycleWait cycleWait = new CycleWait();
        Thread t = new Thread(cycleWait);
        t.start();

//        Waits for this thread to die.
        t.join();
        System.out.println("value :" + cycleWait.value);
    }
}
```

通过`Callable`接口实现
```java
public class MyCallable implements Callable<String> {

    @Override
    public String call() throws Exception {
        String value = "test";
        System.out.println("Ready to work");
        Thread.currentThread().sleep(5000);
        System.out.println("task done");
        return value;
    }
}


public class FutureTaskDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> futureTask = new FutureTask<>(new MyCallable());
        new Thread(futureTask).start();

        if (!futureTask.isDone()) {
            System.out.println("task has not finished, please wait");
        }

        System.out.println("task run: " + futureTask.get());

    }
}


//output:
task has not finished, please wait
Ready to work
task done
task run: test
```

通过`Callable`接口实现 - 线程池版

```java
public class ThreadPoolDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        Future<String> future = executorService.submit(new MyCallable());

        if (!future.isDone()) {
            System.out.println("task has not finished, please wait");
        }

        System.out.println("task run: " + future.get());
    }
}

//output:
task has not finished, please wait
Ready to work
task done
task run: test
```

##  8-5 线程的状态
六个状态
* 新建(New): 创建后尚未启动的线程的状态

* 运行(Runnable):包含Running 和 Ready
* 无限期等待(Waitinng): 不会被分配CPU执行时间，需要显式被唤醒
    * 没有设置Timeout参数的Object.wait()方法
    * 没有设置Timeout参数的Thread.join()方法
    * LockSupport.park()方法

* 限期等待(Timed Waiting): 在一定时间后会由系统自动唤醒
    * Thread.sleep()方法
    * 设置了Timeout参数的Object.wait()方法
    * 设置了Timeout参数的Thread.join()方法
    * LockSupport.parkNanos()方法
    * LockSupport.parkUntil（）方法

* 阻塞(Blocked): 等待获取排他锁

* 结束(Terminnated): 已终止线程的状态，线程已经结束执行


##  8-6 sleep和wait的区别
基本的差别
* `sleep`是`Thread`类的方法，`wait`是`Object`类中的方法
* `sleep()`方法可以在任何地方使用
* `wait()`方法只能在`synchronized`方法或`synchronized`块中使用

最主要的本质区别
* `Thread.sleep`只会让出CPU，不会导致锁行为的改变
* `Object.wait`不仅让出CPU，还会释放**已经占有的**同步资源锁
9    * 这也就说明为什么wait()方法要在synchronized中执行aaaaaaaaaaaaaaaaaaa

##  8-7 notify和notifyall的区别
两个概念
* 锁池`EntryList`
* 等待池`WaitSet`

### 锁池
假设线程A已经拥有了某个对象（不是类）的锁，而其他线程B、C想要调用这个对象的某个synchronized方法（或者块），由于B、C线程在进入对象的synchronized方法（或者块）之前必须先获得该对象锁的拥有权，而恰巧改对象的锁目前正被线程A所占用，此时B、C线程就会被阻塞，进入一个地方去等待锁的释放，这个地方便是该对象的锁池

### 等待池
假设线程A调用了某个对象的`wait()`方法，线程A就会释放该对象的锁，同时线程A就进入到了该对象的等待池中，进入到等待池中的线程不会去竞争该对象的锁。

直到这个对象被notify，就会从等待池中进入锁池

### notify 和 notifyAll的区别
* `notifyAll`会让所有处于等待池的线程全部进入锁池去竞争获取锁的机会
* `notify`只会随机选择一个处于等待池中的线程进入锁池去竞争获取锁的机会。

##  8-8 yield函数
### 概念
当调用Thread.yield() 函数时，会给线程调度器一个当前线程愿意让出CPU使用的暗示，但是线程调度器可能会忽略这个暗示。

`yield`不会对锁行为产生任何影响

##  8-9 interrupt函数
### 如何中断线程
已经被抛弃的方法
* 通过调用`stop（）` 方法停止线程
* 通过调用`suspend()` 和 `resume()`方法

目前使用的方法
* 调用`interrupt()`, 通知线程应该中断了
    * 如果线程处于被阻塞状态（如`sleep`状态），那么线程将立即退出被阻塞状态，并抛出一个InterruptedException 异常
    * 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true。被设置中断标志的线程将继续正常运行，不受影响。

##  8-10 前述方法及线程状态总结
![](http://it-learn.oss-cn-beijing.aliyuncs.com/2020/03/22/15848890093596.jpg)


##  8-11 彩蛋之如何有效谈薪
增加自己的筹码
* 尽量打听公司岗位职位的薪酬幅度
* 感知目标公司的缺人程度，工作的紧急程度
* 最有效的方式是已经具备了有竞争力的offer

