---
layout: post
title: "Objective-C 中的块对象"
subtitle: "Block 的使用方法与特性分析"
author: Hayden Wang
header-img: keyboards.jpg
cdn: header-on
tags: [Objective-C]
date: 2018-02-11 02:07:30
---

**Block是带有自动变量的匿名函数。**

完整形式的Block语法与一般的C语言函数定义相比，仅有两点不同：
1. 没有函数名；
2. 带有`^`。

## 一、Block的定义

```
^ [返回值类型] [(参数列表)] {表达式}
```

### 1. 完整写法

```objectivec
^ int (int count) { return count+1; }
```

### 2. 省略返回值类型

省略返回值类型时，如果表达式中有`return`语句就使用该返回值类型，如果表达式中没有`return`语句就使用`void`类型，表达式中含有多个`return`语句时，所有`return`的返回值类型必须相同。

```objectivec
^ (int count) { return count+1; }
```

### 3. 省略返回值类型和参数列表

```objectivec
^ { printf("Block"); }
```

## 二、Block的类型声明

```objectivec
返回值类型 (^变量名) (参数类型)

// example:
void (^devowelizer) (id, NSInteger, BOOL);
```

### 1. 将Block赋值为Block类型变量：声明一个名为`blk`的变量。
```objectivec
int (^blk) (int) = ^(int count) { return count+1; };
```

### 2. 使用`typedef`来声明Block变量

通过`typedef`关键字，可以将某个Block对象定义为一个新类型，以方便使用。应该在头文件或者实现文件顶部定义`typedef`。

```objectivec
typedef int (^blk_t) (int);
blk_t blk = ^(int count) { return count+1; };
```

向函数传递Block类型参数：
```objectivec
// 常规方法
void func(int (^blk) (int)) {
    // do something
}

// typedef方法
void func(blk_t blk) {
    // do something
}
```

函数有Block类型返回值：
```objectivec
// 常规方法
int (^func()) (int) {
    return ^(int count) { return count+1; }
}

// typedef方法
blk_t func() {
    return ^(int count) { return count+1; }
}
```

## 三、Block捕获机制

Block对象通常会使用外部创建的其他变量（基本类型变量，或者是指向其他对象的指针）。这些外部创建的变量叫做外部变量(external variables)。当执行Block对象时，为了确保其下的外部变量能够始终存在，相应的Block对象会捕获(captured)这些变量。

- 对于基本类型的变量，捕获意味着程序会**拷贝变量的值**，并用Block对象内的局部变量保存。
- 对指针类型变量，Block对象会使用**强引用**，这意味着凡是Block对象用到的对象都会被保留。所以在相应的Block对象被释放前，这些对象一定不会被释放。（这也是Block对象和函数之间的差别，函数无法做到这点）。

**“带有自动变量值”在Block中表现为“截获自动变量值”。**

- Block中，Block表达式截获所使用的自动变量值，即保存该自动变量的瞬间值。
- 在Block中给自动变量赋值，会产生编译错误。若想在Block内给外部变量赋值，需要加上`__block`说明符。
- 截获`Objective-C`对象时，在Block内向对象发送消息，不会产生编译错误。


### 避免self强引用
如果要在一个Block中使用self，需要做几步工作来避免强引用循环。
1. 在Block外部声明self的弱引用（解除引用循环）
2. 在Block内部对外部得弱引用进行强引用（内部变量会在Block结束时销毁，强引用消失）
```objectivec
__weak NSObject *weakSelf = self;
myBlock = ^{
    NSObject *strongSelf = weakSelf;
    NSLog(@"self: %@", strongSelf);
}
```

**Block对象中无意使用self的情况**

在Block对象中使用实例变量，那么Block会捕获self而不是实例变量。因为`_var`在编译器被解读为`self->_var`。因此要用内部的强引用调用实例变量的存取方法。
```objectivec
__weak NSObject *weakSelf = self;
myBlock = ^{
    NSObject *strongSelf = weakSelf;
    NSLog(@"self: %@", strongSelf);
    // NSLog(@"%@", _var);
    NSLog(@"%@", strongSelf.var);
}
```

## 四、Block对象的返回值

对于有返回值的Block对象，可以像调用函数那样调用Block对象，然后使用其返回值。

---

> 通过Block，可以将回调得设置代码和回调方法写在一段代码中。而Delegation和Notification机制里两者相隔很远。


## References

> - [1]《Objective-C高级编程》
> - [2]《Objective-C编程》