---

title: java中枚举的使用
date: 2019-07-16
tags:

---

此处简介
<!--more-->
### java中枚举的使用
**enum** 的全称为 enumeration 是 JDK 1.5  中引入的新特性，存放在 java.lang 包中。
1. 原始的接口定义常量

2. 语法（定义）

3. 遍历、switch 等常用操作

4. enum 对象的常用方法介绍

5. 给 enum 自定义属性和方法

6. EnumSet，EnumMap 的应用

7. enum 的原理分析

8. 总结

***
原始的接口定义常量

```
public interface IConstants {
    String MON = "Mon";
    String TUE = "Tue";
    String WED = "Wed";
    String THU = "Thu";
    String FRI = "Fri";
    String SAT = "Sat";
    String SUN = "Sun";
}
```
***
**语法（定义）**
 创建枚举类型要使用 enum 关键字，隐含了所创建的类型都是 java.lang.Enum 类的子类（java.lang.Enum 是一个抽象类）。枚举类型符合通用模式 Class Enum<E extends Enum<E>>，而 E 表示枚举类型的名称。枚举类型的每一个值都将映射到 protected Enum(String name, int ordinal) 构造函数中，在这里，每个值的名称都被转换成一个字符串，并且序数设置表示了此设置被创建的顺序。



```
package com.hmw.test;
/**
 * 枚举测试类
 * @author <a href="mailto:hemingwang0902@126.com">何明旺</a>
 */
public enum EnumTest {
    MON, TUE, WED, THU, FRI, SAT, SUN;
}
这段代码实际上调用了7次 Enum(String name, int ordinal)：

new Enum<EnumTest>("MON",0);
new Enum<EnumTest>("TUE",1);
new Enum<EnumTest>("WED",2);
```
***
**遍历、switch 等常用操作**


public class Test {
    public static void main(String[] args) {
        for (EnumTest e : EnumTest.values()) {
            System.out.println(e.toString());
        }

        System.out.println("----------------我是分隔线------------------");

        EnumTest test = EnumTest.TUE;
        switch (test) {
        case MON:
            System.out.println("今天是星期一");
            break;
        case TUE:
            System.out.println("今天是星期二");
            break;
        // ... ...
        default:
            System.out.println(test);
            break;
        }
    }
    }
***
**enum 对象的常用方法介绍**
int compareTo(E o)
          比较此枚举与指定对象的顺序。

Class<E> getDeclaringClass()
          返回与此枚举常量的枚举类型相对应的 Class 对象。

String name()
          返回此枚举常量的名称，在其枚举声明中对其进行声明。

int ordinal()
          返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零）。

String toString()

           返回枚举常量的名称，它包含在声明中。

static <T extends Enum<T>> T valueOf(Class<T> enumType, String name)
          返回带指定名称的指定枚举类型的枚举常量。

***
**给 enum 自定义属性和方法**

```
给 enum 对象加一下 value 的属性和 getValue() 的方法：

package com.hmw.test;

/**
 * 枚举测试类
 *
 * @author <a href="mailto:hemingwang0902@126.com">何明旺</a>
 */
public enum EnumTest {
    MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6) {
        @Override
        public boolean isRest() {
            return true;
        }
    },
    SUN(0) {
        @Override
        public boolean isRest() {
            return true;
        }
    };

    private int value;

    private EnumTest(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    public boolean isRest() {
        return false;
    }
}
public class Test {
    public static void main(String[] args) {
        System.out.println("EnumTest.FRI 的 value = " + EnumTest.FRI.getValue());
    }
}
输出结果：
EnumTest.FRI 的 value = 5
```
***
**EnumSet，EnumMap 的应用**

```
public class Test {
    public static void main(String[] args) {
        // EnumSet的使用
        EnumSet<EnumTest> weekSet = EnumSet.allOf(EnumTest.class);
        for (EnumTest day : weekSet) {
            System.out.println(day);
        }

        // EnumMap的使用
        EnumMap<EnumTest, String> weekMap = new EnumMap(EnumTest.class);
        weekMap.put(EnumTest.MON, "星期一");
        weekMap.put(EnumTest.TUE, "星期二");
        // ... ...
        for (Iterator<Entry<EnumTest, String>> iter = weekMap.entrySet().iterator(); iter.hasNext();) {
            Entry<EnumTest, String> entry = iter.next();
            System.out.println(entry.getKey().name() + ":" + entry.getValue());
        }
    }
}
```
***
原理分析
        enum 的语法结构尽管和 class 的语法不一样，但是经过编译器编译之后产生的是一个class文件。该class文件经过反编译可以看到实际上是生成了一个类，该类继承了java.lang.Enum<E>。EnumTest 经过反编译(javap com.hmw.test.EnumTest 命令)之后得到的内容如下：

```
public class com.hmw.test.EnumTest extends java.lang.Enum{
    public static final com.hmw.test.EnumTest MON;
    public static final com.hmw.test.EnumTest TUE;
    public static final com.hmw.test.EnumTest WED;
    public static final com.hmw.test.EnumTest THU;
    public static final com.hmw.test.EnumTest FRI;
    public static final com.hmw.test.EnumTest SAT;
    public static final com.hmw.test.EnumTest SUN;
    static {};
    public int getValue();
    public boolean isRest();
    public static com.hmw.test.EnumTest[] values();
    public static com.hmw.test.EnumTest valueOf(java.lang.String);
    com.hmw.test.EnumTest(java.lang.String, int, int, com.hmw.test.EnumTest);
}
```

所以，实际上 enum 就是一个 class，只不过 java 编译器帮我们做了语法的解析和编译而已。
总结
    可以把 enum 看成是一个普通的 class，它们都可以定义一些属性和方法，不同之处是：enum 不能使用 extends 关键字继承其他类，因为 enum 已经继承了 java.lang.Enum（java是单一继承）。