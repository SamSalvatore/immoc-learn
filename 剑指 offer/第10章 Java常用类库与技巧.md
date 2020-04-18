[TOC]
# 第10章 Java常用类库与技巧

## 10-2 Java异常要点分析
`Throwable`类是所有异常或错误的超类，它有两个子类
* `Error`
* `Exception`
    * `Unchecked Exception`: 非检查异常
    * `Checked Exception`:检查异常

其中非检查异常(`unchecked Exception`)包括RuntimeException 以及他们的子类


### Unchecked Exception
`javac`在编译时，不会提示和发现这样的异常，**不要求在程序处理这些异常**。所以如果愿意，我们可以编写代码处理（使用`try…catch…finally`）这样的异常，也可以不处理。

对于这些异常，我们应该修正代码，而不是去通过异常处理器处理 。这样的异常发生的原因多半是代码写的有问题。如除0错误ArithmeticException，错误的强制类型转换错误ClassCastException，数组索引越界ArrayIndexOutOfBoundsException，使用了空对象NullPointerException等等

>在编写DB层事务时，我们经常跑出了RunTimeException，这样就不用把这个异常一直往上抛了

### 检查异常
检查异常（`checked exception`）: `Exception`除去`RuntimeException`以外的其它异常。编译器会强制要求程序员为这样的异常做预备处理工作（使用try…catch…finally或者throws）。在方法中要么用try-catch语句捕获它并处理，要么用throws子句声明抛出它，否则编译不会通过。这样的异常一般是由程序的运行环境导致的。因为程序可能被运行在各种未知的环境下，而程序员无法干预用户如何使用他编写的程序，于是程序员就应该为这样的异常时刻准备着。如SQLException , IOException,ClassNotFoundException 等

### Error
一般是指`java`虚拟机相关的问题，如系统崩溃、虚拟机出错误、动态链接失败等，这种错误无法恢复或不可能捕获，将导致应用程序中断，通常应用程序无法处理这些错误，因此应用程序不应该捕获`Error`对象，也无须在其`throws`子句中声明该方法抛出任何`Error`或其子类。

