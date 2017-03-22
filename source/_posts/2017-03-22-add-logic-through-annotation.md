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
在此， 确定了方案为使用 aspectJ来实现通过注解添加逻辑。一切参照hugo的代码实现即可。 我的简易实现代码， 稍后补上。 

