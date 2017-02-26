---
layout: post
title: Java 拾遗（之二）
categories: Java
tags:  Java
excerpt:
---

* content
{:toc}

### 背景

~~上一篇《拾遗》只是对Java语言基础知识的一个笼统的梳理，有些之前接触不太多的，或者有待深入学习的内容，如垃圾回收、多线程、序列化等，就略过去了；这是第二遍，将遇到的、遗漏的内容「补全」。其中有些主题还是会暂时只列出个大概，留待接下来一一深入学习。~~

算了算了，不看不知道，一看吓一跳，当初仗着对C++的映像来学Java，很多以为和C++大同小异的内容就直接跳过去了，结果现在翻书，一翻一个懵逼。没办法，只能**一页一页**的仔细扫描了。





### 前言

2011年7月28日，Oracle发布Java SE 7（2009年Oracle收购Sun后发布的第一个版本），引入了二进制整数、支持字符串的switch语句、菱形语法、多异常捕获、支持自动关闭资源的try语句等新特性；

2014年3月18日，Oracle发布Java SE 8，这次版本升级为Java带来了全新的Lambda表达式等等等等新特性；

（说这些是因为公司还在用jdk 7，估计到时候会踩很多坑(＞﹏＜)）

### 正文

#### JVM

Java源文件——javac编译——.class字节码文件——java解释执行

> Java语言既是编译型语言，又是解释型语言；或者说，Java既不是纯粹的编译型语言，也不是纯粹的解释型语言；Java语言程序的执行要经过编译和解释两个步骤；

负责解释、执行.class文件的是Java虚拟机，即JVM（Java Virtual Machine）；字节码是平台无关的，只面向JVM；不同平台上的JVM都是不同的，但它们都提供了相同的接口；JVM是Java程序跨平台的关键，只要为不同的平台实现了相应的JVM，Java字节码就能在该平台运行；JVM向上给字节码的运行提供接口，向下适应所在的平台；

Oracle制定的Java虚拟机规范在技术上规定了JVM的统一标准，具体定义了如下细节：指令集、寄存器、类文件格式、栈、垃圾回收堆、存储区。

#### CLASSPATH

当使用“java java类名”来运行Java程序时，JRE根据CLASSPATH环境变量来搜索Java类（即使该Java类就在当前路径下，JRE也不理会）；另，编译和运行Java程序还需要dt.jar和tools.jar两个文件，所以JDK 1.4之前的CLASSPATH通常长这样：`.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar`

JDK 1.5开始，JRE会自动搜索当前路径下的类文件，并自动加载dt.jar和tools.jar；所以不再需要设置CLASSPATH；

如果你又手动设置了CLASSPATH，那么这些默认的行为就会失去，JRE会向1.4之前那样完全根据CLASSPATH来搜索类文件；

如果在运行程序时想临时指定搜索路径，那么JRE又会完全按照临时路径来搜索，而忽略CLASSPATH或默认行为；

一般可以用如下格式指定临时搜索路径：`java -classpath %CLASSPATH%;.;dir1;dir2;...dirN java类名`

#### 数组

Java数组也是一种**类型**（和类、接口、枚举、基本类型等并列），它是一种引用类型。

内存模型：

![](http://upload-images.jianshu.io/upload_images/658453-387c16e349ccb7e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/658453-062d25ab0c68be16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 面向对象

子类重写父类的方法时，要遵循“两同两小一大”原则：方法名、参数列表相同；返回值类型、声明抛出异常类型要小（即子类）；方法的访问控制权限要更大；

抽象方法&抽象类

有抽象方法的类只能被定义为抽象类，抽象类却不一定含有抽象方法。
抽象类和普通类一样，也可以含有类方法 。
static和abstract不能同时修饰一个方法，但可以同时修饰内部类；
abstract和private不能同时修饰方法。

接口

接口中的成员变量默认默认以public、static、final修饰（都可省略），必须显式指定初值（接口不能含有初始化块）；
接口中的普通方法默认以public abstract修饰；
接口不能继承类，只能继承其他接口；
接口中的类方法和默认方法（JDK 8）可以有方法实现；
实现接口方法时，只能用public访问控制符（“两同两小一大”原则）；

#### 内部类&外部类的相互访问

非静态内部类

在非静态内部类里可以直接访问外部类的private成员；方法是直接用变量名weight或OuterClass.this.weight；

> 在非静态内部类的方法里访问某个变量时，查找顺序是：本地（即方法内）> 内部类中 > 外部类；

外部类想要访问内部类的（private）成员，必须通过内部类的对象来访问；

```java
class OuterClass {
    private int iOutPrivate = 0;

    private class InnerClass {
        private int iInPrivate = 1;

        private InnerClass() {
        }

        private void accessOuterclass() {
            // 直接访问外部类的private成员；
            System.out.println(OuterClass.this.iOutPrivate);
        }
    }

    public void accessInnerClass() {
        // 必须先实例化内部类对象；
        InnerClass innerInstance = new InnerClass();
        // 再通过内部类对象访问内部类的private成员；
        System.out.println(innerInstance.iInPrivate);
        innerInstance.accessOuterclass();
    }
}
```

> 非静态内部类对象存在时，外部类对象一定已经存在——“非静态”成员（内部类），要实例化才能使用；而外部类对象存在时，并不一定在内部持有一个内部类对象；

**这里可以想象，一个类的静态部分是深色的，非静态部分是浅色的；非静态部分必须实例化才能使用；**实例化外部类之后，它包含的非静态内部类必须再实例化一次才能使用。

根据“静态成员不能访问非静态成员”的规则，外部类的静态方法不能访问非静态内部类；非静态内部类中不允许定义静态成员。

![](http://upload-images.jianshu.io/upload_images/658453-b469be8d9530fbe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

静态内部类

根据“静态成员不能访问非静态成员”的规则，静态内部类不能访问外部类的非静态成员，只能访问静态成员；

外部类可以通过类名访问静态内部类的静态成员；或实例化静态内部类后，通过对象访问器非静态成员；

```java
class OuterClass {
    private int iOutPrivate = 0;

    public static class InnerClass {
        private int iInPrivate;

        public InnerClass(int iInPrivate) {
            this.iInPrivate = iInPrivate;
        }

        public OuterClass createOuterClassInstance() {
            // 静态内部类不能访问外部类的非静态成员，
            // 但却可以访问外部类的构造器！
            // 所以构造器是静态的？？
            return new OuterClass(iInPrivate);
        }
    }

    private OuterClass(int iOutPrivate) {
        this.iOutPrivate = iOutPrivate;
    }
}
```

**静态内部类不能访问外部类的非静态成员，却可以访问外部类的构造器！**

（这一小节好拗口(￣.￣)）

局部内部类：在方法里定义的类；这是一个很“鸡肋”的语法，因为局部内部类的作用域太小了，仅限于在当前方法中。实际开发中很少使用。

匿名内部类，只使用一次的类；匿名内部类必须继承一个父类或实现一个接口；

```java
confirmBtn.setOnClickListener(new OnClickListener() {
            public void onClick(View v) {    
                System.out.println("Button Clicked!");
            }    
        });    
```

被匿名内部类访问的外部类的变量自动变成final修饰（Java 8之前需显式final）；

#### 可执行“宏替换”的final变量

满足下列三个条件的变量可被宏替换：

1. 首要&必须条件：final修饰；
2. 在定义时就制定了初始值；
3. 初始值在编译时就可确定；

> final修饰符一个重要的作用就是定义“宏变量”，编译器会把程序中所有用到该变量的地方直接替换为该变量的值。

如下都是宏变量：

```java
final int a = 5;
final int b = 2 + 3;
final String s1 = "ab" + "cd";
final String s2 = "abc" + 99;
// 下面调用了方法，所以值无法在编译阶段确定下来，也就不是宏变量；
// final String s3 = "abc" + String.valueOf(99);

// 宏替换 & 常量池
System.out.println(s1 == "abcd");// true
```

看下面代码：

```java
String s1 = "abc99";

String str1 = "abc";
String str2 = "99";
String s2 = str1 + str2;

System.out.println(s1 == s2);// false
```

> *要让结果为true，只要将str1和str2改为final修饰即可，这样编译器在编译阶段就能确定s2的值，就会让s2指向常量池中的"abc99"；*

#### Lambda表达式（Java 8）

```java
(形参列表) -> {代码块}
stringList.stream().forEach(System.out::println);
```

Lambda表达式是一个对象，实现了某个函数式接口的对象；
以前大都作为参数（这时候其实就是对象），但还可以这样写：

```java
Runnable r = () -> {
    for (int i = 0; i < 100; i++) {
        System.out.println(i);
    }
};
```

“显式”作为对象使用；（我）很少这样写。

方法引用&构造器引用：

```java
类名::类方法
类名::实例方法
某个(任意)对象::实例方法

类名::new

stringList.stream().mapToInt(String::length).forEach(System.out::println);
stringList.stream().mapToInt("hello, world"::indexOf).forEach(System.out::print);
```

#### 枚举类

Java 5新增了enum关键字（和class、interface地位相同），用来定义枚举类。

枚举类是一种特殊的类，它一样可以有自己的成员变量、方法，可以实现一个或多个接口，也可以定义自己的构造器。一个Java文件中最多只有一个public修饰的枚举类，且该文件必须和该public枚举类同名。

但枚举类终究不是普通类，它与普通类有如下区别：
* 枚举类默认继承了java.lang.Enum（Enum继承了Object类）类，所以不能再**【显式】**继承其他类；Enum实现了Serializable和Comparable两个接口；
* 使用enum定义、【非抽象】的枚举类默认使用final修饰，所以枚举类不能派生子类；（注意这句话）；
* 枚举类的构造器默认以private修饰，显式也只能用private修饰；
* 枚举类的所有实例必须在第一行显式列出，否则这个枚举类永远不能产生实例；列出这些实例时，系统会自动添加public static final修饰，无需显式添加；

枚举类提供了一个values()方法，可以方便的遍历所有的枚举值。

```java
public class ReviewJava {
    public static void main(String[] args) {
        for (SeasonEnum season : SeasonEnum.values()) {
            System.out.println(season);
        }
        System.out.println(SeasonEnum.SPRING.compareTo(SeasonEnum.FALL));
    }
}

enum SeasonEnum {
    SPRING, SUMMER, FALL, WINTER;
}

output：

SPRING
SUMMER
FALL
WINTER
-2
```

编译上面的Java程序，将生成一个SeasonEnum.class文件，这说明枚举类确实是一个特殊的Java类。

因为Enum实现了Comparable接口，所以同一个类的枚举值是可以比较的，后面的大。

```	java
interface GenderDesc {
    void info();
}

enum Gender implements GenderDesc {
    MALE("男") {
        @Override
        public void info() {
            System.out.println("这是男");
        }
    }, FEMALE("女") {
        @Override
        public void info() {
            System.out.println("这是女");
        }
    };
    private final String name;

    private Gender(String name) {
        this.name = name;
    }
}
```

1. MALE("男")是**调用私有构造器实例化枚举值**；前面SeasonEnum里SPRING等没有是因为默认的构造器没有参数，所以省略了；
2. FEMALE("女"){...}是实现接口方法，语法类似于匿名内部类；
3. 所以这里的MALE，FEMALE并不是Gender枚举类的实例，而是其**匿名子类**的实例；
4. 上面说“非抽象枚举类不能被继承”，这里包含抽象方法（接口），默认是以abstract修饰；


#### Stream流式编程

java.util.stream

看印象笔记 :)




#### 其他

Java 7新增的二进制整型：`int binVal = 0B011;`

Java的左移（<<）、右移（>>）都是算术移位，要考虑符号位的；无符号右移（>>>）是逻辑移位，不考虑符号位（即带符号位一起移动）；

**不要被溢出的情形迷惑了！**

> 逻辑移位，不管左移右移都添0；
> 
> 算术移位，正数，左移右移都添0；负数，左移添0右移添1；

`Integer.toBinaryString(int i)/toHexString/toOctalString`输出二进制/十六进制/八进制形式整数；

Java中方法的参数传递方式为**值传递**，即传递的是实际参数的副本。（好久好久没碰到这个词了，上一次还是在大一学C的时候吧？）

静态导入**“import static”**用于导入某个指定类的某个静态变量、方法或全部的静态变量、方法。

```java
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);

false
```

Java最基本的东西，想想发生了什么。

Java规范并没有指定boolean类型所占用的内存空间大小；但由于大部分计算机允许分配的最小内存单位是字节，因此boolean大部分时候实际上占用8位。

除了break outer，还有continue outer：

```java
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 4; j++) {
        if (j == 2)
            continue outer;
    }
}
```

术语：类变量、类方法；实例变量、实例方法；静态变量、静态方法；

术语区分：重载vs重写

重载发生在同一个类的多个同名方法之间；返回类型或参数不同；
重写发生在子类和父类的同名方法之间，方法签名必须完全一致，否则就变成了重载从父类继承来的方法；

如果子类定义了和父类同名的实例变量，则会发生**子类实例变量 隐 藏 父类实例变量的情形。**注意是「隐藏」，而不是覆盖！可以通过super.变量名来访问到。

看下面的代码：

```java
public class ReviewJava {
    public static void main(String[] args) {
        Derived d = new Derived();
        // 编译错误
        // System.out.println(d.tag);
        // 访问Parent类的tag；
        System.out.println(((Parent) d).tag);
    }
}

class Parent {
	// 注意是public；如果是private，这个变量就跟子类没关系了；
    public String tag = "Parent";
}

class Derived extends Parent {
    private String tag = "Derived";
}
```

> 实际上初始化块是一个假象，使用javac编译Java类后，该Java类中的初始化块会消失——初始化块中的代码被“还原”到每个构造器中，且位于构造器所有代码的前面。

以前没有注意区分的点：final修饰基本类型变量和引用类型变量的区别——修饰基本类型时，基本类型的值不可变；修饰引用类型时，**引用不可变**，即该引用不可指向其他地方；但**引用所指的对象不受影响**。

---

对于一个private方法，因为它仅在当前类中可见，子类无法访问该方法——但子类可以定义一个和它完全一样的方法——这不是重写，这是完全独立的重新定义！这时用final修饰该private方法也毫无意义or影响，private屏蔽了final的作用。

---

> 接口，体现的是规范和实现分离的设计哲学。
