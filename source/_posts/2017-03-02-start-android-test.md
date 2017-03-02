---
title: Android自动化测试入门篇
date: 2017-03-02 15:40:01
categories:
- Android自动化测试
tags:
- test
- android
---
Android的测试是基于[JUnit](http://junit.org/)的。 你可以基于JVM进行本地的单元测试(Local unit tests)， 也可以基于Android设备进行设备化的测试(instrumented tests)。 本文对构建Android测试的概念和工具， 进行一些简要的介绍.

## 测试类型

对于你的项目中的任何一个模块，Android Studio提供了两个测试代码的目录。 你的测试代码必须写在这两个目录之中。这个两个目录就对应了相应的两个测试方法。 

* 本地单元测试(Local unit tests)

	目录位于 `module-name/src/test/java/.`

	这些测试代码运行于本地JVM, 并且不会调用任何的Android框架的API. 

	如果你想了解详情， 请参阅[构建本地单元测试](https://developer.android.com/training/testing/unit-testing/local-unit-tests.html)

* 设备化测试(instrumented tests)

	目录位于 `module-name/src/androidTest/java/.`

	这些测试代码必须运行在Android硬件设备或者虚拟机上。

	设备化测试在测试app时， 会同时构建一个测试APK运行在设备上。系统会把测试APK与app运行在同一个进程下， 所以测试代码中可以调用app的方法, 修改app的变量, 并且自动执行你的app的各种交互.

	相要了解如何创建设备化测试， 请关注下面的主题：

	* [构建设备化单元测试](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html) : 构建存在Android系统依赖的复杂单元测试案例（在模拟对象无法满足的情况下）。

	* [自动化界面测试](https://developer.android.com/training/testing/ui-testing/index.html): 构建测试案例验证用户界面行为的正确性（单应用的交互或者跨多应用的交互）。

	* [测试App组件](https://developer.android.com/training/testing/integration-testing/index.html): 验证组件的行为， 比如 Service 或 Content Provider等用户不能直接交互的组件。

	<center>![tt](test-types_2x.png)</center>

然而, 本地单元测试和设备化测试仅仅是术语, 用来帮助区分是在本地JVM上运行还是在Android平台上运行的(硬件设备或模拟器)。在构建一个完整的测试案例时，你应该通过下面表中的描述来理解真正的测试类型。

|类型|子类型|描述|
|-|-|-|  
|单元测试(Unit tests)|本地单元测试(Local Unit Tests)|运行于本地虚拟机(JVM)的单元测试. 当你的测试有没有Android框架依赖或者这些依赖可以模拟时， 使用这种测试可以使执行时间最少化.|
||设备化单元测试(Instrumented unit tests)|运行于Android设备或虚拟机的单元测试. 这种类型的测试可以获得[Instrumentation](https://developer.android.com/reference/android/app/Instrumentation.html)信息， 比如你正在测试的app的上下文信息 [Context](https://developer.android.com/reference/android/content/Context.html). 当你的测试有不能被模拟的Android框架依赖时，使用这种测试。|
|集成测试(Integration Tests)|应用内组件测试(Components within your app only)|在交互中当用户执行一个特定的行为，或者键入一个特定的输入， 这种类型的测试可以验证目录app的行为是否符合预期。比如说， 当用户在应用的Activity界面中进行交互时，它可以让你检查目标app的界面响应是否正确。像[Espresso](https://developer.android.com/tools/testing-support-library/index.html#Espresso)这种UI测试框架, 可以让你用编程的方式去模拟用户的行为，和检测应用内部复杂的交互逻辑.|
||跨应用组件测试(Cross-app Components)|这种类型的测试， 可以验证不同的用户app间，用户app与系统app间的交互行为的正确性。比如说， 你可以验证当用户点击Android系统设置按钮时，你的app的行为是否正确。支持跨应用的交互框架，比如[UI Automator](https://developer.android.com/topic/libraries/testing-support-library/index.html#UIAutomator)允许你在这种场景下创建测试。|

## 测试的API

以下为测试Android应用常用的api

### JUnit

你可以像写 [JUnit4](http://junit.org/)测试类一样写你的单元测试和集成测试。JUnit框架提供了一种便捷的方式在你的测试中进行安装， 卸载， 和断言操作。

一个基本的Junit4测试类是一个拥用一个或者多个测试方法的Java类。一个测试方法由` @Test `注解开始， 并且包含一段可以运行和验证单一功能的代码。

下面的代码片段展示了一个JUnit 4集成测试的例子， 例子中使用[Espresso APIs](https://developer.android.com/topic/libraries/testing-support-library/index.html#Espresso) 在一个UI元素上执行点击操作， 然后检查是否有一个预期的字符串展示。

 ```
@RunWith(AndroidJUnit4.class)
@LargeTest
public class MainActivityInstrumentationTest {

    @Rule
    public ActivityTestRule mActivityRule = new ActivityTestRule<>(
            MainActivity.class);

    @Test
    public void sayHello(){
        onView(withText("Say hello!")).perform(click());

        onView(withId(R.id.textView)).check(matches(withText("Hello, World!")));
    }
}
 ```

在你的JUnit4测试类中， 你可以在你的测试代码中使用下面的注解调用特定处理的一部分。

* @Before: 使用这个注解可以指定一段包含测试设置操作的代码。测试类在每个测试执行前调用这段代码。你可以定义多个带有@Before注解的方法， 但是在测试类中这个方法执行的顺序是不确的。

* @After: 这个注解指定一段代码包含测试卸载操作。在每个测试方法执行后调用这段代码。你可以定义多个@After操作的代码。使用这个注解来释放内存中的资源。

* @Test: 使用这个注解标识一个测试方法。 一个单独的测试类可以有多个测试方法

* @Rule: @Rule通过复用的方法，可以允许你可以灵活的添加和修改每个测试方法。 在Android测试中， 此注解需要和Android测试支持库中提供的测试规则类配合命用。比如 [ActivityTestRule](https://developer.android.com/reference/android/support/test/rule/ActivityTestRule.html)和 [ServiceTestRule](https://developer.android.com/reference/android/support/test/rule/ServiceTestRule.html)

* @BeforeClass: 使用此注解来指定一个测试类的静态方法只能调用一次。这种测试步骤对于耗时操作非常有用， 例如连接数据库操作。

* @AfterClass: 使用这个注解来指定一个静态方法， 当类中所有的测试方法都已经运行完成的时候调用。 这个步骤对释放@BeforeClass块中占用的资源非常有用。

* @Test(timeout=): 一些注解支持在注解中设置变量值。 例如， 你可以指定一个测试的超时时间，如果一个测试开始并且没有在指定的超时时间内完成， 它自动认为校验失败。超时时间的单位是毫秒， 如 `@Test(timeout=5000)`

想了解更多注解， 可以参阅文档  [JUnit annotations](http://junit.sourceforge.net/javadoc/org/junit/package-summary.html) 和 [Android annotations](https://developer.android.com/reference/android/support/annotation/package-summary.html).

使用JUnit的[Assert](https://developer.android.com/reference/junit/framework/Assert.html)类来验证一个对象状态的正确性。asset方法比较你的期望值与真实值， 并且当两个值不相符时抛出一个异常。[ Assertion classes](https://developer.android.com/training/testing/start/index.html#AssertionClasses)对这些方法有详细的描述。

### Android的测试支持库

[Android的测试支持库](https://developer.android.com/topic/libraries/testing-support-library/index.html)提供了一些API的集合，　使你可以快速的创建和运行测试代码，　包括JUnit4和UI测试。当你想自动化测试你的应用时，　下面这些库的基于设备的API会非常的有用。

* [AndroidJUnitRunner](https://developer.android.com/topic/libraries/testing-support-library/index.html#AndroidJUnitRunner). 一个Android的兼容JUnit4的测试运行器

* [Espresso](https://developer.android.com/topic/libraries/testing-support-library/index.html#Espresso). 一个适用于应用内部ＵＩ功能验证的ＵＩ测试框架。

* [UI Automator](https://developer.android.com/topic/libraries/testing-support-library/index.html#UIAutomator). 一个适用于跨应用的ＵＩ功能测试的ＵＩ测试框架

### Assertion classes

因为Android测试支持库的API继承于JUnit, 所以你可以使用assetion方法来展示测试的结果。　一个assertion方法会比较实际的值与预期的值，　并且当结果不符时抛出一个AssertionException异常. 使用assertion可以比loggin的方式更方便，　并且拥有更好的性能。

为了简化测试的开发，　你可以使用[ Hamcrest library](https://github.com/hamcrest), 使用Hamcrest matcher APIs可以让你创建更灵活的测试。

### Monkey和monkeyrunner

Android SDK提供两种功能级app测试的工具。

* Monkey 
	
	Monkey是一个命令行工具，　可以向设备发送模拟的随机事件，　比如按钮，　触屏，　手势等。通过[Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb.html)工具来运行。它可以用来进行压力测试，上报错误，或者多次重复执行一个事件。

* monkeyrunner

	这是一个用Python实现的工具，　它拥用测试程序API和执行环境。API的功能包括连接到设备，　安装卸载应用，　截屏，　比较图片，　执行一个app的测试包等。使用它的API，　你可以写出一些强大的复杂的测试。使用命令行工具monkeyrunner, 来运行你的程序。

## 如何构建测试

下面的这些文档提供了更详细的信息，教你如何创建和运行各种类型的测试。

* [构建本地单元测试](https://developer.android.com/training/testing/unit-testing/local-unit-tests.html)

	构建没有依赖或只有可以模拟的简单依赖的单元测试，　运行于你的本地虚拟机

* [构建设备单元测试](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html)

	构建复杂的依赖Android的复杂单元测试，　运行于硬件设备或者模拟器。

* [自动化界面测试](https://developer.android.com/training/testing/ui-testing/index.html)

	创建用户界面行为的校验测试，　基于单应用或者跨应用。

* [测试组件集成](https://developer.android.com/training/testing/integration-testing/index.html)

	测试用户不能直接交互的组件，　比如Service或者Content Provider

* [测试显示性能](https://developer.android.com/training/testing/performance.html)

	测试你的应用的界面显示性能，　来保证有平滑的用户体验。

## 吐槽

话说翻这东西真是劳心劳力,　四个小时，闲的么？
转载请注明来源 http://www.mapeiyu.com

<center>![tt](jls.jpg)</center>