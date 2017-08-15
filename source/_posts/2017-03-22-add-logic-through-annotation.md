---
title: 如何通过注解添加逻辑
date: 2017-03-22 11:00:03
categories:
- 方案
tags: 
- java
- android
- annotation
---
#### 做什么？？

近期在为公司的项目做埋点。 因为之前做过几次， 深知这个东西的麻烦， 要频繁的往代码中添加重复逻辑。什么onStart, onStop全部都重写一遍， 做为一个码农，咱怎么也算是脑力劳动者， 什么时候变成干体力活了。研究了两天，绕了一个大弯， 终于找到了方法。记录一下这个过程。

#### 注解处理器
第一感觉是不是这个东西要用到编译时注解， 因为咱的目的是在编译时添加逻辑嘛。哼哧，哼哧。。。。
    http://www.open-open.com/lib/view/open1470735314518.html

#### 构造代码
添加逻辑就是要自己构造代码喽。一般都是用方块的javapoet. 继续哼哧，哼哧。。。。
    http://www.jianshu.com/p/95f12f72f69a
    https://github.com/square/javapoet 

#### 修改文件?
哦。不对javapoet只能用来生成新的java文件。 不能用来修改class文件， 我们的目的是要修改生成的class文件啊。

#### nuwa怎么做到的
记得之前热更新插件中可以修改class文件的， nuwa就是通过修改class文件来达到使类具有热修复能力的。 继续哼哧，哼哧。。。。
    https://github.com/jasonross/Nuwa

#### asm字节码
原来用的asm字节码工具啊。 继续哼哧， 哼哧。。。。
    https://www.ibm.com/developerworks/cn/java/j-lo-asm30/
    http://asm.ow2.org/

#### 此路不通
发现问题了啊。注解处理器无法处理修改类。同时注解处理器的处理时机是在编译之前， 即这里还没有生成class文件。 完蛋， 此路不通啊。。。。

#### gradle的task
即然注解处理器的处理时机不对，不如我们自己来控制处理时机喽。在gradle的编译task上下文章。在java的compileJava task后面， 或者在 android的compileXXXSources task后面执行 asm的字节码处理。 看下面两个图:
<center>![java](java-task.png)  ![android](android-task.png)</center>

#### 效率问题
但是这个效率太低了啊。 不像注解处理器一样， asm无法专门找到和处理某个annotation. 难倒要让asm扫描所有的类文件， 找到指定的annotation?? 

#### 注解处理器与gradle相结合
只能想方法喽。 把注解处理器和gradle编译结合起来。先通过注解处理器把包含注解的类过滤出来， 然后在gradle的编译task后使用asm处理过滤出来的类。 

#### 找找别的办法
貌似逻辑上可以走通哦，但总是感觉有那么一点点别扭。找找有没有别的办法。国内毛都查不到， 还是老外见多识广。
    http://stackoverflow.com/questions/4851429/modify-a-method-using-annotations

#### 大牛回复
他跟我要实现的效果差不多。初始想法好像也是跟我差不多， 想用annotation processor来处理。 但是被第一个回复的大牛能否决了啊。 大牛还摘了一段wiki的原话， 意思是不能修改原annotation标注的类云云。但是我找了一下， 可能是版本升级吧。 大牛最关键的`but annotation processors cannot modify the annotated code itself.` 一句， 已被wike改成了`and also modify the annotated code itself`。 好像还是可以改， 所以回到第6步， 看来还是能用 annotation processor. 但在编译前的注解处理器，估计只能改java原文件吧。 class还没有编译出来啊。这不是我想要的。 

#### hugo怎么做的
再想想办法. 记得之前用过大神jake walton的log神器hugo. 可以通过一注解来打印函数的变量， 执行时间等等。 显然这只能是通过注解把逻辑注入到了函数中实现的。
    https://github.com/JakeWharton/hugo

#### AspectJ
对于实现原理， 只能拿来了。 果然他用了另一种切面语言 AspectJ注入的逻辑。最初上面的老外那个问题， 大牛也提出了使用`AspectJ Development Tools`的解决方案。同时还不依不饶的让楼主使用。 我最初没在意， 以为就是一个eclipse的源码处理插件。通过ide的编译器做到的识别annotation插件代码， 类似搜索替换. 但通过hugo的使用， 看来是通用的。
    http://blog.csdn.net/xxxzhi/article/details/53048476
    http://blog.csdn.net/hp910315/article/details/52701809

#### 方案确定
在此， 确定了方案为使用 aspectJ来实现通过注解添加逻辑。一切参照hugo的代码实现即可。 我的简易实现代码.
    https://github.com/masonTool/AspectJDemo

#### AspectJ粗浅的理解
对于AspectJ， 这里记录一个粗浅的理解。
1. AspectJ可以理解为java的拓展。因为他是兼容java的。 
2. AspectJ编译时使用自己的编译器ajc
3. 是一种面向切面的语言。 切面与模块是相对应的。如果把面向对象形容为纵向， 则切面可以理解为横向。特别适合做log, 权限管理，埋点， 等等。将逻辑切入对象，同时又不破坏对象的逻辑独立性和耦合性。
4. 一个最关键的概念就是Join Points. 翻译为"切入点". 在Java中他定义了很多切入点，比如函数调用前， 函数执行前，函数执行后， 变量get时， 变量set时。。。总之发挥你的想象，基本上你想切入的点他都定义了。
5. 怎么做到切入某个点呢？很自然， 首先筛选出想要切入的点(pointcut)， 然后处理筛选出来的点的逻辑(advice). 就是这样了。然后使用ajc去编译就可以了。
6. 注意：如果你想在jar包中或者aar中使用aspectJ,不只需要使用ajc去编译生成包， 同时引用者也要使用ajc去编译才行。

如果不想啃官方文档  https://eclipse.org/aspectj/docs.php
这篇文章可以带你入门  http://blog.csdn.net/innost/article/details/49387395

#### 一点改进
要想使用AspectJ不只需要引用`org.aspectj:aspectjrt:1.8.6`就完事了, 这个包只是让你编写代码用的，还需要自己处理编译逻辑。个人认为这个地方可以做得更简易一些， 就像groovy插件的自动编译一样。 

所以我这里写了一个简易的gradle插件，copy到你的工程apply一下，就可以任性的编写aspectj代码，同时你也不用理会编译的事情了。
https://github.com/masonTool/AspectJDemo/blob/master/aspect_compile.gradle

你可以直接使用，或者只做很小的改动来使用.当然这还只一个demo版本，但如果你是像我一样的新手，同时又不需要过多的高级配置的话，这已经足够好了.有时间我会把他以gradleplugin的形式发布到jcenter，让新手可以更方便的使用。

<center>![tt](qc.jpg)</center>


