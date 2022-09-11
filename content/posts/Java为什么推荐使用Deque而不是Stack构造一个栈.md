---
title: Java 为什么推荐使用 Deque 而不是 Stack 构造一个栈
date: 2022-08-16 15:59:33
categories: 
- Java
tags:
- Java
---

前段时间开始从 C++ 转向使用 Java 来刷 LeetCode，在遇到队列和栈相关的题目时，我还是习惯性的凭直觉的输入 `Stack` 这个类来构建一个栈，`Stack` 类确实可以实现栈的功能。但是在查看题解时，发现 Java 的题解中大多数都是使用 `Deque` 这个接口来构建栈，于是深入研究了一下 `Stack` 和 `Deque` 这两种方式构建的栈的区别。

首先我查看了 `Stack` 的 JDK 源码，在源码的开头就发现 Java 推荐使用 `Deque` 来替代 `Stack` 来实现栈的功能。

>A more complete and consistent set of LIFO stack operations is provided by the Deque interface and its implementations, which should be used in preference to this class. For example:
> ```java
> Deque<Integer> stack = new ArrayDeque<Integer>();
> ````

## 原因

首先查看 Stack 的声明，Stack 是继承于 Vector 类的，Vector 类底层的实现方式是数组，并且 Vector 底层使用了 `synchronized` 关键字来保证线程安全，因此 Vector 和 继承于 Vector 的 Stack 的性能比较差。

此外，因为继承了 Vector 类，Stack 可以复用 Vector 的大量方法，这使得 Stack 在设计上不严谨，例如 Vector 中的 `public void add(int index, E element)` 方法可以在指定位置添加元素， 这与Stack 的设计理念相冲突（栈只能在栈顶添加或删除元素）。所以Java后来修正了这个不良好的设计，提出了用Deque代替Stack的建议。


## Deque 使用

既然 Java 更推荐使用 Deque 来代替 Stack，接下来仔细研究下 Deque 这个接口的细节。

Java 中的 Deuqe，即 `double ended queue`，是 Java 中的双端队列集合类型，它继承自Queue，它具备普通队列 FIFO 的特性，同时它也具备栈 LIFO 的特性。查看 Deque 的继承关系发现主要的两个实现类有 `ArrayDeque` 和 `LinkedList`，根据类名可以判断出，这两个类的主要区别是底层是基于数组还是链表实现的。

![Deque 继承关系](https://s2.loli.net/2022/09/02/M1kYI9lyGguD2Qj.png)

### ArrayDeque
- 数组结构实现
- 插入元素不能为 null
- 无法确定数据量时，后期扩容会影响效率

### LinkedList
- 链表结构实现
- 插入元素能为 null
- 无法确定数据量时，有更好性能表现

### Deque 栈操作相关方法

- `void push(E e);`：向双向队列的头部插入一个元素

- `E pop();`：从双向队列的头部移除一个元素

## 参考资料

- https://chengfeng96.com/blog/2018/01/20/Java%E4%B8%AD%E7%94%A8Deque%E6%8E%A5%E5%8F%A3%E4%BB%A3%E6%9B%BFStack%E6%8E%A5%E5%8F%A3%E5%AE%8C%E6%88%90%E6%A0%88%E5%8A%9F%E8%83%BD/
- https://www.cnblogs.com/code-duck/p/13569388.html