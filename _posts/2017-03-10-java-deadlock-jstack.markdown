---
layout:     post
title:      "JAVA简单实现死锁例子以及Jstack分析死锁"
subtitle:   "学习java多线程死锁以及分析方法"
date:       2017-03-10
author:     "Chenyan"
header-img: "img/post-bg-deadlock.jpg"
catalog:    false
tags:
    - Java
---
> 在计算机系统中有很多一次只能由一个进程使用的资源，如打印机，磁带机等。在多道程序设计环境中，若干进程往往要共享这类资源，而且一个进程所需要的资源不止一个。这样，就会出现若干进程竞争有限资源，又推进顺序不当，从而构成无限期循环等待的局面。这种状态就是死锁。系统发生死锁现象不仅浪费大量的系统资源，甚至导致整个系统崩溃，带来灾难性后果。所以，对于死锁问题在理论上和技术上都必须给予高度重视。

#### 程序例子代码

```

public class DeadLockDemo {
	public static String s1 = "s1";
	public static String s2 = "s2";

	public static void main(String[] args) {
		new Thread(new T1()).start();
		new Thread(new T2()).start();
	}
}

class T1 implements Runnable {
	@Override
	public void run() {
		while (true) {
			try {
				// 锁住T1对象
				synchronized (T1.class) {
					System.out.println("T1" + DeadLockDemo.s1);
					Thread.sleep(2000);
					// 锁住T2对象
					synchronized (T2.class) {
						System.out.println("T1" + DeadLockDemo.s2);
					}
				}
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}

class T2 implements Runnable {
	@Override
	public void run() {
		while (true) {
			try {
				// 锁住T2对象
				synchronized (T2.class) {
					System.out.println("T2" + DeadLockDemo.s1);
					Thread.sleep(2000);
					// 锁住T1对象
					synchronized (T1.class) {
						System.out.println("T2" + DeadLockDemo.s2);
					}
				}
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
```
#### Jstack分析
1. 查询进程PID
![image description](http://www.chenyan.pro/img/2017-03/deadlock_1.png)
2. 调用自带的Jstack工具分析<br />
    可以发现Thread-1和Thread-0两个线程已经互相锁住
![image description](http://www.chenyan.pro/img/2017-03/deadlock_2.png)