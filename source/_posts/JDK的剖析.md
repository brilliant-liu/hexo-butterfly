---
title: JDK的相关知识
date: 2021-10-30 11:10:26
tags:
  - JAVA
  - JDK
categories:
  - JAVA
cover: https://img2.baidu.com/it/u=397904468,1915779312&fm=26&fmt=auto
---

## 



# JDK中的Map



**HashMap是非线程安全，死锁一般都是产生于并发情况下。**



## HashMap的数据结构

> JDK8下的数据结构
>
> **JDK1.7的时候使用的是数组+ 单链表的数据结构。但是在JDK1.8及之后时，使用的是数组+链表+红黑树的数据结构（当链表的深度达到8的时候，也就是默认阈值，就会自动扩容把链表转成红黑树的数据结构来把时间复杂度从O（n）变成O（logN）提高了效率）**



HashMap的底层主要是**基于数组和链表**来实现的。

> 它之所以有相当快的查询速度主要是因为它是通过计算散列码来决定存储的位置。HashMap中主要是通过key的hashCode来计算hash值的，只要hashCode相同，计算出来的hash值就一样。如果存储的对象对多了，就有不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。
>
> HashMap底层是通过**链地址法**(链表)来解决hash冲突的。
>
> 解决hash冲突的解决方案：[传送门](https://www.cnblogs.com/wuchaodzxx/p/7396599.html#H1_1)
>
> 例如：开放定址法（再散列算法），再哈希法等

![hashmap.png](https://img-blog.csdn.net/20160605101246837)

## HashMap的实现原理

1. 数组：也称哈希数组，哈希表，存储`Map.Entry`，当添加一个元素（key-value）时，就首先计算元素key的hash值，以此确定插入数组中的位置

2. 链表：同一hash值的元素已经被放在数组同一位置了，这时就添加到同一hash值的元素的后面，他们在数组的同一位置，但是形成了链表，同一各链表上的Hash值是相同的，所以说数组存放的是链表。而当链表长度太长时，链表就转换为红黑树（默认当链表长度超过8时），这样大大提高了查找的效率。

   > 扩展： 
   >
   > [可供参考传送门](https://blog.csdn.net/qq_36520235/article/details/82417949?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare)
   >
   > - 当数组的容量超过初始容量的loadFactor的默认值为0.75时（默认情况下，数组大小为 16），那么当hashmap 中元素个数超过 16\*0.75=12 的时候，就把数组的大小，扩大一倍即16\*2=32，**然后重新计算每个元素在数组中的位置**，而这是一个非常消耗性能的操作，所以如果我们已经预知 hashmap 中元素的个数，那么预设元素的个数能够有效的提高 hashmap 的性能。
   >
   > 注意：扩容时，或者再指定容量大小的时候，最好为2的多少次幂：因为只有2的n次幂的情况时，length -1 最后一位二进制数才一定是1，这样能最大程度减少hash碰撞。[可参考地址](https://www.cnblogs.com/liang1101/p/12728936.html)
   >
   > - 计算元素key的hash值，一般情况是通过`hash(key)%len`获得。



##  jdk7中hashmap的死锁情况



**发生死锁的场景：**

在hashMap扩容时，且在多线程访问时可能发生死锁。



**死锁的前提知识：**

+ 并发，即多线程同时访问HashMap
+ JDK1.7是采用头插法进行节点的添加的
+ 在插入数据时，如果数组的容量超过了loadFactor的加载因子，这个时候会进行rehash操作,HashMap的扩容长度为原来的一倍。





解析示意图：

1： 有两个线程

![初始化的两个线程](https://img-blog.csdnimg.cn/c077a5374018485190f8c98d236440ef.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg5MDA0OQ==,size_16,color_FFFFFF,t_70)



2： 如果thread0在进行计算，其中执行到e和next 的指向如下图。而thread0和thread1都执行到rehash的步骤。

![rehash前的thread0的执行状态](https://img-blog.csdnimg.cn/996526d7f71a49d99b0c07599914395d.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg5MDA0OQ==,size_16,color_FFFFFF,t_70)





3： 线程0停止，线程1运行完，数组扩容后，键值对会进行重排，如下图。由于采用的是头插法，导致thread0的当前节点指针e的下一个指针next跑到了它的上个节点。是的next的下一个节点是当前节点e。这样在执行下去，就会存在死循环。



![重排后的hashmap存储结构](https://img-blog.csdnimg.cn/7a1d1be851b443b0a56068c1e358a8ec.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg5MDA0OQ==,size_16,color_FFFFFF,t_70)







## 扩容和插入的顺序问题

- JDK7  先插入后扩容

  再初始化数组的时候，每个数组内不一定都插入了数据（即待插入位置为空桶），如果待插入数据没有发生hash冲突，则插入。如果后续插入都没有发生hash冲突，先插入后扩容的方法，就避免一次扩容操作。

- JDK8 先扩容后插入

  主要因为对链表转为红黑树进行的优化，因为你插入这个节点的时候有可能是普通链表节点，也有可能是红黑树节点。

  如果是链表节点，是否达到了 链表转化为红黑树的阈值是8，如果没有那么就还可以继续插入。

  如果是红黑树节点，需要看插入红黑树节点是否还能满足当前是红黑树的特性，如果还能继续满足即还没有达到扩容的临界条件。



## HashMap相关面试问题答案

1. 

   ![question.jpg](https://img2020.cnblogs.com/blog/980882/202004/980882-20200419001021074-1792689761.jpg)

2. 为什么 HashMap 中 String、Integer 这样的包装类适合作为 key 键

   ![question2.jpg](https://img2020.cnblogs.com/blog/980882/202004/980882-20200419001041853-603039750.png)

3. HashMap 中的 key若 Object类型， 则需实现哪些方法？

   ![question30.png](https://img2020.cnblogs.com/blog/980882/202004/980882-20200419001108777-1212924729.png)





# List数据结构



**ArrayList**

ArrayList是List使用中最常用的实现类，它的查询速度快，效率高，但增删慢，**线程不安全**。ArrayList底层是由动态数组实现的。为了防止数组动态扩充次数过多，建议创建ArrayList时，给定初始容量。

**Vector**

Vector的底层也是通过数组实现的，默认大小也是10。主要特点：查询快，增删慢 ，其使用了synchronized方法**线程安全**，但是效率低

**LinkedList**

LinkedList底层是一个双向链表，它增删快，效率高，但是查询慢，线程不安全。

> 扩展：
>
> ArrayList 和 LinkedList 的选择问题：
>
> 在不需要频繁扩容的条件下优先选择ArrayList，因为 更轻量级，不需要在每个元素上维护上一个和下一个元素的地址。查询和插入的速度也快。



