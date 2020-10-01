# 关于C#委托

## 前言

当我们对C#的学习到达一定阶段时，便会主动或者被动地了解到委托，那么，委托是什么，有什么用，又应该怎么用，本文将会一一阐述

## 主体

**委托提供了一种可以直接访问类中方法的途径，可以将方法作为方法的参数进行传递使用**

### delegate

提到C#的委托，总是绕不过delegate。那么delegate是什么呢？delegate是一个微软再C#中引入的类型关键字。使用它，我们就可以在程序中声明一个委托，之后再实例化这个这个委托即可，我们先看一下下面的代码

```c#
 //声明一个委托
 public delegate void myDelegate();

 //定义用于委托的方法
 public static void WriteHello()
 {
     Console.WriteLine("Hello World");
 }
```

上面两段代码分别声明一个委托和定义用于该委托的方法，之后在使用委托的地方传入上面定义的方法实例化该委托，在之后即可调用

```C# 
 //使用上面定义的方法实例化该委托
 myDelegate _myDelegate = new myDelegate(WriteHello);
 //调用委托的方法
  _myDelegate();

 //运行程序，会在控制台输出 Hello World
```

### Action、Func、Predicate以及lambda表达式

上面提到的delegate委托是很早以前提出的语法，随着C#的不断进阶，值delegate之上又封装除了现在我们比较常使用的Action、Func、Predicate三种语法，分别为无返回值委托，有返回值委托以及判断委托

#### lambda表达式

在讲解委托新语法前，要先介绍一下其配套使用的lambda表达式

lambda表达式是被视为一个对象的代码（表达式或语句块）的块。它可以作为参数传递给方法，也可以通过方法调用返回，使用 `=>` 来表示。

#### Action

Action主要用于定义无返回值委托，顾名思义，该类型委托方法类型为void（同步）或者Task（异步），不会产生返回值，比如，下面的额代码展示了一个简单的Action委托

```C#
 //定义一个Action委托及其实现方法
 Action action = () =>  Console.WriteLine("Hello World");

 //调用该方法
 action();

 //程序运行后，会在控制台打印输出 Hello World
```

#### Func



#### Predicate



### 匿名方法





