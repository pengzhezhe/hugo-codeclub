---
title: Java 异常处理
date: 2020-06-01 
categories: 
- Java
tags:
- Java
---

异常指不期而至的各种状况，如：文件找不到、网络连接失败、非法参数等。异常是一个事件，它发生在程序运行期间，干扰了正常的指令流程。这些异常有的是因为用户错误引起，有的是程序错误引起的，还有其它一些是因为物理错误引起的。

## 异常类层次

![异常类继承关系](https://s2.loli.net/2022/09/02/LYSixdsImM9trhk.png)

在Java中所有的异常类都继承于Throwable父类,它有两个重要的子类：Exception和Error。

- Error：是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而代表运行时 JVM出现的问题 。这些异常发生时，JVM一般会选择线程终止 。这些错误表示故障发生于JVM自身、或者发生在JVM试图执行应用时 ，如JVM运行错误（Virtual MachineError）、类定义错误（NoClassDefFoundError）等。这些错误是不可查的，因为它们在应用程序的控制和处理能力之外，而且绝大多数是程序运行时不允许出现的状况。对于设计合理的应用程序来说，即使确实发生了错误，本质上也不应该试图去处理它所引起的异常状况。

- Exception: 是程序本身可以处理的异常。Exception 类有一个重要的子类 RuntimeException 。RuntimeException 类及其子类表示JVM 常用操作引发的错误。例如，空值对象引用、除数为零或数组越界，则分别引发运行时异常NullPointerException、ArithmeticException和ArrayIndexOutOfBoundException。

> 注意：异常和错误的区别：异常能被程序本身可以处理，错误是无法处理。

通常，Java 的异常(包括 Exception 和 Error)分为 可查的异常（checked exceptions）和不可查的异常（unchecked exceptions） 。

- 可查异常（编译器要求必须处置的异常）：在运行中，很容易出现的、情理可容的异常状况。可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理 ，要么用 try-catch 语句捕获它，要么用 throws 子句声明抛出它，否则编译不会通过。除了 RuntimeException 及其子类以外，其他的 Exception 类及其子类都属于可查异常。

- 不可查异常 (编译器不要求强制处置的异常):包括 运行时异常（RuntimeException 与其子类）和错误 （Error）。Exception 异常又可分两大类运行时异常和非运行时异常(编译异常) 。程序中应当尽可能去处理这些异常。

运行时异常：都是 RuntimeException 类及其子类异常 ，如 NullPointerException、IndexOutOfBoundsException 等，这些异常是不可查异常，程序中可以选择捕获处理，也可以不处理 。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。运行时异常的特点是 Java 编译器不会检查它，当程序中可能出现这类异常，即使没有用 try-catch 语句捕获它，也没有用 throws 子句声明抛出，也会编译通过。

## 异常处理

Java的异常处理本质上是抛出异常和捕获异常。
所谓运行时异常指的是 RuntimeException，你不用去显式的捕捉一个运行时异常，也不用在方法上声明。反之，如果你的异常只是一个 Exception，它就需要显式去捕捉。
虽然从异常的结构图我们可以看到，RuntimeException 继承自 Exception。但 Java 会“特殊对待”运行时异常。所以如果你的程序里面需要这类异常时，可以继承 RuntimeException。而且如果不是明确要求要把异常交给上层去捕获处理的话，我们建议是优先使用运行时异常，因为它会让你的代码更加简洁。

## 异常覆盖
在 finally 块执行代码的时候，是有可能抛出异常的。与此同时我们可能在 catch 块抛出了另一个异常。那么 catch 块抛出的异常就会被 finally 块的异常“吃掉”。

## 返回值覆盖
跟之前的“异常覆盖”问题类似，finally 块会覆盖掉 try 和 catch 块的返回值。所以最好是不要在 finaly 块使用 return!

## 总结

- 如果可以，尽量使用 RuntimeException

- 尽量不要在 finally 块抛出异常或者返回值