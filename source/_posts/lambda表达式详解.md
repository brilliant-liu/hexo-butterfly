---
title: lambda表达式详解
date: 2020-04-16 23:59:57
tags:
    - java
    - 教程
categories:
    - [教程,java]
cover: 'https://www.linuxidc.com/upload/2014-09/140905163467611.jpg'
---

# lambda表达式详解

## 一、函数式接口 @FunctionalInterface
1.1. 什么是 函数式接口 @FunctionalInterface  
用于指示接口的信息性注释类型，声明该接口是一个函数型接口。是一个函数式接口且具有以下的特点。  
1.2. 特点：
- 接口有且仅有一个抽象方法
- 允许定义静态方法
- 允许定义默认方法
- 允许java.lang.Object中的public方法
- 该注解不是必须的，如果一个接口符合"函数式接口"定义，那么加不加该注解都没有影响。加上该注解能够更好地让编译器进行检查。如果编写的不是函数式接口，但是加上了@FunctionalInterface，那么编译器会报错

例如：
```java
@FunctionalInterface
public interface MyFunc<T,R> {

    /**
     * 抽象方法
     */
    R getValue(T r);

    /**
     * 可以是{@link Object 中的public方法}
     */
    @Override
    boolean equals(Object o);
    @Override
    String toString();

    /**
     * 自定义的默认方法
     */
    default void defaultMethod(){
        System.out.println();
    }

    /**
     * 静态方法
     */
    static void staticMethod(){

    }
}
```


## 二、什么是lambda
Lambda 表达式，也可称为闭包，是Java 8 发布的最重要新特性。其允许把函数（该函数应该是一个函数式接口）作为一个方法的参数（函数作为参数传递进方法中）。使得Java可以进行函数式编程。

## 三、lambda基本语法
引入新的操作符"->"箭头操作符 或lambda 操作符，左侧为参数列表，右边为所需执行的功能（一系列的表达式）。 

语法格式：
- 无参数，无返回。伪代码形如：
```java
 () - System.out.println("")；  
```

- 一个参数，无返回值： 伪代码形如：
```java
(x) -> System.out.println(x);
```
- 只有一个参数时，参数括号可以省略。伪代码形如：
```java
x -> System.out.println();
```
- 有多个参数且有返回，并且lambda中有多个语句，用{return} 。伪代码形如：
```java
String result = (x,y) -> {
    System.out.println("操作1");
    System.out.println("操作2");
    return "操作完成";
};
```
- 有多个参数且有返回，并且lambda中有1个语句，return 和{}可省略。伪代码形如：
```java
int result = (Integer x, Integer y) -> x+y;
```
- lambda参数列表的类型可以不写，jvm编译器可以通过上下文推断出数据类型。伪代码形如：
```java
Comparator<Integer> comparable = (x,y) -> Integer.compare(x,y);
```

## 四、对lambda的理解
首先就是要有一个函数接口，但是该接口的具体实现并没有，所以可以在使用的时候，在形式上以不创建对象的方式（方法的形式）对该接口进行功能的实现。



## 五、java8中内置的四大核心函数式接口
四大函数式接口，分别为：  
* Consumer<T> : 消费型接口 void accept(T t)
* Supplier<T> ：供给型接口 T get();
* Function<T,R> ：函数型接口 R apply(T t)
* Predicate<T> : 断言型接口 boolean test(T t)

使用案例：(这里有两种使用的方式，可以帮助理解)
```java
public class TestLambda3 {

    public static void main(String[] args) {

        /**
         * Consumer<T>
         */
        //函数式接口中抽象方法的实现
        Consumer<Double> consumer = (x) -> System.out.println("本次消费"+x+"元");
        //调用该接口的实现
        consumer.accept(100d);


        /**
         * Supplier<T>
         */
        Supplier<List> supplier = () -> {
            List result = new ArrayList(3);
            result.add(5);
            result.add(2);
            result.add(0);
            return result;
        };
        List list = supplier.get();
        System.out.println(list);



        /**
         * Function<T,R>
         */
        userFunc("to upper case", x -> x.toUpperCase());
        userFunc("substring", x -> x.substring(1,6));
        
    }

    /**
     *  将函数式接口作为参数传入，具体的实现在方法调用的时候，由调用者根据需要，动态传入具体的实现逻辑
     * @param str 参数
     * @param func 函数式接口中抽象方法的实现
     */
    public static void userFunc(String str,Function<String,String> func){
        String applyResult = func.apply(str);
        System.out.println(applyResult);
    }
}
```

## 六、lambda中的引用问题

### 6.1 方法引用：  
如果lambda体中的抽象方法已经实现，我们可以使用”方法引用“进行调用。方法引用可以理解为是lambda表达式的另外一种表现形式，其主要有三种语法格式：  
> 注意: 
> 1. lambda体中调用方法的参数列表和返回类型，要与函数式接口中抽象方法的一值  
> 2. 第一个参数是这个实例方法的调用者，第二个参数是该实例方法的参数，可以使用类::实例方法名  

+ 对象 :: 实例方法名  
    ```java
        //常规的使用
        PrintStream stream = System.out;
        Consumer<String> consumer = x -> stream.println(x);
        consumer.accept("ssss");
        //对象::实例方法名
        PrintStream stream1 = System.out;
        Consumer<String> consumers = stream1::println;
        consumers.accept("3333");

    ```  

+ 类 :: 静态方法名   
 
    ```java
            //常规调用
            Comparator <Integer> comparator = (x,y)->Integer.compare(x,y);
            comparator.compare(555,66);
            //类静态::静态方法
            comparator = Integer::compareTo;
            comparator.compare(99,66);
    ```  
  
+ 类 :: 实例方法名 
 
    ```java
         /**
         * 类：：实例方法名
         * 使用条件：第一个参数是这个实例方法的调用者，第二个参数是该实例方法的参数)
         */
        //常规调用
        BiPredicate<String,String> bp = (x,y)->x.equals(y);
        boolean test = bp.test("ss", "ee");
        System.out.println(test);
        //类：：实例方法名
        BiPredicate<String,String> bp2 = String::equals;
        System.out.println(bp2.test("001","321"));

    ```     

### 6.2 构造器引用
* ClassName::new

    ```java
         //构造器引用,需要调用的构造器的参数列表要与函数式接口中抽象方法的参数列表保持一直
         //常规操作
         Supplier<List> sup = () -> new ArrayList(1);
         //构造器引用
         Supplier<List> sup2 = ArrayList::new;
    ```  
  
### 6.3 数组引用
* Type[]::new

    ```java
            //数组引用
            Function<Integer,String[]> function =(x)->new String[x];
            function.apply(10);
            //数组引用
            Function<Integer,String[]> function2 = String[]::new;
            function.apply(10);
    ```  


