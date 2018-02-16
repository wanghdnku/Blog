---
layout: post
title: "Objective-C 内存管理概述"
subtitle: "ARC、MRC 和 GC"
date: 2018-02-08 11:47:38
author: "Hayden Wang"
header-img: "/images/memory.jpg"
cdn: 'header-off'
tags: [Objective-C]
---


C语言中需要手动利用`malloc()`和`free()`对内存进行管理。程序运行结束时，操作系统会释放为其分配的内存。但是对于长时间运行的程序，如果不及时释放不使用的内存，程序就可能因为内存不足而崩溃。有效的内存管理，能够提高程序的运行效率。

就像漏水一样，**程序未能释放不再使用的内存叫做_内存泄漏 (memmory leak)_**。如果访问已被释放的内存，就会造成数据错误，严重时会导致程序异常终止。**指针指向的对象已经被释放或收回，该指针就叫做_悬垂指针 (dangling pointer)_ 或野指针**。

## 1. 内存管理概述
Cocoa环境的Objective-C提供了一种动态的内存管理方式，称为**引用计数 (reference counter)。这种方式会跟踪每个对象被引用的次数，当对象的引用次数为0时，系统就会释放该对象占用的内存**，这种方式也称为基于引用计数的内存管理。

**_自动引用计数 (ARC, Auto Reference Counting)_** 使开发者不用考虑何时使用`retain`、`release`、`autorelease`来管理内存，它提供自动评估对象生存期的功能，在编译期间加入合适的管理内存的方法。

还有一种自动内存管理的机制——**_垃圾回收 (garbage collection)_**。使用垃圾回收时，不需要再通过引用计数来管理创建的对象，**系统会自动识别哪些对象仍在使用，哪些对象可以回收**。


## 2. 手动引用计数内存管理 MRC
引用计数就是指程序中到底有多少个地方需要访问这个对象。当使用`alloc`和初始化方法创建一个对象时，对象的引用计数初始值为1；当一个类需要使用该对象，为了防止其被别人释放，就先发送一个`retain`消息，使引用计数加1；反之，不再需要该对象时，通过发送`release`消息使引用计数减1。

负责释放内存的是`dealloc`方法。同`alloc`不同，`dealloc`是实例方法。当引用计数为0时，OC会自动向对象发送一条`dealloc`消息来释放内存。通常不允许在程序中直接调用`dealloc`方法。释放一个类的实例对象时，位了释放实例对象所保持的所有对象的所有权，必须重写`dealloc`方法：
```objectivec
- (void)dealloc {
    // 通过release方法放弃子类中所有实例变量的所有权
    [super dealloc];
}
```
### 2.1 安全的 setter 方法
```objectivec
- (void)setMyValue:(id)obj {
    [myValue release];          // 释放原来的myValue对象
    myValue = [obj retain];     // 持有新的myValue对象，即obj
}
```
但是当obj和原来的myValue是同一个对象时，就会发生错误。可以将`release`改为`autorelease`，或者改写成如下形式：
```objectivec
- (void)setMyValue:(id)obj {
    [obj retain];               // 首先持有obj对象
    [myValue release];          // 释放myValue对象
    myValue = obj;              // 将myValue转而指向obj
}
```

### 2.2 自动释放机制
自动释放机制的基本思想是：把所有需要发送`release`消息的对象都记录下来，等到需要释放这些对象的时候，回给这些对象一起发送`release`消息。类`NSAutoReleasePool`起到了记录的作用。

当标记为`autorelease`的时候，引用计数没有改变，对象还可以继续使用。只是原来的持有者放弃了所有权，转交给了自动释放池。当自动释放池被排干的时候，池中对象将被一并发送`release`消息。

便利构造函数 (convenience constructor)创建的对象时临时对象，生成之后会被直接加入到内部的自动释放池。这种方法生成得对象持有者不是调用该方法得对象。

GUI程序往往在休眠中等待用户操作。在用户发出动作之前，程序一直处于空闲状态，当事件发生后，程序会被唤醒并开始工作，执行某些必要的操作来相应这一事件。处理完后程序又回到休眠状态等待下一个事件。这个过程叫做**_运行回路 (Runloop)_**。Cocoa在程序开始时间处理之前隐式创建自动释放池，并在事件结束之后销毁，所以不必手动创建自动释放池，也可以使用临时对象。


## 3. 自动引用计数内存管理 ARC
ARC是一个“编译时”技术，通过在编译期间添加合适的`retain / release / autorelease`等函数，来确保对象被正确地释放。编译器会根据传入的变量是局部变量还是引用变量，返回对象的方法是不是初始化方法等信息来推断应当在何处插入。

### 3.1 使用ARC时需要遵守的规则
- 禁止调用引用计数的相关函数：`retain`、`release`、`autorelease`、`retainCount`。
- 禁止使用`NSAutoreleasePool`，使用新语法`@autoreleasepool`来管理自动释放池。新语法中可以使用`break`、`return`、`goto`等跳转语句，而且性能更好。
- ARC中未指定初始值的变量（包括局部变量）都会被初始化为`nil`。但是用`__autorelease`和`__unsafe_unretained`修饰的变量其初始值是未定的。
- 方法命名要遵循命名规则，不能随意定义以`a/c/i/n`开头且和所有权操作无关的方法。可持有对象的方法族：`alloc`、`copy`、`mutableCopy`、`new`、`init`。
- 不用在`dealloc`中释放实例变量（但可以在`dealloc`中释放资源），也不需要调用`[super dealloc]`。


## 4. 垃圾回收 Garbage Collection
**_垃圾回收_**是指在程序运行过程中不定时地检查是否有不在使用的对象，如果存在就释放它们占用的内存空间。在OS X 10.5 Leopard加入，iOS由于性能考虑不使用GC。

内存检查和回收都是由垃圾收集器 (garbage collector) 完成的。C风格变量、C风格结构体以及C风格申请的内存不属于垃圾回收范围。id类型、OC实例对象是垃圾回收的目标。

全局变量和静态变量引用的对象、栈内临时变量引用的对象称为根集合，如果一个对象的实例变量引用了不允许被回收的对象或根集合对象，那么这个对象不允许被回收。即：**通过全局变量、静态变量或者栈内变量的引用而查找到的对象都不可以被回收。**

> Cocoa中的GUI应用一定会包含类`NSApplication`的实例，它是一个名为`NSApp`的全局变量。这个变量属于根集合，不能被回收，所以所有引用了`NSApp`的窗口和菜单对象都不能被回收。

垃圾收集器的运行是自适应的，当程序运行过程中分配的内存超过一定量时，垃圾收集器就会被自动触发运行。垃圾收集器通常作为一个单独的线程运行，一个程序只有一个垃圾收集器。垃圾收集器并不会一找到不再使用的对象就立即释放，而是线发送`finalize`消息，当对象相应之后再释放对象。垃圾收集环境下释放对象时，不会执行对象的`dealloc`方法。


## 5. 内存管理方式的比较
### ARC 和 GC 相对于 MRC 的优点
- 不需要再在意对象的所有权
- 使代码更加简洁
- 避免手动内存管理时的错误（内存泄漏等）
- 不需要在意引用计数和内存管理中的一些特殊方法（临时对象等）
- 使多线程编程更加简单

### 垃圾回收 GC 的缺点
- 垃圾收集器运行影响程序的速度
- 需要不停监视内存使用，与引用计数相比，程序速度慢
- 不常使用的内存也会被垃圾收集器不时地访问，占用了更多内存
- 需要一些技巧来让对象被回收或者不被回收

### 使用 ARC 需要注意的地方
- 要注意循环引用
- 处理结构体、数组、二重指针等类型的变量时有一些限制
- 需要注意Core Foundation对象和Objective-C对象之间的转换


## Why doesn't iOS have automatic garbage collection?

[Discussion on StackOverflow](http://stackoverflow.com/questions/6385285/why-doesnt-ios-have-automatic-garbage-collection)

{% blockquote Morten Fast http://stackoverflow.com/questions/6385285/why-doesnt-ios-have-automatic-garbage-collection Stack Overflow %}
The problem with garbage collection is that memory usage grows until it's collected, so there might be more memory allocated than necessary. That's bad for devices with restricted memory and no option to swap.
When the garbage collector runs, it scans the heap to find memory that's no longer being used, and that's an expensive process, that will slow down your device until is has completed.
{% endblockquote %}

---
{% blockquote FreeAsInBeer http://stackoverflow.com/questions/6385285/why-doesnt-ios-have-automatic-garbage-collection Stack Overflow %}
At WWDC 2011, Apple explained that they didn't want garbage collection on their mobile devices because they want apps to be able to run with the best use of the provided resources, and with great determinism. The problem with garbage collection is not only that objects build up over time until the garbage collector kicks in, but that you don't have any control over when the garbage collector will kick in. This causes non-deterministic behavior that could lead to slowdowns that could occur when you don't want them.
**Bottom Line**: You can't say, "OK. I know that these objects will be freed at X point in time, and it won't collide with other events that are occurring."
{% endblockquote %}
 
---
{% blockquote JeremyP http://stackoverflow.com/questions/6385285/why-doesnt-ios-have-automatic-garbage-collection Stack Overflow %}
The main reason is probably memory load and performance. Reference counting has a smaller memory profile in that it allows the amount used by the application grow much more than reference counting. Also, there is a performance issue in that when the garbage collector runs, other threads have to be stopped. This is not really a huge issue on Macintoshes with fast multicore processors but could cause the UI to stutter on mobile devices.
The debate may be moot soon anyway. Clang / LLVM has just had a new feature added called automatic reference counting. This leverages the analysing capability to automatically put in the retains, releases and autoreleases so that the programmer doesn't have too.
{% endblockquote %}
