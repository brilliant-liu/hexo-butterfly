---
title: NIO!
date: 2020-07-19 13:53:13
tags:
  - IO/NIO
  - JAVA
categories:
  - 教程
cover: https://pic1.zhimg.com/80/v2-493abe4b27a0e13481e55f5090b0361f_720w.jpg
---


#  什么是NIO

java NIO(New IO或者Non Blocking IO),从JAVA1.4版本开始引入的一个新的IO API，可以替代标准的java IO api. NIO可以更加高效的方式进行文件的读写操作。

# 核心概念

+ buffer(缓冲区)

  一个用于特定基本数据类型的容器，有nio包定义，所有的缓冲区都是bufferc抽象类的子类，用于NIO通道内数据的交互。

+ channel(通道)

  用于源节点和目标节点的连接，在NIO中，负责缓冲区数据的传输。本身不存储数据，因此需要配合缓冲区进行数据的传输。

# NIO和IO的主要区别

| IO                        | NIO                           |
| ------------------------- | ----------------------------- |
| 面向流（Stream Oriented） | 面向缓冲区（Buffer Oriented） |
| 阻塞IO(Blockig IO)        | 非阻塞IO(NON Blocking IO)     |
|                           | 选择器（selectors）           |

> NIO系统的核心在于： Channel（通道）和Buffer(缓冲区)
>
> 通道表示打开IO设备（文件，套接字socket）的连接.
>
> 缓冲区负责数据的存储。



# 阻塞和非阻塞的对比

BIO的优势：可以接受更多的连接，缺点：线程内存浪费，消耗CPU资源，阻塞 

NIO的优势：规避了多线的问题，可以线程复用，非阻塞。



# 基本数据类型的缓冲区

ByteBuffer、CharBuffe、ShortBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer

对于不通类型的缓冲区，均通过allocate()方法获取缓冲区，缓冲区存取数据的核心方法：

`put（）`：存数据到缓冲区，`get() `： 获取缓冲区中的数据

**缓冲区buffer的四个核心属性：**

capacity: 容量，表示缓冲区最大存储数据的容量，一旦声明，不能改变。

limit: 界限，表示缓冲区中可以操纵数据的大小，limit后数据不能进行读写。

position: 位置，表示缓冲区中正在操作数据的位置。

mark: 标记，表示记录当前position的位置，可以通过reset()恢复到位置。

> 四个核心属性的取值范围：
>
> 0 <= position <=  limit <=  capacity





# 利用缓冲区读写的流程

![image.png](https://i.loli.net/2020/07/12/vMQaYie8wkzlqbE.png)



1. 分配缓存区大小

   allocate()方法，初始化后，capacity = limit = buffersize,position = 0;

2. 插入数据

   put()方法：position移动到插入的数据长度位置，limit和capacity不变

3. 切换模式

   flip()方法：将position移动到0的位置，limit位缓存区内数据的size,capacity为容量不变。

4. 读取数据

   get()方法：读取缓冲区的数据操作后position移动到读取的位置。

5. 重复读数据：

   rewind()方法：重新将position移动到0的位置，相当于重新切换flip();

6. 清空数据

   clear(): 清空缓冲区，所有的指针回到初始状态（同allocate()方法），但是缓冲区中数据依然存在，数据处于被遗忘状态，无法被读取。

# 直接缓冲区和非直接缓冲区

+ 非直接缓冲区

  通过allocate()方法分配缓冲区，将缓冲区建立在JVM内存中。

  

+ 直接缓冲区

  通过allocateDirect()方法，分配直接缓冲区，将缓冲区建立在物理内存中，可以提高效率。

  jVM会尽最大努力直接在此缓冲区上执行本机I/O操作。即在每次调用基础操作系统的一个本机I/O操作，JVM都会尽量避免将缓冲区的内容复制到中间缓冲区中（或者从中间缓冲区中复制内容）。

  直接缓冲区进行分配和取消分配所需要的成本通常高于非直接缓冲区，且其缓存区的内容可以驻留在常规的垃圾回收堆之外。

  直接缓冲区还可以通过FileChannel的map方法，将文件区域直接映射到内存中来创建。

  ![image.png](https://i.loli.net/2020/07/12/hQpLJ9c8di7Txyn.png)

> 直接缓冲区和非直接缓冲区可以通过isDirect()方法来确定。
>
> 直接缓冲区应主要分配给那些易受基础系统影响较大的本机I/O操作。



# 通道

## 什么是通道

用于源节点和目标节点的连接，在NIO中，负责缓冲区数据的传输。本身不存储数据，因此需要配合缓冲区进行数据的传输。

![image.png](https://i.loli.net/2020/07/12/MA97Fm6TcjpuNIn.png)



> channel的主要实现：
>
> 本地文件通道：FileChannel
>
> 网络通道：SocketChannel、ServerSocketChannel、DatagramChannel

## 通道的获取

1. 通过getChannel()方法：如本地IO:FileInputStream/RandomAccessFile;网络IO:Socket、ServerSocket、DatagramSocket
2. JDK1.7 中的NIO.2针对各个通道提供了静态方法open()，以及Files工具类的newByteChannel();

## 通道之间的数据传输

+ transferFrom()
+ transferTo()



# 字符集（Charset）

编码： 字符串 - 》 字节数组

解码：字节数组 -》 字符串



# 多路复用器

如果程序自己读取IO，那么这个IO模型，无论十BIO,NIO,多路复用器，都统称为同步IO模型，其中select,poll,epoll都是同步IO模型。

传统NIO和多路复用select和poll的对比

![image.png](https://i.loli.net/2020/07/22/DP3HNwy6fTvhFe4.png)

多路复用select、poll和epoll的对比

![image-20200722095650129](C:\Users\41660\AppData\Roaming\Typora\typora-user-images\image-20200722095650129.png)



> select 和 poll的比较：
>
> selector，有建立的连接限制，上限为1024，poll没有限制，可配置。
>
> select 和 poll的弊端，重复传递fd，所以再epoll中会在内核中开辟空间解决这个问题。

> 扩展：
>
> 文件描述符：
>
> 在Linux中，进程是通过文件描述符（file descriptors，简称fd）而不是文件名来访问文件的，文件描述符实际上是一个整数。
>
> 内核中，对应于**每个进程**都有一个文件描述符表，表示这个进程打开的所有文件。文件描述符就是这个表的索引。
>
> 文件描述表中每一项都是一个指针，指向一个用于描述打开的文件的数据块———file对象，file对象中描述了文件的打开模式，读写位置等重要信息。
>
> 
>
> 系统调用：
>
> 系统调用是应用程序与内核交互的接口，用户在程序中调用操作系统所提供的一些子功能。会发生一次系统终端。
>
> 
>
> C10K问题：
>
> 最初的服务器是基于进程/线程模型。新到来一个TCP连接，就需要分配一个进程。假如有C10K，就需要创建1W个进程，可想而知单机是无法承受的。那么如何突破单机性能是高性能网络编程必须要面对的问题，进而这些局限和问题就统称为C10K问题。





# 零拷贝

> [可供的参考地址](https://zhuanlan.zhihu.com/p/78869158)
>
> 广义零拷贝：能减少拷贝次数，减少不必要的数据拷贝，就算作“零拷贝”。
>
> 狭义零拷贝：Linux 2.4内核新增 sendfile 系统调用，提供了零拷贝。磁盘数据通过 DMA 拷贝到内核态 Buffer 后，直接通过 DMA 拷贝到 NIC Buffer(socket buffer)，无需 CPU 拷贝
>
> 
>
> 扩展：
>
> **DMA（Direct Memory Access）即直接存储器存取，是一种快速传送数据的机制。**
>
> DMA是指外部设备不通过CPU而直接与系统内存交换数据的接口技术。要把外设的数据读入内存或把内存的数据传送到外设，一般都要通过CPU控制完成，如CPU程序查询或中断方式。利用中断进行数据传送，可以大大提高CPU的利用率。
> 　　但是采用中断传送有它的缺点，对于一个高速I/O设备，以及批量交换数据的情况，只能采用DMA方式，才能解决效率和速度问题。DMA在外设与内存间直接进行数据交换，而不通过CPU，这样数据传送的速度就取决于存储器和外设的工作速度。



## 传统的文件读写

对于常见的零拷贝，主要有mmap 和 sendfile 两种方式。

传统数据传送所消耗的成本：4次拷贝，4次上下文切换。4次拷贝，其中两次是DMA copy，两次是CPU copy。拷贝是个IO过程，需要系统调用。



![traditional.jpg](https://i0.hdslb.com/bfs/article/32f16d252ca2d6ddc5a7125b0261b01877039689.jpg@1100w_758h.webp)

> 扩展：
>
> 内核从磁盘上面读取数据 是 不消耗CPU时间的，是通过磁盘控制器完成；称之为DMA Copy。
> 网卡发送也用DMA。

## mmap内存映射

mmap 就是在用户态直接引用文件句柄，也就是用户态和内核态共享内核态的数据缓冲区，此时数据不需要复制到用户态空间。当应用程序往 mmap 输出数据时，此时就直接输出到了内核态数据，如果此时输出设备是磁盘的话，会直接写盘（flush间隔是30秒）。

![mmap.jpg](https://pic1.zhimg.com/80/v2-493abe4b27a0e13481e55f5090b0361f_720w.jpg)

> mmap内存映射将会经历：3次拷贝 : 1次cpu copy，2次DMA copy；以及4次上下文切换



## sendfile

对于sendfile 而言，数据不需要在应用程序做业务处理，仅仅是从一个 DMA 设备传输到另一个 DMA设备。 此时数据只需要复制到内核态，用户态不需要复制数据，并且也不需要像 mmap 那样对内核态的数据的句柄（文件引用）。

当调用sendfile()时，DMA将磁盘数据复制到kernel buffer，然后将内核中的kernel buffer直接拷贝到socket buffer；
一旦数据全都拷贝到socket buffer，sendfile()系统调用将会return、代表数据转化的完成。
socket buffer里的数据就能在网络传输了。

![sendfile.jpg](https://pic2.zhimg.com/80/v2-52681828d066c9f2b35b552b0ed57c16_720w.jpg)

> sendfile会经历：3次拷贝，1次CPU copy 2次DMA copy；以及2次上下文切换



## splice

数据从磁盘读取到OS内核缓冲区后，在内核缓冲区直接可将其转成内核空间其他数据buffer，而不需要拷贝到用户空间。
如下图所示，从磁盘读取到内核buffer后，在内核空间直接与socket buffer建立pipe管道。



![splice.jpg](https://pic1.zhimg.com/80/v2-0f20b6755a5b3662c37f0babd32c5990_720w.jpg)



> splice会经历 2次拷贝: 0次cpu copy 2次DMA copy；以及2次上下文切换
>
> 
>
> splice() 和sendfile()的对比：
>
> splice()不需要硬件支持。sendfile是将磁盘数据加载到kernel buffer后，需要一次CPU copy,拷贝到socket buffer。

## 分散(Scatter)与聚集(Gather)

分散读取（Scattering Reads）： 将通道中的数据分散到多个缓冲区中

> 按照缓冲区的顺序，从 Channel 中读取的数据**依次**将 Buffer **填满**。

聚集写入(Gathering Writes)： 将多个缓冲区的数据聚集到通道中

> 按照**缓冲区的顺序**，写入 position 和 limit **之间**的数据到 Channel



Scatter/Gather可以看作是sendfile的增强版，批量的sendfile操作。

![s_g.jpg](https://picb.zhimg.com/80/v2-eae637ca05cb860ab62ffea9f44bc6f0_720w.jpg)

> Scatter/Gather会经历2次拷贝: 0次cpu copy，2次DMA copy



## java中的零拷贝

Linux提供的领拷贝技术 Java并不是全支持，支持2种(内存映射mmap、sendfile)；

> 首先要说明的是，JavaNlO中 的Channel (通道)就相当于操作系统中的内核缓冲区，有可能是读缓冲区，也有可能是网络缓冲区，而Buffer就相当于操作系统中的用户缓冲区。

+ 内存映射mmap（**MappedByteBuffer**）

  NIO中的FileChannel.map()方法其实就是采用了操作系统中的内存映射方式，底层就是调用Linux mmap()实现的。

  > 注意：
  >
  > 将内核缓冲区的内存和用户缓冲区的内存做了一个地址映射。这种方式适合读取大文件，同时也能对文件内容进行更改。小文件处理效率并不高。
  >
  > 使用 MappedByteBuffer类要注意的是：mmap的文件映射，在full gc时才会进行释放。当close时，需要手动清除内存映射文件，可以反射调用sun.misc.Cleaner方法。

+ sendfile

  FileChannel.transferTo()方法直接将当前通道内容传输到另一个通道，没有涉及到Buffer的任何操作，NIO中 的Buffer是JVM堆或者堆外内存，但不论如何他们都是操作系统内核空间的内存

  transferTo()的实现方式就是通过系统调用sendfile()

  ```java
  //使用sendfile:读取磁盘文件，并网络发送
  FileChannel sourceChannel = new RandomAccessFile(source, "rw").getChannel();
  SocketChannel socketChannel = SocketChannel.open(sa);
  sourceChannel.transferTo(0, sourceChannel.size(), socketChannel);
  ```

  >注意： 
  >
  >Java NIO提供的FileChannel.transferTo 和 transferFrom 并不保证一定能使用零拷贝。实际上是否能使用零拷贝与操作系统相关，如果操作系统提供 sendfile 这样的零拷贝系统调用，则这两个方法会通过这样的系统调用充分利用零拷贝的优势，否则并不能通过这两个方法本身实现零拷贝。



## kafka中的零拷贝

Kafka两个重要过程都使用了零拷贝技术，且都是操作系统层面的狭义零拷贝，分别是：

+ Producer生产的数据存到broker。

  Producer生产的数据持久化到broker，采用mmap文件映射，实现顺序的快速写入；

+  Consumer从broker读取数据。

  Customer从broker读取数据，采用sendfile，将磁盘文件读到OS内核缓冲区后，直接转到socket buffer进行网络发送。

> [kafka为什么这么快](https://www.jianshu.com/p/1c27da322767)

































