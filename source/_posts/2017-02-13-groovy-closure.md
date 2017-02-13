---
title: groovy closure
date: 2017-02-13 11:18:28
categories:
- gradle开发
tags: 
- groovy
- gradle
---

## 概念

闭包是可以用作函数参数和方法参数的代码块. 其实Groovy的闭包更象是一个“代码块”或者方法指针，代码在某处被定义然后在其后的调用处执行

## 使用示例

```
def square = {it * it} // 定义一个叫square的闭包。it是默认的 参数名称
assert 4 == square(2) // 使用闭包
assert [1,4,9] == [1,2,3].collect(square) // 使用闭包

def closure = { param -> println("hello ${param}") }
closure.call("world!")
 
def closure = { greeting, name -> println(greeting + name) }
closure.call("hello ", "world!")

```

闭包用“{}”括起，“->”前面是参数，后面是处理语句，可以直接调用，也可以使用call调用。不管那种调用，最后groovy编译器都会把编译成对doCall方法的调用，这是groovy对闭包的一个隐藏方法。如果只有一个参数，可以不写，而使用缺省的参数“it”。 如

```
def closure = { println("hello ${it}") }
closure.call("world!")
```

闭包还可当作变量返回

```
def localMethod() {
  def localVariable = new java.util.Date()
  return { println localVariable }
}
def clos = localMethod()
clos() 
```

## Groovy闭包的隐含变量

* it：默认的参数名，调用是如果没有传参数，it为null
* this : 跟Java一样，是定义闭包所在类的一个引用，不管有多少层闭包嵌套，this指向的都是最上层的类。
* owner : 封闭闭包的对象(如果只有一层闭包就是this，如果有多层闭包嵌套就是含有此闭包的上层闭包)
* delegate :缺省值是owner，但是可以改变，后面详说。

```
class Class1 {
  def closure = {
    println " ============================== "
    println "this = "+ this.class.name
    println "owner = " + owner.class.name
    println "delegate = " + delegate.class.name
    def nestedClos = {
        println " ============================== "
        println "this = "+ this.class.name
        println "owner = " + owner.class.name
        println "delegate = " + delegate.class.name
      def thirdClos = {
            println " ============================== "
            println "this = "+ this.class.name
            println "owner = " + owner.class.name
            println "delegate = " + delegate.class.name
      }
      thirdClos()  
    }
    nestedClos()
  }
}
 
def clos = new Class1().closure
//clos.delegate = this
clos()
执行结果：
 ============================== 
this = Class1
owner = Class1
delegate = Class1
 ============================== 
this = Class1
owner = Class1$_closure1
delegate = Class1$_closure1
 ============================== 
this = Class1
owner = Class1$_closure1_closure2
delegate = Class1$_closure1_closure2
```

## 闭包实现接口

* 如果接口只有一个方法

```
interface Test
{
 def test()
}

def test = {
 println'ok'
} as Test

test.test()
```

* 多方法接口

```
interface MultiFuncTest
{
    def test1()
    def test2(str)
}
 
def impl = [test1:{println'test'},
        test2:{str -> println str}] as MultiFuncTest

impl.test1()
impl.test2('ok')
```

## delegate委托

```
class Dog{
    def play = {
      "wang wang!"
    }
    def childmind = {
        println       delegate.play();      
    }
}
class Cat {
    def play = {"mi mi !"}
}
def dog = new Dog()
def cat = new Cat()
dog.childmind()
dog.childmind.delegate  = cat;
dog.childmind()
```
<center>OVER! 福利来喽!</center >
<center>![料](11.jpg)</center >
