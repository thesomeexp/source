---
title: Java 基础笔记
date: 2019-11-26 
updated: 2022-03-01
tags:
  - Java
urlname: java-basic-notes
original: false
---
关于Java技术, 官方给出的Hello World解读.
<!--more-->
## 关于Java技术
Java程序都是由.java源代码编译成.class字节码文件后运行在JVM虚拟机上的. 一些不同平台的JVM可能提供一些格外步骤来提高程序性能. Java平台是一个运行在其他基于硬件平台上的纯软件的平台, 它包括JVM和API. JVM是Java平台的基础, 并且移植到了很多基于硬件的平台上了. API是大量现成的软件组件的集合, 这些组件提供了许多有用的功能. Java平台是平台无关的环境, 所以会比本地代码慢一点点, 随着编译器和虚拟机技术进步而使得其性能更接近本地代码. 
## 官方给出的Hello World解读
它包括三部分, 源代码注释(source code comments), HelloWorldApp类定义和主方法. 
Java支持三种类型的注释:
~~~
/* 注释一 */
/** 文档注释 */
// 注释三
~~~
每个Java应用程序都以类定义开头:
~~~
class name{
    ...
}
~~~
主方法是程序的入口点, 习惯上主方法上的public顺序要先于static. 
~~~
public static void main(String[] args) {
    ...
}
~~~
主方法接收一个String数组, 作为程序接收用户命令的参数. 
方法里使用System类里标准输出"Hello World!". 

## 什么是对象?
简单来说现实中的一个对象包括状态(state)和行为(behavior),在Java中对象的状态存在字段Fields(其他语言叫变量variables)里,对象的行为存在方法Methods(其他语言叫函数function)里,方法基于对象内部状态来操作, 用作对象间通讯的主要机制. 隐藏内部状态和并要求通过对象方法对状态进行交互叫做**数据封装**--它是面向对象程序的基本法则.

## 什么是类?
官方指南中利用自行车和自行车蓝图来对类和对象进行举例, 来说明类和对象.  一辆用自行车蓝图做出的自行车, 用面向对象的语言来说,  这辆自行车是自行车蓝图这个类的一个实例(instance). 类不是一个程序,而是供其他类调用的一个蓝图,而通过这个蓝图创建一个对象
就是属于其他类的责任了. 

## 什么是继承?
每个对象之间都有一定的共同点, 官方文档依旧拿自行车举例, 自行车有很多种, 山地车,公路自行车和双人车等, 它们有共同的特点和不同点, 可以将它们共同的特点单独抽出作为一个自行车超类(superclass), 而山地车, 公路自行车和双人车等类可以使用关键字extends继承超类(superclass). 这样就形成了一种类的等级制度, Java中一个类可以被无数个类继承,但一个子类只能继承一个类(单继承).


## 什么是接口?
在上面的例子中, 方法就是对象内部和外部世界的一个接口. 在Java中声明的接口中的方法形式上是一组没有身体的方法. 这么做的目的, 是为了让实现接口的类对它承诺提供的功能更加正式, 所以接口更像是类和外部世界的一个契约. 也就是存储一些承诺提供的功能. 

## 什么是包?
简单理解就是同类的东西放进同类的文件夹里, Java把同类的类放进相同的文件夹里, 那个文件夹就相当于是包, 因为程序会越写越大, 所以组织好各种类和接口放进包里是很有意义的. 

Java平台提供一个巨大的类库(API), 它的程序包代表编程中一些最常用的功能, [Java平台API规范](https://docs.oracle.com/javase/8/docs/api/index.html)包含了Java SE平台提供的所有软件包, 接口, 类, 字段和方法. 

# 变量Variables
## 变量类型:
Java程序语言定义了下面几种变量:
### 1.实例变量Instance Variables(非静态字段Non-Static Fields)
也就是对象存储它们个体变量的字段, 它们对每个对象来说是不一定相同的, 比如每辆自行车的当前速度是不一样的. 
### 2.类变量Class Variables(静态字段Static Fields)
标记了静态static的字段是所有实例化该类对象共享的, 比如说自行车的齿轮变量标记了static, 那么通过这个类实例化出来的自行车就应该是有相同的齿轮数量, 如果再加上final关键字, 那么该字段将无法被修改. 
### 3.局部变量Local Variables
也就是方法里的变量, 基本不需要修饰, 只能在该方法里使用, 其他类无法访问的变量. 
### 4.参数Parameters 
在之前的Hello World程序的main方法里的args就是一个String数组类型的参数. 最重要的一点是, 参数(Parameters)是属于变量(variables)而不是字段(fields).

在接下来的教程里, 字段fields通常不包括局部变量Local Variables和参数Parameters, 如果谈到"上述所有"则代表变量. 成员member则代表类型的字段fields, 方法methods和嵌套类型nested types的集合.

## 命名规则:
不建议使用美元符号$和下划线_开头, 尽量使用有意义的命名, 避免使用[Java的关键词和保留字](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html),
如果名字为单个单词则用小写, 多个单词用驼峰命名法, 如果声明为常量则每个字母大写并用
下划线隔开. 

## 原始数据类型:
Java语言支持8种原始数据类型:
### 1.byte
8位带符号的二进制补码整数, 范围[-128到127], 可以用在大数组中节约内存, 可以替换int. 
### 2.short
16位带符号的二进制补码整数, 范围[-32,768到32,767], 像byte一样可以用来节约内存. 
### 3.int
32位带符号的二进制补码整数, 范围[-2^31到(-2^31)-1], 在Java SE8以后你也可以用int
来代表32位无符号整数,  具体查看Number类. 一些无符号运算方法已经添加到Integer类里了. 
### 4.long
64位带符号的二进制补码整数, 范围[-2^63到(-2^63)-1], 和int差不多. 
### 5.float
单精度32位浮点型, 在[Floating-Point Types, Formats, and Values](https://docs.oracle.com/javase/specs/jls/se7/html/jls-4.html#jls-4.2.3)
中指定了该类型, 如果学过计算机组成原理的话你会很容易理解浮点型的存储方式. 对double
来说float可以用来节约内存, 但永远不要用float和double来用于精确值, 比如货币. 如果
需要你可以使用java.math.BigDecimal类来代替, 在[Numbers and Strings章节](https://docs.oracle.com/javase/tutorial/java/data/index.html)有具体的介绍. 
### 6.double
双精度64位浮点型, 默认的小数类型, 和float一样不能用于精确值, 比如货币. 
### 7.boolean
它只有true或者false值, 虽然能用一位就能表示它, 但它的大小不是精确定义的. 
### 8.char
char数据类型是单个16位Unicode字符, 它的范围'/u0000'或者说是(0), 最大值'/uffff'
或者说是(65,535).

Java对字符串String类型通过java.lang.String提供特殊支持, String不是Java的原始数据类型, Java会默认以用两个引号包括起来的字符串来创建一个Stirng对象, 但这种对象是一成不变的. 详情可以参照[Numbers and Strings章节](https://docs.oracle.com/javase/tutorial/java/data/index.html). 

## 默认值:
声明但未被初始化的字段Fields会被编译器compiler设置一个合理的数值, 通常是0或者null, 通常认为依赖于默认值是一种不好的编程风格. 
但是局部变量编译器compiler并不会对它设置合理数值, 访问未初始化的局部变量会导致编译时错误. 

## 字面量Literals:
原始类型是特殊的类型, 因此你会发现不需要new一个对象来给它赋值, 一个字面量用源代码直接表示为一个固定值. 像true, false, 'A', 100等. 

### 整型字面量Integer Literals
一个以L或者l结尾的整型字面量类型为long, 通常用L而不是l因为l和数字1很像. byte, short, int和long都可以用int字面量来创建, 超出int部分的long可以用L结尾形式创建, 整型字面量还可以表达二进制, 十六进制, 十进制等. 
~~~
// 用十进制表示数字26
int decVal = 26;
//  用十六进制表示数字26
int hexVal = 0x1a;
// 用二进制表示数字26
int binVal = 0b11010;
~~~

### 浮点型字面量Floating-Point Literals
一个以F或者f结尾的浮点型字面量类型为float, 否则就是double, double的结尾D或者d是可选的. 
一个浮点型类型可以使用E或者e结尾, 它表示科学计数法. 
~~~
double d1 = 123.4;
// 用科学计数法表示和d1一样的值
double d2 = 1.234e2;
float f1  = 123.4f;
~~~

### 字符和字符串字面量Character and String Literals
Java支持一些特殊字符的转译, Java中的null一般用来测试值是否存在和对象是否可以使用. 

最后还有一类叫类字面量的特殊字面量类型, 一般在类后面加上.class就是了, 例如String.class就代表这个对象它自己的类型. 

### 在数字字面量中使用下划线字符
当一个二进制或者十六进制非常长的时候, 可以使用下划线让它们隔开以增加可读性, 比如
官方给出的例子: 
~~~
long creditCardNumber = 1234_5678_9012_3456L;
long socialSecurityNumber = 999_99_9999L;
float pi =  3.14_15F;
long hexBytes = 0xFF_EC_DE_5E;
long hexWords = 0xCAFE_BABE;
long maxLong = 0x7fff_ffff_ffff_ffffL;
byte nybbles = 0b0010_0101;
long bytes = 0b11010010_01101001_10010100_10010010;
~~~
规则是你只能把下划线放在数字之间. [参阅Using Underscore Characters in Numeric Literals](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

## 数组
数组是一个保存固定长度值的单个类型的容器对象, 数组里的每一项叫做元素element, 元素可以通过数组下标访问numerical index, 下标通常是0开始的. 

### 定义一个数组变量
和其他变量类型一样, 定义一个数组变量也要两部分, 数组类型和数组名字. 数组类型写成类型加两个方括号type[]; 数组名字就是变量名字. 创建了一个数组变量并不意味着创建了数组, 这只是告诉编译器这个变量将保存指定类型的数组. 建议的命名方式: 
~~~
byte[] anArrayOfBytes;
short[] anArrayOfShorts;
long[] anArrayOfLongs;
float[] anArrayOfFloats;
double[] anArrayOfDoubles;
boolean[] anArrayOfBooleans;
char[] anArrayOfChars;
String[] anArrayOfStrings;
~~~
括号也可以放在变量后面, 但是不鼓励这种形式. 括号作为数组类型的标识应该定义在类型里. 
~~~
float anArrayOfFloats[];
~~~
### 创建, 初始化, 和访问数组
一种方式去真正创建一个数组是使用new关键字, 如果没有这个声明, 编译器会抛出数组变量没有被初始化的错误. 
~~~
anArray = new int[10]; // 创建
anArray[0] = 100; // 初始化第一个元素
~~~
### 多维数组
Java中的多维数组和C语言Fortran语言不一样, 多维数组通过一维数组组装产生, 可以通过数组内置的length属性查看数组的大小. 
~~~
anArray.length // 一维数组长度
anArray[0].length // 二维数组第一个数组长度
~~~
### 复制数组
System类提供了一个arraycopy方法来高效率复制一个数组. 也可以使用java.util.Arrays类里的copyOfRange方法, 后者会返回一个数组. 

java.util.Arrays类里一些有用的方法:
1.搜索特定值获取索引(binarySearch方法)
2.比较两个数组是否相等(equals方法)
3.用特定值填充数组(fill方法)
4.排序(使用sort方法, 在多处理器排序大数组时parallelSort方法比顺序排序更有优势)

## 变量总结
Java使用字段fields和变量variables两种术语, 实例变量Instance variables是每个实例类都可以不一样的, 而类变量Class variables是每个实例的类共有的. 
8种数据类型是:byte, short, int, long, float, double, boolean, and char. 而java.lang.String类代表字符串. 编译器会自动分配合理的默认值给上面的类型. 编译器不会给本地变量赋值, 字面量literal是固定值的源代码表示. 数组是存储单一类型的固定长度容器. 数组在创建后大小是固定的. 

参考: [Variables](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/variables.html)

# 运算符
在联合使用运算符时需要注意运算符优先级问题. 
## 赋值, 运算, 一元运算符
### 赋值
除了简单的赋值, 还能用来赋值对象的引用. 
### 运算符
加, 减, 乘, 除, 取余
加还能用来连接字符串. 
### 一元运算符
正负标识符, 自增自减, 取反. 
区分前缀自增自减和后缀自增自减区别. 
## 等于, 关系和条件运算符
### 等于和关系运算符
等于, 不等于, 大于, 大于等于, 小于, 小于等于
### 条件运算符
与, 或
还有一个常用条件运算符是 condition ? value1 : value2 , 这个是一个三元运算符. 
### 类型比较运算符
instanceof 运算符将对象与指定类型进行比较, 你可以用它来测试对象是否是类的实例, 是否是子类的实例, 或者一个类的实例是否实现特定的接口. 注意null不是实例. 
## 按位和移位操作
补数运算符, 左移或者右移运算符第二个值为移动的位数, 无符号左移是左边补0, 否则补符号. 
按位与, 按位或, 异或

## 表达式, 语句和块 Expressions, Statements, and Blocks
### 表达式Expressions
一条表达式由变量, 运算符, 或者说带回返回值的方法组成. 
主要是避免歧义, 例子:
~~~
x + y / 100    // 含糊的
(x + y) / 100  // 不含糊
x + (y / 100) // 不含糊
~~~
### 语句Statements
有些表达式通过结尾加分号变成语句叫做表达语句expression statements, 
语句分为声明语句(declaration statements)和控制流语句(control flow statements).
声明语句:
~~~
// declaration statement
double aValue = 8933.234;
~~~
### 块Blocks
一个语句块是指由0或者多个语句组成的代码块.
~~~
class BlockDemo {
     public static void main(String[] args) {
          boolean condition = true;
          if (condition) { // begin block 1 
               System.out.println("Condition is true.");
          } // end block one
          else { // begin block 2
               System.out.println("Condition is false.");
          } // end block 2
     }
}
~~~
## 控制流语句Control flow statement
简单来说就是控制代码块的执行顺序, 使他们不仅仅是从上到下执行. 
if/switch/while/do-while/for

用switch写日数判断:
~~~
class SwitchDemo2 {
    public static void main(String[] args) {
        int month = 2;
        int year = 2000;
        int numDays = 0;
        switch (month) {
            case 1: case 3: case 5:
            case 7: case 8: case 10:
            case 12:
                numDays = 31;
                break;
            case 4: case 6:
            case 9: case 11:
                numDays = 30;
                break;
            case 2:
                if (((year % 4 == 0) && 
                     !(year % 100 == 0))
                     || (year % 400 == 0))
                    numDays = 29;
                else
                    numDays = 28;
                break;
            default:
                System.out.println("Invalid month.");
                break;
        }
        System.out.println("Number of Days = "
                           + numDays);
    }
}
~~~

for可以迭代集合(Collections)和数组(arrays)

没有标签的break会结束最内层的switch, for, while, or do-while语句. 给break加上标签可以中断指定的语句. continue同理. 
例如: 
~~~
class BreakWithLabelDemo {
    public static void main(String[] args) {

        int[][] arrayOfInts = { 
            { 32, 87, 3, 589 },
            { 12, 1076, 2000, 8 },
            { 622, 127, 77, 955 }
        };
        int searchfor = 12;

        int i;
        int j = 0;
        boolean foundIt = false;

    search:
        for (i = 0; i < arrayOfInts.length; i++) {
            for (j = 0; j < arrayOfInts[i].length;
                 j++) {
                if (arrayOfInts[i][j] == searchfor) {
                    foundIt = true;
                    break search;
                }
            }
        }

        if (foundIt) {
            System.out.println("Found " + searchfor + " at " + i + ", " + j);
        } else {
            System.out.println(searchfor + " not in the array");
        }
    }
}
~~~

# 类Classes
## 声明一个类
~~~
class MyClass extends MySuperClass implements YourInterface {
    // field, constructor, and
    // method declarations
}
~~~
类前面还能加public或private之类的修饰符来决定哪些类能访问它. 
## 声明成员变量
在类中的变量叫成员变量(fields).
在方法或块中的变量叫本地变量(local variables).
在方法定义时的变量叫参数(parameters).
public修饰符意味着其他对象可以可以访问这个类. 
private修饰符意味着该field只能在该类中访问. 
每个变量都需要声明类类型, 变量的命名也是有一套规则的. 
## 定义一个方法
根据约定, 方法名应该是一个以小写开头的动词或多个单词的驼峰命名方式. 
通常方法在类中有独一无二的名字, 除非方法重载(method overloading同名但不同参数). 
Java通过不同的方法签名(method signatures)来区分不同的方法, 它是根据参数数量和类型来区分的. 
## 为类提供构造方法(Constructors)
构造方法可以看成是没有返回值的和类同名的方法. 
编译器会自动给没有构造方法的类添加一个无参构造方法, 如果其父类无参, 编译器也会提供, 这里还得重点了解一下super关键字. 子类构造函数第一行必须使用super来调用父类构造方法. 

## 参数传递
可以传递任何类型的参数. 除非写构造方法和set方法, 参数名不应该和成员变量名(field)重名, 成员变量名尽量不使用类似x, y这种命名. 区分和成员变量重名的参数需要了解一下this关键字. 
### 任意数量参数传递
当你不知道传递参数的个数, 可以使用可变参数(varargs), 可变参数可以像数组一样被使用, 你可能经常看到printf函数就使用了可变参数. 可变参数一般放在参数list的最后. 它可以是0个. 
~~~
public PrintStream printf(String format, Object... args)
~~~
### 传递基础数据类型
传递基础数据类型是值传递, 在方法内修改该参数不会对原参数造成影响. 例如修改传过来的int, double类型. 
### 传递引用数据类型
传入的引用仍然引用之前的同一个对象, 如果有合适的访问权限它能改变成员变量的值. 
~~~
public void moveCircle(Circle circle, int deltaX, int deltaY) {
    // code to move origin of circle to x+deltaX, y+deltaY
    circle.setX(circle.getX() + deltaX);
    circle.setY(circle.getY() + deltaY);
        
    // code to assign a new reference to circle
    circle = new Circle(0, 0);
}
~~~
例如上面的第7行代码, 只是代表circle指向了新的对象, 而不是传入的那个对象原来的引用发生了改变. 

# 对象Objects
## 创建对象
这里有一句语句:
~~~
Point originOne = new Point(23, 94);
~~~
它由三部分组成, 一是声明, 声明变量名; 二是实例化对象, 上面的new关键词便是创建一个对象; 三是初始化, new之后会调用构造方法, 用来初始化一个新的对象. new运算符返回对象的引用(reference), 有时候使用new不一定必须分配给指定变量, 例如: 
~~~
int height = new Rectangle().height;
~~~
上面那种情况是new关键词创建了一个对象, 然后返回该对象的引用, 然后调用该对象的height()方法. 上面的代码在调用了方法后, 新的对象引用并没有变量存储它, 所以最后会被JVM回收. 变量超出范围时, 通常会删除变量包含的引用. JVM会回收没有被引用的对象, 你可以用null使一些对象不被引用. 

## 关于类
### 协变返回类型covariant return type
简单来说, 协变返回值类型就是在重写父类方法时, 正常情况下返回值是不允许你改变的, 而JavaSE1.5开始支持的协变返回类型技术允许你在方法重载时, 改变方法返回值, 但前提是改变的返回值必须是被重载方法返回值的子类. 你也可以使用接口名作为返回值, 但必须实现该接口. 

修饰符决定各成员变量访问级别
<table border="1" summary="This table defines levels of access conferred by a modifier">
<caption style="font-weight: bold" id="accesscontrol-levels">Access Levels</caption>
<tbody><tr>
    <th>Modifier</th>
    <th>Class</th>
    <th>Package</th>
    <th>Subclass</th>
    <th>World</th>
</tr>
<tr>
    <td>public</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
</tr>
<tr>
    <td>protected</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
    <td>N</td>
</tr>
<tr>
    <td style="font-style: italic">no modifier</td>
    <td>Y</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
</tr>
<tr>
    <td>private</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
    <td>N</td>
</tr>
</tbody></table>
为了避免别人引错包, 访问级别的使用建议: 

- 对成员变量使用严格而有意义的访问级别, 应该使用private除非你有充分合理的缘由. 
- 除了常量外, 应避免使用public. 

### static修饰符
在使用static修饰符修饰的变量和方法时, 建议使用类名来调用而不是对象名, 容易引起歧义. 静态方法经常用来访问静态成员变量. 

### 常量Constants
使用final和static组合通常定义为常量, 通常以大写和用下划线分隔多个单词来命名. 

### 编译时常量compile-time constant
如果常量是一个基本类型或者String, 那么编译时编译器会直接把常量替换为该值. 

### 初始化块Initializing Fields
我们可以给成员变量赋值的方法来初始化成员变量, 但是这种办法不能给复杂的数组赋值和异常处理, 所以初始化的工作最好在构造器里做, 这样一些复杂的初始化和错误处理就能解决了. 初始化块里的代码相当于在执行构造函数前执行的代码, 设计的本意好像是为了解决构造器代码重复的问题. 在new对象时该代码块执行一次. 它长这样: 
~~~
class Example{
 {
    // 对, 在类里就两个括号, 啥也没有
 }
}
~~~

### 静态初始化块Static Initialization Blocks
为了提供和初始化块(Initializing Fields)一样的功能, 静态初始化块就是给静态变量初始化的, 一般只执行一次, 在new对象时静态代码块都优先执行: 
~~~
class Example{
static {
    // 静态代码块
 }
}
~~~
你可以写一个给静态变量赋值的private方法: 
~~~
class Whatever {
    public static varType myVar = initializeClassVariable();
        
    private static varType initializeClassVariable() {

        // initialization code goes here
    }
}
~~~
这样做的好处是你之后可以在以后重用该初始化方法. 方法被定义为final是因为在初始化时调用非final方法可能会导致问题. 

## 嵌套类Nested Classes
嵌套类分为: 静态内部类(static nested classes), 内部类(inner classes)
~~~
class OuterClass {
    ...
    static class StaticNestedClass {
        ...
    }
    class InnerClass {
        ...
    }
}
~~~
内部类可以访问OuterClass里的其他成员变量, 尽管被声明称private. 但静态内部类不行. 内部类可以被private, public, protected修饰, 但是最外部的类不能被private修饰. 
### 为什么使用嵌套类
- 对一些只对一个类有用的类做逻辑分组, 精简项目代码
    上面的代码中, InnerClass里可以访问OuterClass里的private成员. 
- 封装性, 隐藏不让别人知道的操作
- 使代码更可读和易于维护
### 静态内部类Static Nested Classes
声明在类里面的静态类. 
静态内部类StaticNestedClass不能直接访问和使用外部类OuterClass的变量和方法. 
静态内部类通过以下方式访问和创建: 
~~~
OuterClass.StaticNestedClass

OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
~~~
### 内部类Inner Classes
声明在类里面的类. 
内部类可以直接访问该类外部类的变量和方法, 但自己不能创建static成员. 
内部类的实例只能存在于外部类的实例中, 并且可以直接访问封闭实例的方法和字段. 
实例化内部类的前提是外部类已经被实例化, 应该这样实例化内部类: 
~~~
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
~~~
还有两种特殊的内部类: 本地类(local classes)和匿名类(anonymous classes). 
### 变量重名Shadowing
~~~
public class ShadowTest {

    public int x = 0;

    class FirstLevel {

        public int x = 1;

        void methodInFirstLevel(int x) {
            System.out.println("x = " + x); // x = 23
            System.out.println("this.x = " + this.x); // this.x = 1 使用this代表该封闭范围
            System.out.println("ShadowTest.this.x = " + ShadowTest.this.x); // ShadowTest.this.x = 0
        }
    }

    public static void main(String... args) {
        ShadowTest st = new ShadowTest();
        ShadowTest.FirstLevel fl = st.new FirstLevel();
        fl.methodInFirstLevel(23);
    }
}
~~~
在上面的例子中有三个变量重名了, FirstLevel的methodInFirstLevel方法的参数, 和内部类的成员变量, 和内部类的外部类成员变量. 看完例子就知道应该怎么区分三个变量了. 
### 序列化
序列化内部类是不建议的, 它可能产生一些问题. 
### 内部类例子
~~~
public class DataStructure {
    
    // Create an array
    private final static int SIZE = 15;
    private int[] arrayOfInts = new int[SIZE];
    
    public DataStructure() {
        // fill the array with ascending integer values
        for (int i = 0; i < SIZE; i++) {
            arrayOfInts[i] = i;
        }
    }
    
    public void printEven() {
        
        // Print out values of even indices of the array
        DataStructureIterator iterator = this.new EvenIterator();
        while (iterator.hasNext()) {
            System.out.print(iterator.next() + " ");
        }
        System.out.println();
    }
    
    interface DataStructureIterator extends java.util.Iterator<Integer> { } 

    // Inner class implements the DataStructureIterator interface,
    // which extends the Iterator<Integer> interface
    
    private class EvenIterator implements DataStructureIterator {
        
        // Start stepping through the array from the beginning
        private int nextIndex = 0;
        
        public boolean hasNext() {
            
            // Check if the current element is the last in the array
            return (nextIndex <= SIZE - 1);
        }        
        
        public Integer next() {
            
            // Record a value of an even index of the array
            Integer retValue = Integer.valueOf(arrayOfInts[nextIndex]);
            
            // Get the next even element
            nextIndex += 2;
            return retValue;
        }
    }
    
    public static void main(String s[]) {
        
        // Fill the array with integer values and print out only
        // values of even indices
        DataStructure ds = new DataStructure();
        ds.printEven();
    }
}
~~~
输出内容是: 
~~~
0 2 4 6 8 10 12 14 
~~~
上面的例子中EvenIterator类直接引用DataStructure对象的arrayOfInts实例变量. 
你可以像例子一样使用内部类去实现一些帮助类. 一般用内部类处理用户界面的事务处理. 
### 声明本地类Declaring Local Classes
声明在方法里的类. 这里有一个验证手机号码的例子: 
~~~
public class LocalClassExample {
  
    static String regularExpression = "[^0-9]";
  
    public static void validatePhoneNumber(
        String phoneNumber1, String phoneNumber2) {
      
        final int numberLength = 10;
        
        // Valid in JDK 8 and later:
       
        // int numberLength = 10;
       
        class PhoneNumber {
            
            String formattedPhoneNumber = null;

            PhoneNumber(String phoneNumber){
                // numberLength = 7;
                String currentNumber = phoneNumber.replaceAll(
                  regularExpression, "");
                if (currentNumber.length() == numberLength)
                    formattedPhoneNumber = currentNumber;
                else
                    formattedPhoneNumber = null;
            }

            public String getNumber() {
                return formattedPhoneNumber;
            }
            
            // Valid in JDK 8 and later:

//            public void printOriginalNumbers() {
//                System.out.println("Original numbers are " + phoneNumber1 +
//                    " and " + phoneNumber2);
//            }
        }

        PhoneNumber myNumber1 = new PhoneNumber(phoneNumber1);
        PhoneNumber myNumber2 = new PhoneNumber(phoneNumber2);
        
        // Valid in JDK 8 and later:

//        myNumber1.printOriginalNumbers();

        if (myNumber1.getNumber() == null) 
            System.out.println("First number is invalid");
        else
            System.out.println("First number is " + myNumber1.getNumber());
        if (myNumber2.getNumber() == null)
            System.out.println("Second number is invalid");
        else
            System.out.println("Second number is " + myNumber2.getNumber());

    }

    public static void main(String... args) {
        validatePhoneNumber("123-456-7890", "456-7890");
    }
}
~~~
本地类可以访问该类外类的成员变量, 比如例子中本地类构造方法使用了LocalClassExample.regularExpression.
本地类仅能访问定义为final的外部类变量. 如果本地类访问一个本地变量和代码块的参数, 它能捕获这个本地变量和参数, 例如在例子中的本地类可以捕获numberLength. 在Java SE8之后, 本地类能访问定义为final或者是定义后没有改变的(effectively final)外部类变量和参数, 例如上面例子中, 把final去掉程序依然可以运作, 但把修改numberLength的语句取消注释, 编译器就会提醒你该变量不数据定义后没有改变(effectively final). 在本地类中你还可以访问phoneNumber1和phoneNumber2的值. 本地类和内部类相似, 他们都不能定义静态成员. 在静态方法里面的本地类只能引用外部同是静态成员. 例如例子里的regularExpression是静态的. 但它可以声明常量(constant variables). 
### 匿名类Anonymous Classes
匿名类例子: 
~~~
public class HelloWorldAnonymousClasses {
    
    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }
  
    public void sayHello() {
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };
        frenchGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp =
            new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }            
}
~~~
一个匿名类声明包括: 
- 一个new操作符
- 实现的接口名或者继承的类名
- 括号中包含构造函数的参数, 但在上面的例子中由于是实现接口所以没有构造函数参数. 
- 一个主体(body大括号), 在主体中允许方法声明但不允许使用语句. 

和本地类一样, 匿名类也能捕获在它外部类括号内的成员, 但该成员必须也是final或者是定义后未被改变的变量. 在匿名类中的变量名如果重名就参照之前写的变量重名. 
匿名类里的成员和本地类一样有声明约束: 
- 你不能定义静态初始化器(static initializers)或成员接口(member interfaces)
- 静态类可以使用常量. 

你可以定义以下内容在匿名类中: 
- 字段(Fields)
- 额外的方法(Extra methods)
- 实例初始化器(Instance initializers不是构造器(constructors), 就是那个只有括号的代码块)
- 本地类(Local classes)

## Lambda表达式(Lambda Expressions)
当接口只有一个方法时, Lambda表达式可以简化代码, 它可以把函数当作一个方法参数, 或者把代码当作数据. 使得表达单方法的实例类代码更紧凑. 用的不多, [点这里查看官方指南.](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)

# 接口Interfaces

## Java中的接口

Java中接口是引用类型, 和类很类似, 它只能包含常量, 方法名, 默认方法, 静态方法和嵌套类型. 只有默认方法和静态方法有方法体. 接口不能被实例化, 只能被类实现或者其他接口继承. 默认方法举例: 
~~~
public interface InterfaceTest {
    default void test() {
        // do something
    }
}
~~~

### 定义一个接口

和类继承不同, 一个接口可以继承多个接口: 
~~~
public interface GroupedInterface extends Interface1, Interface2, Interface3 { 
    // interface body
}
~~~
接口可以包含抽象方法, 默认方法, 静态方法. 抽象方法以分号结束(因为抽象方法不需要包含实现). 所有抽象方法, 默认方法, 静态方法在接口里都隐式的声明为public, 因此在声明时可以忽略public修饰符. 同样, 接口里定义的常量都隐式的声明为public, static和final. 在接口里只有默认方法和静态方法是需要接口实现的, 其余的方法在类实现该接口时都要实现. 

### 接口的发展Evolving Interfaces

如果你想在已经被类实现的接口中扩充方法, 你可以新建一个接口继承至该接口, 或者直接在接口里添加default方法. 

### 默认方法

[这篇文章](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)用时区, 模拟扑克牌例子来说明了如何使用默认方法来扩展程序, 在扑克牌例子中还使用了lambda表达式和方法引用. 

# 继承Inheritance

## Java中的继承

当你想创建一个新类时, 已经有一个类包含了你想要的代码, 你可以继承这个已经存在的类. 继承之后你可以复用一些父类的成员变量而不需要重新写. 子类继承所有父类字段(成员变量, 成员方法, 嵌套类等, 前提是父类的成员被设计为public或者protected, 或者不加修饰, 而private字段是无法被继承的), 而构造器并不属于这个范围, 但子类可以调用父类构造器. 

### 对象转换

假设我们有这么一个类结构: Father类继承Object类, Son类继承Father类. 我们可以: 
~~~
Object obj = new Son();
~~~
此时Object类型变量obj指向Son类对象的引用(隐式转换), 此时obj既是Object又是Son, 也是Father. 
如果此时我们写: 
~~~
Son son = obj;
~~~
编译器会抛出一个运行时错误, 除非你告诉编译器要强转: 
~~~
Son son = (Son)obj;
~~~
你也可以使用instanceof来测试一下特定的对象是否是某个类, 它可以避免运行时错误: 
~~~
    if(obj instanceof Son) {
        Son son = (Son) obj;
    }
~~~

### 关于类的多重继承

类和接口的重大区别在于类可以拥有自己的字段. 你可以通过类直接创建一个对象而接口不能. 我们之前说过, 对象可以在字段中保存在类中定义过的状态. 一个Java语言为什么不允许类多继承的原因是避免对象的状态问题, 也就是类继承多个类字段的能力. 例如: 假设你能够定义一个能继承多个类的类. 当你实例化这个类时, 这个对象会继承父类的所有字段, 那么如果来自多个父类的方法和构造函数如何初始化同名的字段呢? 那个方法或者构造函数会执行? 因为接口不包含字段, 所以你不需要担心多继承的对象状态问题. 

Java支持类型的多继承, 也就是一个类能实现多接口的能力. 一个对象可以有多种类型, 一个是它自己的类型还有它实现的接口类型. 这也就意味着如果一个变量声明为接口类型, 那么它就可以引用实现了该接口的对象. 如果不同接口的方法冲突, 继承的类可以选择使用来自那个接口的方法. 

## 方法覆盖和方法隐藏Overriding and Hiding Methods

### 实例方法

子类方法如果和父类方法的方法签名(方法名加上同参数数量和类型)和返回值与父类一样, 那么子类的方法就会覆盖父类的方法. 

子类覆盖父类方法的能力允许它继承一个行为相近的父类, 并根据需要重写该方法. 被覆盖的方法在子类的返回值可以是原返回值的子类, 这个子类型叫协变返回类型. 

每当覆盖一个方法时, 你需要使用@Override注解来告诉编译器你想覆盖一个父类方法. 如果编译器检查发现父类没有这个方法就会产生一个编译错误. (有时候使用eclipse时覆盖不写@Override也行是因为Java语言等级(language level)的问题. 每一代JDK都有它的新特性, Java语言等级可以理解成编译器检查时的最低要求, @Override注解在不同语言等级之间可能不是强制的, 为了方便理解最好还是写上)

### 静态方法

如果子类定义了一个方法签名和父类一样的静态方法, 那么子类的方法会隐藏父类的方法. 

区分静态方法的隐藏和实例方法的覆盖的区别具有重要的含义: 
- 覆盖的实例方法的调用是取决于你实例出来的对象. 
- 隐藏的静态方法的调用是取决于你从父类还是子类去调用. 

官方指南写了一个例子来说明两者间的区别, 首先有一个Animal类: 
~~~
public class Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Animal");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Animal");
    }
}
~~~
Cat类继承Animal类: 
~~~
public class Cat extends Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Cat");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Cat");
    }

    public static void main(String[] args) {
        Cat myCat = new Cat();
        Animal myAnimal = myCat;
        Animal.testClassMethod();
        myAnimal.testInstanceMethod();
    }
}
~~~
Cat类覆盖了Animal类的实例方法和隐藏了Animal的静态方法. 在main方法里创建了一个Cat对象分别调用两个方法, 输出结果: 
~~~
The static method in Animal
The instance method in Cat
~~~
所以方法隐藏指的是当父类类型引用子类对象时, 调用了子类覆盖的方法那么就会调用到子类的方法, 父类的则被隐藏了. 当然这是不好的编程习惯. 

### 接口方法

接口中的默认方法和抽象方法的继承类似于实例方法. 然而, 当超级类或者接口提供多个方法签名相同的方法时, Java编译器会根据以下两个原则来解决方法名冲突的问题: 
- 1. 实例方法要优先于接口默认方法
官方举例: 
~~~
public class Horse {
    public String identifyMyself() {
        return "I am a horse.";
    }
}
public interface Flyer {
    default public String identifyMyself() {
        return "I am able to fly.";
    }
}
public interface Mythical {
    default public String identifyMyself() {
        return "I am a mythical creature.";
    }
}
public class Pegasus extends Horse implements Flyer, Mythical {
    public static void main(String... args) {
        Pegasus myApp = new Pegasus();
        System.out.println(myApp.identifyMyself());
    }
}
~~~
方法Pegasus.identifyMyself()会返回I am a horse.

- 2. 已经被其他子类覆盖的方法将会被忽略. 这通常发生在实现的多个接口拥有共同的祖先. 
官方举例: 
~~~
public interface Animal {
    default public String identifyMyself() {
        return "I am an animal.";
    }
}
public interface EggLayer extends Animal {
    default public String identifyMyself() {
        return "I am able to lay eggs.";
    }
}
public interface FireBreather extends Animal { }
public class Dragon implements EggLayer, FireBreather {
    public static void main (String... args) {
        Dragon myApp = new Dragon();
        System.out.println(myApp.identifyMyself());
    }
}
~~~
方法Dragon.identifyMyself()会返回I am able to lay eggs.
如果两个或者多个独立定义的默认方法冲突, 或者默认方法和抽象方法冲突, 你必须明确覆盖该方法否则编译器会产生错误. 
官方举例: 
~~~
public interface OperateCar {
    // ...
    default public int startEngine(EncryptedKey key) {
        // Implementation
    }
}
public interface FlyCar {
    // ...
    default public int startEngine(EncryptedKey key) {
        // Implementation
    }
}
~~~
如果一个类实现上面两个接口则必须覆盖startEngine方法, 你可以用super关键词来指定调用哪个接口的默认方法. 
~~~
public class FlyingCar implements OperateCar, FlyCar {
    // ...
    public int startEngine(EncryptedKey key) {
        FlyCar.super.startEngine(key);
        OperateCar.super.startEngine(key);
    }
}
~~~
在关键词super之前必须指明需要使用的父接口. 该关键词也可以用在类中, 你也可以直接用super而前面不加父接口名, 这样会调用到父类的方法而不是接口的方法. 
从类继承实例方法会覆盖接口的抽象方法, 官方例子: 
~~~
public interface Mammal {
    String identifyMyself();
}
public class Horse {
    public String identifyMyself() {
        return "I am a horse.";
    }
}
public class Mustang extends Horse implements Mammal {
    public static void main(String... args) {
        Mustang myApp = new Mustang();
        System.out.println(myApp.identifyMyself());
    }
}
~~~
方法Mustang.identifyMyself 会返回I am a horse. 
注: 接口的静态方法不能被继承. 

### 修饰符Modifiers

被覆盖的方法的修饰符的能见度应该不小于被覆盖方法的修饰符. 例如一个父类protected方法在被覆盖后可以被修饰为public, 但不能修饰为private. 

### 总结

下面的表格总结了当你定义了一个父类同方法签名方法时会发生什么. 
<table>
<tr>
<th>&nbsp;</th>
<th>Superclass Instance Method</th>
<th>Superclass Static Method</th>
</tr>
<tr>
<th>Subclass Instance Method</th>
<td>Overrides</td>
<td>Generates a compile-time error</td>
</tr>
<tr>
<th>Subclass Static Method</th>
<td>Generates a compile-time error</td>
<td>Hides</td>
</tr>
</table>
注: 在子类, 你可以重载父类方法, 重载的方法既不覆盖也不隐藏父类的方法, 他们是独一无二的新的方法. 

## 多态Polymorphism

字典里的多态性定义源于一个生物原理, 一个生物或者物种可以具有不同的形式或者阶段. 这一原则也可以适用于面向对象编程和语言. 一个类的子类可以定义自己的独特行为, 也可以共享父类的某些相同功能. 

官方用[自行车的例子](https://docs.oracle.com/javase/tutorial/java/IandI/polymorphism.html)来展示了Java中的多态. 

他通过山地车和公路自行车都继承了自行车类, 并根据各自不同的特点来改写输出状态的方法. 然后在测试类中用自行车类型来引用不同的对象, 调用同样的方法输出不同的结果. 

他解释到JVM会根据变量中引用的对象来调用合适的方法. 它不是调用变量类型定义的方法. 这种行为称作虚拟方法调用(virtual method invocation). 

## 隐藏字段Hiding Fields

在一个类中, 有与父类相同字段名的字段会隐藏父类的字段, 尽管类型并不相同. 在子类, 父类的字段不能直接用简单的名字来引用, 必须通过super关键词来访问. 一般来说是不建议隐藏字段的, 因为这会使得代码难以阅读. 

## 使用super关键词

### 访问父类成员

如果你覆盖了父类的方法, 你可以在子类用super调用父类被覆盖的方法. 同样你也可以用它来访问你覆盖的字段名. 
官方举例: 
~~~
public class Superclass {
    public void printMethod() {
        System.out.println("Printed in Superclass.");
    }
}
~~~
这是子类, 覆盖了父类的方法, 并在覆盖的方法中调用了父类被覆盖的方法:  
~~~
public class Subclass extends Superclass {

    // overrides printMethod in Superclass
    public void printMethod() {
        super.printMethod();
        System.out.println("Printed in Subclass");
    }
    public static void main(String[] args) {
        Subclass s = new Subclass();
        s.printMethod();    
    }
}
~~~
输出结果: 
~~~
Printed in Superclass.
Printed in Subclass
~~~

### 子类构造器Subclass Constructors

子类构造器在调用父类构造方法时必须写在构造器第一行, 语法是: 
~~~
super();
~~~
或者带参数的构造方法: 
~~~
super(parameter list);
~~~
在super()里, 父类的无参构造器会被调用, 而有参的构造会调用父类匹配上的有参构造. 
注: 如果构造器没有显示调用父类构造器, 编译器会自动的给你插入一个调用父类无参构造. 如果父类没有无参构造, 那么我们就会得到一个编译时错误. 而Object对象是有这么一个空构造函数的. (虽然看源代码好像没有显式的写出来)

如果一个子类构造器调用一个父类构造器, 不管显式还是隐式调用, 你可能会觉得这像是一条构造器链, 无论那条路最终都会调用到Object类的构造器. 官方说事实就是这样的...这个叫构造函数链(constructor chaining), 当这条链比较长时你就要注意一下啦. 

## 作为父类的Object

在java.lang包里的Object类, 坐在所有类层次树的顶端. 每个类都是他的后代, 直接或者不直接. 每个你用或者写的类, 都继承Object的方法. 你不需要使用任何这些方法, 但是如果你选择使用它, 那么你可能需要在你的特定类覆盖它. 在本章将讨论继承自Object的方法. 

~~~ java
protected Object clone() throws CloneNotSupportedException// 创建和返回一个对象复制品
public boolean equals(Object obj)// 表明对象是否"equal to"另外一个对象
protected void finalize() throws Throwable// 当垃圾收集器garbage collection 确定没有引用这个对象时, 该对象的这个方法被垃圾收集器调用
public final Class getClass()// 返回一个对象的运行时类, 如果是.class是编译时确定
public int hashCode()// 返回该对象的hash code值
public String toString()// 返回一个对象的字符串表示
~~~

对象的notify, notifyAll, 和wait方法都参与同步程序的线程活动, 它们将会在后面的章节讨论而不是这里. 他们有五个这种方法: 

~~~ java
public final void notify()
public final void notifyAll()
public final void wait()
public final void wait(long timeout)
public final void wait(long timeout, int nanos)
~~~
注: 这些方法都有微妙的方面, 特别是clone方法. 

### clone()方法

如果一个类或者它其中一个父类实现了Cloneable接口, 你可以使用clone()方法来给现存的对象创建一个复制品. 创建一份克隆内容, 使用: 
~~~
aCloneableObject.clone();
~~~
这个方法的对象实现检查调用clone()方法的对象是否实现Cloneable接口. 如果对象没有, 方法则抛出一个CloneNotSupportedException异常. 异常处理会在以后的内容介绍. 目前, 如果你需要重写clone()方法, 你只需要知道clone()必须声明为: 
~~~
protected Object clone() throws CloneNotSupportedException
~~~
或者
~~~
public Object clone() throws CloneNotSupportedException
~~~
如果调用clone()方法的对象实现了Cloneable接口, 那么clone()方法的对象实现会创建一个和原类相同的对象, 初始化新对象的成员变量对应的值和调用的对象的值一样. 
最简单的方式让你的类可克隆就是添加实现Cloneable的接口到你的类声明上. 然后该类创建的对象就能调用clone()方法. 
对于某些类, Object类的克隆方法的默认行为可以正常工作. 如果, 一个对象包含一个外部对象引用, 假设叫ObjExternal, 你可能需要覆盖clone()方法使其正常工作. 否则对ObjExternal对象的更改在另一个克隆对象也会可见. (也就是浅克隆, ObjExternal对象并没有再克隆一份, 原克隆对象和克隆对象引用同一个ObjExternal) 这意味着原对象和它克隆出的对象都不是独立的. 如果要解耦它们, 你必须重写clone()然后它才克隆对象和ObjExternal. 然后原对象引用一个ObjExternal, 克隆出的对象引用不同的ObjExternal对象. 然后对象和克隆才算是真正独立了. 

### equals()方法(注意这里末尾有个小s)

equals()方法比较两个对象是否相等, 如果相等返回true. 在Object里提供的equals是使用==操作符来决定两个对象是否相等. 对于原始数据类型这将给出正确的结果. 但对于对象来说不是这样的. 对于对象来说equals()方法是比较两个引用是否相同, 如果相同那么就是引用同一个对象. 
想要知道两个对象(对象内的信息也相同)是否相同, 你必须重写equals()方法. 这里有一个Book类覆盖equals()方法: 
~~~
public class Book {
    ...
    public boolean equals(Object obj) {
        if (obj instanceof Book)
            return ISBN.equals((Book)obj.getISBN()); 
        else
            return false;
    }
}
~~~
思考一下代码, 它测试两个Book类的实例是否相等: 
~~~
// Swing Tutorial, 2nd edition
Book firstBook  = new Book("0201914670");
Book secondBook = new Book("0201914670");
if (firstBook.equals(secondBook)) {
    System.out.println("objects are equal");
} else {
    System.out.println("objects are not equal");
}
~~~
这个程序显示"objects are equal"尽管两个对象引用并不相等. 它们被认为是平等的因为两个对象包含同样的ISBN号码. 
你应该总是覆盖你的equals()方法, 如果==操作对你的类不合适. 
注: 如果你覆盖了equals(), 你最好也覆盖hashCode(). 

### finalize()方法

Object对象提供一个回调函数, finalize()对就是这个玩意, 它将会在一个将被回收的对象调用. Object的finalize()方法默认啥也不干, 你可以覆盖他来做一些清理工作, 比如释放资源. 
finalize()方法也可能自动的被系统调用, 但是什么时候调用, 甚至是调用了都是不确定的. 因此你不应该以来这个方法去做一些清洁工作. (真想捶死出教程这个人前面还说可以的) 例如, 在你执行文件I/O操作之后没有关闭文件描述符, 你期望finalize()去关闭它, 你可能就用完了文件描述符. (文件描述符file descriptors简单来说就是操作系统会创建一个实体来代表文件并存储这个打开了的文件的信息. 你打开100个系统就创建100个, 通常创建在系统核心. 通常实体会有非负的整数, 比如(100, 101, 102...)这些实体数字就是文件描述符. 同样当你打开一个网络套接字network socket也有相应的叫套接字描述符Socket Descriptor的东西)

### getClass()方法

你不能覆盖getClass. 

这个getClass()方法返回一个Class对象, 你可以拿这个Class对象获得一些类的信息, 例如getSimpleName()获得类名, getSuperclass()获得父类, getInterfaces()获得它实现的接口. 例如, 下面的方法得到和显示一个对象的类名: 
~~~
void printClassName(Object obj) {
    System.out.println("The object's" + " class is " +
        obj.getClass().getSimpleName());
}
~~~
这个在java.lang包中的Class类由非常多的方法(大于50). 例如, 你可以使用isAnnotation()测试看看这个类是不是注解, 用isInterface()测试是不是接口, 或者isEnum()是不是枚举. 你可以看这个对象的字段是啥getFields(), 方法有啥getMethods(), 等等. 

### hashCode()方法

由hashCode()方法返回的值是对象的hash code, 是对象在内存中的地址(十六进制). 
根据定义, 如果两个对象是相等的, 那么他们的hash code必须相同.(它的意思是对象内容相同地址不同也算对象相同, 说了是两个对象嘛) 如果你覆盖了equals()方法, 更改了两个对象的相等方式, 那么hashCode()方法Object的实现不再有效. 因此你覆盖了equals()方法, 你必须也覆盖hashCode()方法. (扩展Hash Table和Hash Map)

### toString()方法

你在类中应该总是考虑一下覆盖toString()方法. 
对象的toString()方法返回一个String代表这个对象, 这个对于debug非常有用. 对象的String表示完全依赖于对象, 这就是为什么你需要覆盖toString()的原因. 
你可以随着System.out.println()使用toString来显示一个对象的文字代表. 例如Book的一个实例: 
~~~
System.out.println(firstBook.toString());
~~~
你应该在toString写一些有用的对象内容比如: 
~~~
ISBN: 0201914670; The Swing Tutorial; A Guide to Constructing GUIs, 2nd Edition
~~~

## 写Final类和方法

你可以声明一些或者所有类的方法为final. 你使用final修饰一个方法代表它不能被子类覆盖. Object类里有一些final方法. 
你可能希望使用final来使一个方法不被改变并且保持严格的一致状态. 例如, 你可能想做一个getFirstPlayer 在ChessAlgorithm 类中: 
~~~
class ChessAlgorithm {
    enum ChessPlayer { WHITE, BLACK }
    ...
    final ChessPlayer getFirstPlayer() {
        return ChessPlayer.WHITE;
    }
    ...
}
~~~
被构造器调用的方法通常最好定义成final. 如果一个构造器调用一个非final方法, 子类可能重新那个方法, 但可能会有一些意想不到的后果. 
你可以定义一个实体类为final, 但是这种类无法被子类继承. 这挺有用的, 比如当创建一个像String类一样不变的类时. 

## 抽象方法和类

一个抽象类是一个被abstract修饰符修饰的类, 它可能包括或者不包括抽象方法. 抽象类无法被实例化但是可以被继承. 
一个抽象方法是定义了然后没有实现的方法. (没有大括号, 只是后面跟着一个分号)比如: 
~~~
abstract void moveTo(double deltaX, double deltaY);
~~~
如果一个类有抽象方法, 那么类自身必须定义为抽象的, 比如: 
~~~
public abstract class GraphicObject {
   // declare fields
   // declare nonabstract methods
   abstract void draw();
}
~~~
当子类继承一个抽象类, 通常子类必须实现所有的父类抽象方法. 如果它没有完全实现, 那也得定义为abstract. 
注: 在接口里的方法没有定义为default或者static都是隐式abstract的, 所以abstract修饰符在定义接口方法时不是很必要. 你可以用但是不需要. 

### 抽象类和接口的比较

抽象类和接口非常像. 你不能实例化它们, 而且包含了声明了但没有实现或者实现了的方法. 然而, 对于抽象类你可以定义不是静态和final的字段, 而且还能定义public, protected, private的具体方法. 对于接口, 所有字段默认是public, static和final, 而且所有方法都是public的. 此外, 你只能继承一个方法, 不管他是否被abstract修饰, 而你可以实现任何数量的接口. 
我们应该使用哪一个呢? 抽象类还是接口? (疑问脸)
- 如果符合下面条件可以考虑用抽象类: 
  - 你想在几个紧密相关的类中共享代码. 
  - 你希望扩展抽象类由非常多常见的方法或字段, 或者需要访问修饰符不是public(例如protected和private)
  - 你想定义非静态或非final字段. 这个使你能够定义方法来修改一些属于对象自己的状态. 
- 如果符和下面这些条件可以考虑用接口: 
  - 你希望不相干的类能实现你的接口. 例如接口Comparable 和 Cloneable被很多不想关的类实现. 
  - 你想指定特定数据类型的行为, 但不关心谁实现这些行为. 
  - 你希望利用多继承. 
  
一个在JDK里的抽象类例子是AbstractMap, 它是集合框架中的一部分. 它的子类(包括HashMap, TreeMap, ConcurrentHashMap)共享了很多AbstractMap定义的方法(包括get, put, isEmpty, containsKey, containsValue). 
一个JDK中实现了很多接口的类是HashMap, 实现了Serializable, Cloneable, 和Map<K, V>. 根据这些接口你可以判断一个HashMap(不管哪个开发者或者公司实现这个类. 其实java有标准, 然后有很多种java实现比如Oracle的或者OpenJDK)的实例是可以克隆的, 可序列化的(意思是可以转成字节流), 具有map功能. 此外, Map<K, V> 接口已经通过许多默认方法得到了增强, 比如merge和forEach, 而曾经实现了这个接口的旧类就不需要定义啦. 
注意很多软件库抽象类和接口都是用. 那个HashMap类实现很多接口, 但也继承抽象类AbstractMap. 

### 一个抽象类例子

在一个面向对象的画图应用中, 你可以画圆, 矩形, 线, 贝兹曲线Bezier curves和非常多的图形对象. 他们都有确定的状态(例如: 位置, 方向, 线条颜色, 填充色)和共同行为(比如: moveTo, 旋转, 改变大小, 绘画). 其中一些状态和行为对于图像对象来说是一样的. (例如: 位置, 填充色和倒向moveTo?) 其他需要不同的实现. (比如重置大小或绘画) 所有GraphicObject应该必须能够重置大小或绘画. 它们不同的地方是如何做. 这是抽象父类理想的情况. 你可以利用相同点定义在一个对象中, 并让子类继承这个抽象对象. 例如下图的GraphicObject. 
![GraphicObject结构图](/picture/20200505-0.jpg)
首先你定义一个抽象类, GraphicObject, 来提供一些所有子类都应该共有的一些方法和成员变量. 而且也提供一些应该由不同子类以不同方式实现的抽象方法. 这个对象很可能是这样的: 
~~~
abstract class GraphicObject {
    int x, y;
    ...
    void moveTo(int newX, int newY) {
        ...
    }
    abstract void draw();
    abstract void resize();
}
~~~
每个非抽象的子类必须提供draw()和resize()的实现, 例如下面的: 
~~~
class Circle extends GraphicObject {
    void draw() {
        ...
    }
    void resize() {
        ...
    }
}
class Rectangle extends GraphicObject {
    void draw() {
        ...
    }
    void resize() {
        ...
    }
}
~~~

### 当一个抽象类实现了一个接口

在前面的章节我们知道了实现了接口的类必须实现接口里所有的方法. 这是可能的情况, 然而如果一个类没有实现所有的接口方法, 那这个类需要被定义为抽象的. 
~~~
abstract class X implements Y {
  // implements all but one method of Y
}

class XX extends X {
  // implements the remaining method in Y
}
~~~
在上面的例子中X必须为abstract因为它没有完全实现Y里面的方法. 但是类XX完成了实现Y中方法的使命. 

### 类成员

一个抽象类可能含有静态字段和静态方法. 你可以使用类名加静态方法和其它类一样直接调用该方法. (比如: AbstractClass.staticMethod())

## 总结

除了Object类之外, 所有类都有它的直接父类. 一个类继承父类的所有字段和方法无论直接或者间接. 子类可以覆盖它继承的方法或者隐藏它. (隐藏字段通常是不好的编程习惯)
Object类是整个类层级的顶部. 所有类都是Object类的子孙后代并且继承它的方法. 来自Object类中常用的方法包括toString(), equals(), clone(), 和 getClass().
你可以使用final来避免一个类被继承. 同样的你可以使用final来避免一个方法在子类被重写. 
抽象类仅能被继承不能直接被实例化. 一个抽象类可以包含抽象方法--那些声明了但没有实现的方法. 子类提供抽象方法的实现. 

# 注解Annotations
注解是元数据的一种, 提供有关程序的数据, 该数据不属于程序本身. 注解对代码本身没有直接的影响. 
## 注解格式The Format of an Annotation
一个简单的注解看起来是这样子的: 
~~~
@Entity
~~~
它用一个艾特@来告诉编译器他后面的内容是注解, 下面的注解名是Override
~~~
@Override
void myMethod()	{...}
~~~
注解可以包括元素, 比如我们使用文档注释的时候, 就会有@author注解
~~~
@Author(
   name = "my Name",
   date = "3/27/2003"
)
class MyClass() { ... }
~~~
或者
~~~
@SuppressWarnings(value = "unchecked")
void myMethod() { ... }
~~~
如果只有一个元素名称叫value, 那么名称可以省略: 
~~~
@SuppressWarnings("unchecked")
void myMethod() { ... }
~~~
如果注解没有元素, 可以像@Override一样省去括号. 
可以在声明处声明多个注解: 
~~~
@Author(name = "Jane Doe")
@EBook
class MyClass { ... }
~~~
如果注解有多个相似的类型, 那么这就是重复注解(repeating annotation): 
~~~
@Author(name = "Jane Doe")
@Author(name = "John Smith")
class MyClass { ... }
~~~
重复注解在Java SE 8版本后支持, 详情: [Repeating Annotations.](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html)
上面的Override和SuppressWarnings是Java中预定义的注解, 你自己也可以创建自己的注解. 
Java8之后注解几乎可以用在各种[奇奇怪怪的地方](https://docs.oracle.com/javase/tutorial/java/annotations/basics.html). 
## 声明一个注解类型[Declaring an Annotation Type](https://docs.oracle.com/javase/tutorial/java/annotations/declaring.html)
## 预定义的注解类型[Predefined Annotation Types](https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html)
## 类型注解和可插拔类型系统[Type Annotations and Pluggable Type Systems](https://docs.oracle.com/javase/tutorial/java/annotations/type_annotations.html)
Java8之后, 注解可以用在类型上, 所以也叫类型注解. 它是为了支持对Java程序的改进分析, 更强的代码检查方式而出现的. Java8不提供类型检查框架, 它允许你编写或下载类型检查框架. 
大多数情况下你不需要手动写类型检查模块, 你可以使用[The Checker Framework](https://checkerframework.org/), 它帮你实现了防止空引用@NonNull, 互斥锁@mutex lock等模块. 
## 重复注解[Repeating Annotations](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html)

# 0.反射API

## 反射的使用

反射经常被程序用于检查或修改运行在JVM中的程序的行为. 这是一项相对先进的功能, 仅适用于对JAVA语言有较深把握的开发者们. 注意事项: 反射是一种强大的技术, 可以让程序执行原本不可能执行的操作. 

### 扩展功能

一个程序可以通过fully-qualified names利用到外部或者用户定义的类来创建一个可扩展性对象实例. (fully-qualified names在计算机编程里指的是不含糊的名称, 它唯一指定一个程序中的对象, 函数或者变量.[维基百科Fully qualified name](https://en.wikipedia.org/wiki/Fully_qualified_name))

### 类浏览器(Class Browsers)和可视化开发环境

一个类浏览器需要能够去枚举类中的成员. 可视化开发环境可以从反射中的类型信息受益, 从而去帮助开发者编写正确的代码. 

### 调试器和测试工具Debuggers and Test Tools

调试器需要能够去检查类中的私有成员. 测试工具可以利用反射系统地调用定义在类中的可发现的API集合, 来确保在测试套件中的高级的代码覆盖率. 

## 反射的缺点

反射很强大但是不应该随便的使用. 如果能够不使用反射来执行同样的功能, 那么最好避免使用反射. 使用反射时应该记住下面的内容. 

### 性能开销

因为反射涉及类型是动态解析的, 某些JVM优化可能不会执行. 因此, 反射的操作性能比没有反射的操作性能低, 和应该避免使用在对性能敏感的应用程序经常调用的代码段中. 

### 内部暴露

因为反射允许执行一些在正常代码中不合法的操作, 例如可以直接访问私有字段和方法, 使用反射可能会导致不可预估的副作用, 它可能会使代码功能失调和破坏可移植性. 反射打破了抽象, 因此可能随着平台的升级它的行为会改变. 

# 1.类Classes

该课程会展示获取一个类对象的多种方式和使用它来检查一个类的属性, 包括声明和内容. 
Java中所有类型不是引用就是基本类型. 类Classes, 枚举enums, 数组arrays(继承java.lang.Object的)以及接口interface都是引用类型reference types. 引用类型的例子包括java.lang.String, 所有基础类型的包装类比如java.lang.Double, 接口java.io.Serializable和枚举javax.swing.SortOrder. 下面是一组固定的原始类型: boolean, byte, short, int, long, char, float, 和double. 
对于所有类型的对象, JVM会实例化一个java.lang.Class类型的不变实例(immutable instance), 它能提供方法来检查运行中对象的属性, 包括方法和类型信息. 它也提供创建新的类和对象的能力. 最重要的是它是所有反射API的入口. 下面将展示类中最常用的一些反射操作: 
- 检索类对象Retrieving Class Objects, 描述得到一个类Class的方式
- 检查类的修饰符和类型, 展示如何访问修饰符信息. 
- 发现类成员, 展示如何列出类中的构造器, 字段, 方法和嵌套类. 
- 故障排除, 描述当使用Class类时遇到的常见错误. 

## 检索类对象Retrieving Class Objects

所有反射操作的入口是java.lang.Class. 除了java.lang.reflect.ReflectPermission类, java.lang.reflect里的所有类都没有public的构造函数. 我们有必要调用Class类中合适的方法来得到一个类. 下面有几种获取Class的方式, 它基于代码是否有权访问一个对象, 类名, 类型, 或者一个存在的类. 

### Object.getClass()

如果有对象实例可用, 最简单的方式就是直接调用Object.getClass()来获得一个Class. 当然这只适用于继承了Object的对象. 一些例子: 
~~~
Class c = "foo".getClass();
~~~
返回String的Class
~~~
Class c = System.console().getClass();
~~~
这是和虚拟机关联的独一无二的控制台, 它是通过static方法System.console()返回的. 通过getClass()返回的值是对应于java.io.Console的Class. (FIXME我在测试的时候报了空指针异常...)
~~~
enum E { A, B }
Class c = A.getClass();
~~~
A是枚举类型E的实例, 因此getClass()返回的Class对应于枚举类型E. 
~~~
byte[] bytes = new byte[1024];
Class c = bytes.getClass();
~~~
由于数组arrays是一个对象, 所以可以在一个实例中调用getClass(), 它的返回值对应类型byte. 
~~~
import java.util.HashSet;
import java.util.Set;

Set<String> s = new HashSet<String>();
Class c = s.getClass();
~~~
在这个例子中, java.util.Set是一个java.util.HashSet类的接口. 所以getClass()的返回值class是对应java.util.HashSet. 

### .class语法

如果类型可用但是没有实例方法, 那可以通过在类型名字后附加".class"来获取一个Class. 这也是获取原始类型Class最简单的方式. 
~~~
boolean b;
Class c = b.getClass();   // 编译时错误

Class c = boolean.class;  // 正确
~~~
注意上面的语句, boolean.getClass()会产生一个编译错误, 因为boolean是原始类型不能被取消引用dereferenced. (FIXME我也不是很清楚dereferenced的真正含义. ) .class语法返回对应boolean的Class. 
~~~
Class c = java.io.PrintStream.class;
~~~
变量c会是一个对应java.io.PrintStream类的类型. 
~~~
Class c = int[][][].class;
~~~
它会返回给定类型的多维数组Class.
 
### Class.forName()

如果一个类的fully-qualified name可用, 那么可以通过静态方法Class.forName()来获取对应的Class. 它不能用于基础类型. 为数组类名称的使用的方法是Class.getName(). 这个语法适用于引用类型和基础类型. 
~~~
Class c = Class.forName("com.duke.MyLocaleServiceProvider");
~~~
改语句会根据fully-qualified name创建一个类. 
~~~
Class cDoubleArray = Class.forName("[D");

Class cStringArray = Class.forName("[[Ljava.lang.String;");
~~~
变量cDoubleArray会获得一个对应基础类型double的Class. (比如和double[].class获取的一样). 而变量cStringArray获得一个对应二维String数组的Class. (比如和String[][].class相同). 

### 为基础类型包装的类型字段TYPE Field

.class语法是非常方便和推荐的获取一个基础类型Class的方式, 但他还有其他方法. 每个基础类型包括void在java.lang包下都有包装类, 它是用来把基础类型封装成引用类型的. 每个包装类包含一个TYPE的字段名, 它等于被包装原始类型的Class. 
~~~
Class c = Double.TYPE;
~~~
这是一个java.lang.Double类, 每当请求一个对象的时候会自动把double包装成它. 它的值等同于doublic.class. 
~~~
Class c = Void.TYPE;
~~~
Void.TYPE等同于void.class. 

### 返回类Class的方式

反射API中有很多返回类的方法, 但前提是它们已经直接或间接获得了Class. 
- Class.getSuperclass()
返回给定类的父类Class. 
~~~
Class c = javax.swing.JButton.class.getSuperclass();
~~~
javax.swing.JButton的父类是javax.swing.AbstractButton. 
- Class.getClasses()
返回该类所有public修饰的类, 接口和枚举成员, 包括继承的成员. 
~~~
Class<?>[] c = Character.class.getClasses();
~~~
Character包含两个成员Class, Character.Subset and Character.UnicodeBlock. (我测试的时候其实还有Character.UnicodeScript, 应该是后面版本新增的啦)
- Class.getDeclaredClasses()
返回类中所有显示声明的类接口和枚举. 
~~~
Class<?>[] c = Character.class.getDeclaredClasses();
~~~
跟上面的Class.getClasses()方法相比它还列出了私有成员Character.CharacterCache. 
- Class.getDeclaringClass()
- java.lang.reflect.Field.getDeclaringClass()
- java.lang.reflect.Method.getDeclaringClass()
- java.lang.reflect.Constructor.getDeclaringClass()
返回这些成员字段被定义的那个Class, 也就是定义在哪个Class里. 匿名类的声明不会有声明类declaring class但是会有封闭类enclosing class. 
~~~
import java.lang.reflect.Field;

Field f = System.class.getField("out");
Class c = f.getDeclaringClass();
~~~
成员字段out定义在System. 
~~~
public class MyClass {
    static Object o = new Object() {
        public void m() {} 
    };
    static Class<c> = o.getClass().getEnclosingClass();
}
~~~
匿名类的声明类declaring class由o定义的是null. 
- Class.getEnclosingClass()
返回该类的直接封闭类. (应该是谁包裹这个类的类的意思吧)
~~~
Thread.State.class.getEnclosingClass();
~~~
封闭类Thread.State是Thread. 也就是直接包裹Thread.State的是Thread类. 
在上面的MyClass类中, 用o定义的匿名类是封闭enclosed by在MyClass中的, 所以使用getEnclosingClass()方法得到封闭它的类MyClass. 

## 检查类Class修饰符和类型

一个类可能定义一个或多个修饰符, 它影响运行时的行为. 
- 访问修饰符: public, protected, private
- 要求覆盖修饰符: abstract
- 限制一个实例的修饰符: static
- 禁止值修改的修饰符: final
- 强制严格的浮点行为修饰符: strictfp(JDK 1.2版本中引入, 确保浮点数计算出的结果不会随着平台的不同而不同[ref: geeksforgeeks: strictfp keyword in java](https://www.geeksforgeeks.org/strictfp-keyword-java/))
- 注解

不是所有的修饰符在类中都是允许使用的, 比如接口不能被final修饰和枚举类型不能被abstract修饰. java.lang.reflect.Modifier包含了所有修饰符可能的声明. 它也包含给Class.getModifiers()返回的修饰符集合解码的方法.  
下面的ClassDeclarationSpy例子中展示了如何获得类的声明组件包括修饰符, 泛型参数, 实现的接口, 继承路径. 自从Class实现了java.lang.reflect.AnnotatedElement接口, 所以也可以查询运行时的注解. 
~~~
import java.lang.annotation.Annotation;
import java.lang.reflect.Modifier;
import java.lang.reflect.Type;
import java.lang.reflect.TypeVariable;
import java.util.Arrays;
import java.util.ArrayList;
import java.util.List;
import static java.lang.System.out;

public class ClassDeclarationSpy {
    public static void main(String... args) {
	try {
	    Class<?> c = Class.forName(args[0]);
	    out.format("Class:%n  %s%n%n", c.getCanonicalName());
	    out.format("Modifiers:%n  %s%n%n",
		       Modifier.toString(c.getModifiers()));

	    out.format("Type Parameters:%n");
	    TypeVariable[] tv = c.getTypeParameters();
	    if (tv.length != 0) {
		out.format("  ");
		for (TypeVariable t : tv)
		    out.format("%s ", t.getName());
		out.format("%n%n");
	    } else {
		out.format("  -- No Type Parameters --%n%n");
	    }

	    out.format("Implemented Interfaces:%n");
	    Type[] intfs = c.getGenericInterfaces();
	    if (intfs.length != 0) {
		for (Type intf : intfs)
		    out.format("  %s%n", intf.toString());
		out.format("%n");
	    } else {
		out.format("  -- No Implemented Interfaces --%n%n");
	    }

	    out.format("Inheritance Path:%n");
	    List<Class> l = new ArrayList<Class>();
	    printAncestor(c, l);
	    if (l.size() != 0) {
		for (Class<?> cl : l)
		    out.format("  %s%n", cl.getCanonicalName());
		out.format("%n");
	    } else {
		out.format("  -- No Super Classes --%n%n");
	    }

	    out.format("Annotations:%n");
	    Annotation[] ann = c.getAnnotations();
	    if (ann.length != 0) {
		for (Annotation a : ann)
		    out.format("  %s%n", a.toString());
		out.format("%n");
	    } else {
		out.format("  -- No Annotations --%n%n");
	    }

        // production code should handle this exception more gracefully
	} catch (ClassNotFoundException x) {
	    x.printStackTrace();
	}
    }

    private static void printAncestor(Class<?> c, List<Class> l) {
	Class<?> ancestor = c.getSuperclass();
 	if (ancestor != null) {
	    l.add(ancestor);
	    printAncestor(ancestor, l);
 	}
    }
}
~~~
下面是输出的一些样本. 
输入: 
~~~
$ java ClassDeclarationSpy java.util.concurrent.ConcurrentNavigableMap
~~~
输出: 
~~~
Class:
  java.util.concurrent.ConcurrentNavigableMap

Modifiers:
  public abstract interface

Type Parameters:
  K V

Implemented Interfaces:
  java.util.concurrent.ConcurrentMap<K, V>
  java.util.NavigableMap<K, V>

Inheritance Path:
  -- No Super Classes --

Annotations:
  -- No Annotations --
~~~
这也确实是java.util.concurrent.ConcurrentNavigableMap在源代码中的实际声明: 
~~~
public interface ConcurrentNavigableMap<K,V>
    extends ConcurrentMap<K,V>, NavigableMap<K,V>
~~~
注意它是个接口, 所以修饰符里含有隐式的abstract. 编译器给所有接口都添加这个修饰符. 同样这个声明包含了两个泛型参数K和V. 例子中只是简单的输出了参数的名字, 我们还可以使用java.lang.reflect.TypeVariable中的一些方法获取更多的信息. 在上面的例子中接口可以实现其他接口. 
输入: 
~~~
$ java ClassDeclarationSpy "[Ljava.lang.String;"
~~~
输出: 
~~~
Class:
  java.lang.String[]

Modifiers:
  public abstract final

Type Parameters:
  -- No Type Parameters --

Implemented Interfaces:
  interface java.lang.Cloneable
  interface java.io.Serializable

Inheritance Path:
  java.lang.Object

Annotations:
  -- No Annotations --
~~~
因为数组是一个运行时对象runtime objects(理解成JVM虚拟机底层的内容就可以啦), 所有类型信息由JVM定义. 特别是, 数组实现了Cloneable和java.io.Serializable接口, 它的直接父类也是Object. 
输入: 
~~~
$ java ClassDeclarationSpy java.io.InterruptedIOException
~~~
输出: 
~~~
Class:
  java.io.InterruptedIOException

Modifiers:
  public

Type Parameters:
  -- No Type Parameters --

Implemented Interfaces:
  -- No Implemented Interfaces --

Inheritance Path:
  java.io.IOException
  java.lang.Exception
  java.lang.Throwable
  java.lang.Object

Annotations:
  -- No Annotations --
~~~
从继承路径可以看到java.io.InterruptedIOException是一个检查异常checked exception因为路径中没有RuntimeException. (除了Error及它子类和RuntimeException及它子类是非检查异常unchecked exception其他的都是检查异常)
输入:
~~~
$ java ClassDeclarationSpy java.security.Identity
~~~
输出:
~~~
Class:
  java.security.Identity

Modifiers:
  public abstract

Type Parameters:
  -- No Type Parameters --

Implemented Interfaces:
  interface java.security.Principal
  interface java.io.Serializable

Inheritance Path:
  java.lang.Object

Annotations:
  @java.lang.Deprecated()
~~~
从输出中的注解我们java.lang.Deprecated可以知道java.security.Identity是一个不推荐再使用的deprecated API. 这可以被反射代码检测出哪些是不推荐再使用的API. 
注: 不是所有的注解都能通过反射获取, 只有RUNTIME(注解记录在类文件并且运行时VM保留)在java.lang.annotation.RetentionPolicy中的是可访问的. 在预定义的三个注解 @Deprecated, @Override, 和 @SuppressWarnings中, 只有@Deprecated在运行时可用. 

## 发现类成员

Class类中提供了两类方法来访问字段, 方法和构造器: 枚举这些成员的方法和搜索特定的成员的方法. 同样有不同的方法来直接访问类的成员与在超类接口和超类中寻找继承成员. 下面的表格总结了所有定位成员的方法和特点. 
定位字段的类方法: 
<table><tr>
<th>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html"><code>Class</code></a> API</th>
<th>List of members?</th>
<th>Inherited members?</th>
<th>Private members? <!-- Field =====================--></th>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredField-java.lang.String-"><code>getDeclaredField()</code></a></td>
<td align="center">no</td>
<td align="center">no</td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getField-java.lang.String-"><code>getField()</code></a></td>
<td align="center">no</td>
<td align="center">yes</td>
<td align="center">no</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredFields--"><code>getDeclaredFields()</code></a></td>
<td align="center">yes</td>
<td align="center">no</td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getFields--"><code>getFields()</code></a></td>
<td align="center">yes</td>
<td align="center">yes</td>
<td align="center">no</td>
</tr>
</table>
定位方法的类方法: 
<table><tr>
<th>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html"><code>Class</code></a> API</th>
<th>List of members?</th>
<th>Inherited members?</th>
<th>Private members?</th>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredMethod-java.lang.String-java.lang.Class...-"><code>getDeclaredMethod()</code></a></td>
<td align="center">no</td>
<td align="center">no</td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getMethod-java.lang.String-java.lang.Class...-"><code>getMethod()</code></a></td>
<td align="center">no</td>
<td align="center">yes</td>
<td align="center">no</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredMethods--"><code>getDeclaredMethods()</code></a></td>
<td align="center">yes</td>
<td align="center">no</td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getMethods--"><code>getMethods()</code></a></td>
<td align="center">yes</td>
<td align="center">yes</td>
<td align="center">no</td>
</tr>
</table>
定位构造方法的类方法: 
<table><tr>
<th>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html"><code>Class</code></a> API</th>
<th>List of members?</th>
<th>Inherited members?</th>
<th>Private members?</th>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredConstructor-java.lang.Class...-"><code>getDeclaredConstructor()</code></a></td>
<td align="center">no</td>
<td align="center">N/A<sup>1</sup></td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getConstructor-java.lang.Class...-"><code>getConstructor()</code></a></td>
<td align="center">no</td>
<td align="center">N/A<sup>1</sup></td>
<td align="center">no</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getDeclaredConstructors--"><code>getDeclaredConstructors()</code></a></td>
<td align="center">yes</td>
<td align="center">N/A<sup>1</sup></td>
<td align="center">yes</td>
</tr>
<tr>
<td>
<a class="APILink" target="_blank" href="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getConstructors--"><code>getConstructors()</code></a></td>
<td align="center">yes</td>
<td align="center">N/A<sup>1</sup></td>
<td align="center">no</td>
</tr>
</table>
注1: 构造函数不继承. 
给定一个类名和感兴趣的成员, ClassSpy例子会使用get*s()方法决定列出的元素, 包括继承的内容. 
官方给出的例子: 
~~~
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.Member;
import static java.lang.System.out;

enum ClassMember { CONSTRUCTOR, FIELD, METHOD, CLASS, ALL }

public class ClassSpy {
    public static void main(String[] args) {
        try {
            args = new String[]{"java.lang.Integer", "ALL"};
            Class<?> c = Class.forName(args[0]);
            out.format("Class:%n  %s%n%n", c.getCanonicalName());

            Package p = c.getPackage();
            out.format("Package:%n  %s%n%n",
                    (p != null ? p.getName() : "-- No Package --"));

            for (int i = 1; i < args.length; i++) {
                switch (ClassMember.valueOf(args[i])) {
                    case CONSTRUCTOR:
                        printMembers(c.getConstructors(), "Constructor");
                        break;
                    case FIELD:
                        printMembers(c.getFields(), "Fields");
                        break;
                    case METHOD:
                        printMembers(c.getMethods(), "Methods");
                        break;
                    case CLASS:
                        printClasses(c);
                        break;
                    case ALL:
                        printMembers(c.getConstructors(), "Constuctors");
                        printMembers(c.getFields(), "Fields");
                        printMembers(c.getMethods(), "Methods");
                        printClasses(c);
                        break;
                    default:
                        assert false;
                }
            }

            // production code should handle these exceptions more gracefully
        } catch (ClassNotFoundException x) {
            x.printStackTrace();
        }
    }

    private static void printMembers(Member[] mbrs, String s) {
        out.format("%s:%n", s);
        for (Member mbr : mbrs) {
            if (mbr instanceof Field)
                out.format("  %s%n", ((Field)mbr).toGenericString());
            else if (mbr instanceof Constructor)
                out.format("  %s%n", ((Constructor)mbr).toGenericString());
            else if (mbr instanceof Method)
                out.format("  %s%n", ((Method)mbr).toGenericString());
        }
        if (mbrs.length == 0)
            out.format("  -- No %s --%n", s);
        out.format("%n");
    }

    private static void printClasses(Class<?> c) {
        out.format("Classes:%n");
        Class<?>[] clss = c.getClasses();
        for (Class<?> cls : clss)
            out.format("  %s%n", cls.getCanonicalName());
        if (clss.length == 0)
            out.format("  -- No member interfaces, classes, or enums --%n");
        out.format("%n");
    }
}
~~~
这个例子代码非常紧凑, 然而printMembers()方法略显尴尬, 因为在这个方法在早期的反射实现中java.lang.reflect.Member接口里已经存在, 而且在泛型被引入时无法修改它来引入更有用的getGenericString()方法. 唯一的选择就是向上面那样测试和转换, 使用printConstructors(), printFields(), 和 printMethods()来替换, 或者使用相对多余的Member.getName(). 
输入输出样例(当然上面代码中的第12行被我修改了, 如果想使用下面的输入输出删掉就好了): 
~~~
$ java ClassSpy java.lang.ClassCastException CONSTRUCTOR
~~~
输出: 
~~~
Class:
  java.lang.ClassCastException

Package:
  java.lang

Constructor:
  public java.lang.ClassCastException()
  public java.lang.ClassCastException(java.lang.String)
~~~
因为构造函数不继承, 所以定义在直接父类RuntimeException的异常链接机制构造函数(那些拥有Throwable参数的)和其他父类都找不到. 
输入: 
~~~
$ java ClassSpy java.nio.channels.ReadableByteChannel METHOD
~~~
输出: 
~~~
Class:
  java.nio.channels.ReadableByteChannel

Package:
  java.nio.channels

Methods:
  public abstract int java.nio.channels.ReadableByteChannel.read
    (java.nio.ByteBuffer) throws java.io.IOException
  public abstract void java.nio.channels.Channel.close() throws
    java.io.IOException
  public abstract boolean java.nio.channels.Channel.isOpen()
~~~
接口java.nio.channels.ReadableByteChannel定义了一个read()方法. 其余方法均继承于父类接口. 这段代码可以轻松的修改成仅列出某些方法, 把get*s()用getDeclared*s()替代就好了. 
输入(这个ClassMember就是目前写的这个程序): 
~~~
$ java ClassSpy ClassMember FIELD METHOD
~~~
输出: 
~~~
Class:
  ClassMember

Package:
  -- No Package --

Fields:
  public static final ClassMember ClassMember.CONSTRUCTOR
  public static final ClassMember ClassMember.FIELD
  public static final ClassMember ClassMember.METHOD
  public static final ClassMember ClassMember.CLASS
  public static final ClassMember ClassMember.ALL

Methods:
  public static ClassMember ClassMember.valueOf(java.lang.String)
  public static ClassMember[] ClassMember.values()
  public final int java.lang.Enum.hashCode()
  public final int java.lang.Enum.compareTo(E)
  public int java.lang.Enum.compareTo(java.lang.Object)
  public final java.lang.String java.lang.Enum.name()
  public final boolean java.lang.Enum.equals(java.lang.Object)
  public java.lang.String java.lang.Enum.toString()
  public static <T> T java.lang.Enum.valueOf
    (java.lang.Class<T>,java.lang.String)
  public final java.lang.Class<E> java.lang.Enum.getDeclaringClass()
  public final int java.lang.Enum.ordinal()
  public final native java.lang.Class<?> java.lang.Object.getClass()
  public final native void java.lang.Object.wait(long) throws
    java.lang.InterruptedException
  public final void java.lang.Object.wait(long,int) throws
    java.lang.InterruptedException
  public final void java.lang.Object.wait() hrows java.lang.InterruptedException
  public final native void java.lang.Object.notify()
  public final native void java.lang.Object.notifyAll()
~~~
在结果的字段部分, 枚举常量列了出来. 而这些是一些技术上的常量, 将它们和其他字段分清是很有用的. 为此可以修改实例使用java.lang.reflect.Field.isEnumConstant()来达到这个目的. 这个例子在后面的教程检查枚举包含一些可能的实现. (当然看我有没有偷懒继续写了)
在输出的方法部分中, 可以观察到方法名包括声明类. 因此toString()方法是被Enum实现的, 而不是继承自Object. 代码可以修改成Field.getDeclaringClass()来使其更明显. 以下片段说明了潜在的解决方法. 
~~~
if (mbr instanceof Field) {
    Field f = (Field)mbr;
    out.format("  %s%n", f.toGenericString());
    out.format("  -- declared in: %s%n", f.getDeclaringClass());
}
~~~

[故障排除Troubleshooting
](https://docs.oracle.com/javase/tutorial/reflect/class/classTrouble.html)

# 2.成员Members

反射定义了一个java.lang.reflect.Member接口, 被java.lang.reflect.Field, java.lang.reflect.Method, 和 java.lang.reflect.Constructor实现. 接下来会介绍这些类. 对于每个成员, 都将会介绍获取修饰符和信息的一些API, 还有对每个成员都独一无二的操作(例如设置字段值或调用一个方法), 和遇到的常见错误. 每一章会演示一些代码和相关的输出, 这些相关的输出来近似预期一些反射的用途. 
注: 根据[Java语言规范, JavaSE7版](https://docs.oracle.com/javase/specs/jls/se7/html/index.html), 一个类的成员是类体继承的组件包括字段, 方法, 嵌套类, 接口和枚举类型. 因为构造函数不继承, 所以它不是成员. 这与java.lang.reflect.Member实现类不同. 

## 字段Fields

字段拥有类型和值. java.lang.reflect.Field类提供了对给定的对象可以访问类型信息和get, set对应值的一些方法. 
- 获得字段类型Obtaining Field Types章节描述了如何得到字段的声明和通用类型. 
- 检索和解析字段修饰符Retrieving and Parsing Field Modifiers展示了如何得到一部分的字段声明, 例如public或者transient. 
- 读和设置字段值Getting and Setting Field Values展示了如何访问字段值. 
- 故障排除Troubleshooting描述了一些常见的使用混乱的代码错误. 

## 方法Methods

方法拥有返回值, 参数还可能抛出异常. java.lang.reflect.Method类提供了一些用于获取参数和返回值类型信息的方法. 它也可以用于调用给定对象的方法. 
- 获取方法类型信息Obtaining Method Type Information展示如何去列举类中声明的方法和获取它的类型信息. 
- 获取方法参数名Obtaining Names of Method Parameters展示了如何检索方法参数或构造器参数的名字和其他信息. 
- 检索和解析方法修饰符Retrieving and Parsing Method Modifiers阐述了如何访问和解析方法的修饰符和其他信息. 
- 调用方法Invoking Methods展示如何执行一个方法并获得返回值. 
- 故障排除Troubleshooting描述了一些常见的错误在找或者调用一个方法时.
 
## 构造器Constructors

反射中关于构造器的API定义在java.lang.reflect.Constructor, 和上面的方法很像, 但有两处不一样: 首先构造器没有返回值; 其次构造器的调用是用给定类创建一个新的实例. 
- 寻找构造器Finding Constructors展示根据特定参数寻找构造器. 
- 检索和解析构造器的修饰符Retrieving and Parsing Constructor Modifiers展示如何获取一个构造器的修饰符和其他信息. 
- 创建新的实例Creating New Class Instances展示了如何通过调用构造器去实例化一个对象. 
- 故障排除Troubleshooting描述了一些常见的错误在找或者调用一个构造函数时.

(偷懒了后面内容和前面差不多的啦. 想看哪个接着看吧) 
[Members](https://docs.oracle.com/javase/tutorial/reflect/member/index.html)

# Number相关

讨论java.lang.Number类以及相关子类, 以及什么情况下你用这个类而不是基础数据类型. 
这个章节也提供PrintStream和DecimalFormat类的说明, 它们提供数字类型的格式化输出方法. 
最后讨论java.lang.Math类, 它包含数学函数补充了语言内置的运算符. 如三角函数, 指数函数等. 

## 数字类The Numbers Classes

使用数字时很多时候我们都在代码中使用基础类型. 比如: 
~~~
int i = 500;
float gpa = 3.65f;
byte mask = 0xff;
~~~
然而有时候有使用对象代替数字的原因, 而Java平台给每个基础类型提供包装类型. 这些类把基础类型包装成对象. 一般包装的过程是由编译器完成的(如果你使用基础类型但是却希望得到一个对象, 编译器就会封装一个基础类型到包装类中. 同样的, 你希望从一个对象得到基础类型, 编译器就会拆箱. ). 了解更多看[自动装箱和拆箱Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)
所有数字的包装类都是抽象类Number的子类: 
![Oracle官方提供的图片](/picture/20200519-0.jpg)
注: 有4个其他Number的子类没有在这里讨论. BigDecimal和BigInteger是用来进行高精度计算的. AtomicInteger和AtomicLong是用在多线程应用的. 

这里有三个你可能使用Number对象而不是基础类型的理由: 
- 1. 作为一个需要对象方法参数的时候(当使用和操作集合的时候经常用到). 
- 2. 需要利用到类中定义的常量, 比如MIN_VALUE最小值和MAX_VALUE最大值, 它提供一个数据类型的上下限. 
- 3. 使用类方法在其他原始类型之间进行值转换, 在字符串之间进行转换以及在数字系统之间进行转换(小数, 八进制, 十六进制, 二进制). 

下面的表格列出了Number类所有子类实例实现的实例方法. 
<table><tr>
<th>方法</th>
<th>描述</th>
</tr>
<tr>
<td>
byte byteValue()<br>
short shortValue()<br>
int intValue()<br>
long longValue()<br>
float floatValue()<br>
double doubleValue()
</td>
<td>将Number对象的值转换成基础类型</td>
</tr>
<tr>
<td>int compareTo(Byte anotherByte)<br>
int compareTo(Double anotherDouble)<br>
int compareTo(Float anotherFloat)<br>
int compareTo(Integer anotherInteger)<br>
int compareTo(Long anotherLong)<br>
int compareTo(Short anotherShort)</td>
<td>将Number对象和传入的参数做比较</td>
</tr>
<tr>
<td>boolean equals(Object obj)</td>
<td>确定Number对象是否等于参数. <br>
如果参数非空, 类型和值相同那么将返回true. <br>
但对于Double类和Float类有格外的要求, API文档中有说明. </td>
</tr>
</table>
每个Number类都包含了在字符串和数字之间和数字和数字系统之间转换的方法. 下面的表列出Integer类中的方法. 其他Number类的子类是类似的: 
<table><tr>
<th>方法</th>
<th>描述</th>
</tr>
<tr>
<td>static Integer decode(String s)</td>
<td>解码String作为一个整型对象. 可以接受一些十进制, 八进制或者十六进制作为输入. </td>
</tr>
<tr>
<td>static int parseInt(String s)</td>
<td>返回一个整数对象(仅十进制). </td>
</tr>
<tr>
<td>static int parseInt(String s, int radix)</td>
<td>根据给定的String和进制解析并返回int值. </td>
</tr>
<tr>
<td>String toString()</td>
<td>返回Integer对象的String对象. </td>
</tr>
<tr>
<td>static String toString(int i)</td>
<td>返回代表指定int的String对象(10进制). </td>
</tr>
<tr>
<td>static Integer valueOf(int i)</td>
<td>下面的懒得翻译了. Returns an Integer object holding the value of the specified primitive.</td>
</tr>
<tr>
<td>static Integer valueOf(String s)</td>
<td>Returns an Integer object holding the value of the specified string representation.</td>
</tr>
<tr>
<td>static Integer valueOf(String s, int radix)</td>
<td>Returns an Integer object holding the integer value of the specified string representation, parsed with the value of radix. For example, if s = "333" and radix = 8, the method returns the base-ten integer equivalent of the octal number 333.</td>
</tr>
</table>

## 格式化数字打印输出Formatting Numeric Print Output

在之前你看到了使用print和println方法来输出字符串到标准输出(System.out)中. 因为所有数字都可以转换成字符串, 因此你可以使用这些方法来混合输出字符串和数字. Java语言提供了其他输出的方法, 它们使得数字的输出更易于控制(格式化输出数字之类). 

在java.io包中包含一个PrintStream类, 该类有两个方法可以用来替换平常使用的print和println方法. 那就是format和printf方法, 它们彼此等效. 你熟悉使用的System.out恰好是一个PrintStream对象, 所以你可以通过System.out来使用PrintStream的方法. 所以你在使用print或者println的时候也可以使用format和printf方法. 
相关使用方法就不在这里列出了. 格式化输出, 小数等. 

## 基本算法之外Beyond Basic Arithmetic

Java提供基本的加减乘除还有取余算法, java.lang.Math类中提供了高级的数学算式方法和常数. 
Math类里的方法都是静态的, 所以可以直接调用它们. 
(注: 你可以使用静态导入(static import)的语言特性来导入Math类, 使得你不必再在使用该类的静态成员时加类名. )
例如: 
~~~
import static java.lang.Math.*;
~~~

### 常量和基本方法

这样在代码中就能直接使用E(自然对数)或者PI(圆周率)来使用Math类中的两个常数了. 
Math类包括了超过40个静态方法. 
<table><tr>
<th>方法名</th>
<th>描述</th>
</tr><tr>
<td>double abs(double d)<br>
float abs(float f)<br>
int abs(int i)<br>
long abs(long lng)</td>
<td>返回绝对值. </td>
</tr>
<tr>
<td>double ceil(double d)</td>
<td>返回一个大于或等于参数的最小浮点整数.(比如100.675返回101.0) </td>
</tr>
<tr>
<td>double floor(double d)</td>
<td>返回小于或等于参数的最大浮点整数.(比如100.675返回100.0) </td>
</tr>
<tr>
<td>double rint(double d)</td>
<td>返回接近参数的整数. 比如(100.5返回100.0, 100.6返回101.0)</td>
</tr>
<tr>
<td>long round(double d)<br>
int round(float f)</td>
<td>返回一个最接近的值(四舍五入). </td>
</tr>
<tr>
<td>double min(double arg1, double arg2)<br>
float min(float arg1, float arg2)<br>
int min(int arg1, int arg2)<br>
long min(long arg1, long arg2)</td>
<td>返回两个参数中最小的数. </td>
</tr>
<tr>
<td>double max(double arg1, double arg2)<br>
float max(float arg1, float arg2)<br>
int max(int arg1, int arg2)<br>
long max(long arg1, long arg2)</td>
<td>返回两个参数中最大的数. </td>
</tr>
</table>

### 指数和对数方法

<table><tr>
<th>Method</th>
<th width="50%">Description</th>
</tr>
<tr>
<td>double exp(double d)</td>
<td>返回底数为E(2.7183...)的参数次方. (也就是: e^d)</td>
</tr>
<tr>
<td>double log(double d)</td>
<td>返回参数的自然数底数的对数值. (也就是e的几次方等于d)</td>
</tr>
<tr>
<td>double pow(double base, double exponent)</td>
<td>返回base的exponent次方. </td>
</tr>
<tr>
<td>double sqrt(double d)</td>
<td>返回一个数的平方根. </td>
</tr>
</table>

### 三角函数方法

不常用不写了. 

### 随机数字

random()方法返回一个0.0到1.0(包括0.0不包括1.0)的伪随机数. 当你需要一串随机数时可以使用java.util.Random里的方法. 

# Character相关

## Characters
很多时候如果你使用一个字符, 你会使用char基础类型, 比如: 
~~~
// 字母a
char ch = 'a';
// 使用Unicode表示Ω字符(欧米伽字符哈哈哈omega)
char uniChar = '\u03A9';
// 字符数组
char[] charArray = { 'a', 'b', 'c', 'd', 'e' };
~~~
java会自动对char与Character类进行自动装箱和拆箱. 
注: Character类是一成不变的, 所以Character对象一旦被创建就无法改变)

### 转义序列Escape Sequences

以反斜杠\开头的是转义序列, 它对编译器有特殊含义. java中的转义序列: 
<table><tr>
<th width="30%">Escape Sequence</th>
<th>Description</th>
</tr>
<tr>
<td>\t</td>
<td>插入tab</td>
</tr>
<tr>
<td>\b</td>
<td>插入退格键backspace </td>
</tr>
<tr>
<td>\n</td>
<td>插入新的一行. </td>
</tr>
<tr>
<td>\r</td>
<td>回车回车??</td>
</tr>
<tr>
<td>\f</td>
<td>换页? 没查到是啥意思formfeed </td>
</tr>
<tr>
<td>\'</td>
<td>单引号字符. </td>
</tr>
<tr>
<td>\"</td>
<td>双引号字符. </td>
</tr>
<tr>
<td>\\</td>
<td>反斜杠字符. </td>
</tr>
</table>
当在打印语句遇到转义字符时, 编译器会相应的解释它. 

# String相关

String是在Java程序中用的最广泛的的东西, 它是由一串的字符构成. 

## 创建String字符串

你可以这么写来创建一个String对象: 
~~~
String greeting = "Hello world!";
~~~
在上面的例子中被引号包裹的Hello world!就是字面量string literal, 它由一系列字符串组成. 每当编译器遇到字面量的时候就会创建一个String对象(String有字符串池的概念, 有时候不一定每次都创建新的对象), 在这个例子中是Hello world!. 
对于其他对象, 你可以使用new关键词和String的构造函数来创造来自不同源的String对象(这样new出来的对象就真的是new出来的单独的对象, 即使它和字符串池中的字符串重复). 
(注: String类是一成不变的, 这意味着创建一个String对象就不能被改变. String类有很多方法, 某些方法会在下面讨论, 它们看起来改变了String. 因为String对象的一层不变的, 那么改变了字符串的那些方法真正做的是返回一个新的, 通过操作制造的String对象)

## String的长度

获得一个对象信息的方法一般称作访问器方法accessor methods, String对象的一个访问器方法是length(), 它可以返回String对象包含的字符数量. 
~~~
String palindrome = "Dot saw I was Tod";
int len = palindrome.length();  // len为17
~~~

## 连接字符串

String类包含一个concat()方法来连接两个字符串: 
~~~
string1.concat(string2); 
~~~
它将返回一个新的String对象, 由string1+string2组成. 
String最常用的是用+操作符来连接. 
~~~
"Hello," + " world" + "!"
~~~
这个操作符可以混合连接任何String对象, 对于那些不是String的对象它会调用toString()方法来转换成对象. 
(注: Java中不支持源代码中一个字符串占用两行或以上, 比如
~~~
String str = "abcd 
         efgh";
~~~
所以对于长的String你需要在末尾添加+来连接. 例如: 
~~~
String quote = 
    "Now is the time for all good " +
    "men to come to the aid of their country.";
~~~
)

## 创建格式化字符串

上面提到过printf()和format()方法, 而String类中也有等量的格式化输出的format()方法, 它返回一个String对象而不是PrintStream对象. 
使用format()方法允许你创建一个格式化的String对象以至于你可以重用该对象. (虽然看起来没什么用)为了替代: 
~~~
System.out.printf("The value of the float " +
                  "variable is %f, while " +
                  "the value of the " + 
                  "integer variable is %d, " +
                  "and the string is %s", 
                  floatVar, intVar, stringVar);
~~~
你可以这么写: 
~~~
String fs;
fs = String.format("The value of the float " +
                   "variable is %f, while " +
                   "the value of the " + 
                   "integer variable is %d, " +
                   " and the string is %s",
                   floatVar, intVar, stringVar);
System.out.println(fs);
~~~

## Number和String之间相互转换

### String转换Number

一般情况下用户输入的都是String类型的数字数据. 包装基础类型的Number的子类提供了valueOf方法来将Stirng转换为相应的包装对象. 
官方举出来一个[例子](https://docs.oracle.com/javase/tutorial/java/data/converting.html). 
注: 每个包装基础数字型的Number类的子类都提供一个parseXXXX()的方法来将String转换成基础类型, 因为和valueOf()方法对比, parseXXXX()方法返回的是基础类型而不是对象, 所以比较直接易用. 

### Number转换String

下面是几种number转换成String的方法: 
~~~
int i;
// Concatenate "i" with an empty string; conversion is handled for you.
String s1 = "" + i;
~~~
或: 
~~~
// The valueOf class method.
String s2 = String.valueOf(i);
~~~
每个Number子类包含一个toString()方法, 它会转换基础类型到String. 
~~~
int i;
double d;
String s3 = Integer.toString(i); 
String s4 = Double.toString(d); 
~~~

## 操纵String中的字符 Manipulating Characters in a String

String类中有通过字符串查找字符和子串, 改变字符串内容等方法. 

### 通过索引获取字符和字串

你可以通过charAt()方法来访问特定索引的String字符. index从0开始到字符串的长度减一. 下面的代码将得到索引为9的字符: 
~~~
String anotherPalindrome = "Niagara. O roar again!"; 
char aChar = anotherPalindrome.charAt(9); // 输出O
~~~
![索引为9的字符](/picture/20200624-0.jpg)
如果你想获得超过一个字符, 你可以使用substring方法. 
然后官方文章介绍了很多操作String的方法, 我在这里就懒得写啦. 

## 比较String和一部分的Stirng Comparing Strings and Portions of Strings

官方列出了一些String的比较方法, 这里就不多说了. 

## StringBuilder 类

StringBuilder对象和String对象很类似, 但是它是可以修改的. 在对象内部, 它把一系列字符当成可变数组来对待. 在任何情况下, 它的长度和内容都可以通过调用方法来改变. 

一般情况下使用String也就足够了, 除非你需要做一些大量的追加String操作, 这类操作使用StringBuilder能提高效率. 

StringBuilder默认分配容量为16. 官方给出了StringBuilder的一些介绍和用法. 


# 自动装箱和拆箱

自动装箱是Java编译器对基础类型和对应的包装类的自动转换机制. 例如将int转换成Integer, double转换成Double等. 如果反过来就叫拆箱. 

下面是自动装箱的例子: 
~~~
Character ch = 'a';
~~~
下面的例子中使用到了泛型: 
~~~
List<Integer> li = new ArrayList<>();
for (int i = 1; i < 50; i += 2)
    li.add(i);
~~~
在上面的代码中尽管你添加的是int基础类型, 而不是Integer到li里面, 代码还编译通过了. 因为li是一列Integer对象, 不是一列int, 所以你可能会疑惑为什么Java编译器没有抛出编译时错误. 它没有抛出错误的原因是因为它为i创建了一个Integer对象然后加到li里面去. 因此在运行时编译器转换上面的代码为: 
~~~
List<Integer> li = new ArrayList<>();
for (int i = 1; i < 50; i += 2)
    li.add(Integer.valueOf(i));
~~~
把基础类型转换成对应的对象就叫自动装箱. 当基础类型应用在下面两种场景时会自动装箱: 
- 作为参数传递到方法中. 
- 分配值给对应的包装类

考虑下面的代码: 
~~~
public static int sumEven(List<Integer> li) {
    int sum = 0;
    for (Integer i: li)
        if (i % 2 == 0)
            sum += i;
        return sum;
}
~~~
因为取余%和一元加法+=操作不支持Integer对象, 你可能会疑惑为什么Java编译器编译了这段代码而没有产生错误. 编译器没有产生错误是因为它调用了intValue()方法在运行时将Integer转换成了int: 
~~~
public static int sumEven(List<Integer> li) {
    int sum = 0;
    for (Integer i : li)
        if (i.intValue() % 2 == 0)
            sum += i.intValue();
        return sum;
}
~~~
将包装类对象转换成对应的基础类型叫做拆箱. Java编译器在下面的情况下使用拆箱: 
- 作为方法参数传递时
- 分配给对应的基础类型


# 为什么使用泛型? 

简单来说泛型使你在定义类, 接口和方法时可以把类型作为参数. 和熟悉的方法参数一样, 类型参数也提供一种重用代码的功能. 不一样的地方在于方法参数是值, 而这个输入参数是类型. 

使用泛型的优点: 

- 编译时的强类型检查
Java编译器对泛型使用强类型检查, 如果代码违背类型安全就会产生错误. 修复编译时错误比修复运行时错误容易得多. 

- 避免类型转换
下面的代码不适用泛型需要类型转换: 

~~~ java
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0);
~~~

如果使用泛型重写, 那么就不需要类型转换: 

~~~ java
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0);   // no cast
~~~

- 程序员能实现泛型算法
例如针对不同类型的集合使用相同或不同的算法, 可自定义, 类型安全和容易阅读. 

# 泛型类型Generic Types

一个泛型类型是参数在类型之上的泛型类或接口. 下面的Box类会展示一下这种概念. 

### 一个简单的Box类

从观察一个可以操作任意类型的非泛型Box类开始. 它只需要提供两种方法: set()用来添加一个对象到box中, 还有get()来取回它: 

~~~ java
public class Box {
    private Object object;

    public void set(Object object) { this.object = object; }
    public Object get() { return object; }
}
~~~

由于方法接收或返回一个Object, 所以我们可以自由的传递我们想传递的对象, 除了基础类型. 但是这里没有方法在编译时验证程序如何使用这个类. 比如正常情况我们放了一个Integer进去, 那么我们想拿出来应该是个Integer, 但代码中某一部分不小心把Integer错拼成String了, 结果就导致了一个运行时错误. 

### 一个泛型版本的Box类

一个泛型类的定义格式差不多长这样: 

~~~ java
class name<T1, T2, ..., Tn> { /* ... */ }
~~~

在类型参数部分, 通过尖括号来限制, 然后接着类名. 它通过逗号将类型变量分隔. 

将Box类更新成使用泛型, 你需要改"public class Box"为"public class Box&lt;T>"来创建一个泛型类型声明. 这样就引进了类型变量T, 它可以在类内任何地方使用. 

经过上面的改变, Box类变成了: 

~~~ java
/**
 * Generic version of the Box class.
 * @param <T> the type of the value being boxed
 */
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
~~~

你可以看到, 所有出现的Object被T取代了. 一个类型变量可以是任何你声明的非基础类型类型: 任何类类型, 任何接口类型, 任何数组类型, 或者甚至是其他类型变量. 

同样的方法可以用来创建泛型接口. 

### 类型参数命名惯例Type Parameter Naming Conventions

按照惯例, 类型参数以单个大写字母命名. 这种命名和我们之前了解的命名形成鲜明对比, 而且有好的原因: 没有这个惯例, 我们将很难判断类型变量, 普通类和接口名. 

最常用的类型参数名是: 
- E - 元素Element(广泛的用于Java集合框架)
- K - 键Key
- N - 数字Number
- T - 类型Type
- V - 值Value
- S, U, V 等等. - 第二, 第三, 第四类型

你可以看到这些名字在Java SE API每个部分几乎都用到了. 

### 调用和定义一个泛型类型Invoking and Instantiating a Generic Type

为了在你的代码中使用这个泛型Box, 你必须执行一条泛型类型调用, 就是把T用一些确定的值替代掉, 比如Integer: 

~~~ java
Box<Integer> integerBox;
~~~

你可以把泛型类型调用想做是一次普通的方法调用, 只是把传递参数给方法变成了传递一个类型参数. (在这个例子中是把Integer传给Box类)

注: Type Parameter 和 Type Argument 是有区别的, 但翻译的时候可能都会翻译成一个意思... 所以有时候还是看官方文档吧. 

和其他变量声明一样, 上面的代码没有真正创建一个新的Box对象. 它只是简单的说明了integerBox指向装有Integer的Box类的引用. 说明了Box&lt;Integer>如何读取. 

为了实例化这个类, 我们使用new关键词, 和之前一样, 但是需要把&lt;Integer>放在类名和末尾括号之间. 

~~~ java
Box<Integer> integerBox = new Box<Integer>();
~~~

### 钻石运算符The Diamond

JavaSE7之后, 构造器中的类型参数可以省略不写, 编译器会根据上下文来决定. 这种由两个尖括号组成的东西&lt;>非官方的叫法就叫钻石运算符了. 下面是例子: 

~~~ java
Box<Integer> integerBox = new Box<>();
~~~

### 多类型参数

之前好像也有提到一个泛型类可以有多个类型参数. 例如下面的OrderedPair泛型类, 实现了Pair接口:
 
~~~ java
public interface Pair<K, V> {
    public K getKey();
    public V getValue();
}

public class OrderedPair<K, V> implements Pair<K, V> {

    private K key;
    private V value;

    public OrderedPair(K key, V value) {
	this.key = key;
	this.value = value;
    }

    public K getKey()	{ return key; }
    public V getValue() { return value; }
}
~~~

下面的声明中创建了两个OrderedPair类的实例: 

~~~ java
Pair<String, Integer> p1 = new OrderedPair<String, Integer>("Even", 8);
Pair<String, String>  p2 = new OrderedPair<String, String>("hello", "world");
~~~

上面的代码new OrderedPair&lt;String, Integer>可以看作使用String来实例了K, 使用Integer来实例化了V. 因此OrderedPair类构造器的参数类型是按顺序的String和Integer. 因为有自动装箱和拆箱, 传递String和int进去类中也是合法的. 

根据上面提及的The Diamond钻石操作符, 因为Java编译器可以从定义OrderedPair&lt;String, Integer>中获取对应的类型, 所以这些声明语句可以使用简短的钻石符号. 

~~~ java
OrderedPair<String, Integer> p1 = new OrderedPair<>("Even", 8);
OrderedPair<String, String>  p2 = new OrderedPair<>("hello", "world");
~~~

### 参数化的类型Parameterized Types

你可以把参数化过的类型类作为泛型类型使用, 例如: 

~~~ java
OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));
~~~

## 生(未分配)类型Raw Types

生类型是指没有任何类型参数的泛型类或者接口. 例如给定的Box类: 

~~~ java
public class Box<T> {
    public void set(T t) { /* ... */ }
    // ...
}
~~~

创建一个参数化过的Box&lt;T>类型, 你需要给形式参数T提供一个确切的类型参数: 

~~~ java
Box<Integer> intBox = new Box<>();
~~~

如果这个形参被省略了, 你就创建了一个Box&lt;T>的生类型: 

~~~ java
Box rawBox = new Box();
~~~

因此Box是属于泛型类型Box&lt;T>的生类型. 然而一个非泛型类或者接口不是一个生类型. 

这种生类型一般在一些遗留的代码中体现, 因为在JDK5.0之前很多API类(比如Collections类)不是泛型的. 当你使用生类型, 你将会遇到没有泛型时的问题: Box类会给你返回Object. 为了向后兼容性, 分配一个参数化过的类型给生类型是允许的: 

~~~ java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;               // OK
~~~

但如果你分配一个生类型给一个参数化过的类型, 你将会得到一个警告: 

~~~ java
Box rawBox = new Box();           // rawBox is a raw type of Box<T>
Box<Integer> intBox = rawBox;     // warning: unchecked conversion
~~~

你使用一个生类型调用对应泛型类型的泛型方法你也会得到一个警告: 

~~~ java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;
rawBox.set(8);  // warning: unchecked invocation to set(T)
~~~

警告显示这个生类型绕过了泛型检查, 将不安全的代码检查推迟到了运行时. 因此你应该避免在代码中使用生类型. 

### 未检查的错误信息Unchecked Error Messages

前面提到过, 当你与历史遗留的远古代码混合, 你可能会遇到一个一个类似这样的警告: 

~~~
Note: Example.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
~~~

在使用老的操作生类型的API时会产生上面的警告. 但一般都是建议不使用老的API, 这里就略了. 

## 泛型方法

泛型方法就是能引进他们自己的参数类型的方法. 这和定义泛型类型很像, 但类型参数的作用域仅限在声明的方法内. 静态和非静态泛型方法都是允许的, 泛型类构造器也一样. 

泛型方法的语法是, 在尖括号内可以包含一系列的类型参数, 这些东西要出现在方法返回类型前面. 对于静态泛型方法, 类型参数部分一定要出现在方法返回类型之前. 

下面的Util类包含一个泛型方法, compare, 它比较两个传入的Pair对象: 

~~~ java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
~~~

调用上面内容的完整语法是这样的: 

~~~ java
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.<Integer, String>compare(p1, p2);
~~~

上面的代码中, 调用方法时类型需要单独提供. 但一般情况那段代码可以不写, 编译器会知道怎么做的啦:

~~~ java
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.compare(p1, p2);
~~~

这个特性叫做类型引用(type inference), 它允许你调用泛型方法和调用普通方法一样, 不需要写尖括号指定类型. 

## 限定类型参数Bounded Type Parameters

有时候你想限制泛型类或者方法的使用类型. 比如, 一个操作数字的方法只想接收Number类的实例或者它的子类. 这就是限定类型参数要做的事. 

定义一个泛型界限, 只需要在定义的泛型类型名后加extend关键字, 然后接着它的上界类型, 在这个例子中是Number. 注意了, 在这里说的extends是一个大概的意思, 也就是说其实这个类继承了, 或者实现了这个类的接口也是可以的. 

~~~ java
public class Box<T> {

    private T t;          

    public void set(T t) {
        this.t = t;
    }

    public T get() {
        return t;
    }

    public <U extends Number> void inspect(U u){
        System.out.println("T: " + t.getClass().getName());
        System.out.println("U: " + u.getClass().getName());
    }

    public static void main(String[] args) {
        Box<Integer> integerBox = new Box<Integer>();
        integerBox.set(new Integer(10));
        integerBox.inspect("some text"); // error: this is still String!
    }
}
~~~

通过修改我们的泛型方法来引入这个限定泛型类型的功能, 编译的过程将会失败, 因为我们的inspect方法调用仍然使用着String类型, 而它所希望的是Number或者是Number的子类: 

~~~
Box.java:21: <U>inspect(U) in Box<java.lang.Integer> cannot
  be applied to (java.lang.String)
                        integerBox.inspect("10");
                                  ^
1 error
~~~

除了限定类型, 你可以实例化你的泛型类型, 也就是说限定了泛型类型之后这允许你调用界限内你可以调用的方法: 

~~~ java
public class NaturalNumber<T extends Integer> {

    private T n;

    public NaturalNumber(T n)  { this.n = n; }

    public boolean isEven() {
        return n.intValue() % 2 == 0; // 这里调用的intValue()方法是来自Integer的
    }

    // ...
}
~~~

上面的isEven方法通过n调用了定义在Integer类中的intValue方法. 

### 多重限定Multiple Bounds

上面的例子展示了使用一个限定来限定泛型参数类型, 但一个泛型参数可以被多重限定: 
~~~ java
<T extends B1 & B2 & B3>
~~~
一个被这样限定的泛型参数相当于是它们的子类型了. 如果它们其中一个是类, 那么它就必须放在前面, 例如: 

~~~ java
Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }

class D <T extends A & B & C> { /* ... */ }
~~~

如果A类没有放在前面, 那么你将会得到一个编译时错误: 
~~~
class D <T extends B & A & C> { /* ... */ }  // compile-time error
~~~

## 泛型方法和限定类型参数

限定参数类型是实现泛型算法的关键. 思考一下下面的计算数组anArray中大于指定elem的个数的方法: 

~~~ java
public static <T> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e > elem)  // compiler error
            ++count;
    return count;
}
~~~

这个方法的实现体非常简单, 但这并不能编译, 因为这个大于运算符只对基础类型short, int, double, long, float, byte和char有效. 你不能使用这个大于操作符来比较对象. 为了修复这个问题, 可以使用Comparable&lt;T>接口进行泛型参数限定: 

~~~ java
public interface Comparable<T> {
    public int compareTo(T o);
}
~~~

最终的代码将是: 

~~~ java
public static <T extends Comparable<T>> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e.compareTo(elem) > 0)
            ++count;
    return count;
}
~~~

## 泛型, 继承和子类型

你知道的, 将一个类型的对象分配给另外一个匹配的对象是可能的. 例如你可以将Integer对象分配给Object对象, 因为Object对象是Integer的其中一个超类: 

~~~ java
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger;   // OK
~~~

在面向对象术语中, 这个叫棣属(is a)关系. 因为Integer是Object的一种所以这种分配是允许的. 但Integer也是Number的一种, 所以下面的代码也应该是合法的: 

~~~ java
public void someMethod(Number n) { /* ... */ }

someMethod(new Integer(10));   // OK
someMethod(new Double(10.1));   // OK
~~~

对泛型来说这也是成立的. 你可以执行一次泛型的类型调用, 传递Number类型作为它的类型参数, 然后今后的任何add方法的调用都是允许的, 只要参数和Number对应: 

~~~ java
Box<Number> box = new Box<Number>();
box.add(new Integer(10));   // OK
box.add(new Double(10.1));  // OK
~~~

现在考虑下面的方法: 

~~~ java
public void boxTest(Box<Number> n) { /* ... */ }
~~~

这个方法接收什么参数? 通过看他的方法签名, 你可以看到它接收一个 Box&lt;Number> 参数类型. 但是这是什么意思呢? 你允许传递 Box&lt;Integer> 或者 Box&lt;Double> 类型吗? 或者你是这么期望的? 但很可惜答案是"不", 因为 Box&lt;Integer> 和 Box&lt;Double> 不是 Box&lt;Number> 的子类类型. 

这是一个使用泛型编程时的常用的误解, 但是这是一个值得学习的重要的观念. 

![Box&lt;Integer>不是Box&lt;Number>的子类型, 尽管Integer是Number的子类型](/picture/20200720-0.png)

注: 给定两个不同的类型A和B(例如Number和Integer), MyClass&lt;A>和MyClass&lt;B> 没有关系, 不管A和B有没有关系. MyClass&lt;A> 和MyClass&lt;B>的父类都是Object. 

### 泛型类和子类型Generic Classes and Subtyping

你可以通过继承和实现来产生泛型类型的子类型. 泛型类型参数的关系是由extends和implement决定的. 

我们使用Collections类作为例子, ArrayList&lt;E>实现List&lt;E>, 而List&lt;E>继承了Collection&lt;E>. 所以ArrayList&lt;String>是List&lt;String>的子类型, 而它也是Collection&lt;String>的子类型. 

只要你不更改参数类型, 类型间的子类参数关系就一直保持这个样子. 

![Collections的结构层次](/picture/20200720-1.png)

现在想象一下我们准备定义我们自己的list接口, PayloadList, 它关联一个可选的泛型值P. 它的声明可能是这个样子的: 

~~~ java
interface PayloadList<E,P> extends List<E> {
  void setPayload(int index, P val);
  ...
}
~~~

然后下面的参数化了类型的PayloadList都是List<String>的子类哦: 
- PayloadList<String,String>
- PayloadList<String,Integer>
- PayloadList<String,Exception>

![PayloadList的结构层次](/picture/20200720-2.png)

## 类型推断Type Inference

类型推断是Java编译器根据方法调用和对应的类型声明来决定类型参数使得语句能合法执行的能力. (说着很晕大概就是你使用泛型时, 有时不用指定类型编译器会帮你做决策) 推断算法决定了后面的参数类型, 如果可用的话, 那么就会自动分配. 最后推断算法会尝试寻找到最合适的类型来匹配你没有填写的类型. 

为了说明最后一点, 在下面的例子中推断算法决定了第二个传进pick方法中的参数类型是Serializable. 因为ArrayList实现了Serializable接口所以这里没有问题. 

~~~ java
static <T> T pick(T a1, T a2) { return a2; } // 方法中声明了返回值是泛型T, 那个尖括号说明用到了泛型T
Serializable s = pick("d", new ArrayList<String>()); 
~~~

### 类型推断和泛型方法

泛型方法来给你介绍一下这个类型推断, 它使得你可以像调用普通方法一样调用泛型方法, 不需要加过多尖括号的修饰. 来看看下面的例子你就知道了: 

~~~ java
public class BoxDemo {

  public static <U> void addBox(U u, 
      java.util.List<Box<U>> boxes) {
    Box<U> box = new Box<>();
    box.set(u);
    boxes.add(box);
  }

  public static <U> void outputBoxes(java.util.List<Box<U>> boxes) {
    int counter = 0;
    for (Box<U> box: boxes) {
      U boxContents = box.get();
      System.out.println("Box #" + counter + " contains [" +
             boxContents.toString() + "]");
      counter++;
    }
  }

  public static void main(String[] args) {
    java.util.ArrayList<Box<Integer>> listOfIntegerBoxes =
      new java.util.ArrayList<>();
    BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(30), listOfIntegerBoxes);
    BoxDemo.outputBoxes(listOfIntegerBoxes);
  }
}
~~~

上面的输出是: 

~~~
Box #0 contains [10]
Box #1 contains [20]
Box #2 contains [30]
~~~

上面的泛型方法addBox定义了一个泛型类型U. 一般来说Java编译器能够推断出泛型方法调用的类型参数. 因此在大多数情况下, 你不需要单独指定他. 例如上面的例子, 为了调用addBox方法你可以用尖括号指定泛型方法中的泛型类型为Integer: 

~~~ java
BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
~~~

如果你忽略这个尖括号指定的类型, 那么Java编译器会自动推断(根据方法参数)那个类型参数是Integer: 

~~~ java
BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
~~~

### 类型推断和实例化泛型类

// TODO 

来源: 
[Getting Started](https://docs.oracle.com/javase/tutorial/getStarted/index.html)
[Object-Oriented Programming Concepts](https://docs.oracle.com/javase/tutorial/java/concepts/index.html)
[Lesson: Language Basics](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/index.html)
[Classes and Objects](https://docs.oracle.com/javase/tutorial/java/javaOO/index.html)
[Interfaces and Inheritance](https://docs.oracle.com/javase/tutorial/java/IandI/index.html)
[Annotations](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)
[Numbers and Strings](https://docs.oracle.com/javase/tutorial/java/data/index.html)
[Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)
