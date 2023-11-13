---
title: 关于 Java 中 String 类的浅克隆和深克隆
date: 2020-03-25
updated: 2022-05-30
tags:
  - Java
urlname: shallow-clone-and-deep-clone-of-string-in-java
original: true
---
String 类在 Java 中不是基础类型, 它又不继承 Cloneable, 那么该如何深克隆它呢? 
<!--more-->
我在这里就不写浅克隆, 深克隆, 字符串池的扫盲了, 直接表达我如何理解String对象是如何进行克隆的.
# String对象是如何进行克隆的 
在学习原型模式时, 会用到Cloneable接口和重写Object的clone方法来对一个对象进行克隆. 浅克隆和深克隆的区别, 简单来说就是是否从内存中复制一份新的数组或者引用. 而String作为一个特殊对象, 它没有实现Cloneable接口那么它应该如何进行浅克隆和深克隆? 
我们先看一段例子: 
~~~
public class CloneTest implements Cloneable{
    
    public String nameString;

    @Override
    public CloneTest clone() throws CloneNotSupportedException {
        return (CloneTest) super.clone();
    }

    public void print() {
        System.out.println(this.toString() + "---" + this.nameString + "---" +
            System.identityHashCode(this.nameString));
    }
    
    public static void main(String[] args) throws Exception {
        CloneTest cloneTest1 = new CloneTest();
        cloneTest1.nameString = "String1";
        cloneTest1.print();
        
        CloneTest cloneTest2 = cloneTest1.clone();
        cloneTest2.print();
  
        System.out.println("通过反射修改1的String");
        Class clz = cloneTest2.nameString.getClass();
        // 需要使用getDeclaredField(), getField()只能获取公共成员字段
        java.lang.reflect.Field field = clz.getDeclaredField("value");
        field.setAccessible(true);
        char[] ch = (char[])field.get(cloneTest2.nameString);
        ch[1]='*';

        cloneTest2.print();
        cloneTest1.print();
    }
}
~~~
上面代码输出:
~~~
CloneTest@15db9742---String1---1829164700
CloneTest@7852e922---String1---1829164700
通过反射修改1的String
CloneTest@7852e922---S*ring1---1829164700
CloneTest@15db9742---S*ring1---1829164700
~~~
可以看出, 在对cloneTest1进行克隆赋值给cloneTest2的时候, 这两个对象的那么String指向的是同一个引用, 这是String的浅克隆, 但是String没有继承Cloneable接口, 也没有重写clone方法, 它无法深克隆, 那怎么对它进行深克隆呢? 我觉得答案是不需要对String进行刻意的深克隆, 因为String类在堆内存中有相应的字符串池String Pool, 设想如果你给cloneTest2的nameString做出了修改, 那它就会指向相应的字符串引用, 该引用可能在字符串池也可能在堆内存(取决于你如何创建String对象). 所以String类是没必要实现Cloneable接口的. 

通过上面的例子你发现你成功的改写了在字符串池里面的值, 理论上这是不应该修改的. 但如果你设想一下, 假如程序在运行过程中一些作为判断的输出(比如true或false)被你这样修改了, 那程序跑起来就很有趣了, 比方说下面这个程序: 
~~~
public class ModifyTrue {
        public static void main(String[] args) throws Exception{
//        System.out.println(true);
        String s1 = String.valueOf(true);
        System.out.println("s1:" + s1 + " " + System.identityHashCode(s1));
        
        String s2 = "true";
        Class clzClass = s2.getClass();
        java.lang.reflect.Field field = clzClass.getDeclaredField("value");
        field.setAccessible(true);
        char[] ch = (char[]) field.get(s2);
        ch[1]='*';
        ch[2]='*';
        System.out.println("s2:" + s2 + " " + System.identityHashCode(s2));
        
        boolean b1 = true;
        System.out.println(b1);
        
    }
}
~~~
它的输出是这样的: 
~~~
s1:true 366712642
s1:t**e 366712642
s2:t**e 366712642
true
~~~
因为s1和s2都同样指向字符串池里的"true", 所以通过反射修改s2指向的值时, 输出的s1同样受到影响. 然后我们把第三行的注释放开, 发现输出变成了: 
~~~
true
s1:true 366712642
s1:t**e 366712642
s2:t**e 366712642
t**e
~~~
你会发现最后的true也发生了改变, 这是为什么呢? 我觉得这涉及到编译时和运行时的概念, 两者的区别在于一开始有没有调用PrintStream类里的print(boolean b)方法. 

## 为什么clone方法在Object里而不是Cloneable接口里?
你可以发现clone方法是用native关键字修饰的, 说明该方法是一个原生态方法, 方法对应的实现不是在当前文件, 而是在用其他语言(如C和C++)实现的文件中. 如果不是这样它将无法复制. 

参考: [Why is #clone() not in the Cloneable interface?](https://stackoverflow.com/questions/13670152/why-is-clone-not-in-the-cloneable-interface)























