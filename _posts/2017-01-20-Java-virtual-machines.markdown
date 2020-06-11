---
layout:     post
title:      "初识JAVA虚拟机"
subtitle:   "了解 Java Virtual Machines 各个版本之间的差异。"
date:       2017-01-20
author:     "Chenyan"
header-img: "img/post-bg-universe.jpg"
catalog:    true
tags:
    - Java program
    - Java virtual machines
---
>最近公司项目上线，需要去机房部署上线程序，然后进行数据迁移并且与核心业务系统挂接，在这段时间中，遇到了各种系统配置问题。之从未深入了解过Java虚拟机方面的知识，只是仅仅作为一款主流开发语言进行程序设计，却不曾想过在生产环境则需要掌握更深的知识。

要说JVM是什么，首先得区分清楚场景

光谈部署量的话，搞不好现在部署量最多的JVM是Dalvik / ART…虽然Google会告诉大家Dalvik和ART不是“JVM”，但大家都知道骨子里它就是不折不扣的JVM，毫无疑问。它们的设计处处有标注对JVM规范的参考，以保证语义符合JVM规范的要求；那个基于寄存器的字节码设计只是一种实现优化而已。

![image description](https://jiafeiyan.github.io/chenyan/img/2017-01/JVMversion.png)

##### Java SE / Java EE

在这个类别下，主流选择有：（按流行程度递减）

- HotSpot VM
- J9 VM
- Zing VM

###### Hotspot VM

HotSpot VM是绝对的主流。大家用它的时候很可能就没想过还有别的选择，或者是为了迁就依赖了Oracle/Sun JDK某些具体实现的烂代码而选择用HotSpot VM省点心。

Oracle / Sun JDK、OpenJDK的各种变种（例如IcedTea、Zulu），用的都是相同核心的HotSpot VM。
从Java SE 7开始，HotSpot VM就是Java规范的“参考实现”（RI，Reference Implementation）。把它叫做“标准JVM”完全不为过。

当大家说起“Java性能如何如何”、“Java有多少种GC”、“JVM如何调优”云云，经常默认说的就是特指HotSpot VM。可见其“主流性”。

其中，JDK8的HotSpot VM已经是以前的HotSpot VM与JRockit VM的合并版，也就是传说中的“HotRockit”，只是产品里名字还是叫HotSpot VM。
这个合并并不是要把JRockit的部分代码插进HotSpot里，而是把前者一些有价值的功能在后者里重新实现一遍。移除PermGen、Java Flight Recorder、jcmd等都属于合并项目的一部分。

###### J9 VM

J9是IBM开发的一个高度模块化的JVM。

在许多平台上，IBM J9 VM都只能跟IBM产品一起使用。这不是技术限制，而是许可证限制。
例如说在Windows上IBM JDK不是免费公开的，而是要跟IBM其它产品一起捆绑发布的；使用IBM Rational、IBM WebSphere的话都有机会用到J9 VM（也可以自己选择配置使用别的Java SE JVM）。
根据许可证，这种捆绑在产品里的J9 VM不应该用于运行别的Java程序…大家有没有自己“偷偷的”拿来跑别的程序IBM也没力气管（咳咳

而在一些IBM的硬件平台上，很少客户是只买硬件不买配套软件的，IBM给一整套解决方案，里面可能就包括了IBM JDK。这样自然而然就用上了J9 VM。

所以J9 VM得算在主流里，虽然很少是大家主动选择的首选。

J9 VM的性能水平大致跟HotSpot VM是一个档次的。有时HotSpot快些，有时J9
快些。
不过J9 VM有一些HotSpot VM在JDK8还不支持的功能，最显著的一个就是J9支持AOT编译和更强大的class data sharing。

###### Zing VM

![image description](https://jiafeiyan.github.io/chenyan/img/2017-01/ZingJVM.png)

Zing VM是一个从Sun HoSpot VM fork出来的一个高性能JVM，可以运行在Linux/x86-64平台上。Azul为它重新写了一套GC，也修改了VM内的许多实现细节，所以从我们自己的角度看，与其说它是HotSpot VM的一个变种，还不如把它看作“一个全新的JVM、只是凑巧与HotSpot VM很像”更合适。

在要求低延迟、快速预热等的场景里，Zing VM都会比HotSpot VM表现更好。
Zing的C4 GC目前可以支持1TB的-Xmx，而且GC暂停时间仍然可以维持在< 10ms的范围里。实际上Zing现在能支持接近2TB的-Xmx。
Zing的ReadyNow!功能可以利用之前收集到的profile数据，引导JVM在启动后快速达到稳定的高性能水平，减少启动后从解释执行到JIT编译的等待时间。
Zing自带的ZVision / ZVRobot功能可以方便用户监控JVM的运行状态，从找出代码热点到对象分配监控、锁竞争监控等都可以做到。

最关键的是，Zing能让普通没有为GC之类的底层方向调优的Java应用享有低延迟、快速预热、易于监控的功能，为用户节省苦苦调优的精力和时间。这是Zing的主要价值——很多Java应用都可以通过长期努力在应用/框架层面优化来提升性能，但使用Zing的话就可以更多把精力集中在业务方面。

##### 研究性质的JVM


去找比较新的、跟JVM相关的学术界的研究论文，基本上就下面几个JVM可选：
 
 - Jikes RVM
 - Maxine VM
 - Graal VM
 
这仨就算是现在的主流研究性JVM吧。

###### Jikes RVM

Jikes RVM是IBM开发的专门用来研究JVM实现技术的项目。曾用名Jalapeño。
它是一个元循环虚拟机（metacircular VM），整个JVM都是用Java实现的。

最有趣的地方是它有一个名为MMTk的框架：Jikes RVM - MMTk，可以很方便的编写新的GC算法插入到Jikes RVM里去，而且已有的实现的选择也很丰富。所以很多研究GC算法的论文会选择基于Jikes RVM/MMTk来做初步实现，不但做起来容易，还可以很方便的与别的算法对比。

###### Maxine VM

Maxine VM是Oracle/Sun Labs开发的研究项目。跟Jikes RVM一样是元循环虚拟机。
Maxine VM自从有了C1X/T1X编译器之后，性能似乎就超过了Jikes RVM；有了Graal编译器之后更加压倒Jikes RVM。做JIT编译器研究的话我会选择用Maxine VM。

###### Graal VM

Graal VM就是Maxine VM的Graal编译器插在HotSpot VM上。现在Oracle Labs和一些大学做JIT编译器研究都是基于Graal VM来做的，效果出奇的好，性能基本跟HotSpot Server Compiler（C2）持平甚至有所超越。势头正猛。