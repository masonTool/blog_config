---
title: proguard点滴
date: 2017-06-06 21:29:20
categories:
- 方案
tags:
- proguard
- android
---

## 关于混淆
proguard不止能混淆, 还会做代码裁剪, 方法内联等等优化, 一般在Android工程里, 我们用的最多的是裁剪(shrink), 优化(optimize)和混淆(obfuscate), 其中代码裁剪, 特别是对方法的裁剪, 是降低dex方法数的一条捷径. 在日常使用中, 我们把proguard的裁剪, 优化, 混淆等一系列步骤笼统的称为混淆

1. shrink裁剪. 会将一些无效代码给移除，即没有被显式调用的代码
2. optimize优化. 基于控制流、数据流分析后，删除、合并一些代码. 如给类加final, 内联方法， 合并方法等。。
3. obfuscate混淆. 混淆类名、属性名、方法名、变量名等，变成无意义的类似a,b,c,d...的名字

## 第三方SDK的混淆:
1. SDK必须keep的部分, 如使用了反射等技术时需要做对应的keep
2. 对公开API的keep, 这部分如果不keep, SDK接入方根本没法用. 但是SDK接入方并不可能使用到SDK里所有的公开API



## 科学做法

### 提取共性

activity类名必须keep

    -keep public class * extends android.app.Activity 

JNI方法必须keep

    -keepclasseswithmembers,includedescriptorclasses class * {
        native <methods>;
    }
其中 includedescriptorclasses, 加上它就能保证方法参数的类型不被混淆

keep住所有会在xml中使用的控件

    -keepclasseswithmembers class * { 
        public <init>(android.content.Context, android.util.AttributeSet);
    }

### 使用注解

定义注解

    @Retention(RetentionPolicy.CLASS)
    @Target({ElementType.TYPE, ElementType.ANNOTATION_TYPE, ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.FIELD})
    public @interface Keep { }

proguard文件中定义规则

    -keep,allowobfuscation @interface com.shaw.Keep
    -keep @com.shaw.Keep class * {
        *;
    }
    -keepclasseswithmembers class * {
        @com.shaw.Keep <fields>;
    }
    -keepclasseswithmembers class * {
        @com.shaw.Keep <init>(...);
    }
    -keepclasseswithmembers class * {
        @com.shaw.Keep <methods>;
    }

### 使用aar

consumerProguardFiles  是aar的接入方在构建时会使用的混淆规则
proguardFiles  是用于构建aar的混淆规则

注：consumerProguardFiles这个属性只能在com.android.library即aar工程中使用。 这个很好理解。 但是consumerProguardFiles的规则是合并到调用方的proguard中一起在编译中生效的。 所以如果一个aar包中使用了

    -keep class com.** { *; }

那SDK接入方com包下的一切都没法混淆了.

## 附

### proguard时机
<center>![编译流程](java-task.png)</center>

### 参考

* http://blog.csdn.net/jjwwmlp456/article/details/44977721
* http://www.jianshu.com/p/14af4a474d55
* https://www.guardsquare.com/en/proguard/manual/usage

## 联系

* 访问我的个人博客 [马培羽][1]
* 邮件 [mason.mpy@gmail.com](mason.mpy@gmail.com)

[1]: http://www.mapeiyu.com
