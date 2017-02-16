---
title: Java Debug 之 命令行调试 （一）
date: 2017-02-16 09:52:27
categories:
- Java Debug
tags: 
- java
- debug
---

最近在研究编译时注解的一些东东。但是发现调试貌似有点麻烦， 于是找了网上的一些资料也找到了一些处理方法。 但是都是拿来主义， 为什么我们自己不能研究出来解决方法呢？ 本着刨根问底的态度， 决定先研究一下debug的原理。本篇做为系列的第一篇， 从一个小白的角度出发， 一步步把debug研究透彻。要研究一个协议， 先找一个入手点。 平时大家都是用ide来调试， 但是本质一定是有一套协议的， 所以我们先从命令行入手。

## 准备

* 首先你要有一个java环境（费话么）。相信对这个主题感性趣的人，这个条件一定是具备的。
* 准备一个java工程。一个简单的.java文件也可以。这里我提供一个demo。 为了省事拿自己之前写的小工具做例子吧。 希望你能down下来， 后的讲解都是以这个demo为基础的. 路径为 [reflect工具](https://github.com/masonTool/reflect)
* 本文基于ubuntu环境。其他环境都不多。

## 动手

* 进入命令行， 选一个目录开始我们旅程.

* 工程拉下来没有？ 没有就执行一下git clone 

		
        git clone https://github.com/masonTool/reflect

* 编译工程。 按下面的步骤一步步执行


		//进入工程目录
        cd reflect  

        //创建类目录, 名字whatever, 用于存放类文件
        mkdir aaaa  

        //编译依赖. 我们是要编译sample里面的Main.java, 因为Main.java是java的入口啊。
        //说明一下:  -g 调试信息选项, 否则不能查看调试过程中的调试信息. 这也是ide的debug模式与release模式的区别
                    -d 指定类文件编译进我们刚创建的aaaa目录里面
        javac -g -d aaaa reflect/src/main/java/com/mason/meizu/reflect/*.java 

        //编译目标类 Main.java 和 prvclass/下的所有的java文件。
        //此处选项 -cp 实际是 -classpath. 指定编译上述java文件的依赖类的路径。 刚刚我们编进了aaaa, 此处用一下。
        //同时也指定了 -d aaaa , 所以此次编译的结果也放进 aaaa 目录
        javac -g -d aaaa -cp aaaa sample/src/com/mason/meizu/sample/prvclass/*.java sample/src/com/mason/meizu/sample/Main.java

* OK啦， 我们需要调试的资源就具备了。 进入 aaaa目录， tree 一下， 你看到的应该是这样

        cd aaaa

        tree //如果没有命令就 sudo apt-get install tree 装一个

		└── com
		    └── mason
		        └── meizu
		            ├── reflect
		            │   ├── RClass.class
		            │   ├── RExecutor.class
		            │   ├── RInstance.class
		            │   ├── RInterface.class
		            │   ├── RInterface$ProxyHandler.class
		            │   └── RParam.class
		            └── sample
		                ├── Main.class
		                ├── Main$Demo.class
		                └── prvclass
		                    ├── ClassA.class
		                    ├── ClassB.class
		                    ├── ClassC.class
		                    ├── ClassD4Listener.class
		                    └── ClassD4Listener$Listener.class

## 进入正题

* 进入调试模式
		
        jdb com.mason.meizu.sample.Main

    结果

        Initializing jdb ...
		> 

* 设置断点

	输入

        stop at com.mason.meizu.sample.Main:30//此处30指定断点在源文件的第30行

    结果

    	Deferring breakpoint com.mason.meizu.sample.Main:30.
		It will be set after the class is loaded.

    运行程程，到断点处停止。 输入

        run

    结果

        run com.mason.meizu.sample.Main
		Set uncaught java.lang.Throwable
		Set deferred uncaught java.lang.Throwable
		> 
		VM Started: Set deferred breakpoint com.mason.meizu.sample.Main:30

		Breakpoint hit: "thread=main", com.mason.meizu.sample.Main.main(), line=30 bci=10

		main[1] 

	看一下当前的变量, 输入 

		locals
   
    结果

		Method arguments:
		args = instance of java.lang.String[0] (id=408)
		Local variables:
		clazzA = instance of com.mason.meizu.reflect.RClass(id=409)
		main[1]

    看一下变量clazzA的详细情况, 输入

        dump clazzA

    结果

        clazzA = {
		    sClassMap: instance of java.util.HashMap(id=410)
		    sConstructorMap: instance of java.util.HashMap(id=411)
		    className: "com.mason.meizu.sample.prvclass.ClassA"
		    classObj: instance of java.lang.Class(reflected class=com.mason.meizu.sample.prvclass.ClassA, id=407)
		    com.mason.meizu.reflect.RExecutor.sMethodMap: instance of java.util.HashMap(id=413)
		    com.mason.meizu.reflect.RExecutor.sFieldMap: instance of java.util.HashMap(id=414)
		}
		main[1] 

	看一下当前运行的线程. 输入

		where all

    结果

        Signal Dispatcher:
		Finalizer:
		  [1] java.lang.Object.wait (native method)
		  [2] java.lang.ref.ReferenceQueue.remove (ReferenceQueue.java:143)
		  [3] java.lang.ref.ReferenceQueue.remove (ReferenceQueue.java:164)
		  [4] java.lang.ref.Finalizer$FinalizerThread.run (Finalizer.java:209)
		Reference Handler:
		  [1] java.lang.Object.wait (native method)
		  [2] java.lang.Object.wait (Object.java:502)
		  [3] java.lang.ref.Reference.tryHandlePending (Reference.java:191)
		  [4] java.lang.ref.Reference$ReferenceHandler.run (Reference.java:153)
		main:
		  [1] com.mason.meizu.sample.Main.main (Main.java:30)
		main[1] 


* 类似的命令还有如下， 建议你都试一下

    * next  是执行下一步，相当于Eclipse中的F6
    * stop in 进入函数实现, 相当于Eclipse中的F5
    * stop out 运行完当前函数, 相当于Eclipse中的F7

## 总结

本文介绍了一个命令行编译与命令行调试的过程。 这部分工作在日常的开发中都被我们的ide做了。 如果觉得不详细， 看看那个真男人是怎么告诉我们的:

    man jdb

抽时间我再把他翻译一下， 方便理解。


## 放松一下吧。

<center>![福利](ssssss.jpg)</center >




