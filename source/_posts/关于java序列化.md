---
title: 关于java序列化
date: 2020-05-15 09:59:57
tags:
  - 教程
  - java
categories:
  - [java]
cover: 'https://i.loli.net/2020/05/15/FqC2jtznbeHglaO.png'
---

# 关于java序列化

## 一、什么是序列化
序列化是把对象改成可以存到磁盘或通过网络发送到其他运行中的 Java 虚拟机的二进制格式的过程, 并可以通过反序列化恢复对象状态。

## 二、Java 序列化API的标准机制,
Java 序列化API给开发人员提供了一个标准机制, 通过 java.io.Serializable 和 java.io.Externalizable 接口, ObjectInputStream 及ObjectOutputStream 处理对象序列化。
通过 java.io.Serializable 和 java.io.Externalizable 接口, ObjectInputStream 及ObjectOutputStream 处理对象序列化。

## 三、如何序列化
让 Java 中的类可以序列化很简单. 你的 Java 类只需要实现 java.io.Serializable 接口, JVM 就会把 Object 对象按默认格式序列化。

## 四、SerialVersionUID的作用
1. serialVersionUID 是一个 private static final long 型 ID, 当它被印在对象上时, 它通常是对象的哈希码serialVersionUID 是一个 private static final long 型 ID, 当它被印在对象上时, 它通常是对象的哈希码  
2. SerialVerionUID 用于对象的版本控制。也可以在类文件中指定 serialVersionUID。不指定 serialVersionUID的后果是,当你添加或修改类中的任何字段时, 则已序列化类将无法恢复, 因为为新类和旧序列化对象生成的 serialVersionUID 将有所不同。

## 五、java.io.Serializable 和 java.io.Externalizable 接口的区别
Externalizable 给我们提供 writeExternal() 和 readExternal() 方法, 这让我们灵活地控制 Java 序列化机制, 而不是依赖于 Java 的默认序列化。
正确实现 Externalizable 接口可以显著提高应用程序的性能。
 
## 六、类中的一个成员未实现可序列化接口在实例化时的问题
如果尝试序列化实现了可序列化接口的类的对象，但该对象包含对不可序列化类的引用，则在运行时将引发不可序列化异常 NotSerializableException。

## 七、如果类是可序列化的, 但其超类不是, 则反序列化后从超级类继承的实例变量的状态如何？
Java 序列化过程仅在对象层级都是可序列化的类中继续， 即：实现了可序列化接口， 如果从超级类没有实现可序列化接口，则超级类继承的实例变量的值将通过【调用构造函数初始化】。

## 八、是否可以自定义序列化过程, 或者是否可以覆盖 Java 中的默认序列化过程？
1. 对于序列化一个对象需调用 ObjectOutputStream.writeObject(saveThisObject), 并用 ObjectInputStream.readObject() 读取对象, 但 Java 虚拟机为你提供的还有一件事, 是定义这两个方法。
如果在类中定义这两种方法, 则 JVM 将调用这两种方法, 而不是应用默认序列化机制。你可以在此处通过执行任何类型的预处理或后处理任务来自定义对象序列化和反序列化的行为。
2.如果某个字段不想进行序列化，可以使用 `transient` 关键字修饰该字段。

## 九、在 Java 中的序列化和反序列化过程中使用哪些方法？
readObject() 的用法、writeObject()、readExternal() 和 writeExternal()。Java 序列化由java.io.ObjectOutputStream类完成。该类是一个筛选器流,
它封装在较低级别的字节流中, 以处理序列化机制。要通过序列化机制存储任何对象, 我们调用 ObjectOutputStream.writeObject(savethisobject), 并反序列化该对象, 
我们称之为 ObjectInputStream.readObject()方法。  
使用案例：  
```java

public class Xiaoming implements Serializable{
    private static final long serialVersionUID = 3068307191088956342L;
    private String eye;
}

public class SerializableTest {

    @Test
    public void test() throws IOException, ClassNotFoundException {
        Xiaoming xiaoming = new Xiaoming();
        xiaoming.setEye("大眼睛");

        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream =  new ObjectOutputStream(byteArrayOutputStream);
        objectOutputStream.writeObject(xiaoming);

        byte[] bytes = byteArrayOutputStream.toByteArray();

        ByteArrayInputStream arrayInputStream = new ByteArrayInputStream(bytes);
        ObjectInputStream objectInputStream = new ObjectInputStream(arrayInputStream);
        Xiaoming xiaoming2 = (Xiaoming) objectInputStream.readObject();

        System.out.println(xiaoming2.getEye());
    }
}
```

## 十、假设你有一个类,它序列化并存储在持久性中， 然后修改了该类以添加新字段。如果对已序列化的对象进行反序列化, 会发生什么情况？
如果该对象未提供自己的serialVersionUID，那么 Java 序列化 API 将引发 java.io.InvalidClassException, 因此建议在代码中拥有自己的 serialVersionUID, 并确保在单个类中始终保持不变。
因为如果我们不提供 serialVersionUID, 则 Java 编译器将生成它, 通常它等于对象的哈希代码。通过添加任何新字段, 有可能为该类新版本生成的新 serialVersionUID 与已序列化的对象不同。

## 十一、在 Java 序列化期间,哪些变量未序列化？
静态变量和瞬态变量不会被序列化。  
瞬态变量：被transient关键字修饰的变量我们称之为瞬态变量。这些字段的生命周期就只存在于调用者的内存中，而不会在磁盘中持久化。
静态变量：由于静态变量属于类, 而不是对象, 因此它们不是对象状态的一部分, 因此在 Java 序列化过程中不会保存它们。
 
 
## 十二、总结
- 序列化时，只对对象的状态进行保存，而不管对象的方法；
- 当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口；
- 当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化，若引用对象为序列化汇报NotSerializableException；