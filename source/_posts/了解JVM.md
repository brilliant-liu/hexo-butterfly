---
title: 了解JVM
date: 2020-07-16 13:14:53
tags:
  - JAVA
  - JVM
categories:
  - [教程,JVM]
cover: 'https://upload-images.jianshu.io/upload_images/5401760-cde4aefdad5438ca.png'
---



#  JVM

> 写在前面：
>
> 文档内容均来自互联网，加上写者的部分观点，如有不正确的地方，还请谅解，您也可以提[issue](https://github.com/brilliant-liu/hexo-butterfly),本人核实后，会进行更改；
>
> [优秀帖子参考连接](https://www.cnblogs.com/itzlg/p/13286333.html)



## Java虚拟机运行时数据区图

![jvmframe.png](https://upload-images.jianshu.io/upload_images/5401760-cde4aefdad5438ca.png)



## 方法区

方法区（Method Area）与Java堆一样，是各个**线程共享**的内存区域。也被称为非堆（Non-Heap）

存储:

**它存储已被Java虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等**

> 方法区默认最小值为16MB，最大值为64MB，
>
> 可以通过`-XX:PermSize `和 `-XX:MaxPermSize `参数限制方法区的大小。



![method.jpg](http://img1.tbcdn.cn/L1/461/1/8189e84069fb7da05aaebfa345c6c18a4d095945)





> 扩展：
>
> + 运行时常量池：是方法区的一部分，Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池，用于存放编译器生成的各种符号引用，这部分内容将在类加载后放到方法区的运行时常量池中。
>
> + 方法区同样存在垃圾收集，因为通过**用户定义的类加载器**可以动态扩展Java程序，这样可能会导致一些类，不再被使用，变为垃圾。
>
> + **static final修饰的成员变量**都存储于 方法区（Method Area）中。
>
>   - 八种基本数据类型（byte、short、int、long、float、double、char、boolean）的静态变量会在方法区开辟空间，并将对应的值存储在方法方法区.
>   - 对于引用类型的静态变量如果**未用new关键字为引用类型的静态变量分配对象**（如：static Object obj;）那么对象的引用obj会存储在方法区中，并为其指定默认值null;
>   - 对于引用类型的静态变量**如果用new关键字为引用类型的静态变量分配对象**（如：static Person person = new Person();），那么对象的引用person 会存储在方法区中，并且该对象在堆中的地址也会存储在方法区中（即此时静态变量只存储了对象的堆地址，而对象本身仍在堆内存中）;
>
> + 方法区中存储方法
>
>   + 程序运行时会加载**类编译生成的字节码**，这个过程中**静态变量（类变量）和静态方法**及**普通方法**对应的字节码加载到方法区。
>
>   + 方法区中**没有实例变量**
>
>     > 类加载先于对应类对象的产生，而实例变量是和对象关联在一起的，没有对象就不存在实例变量，类加载时没有对象，所以方法区中没有实例变量
>
>     



## 堆

Java堆（Java Heap） ：被所有线程共享的一块内存区域，在虚拟机启动时创建。Java堆（Java Heap）唯一目的就是存放对象实例。所有的**对象实例**及**数组**都要在Java堆（Java Heap）上分配内存空间。

Java堆在逻辑上是连续的，但在物理上并不要求连续。

ava堆既可以是固定大小，也可以是可扩展的。目前主流虚拟机都是可扩展的，通过参数-Xmx和-Xms设定。如果在Java堆中没有内存给对象实例分配，并且无法再扩展时，Java虚拟机将会抛出OutOfMemoryError异常。

> 扩展：
>
> `-Xms<size>`: 初始化堆大小
>
> `-Xmx<size>`: 允许使用的最大堆大小

Java堆是垃圾收集器管理的内存区域，因此也被称为"GC堆"

> 扩展：
>
> [优秀参考地址](https://www.cnblogs.com/snowwhite/p/9532311.html)
>
> Java 中的堆也是 GC 收集垃圾的主要区域。GC 分为两种：**Minor GC、Full GC ( 或称为 Major GC )**。
>
> 在 Java 中，堆被划分成两个不同的区域：新生代 ( Young )、老年代 ( Old)。新生代 ( Young ) 又被划分为三个区域：Eden、S0、S1。 **这样划分的目的是为了使 JVM 能够更好的管理堆内存中的对象，包括内存的分配以及回收。**
>
> + 年轻代
>
>   年轻代的特点是产生大量的死亡对象,并且要是产生连续可用的空间, 所以使用复制清除算法和并行收集器进行垃圾回收.对年轻代的垃圾回收称作初级回收 (minor gc)。
>
> + 老年代
>
>   Full GC 是发生在老年代的垃圾收集动作，所采用的是**标记-清除算法**。
>
>   Full GC 发生的次数不会有 Minor GC 那么频繁，并且做一次 Full GC 要比进行一次 Minor GC 的时间更长。 另外，标记-清除算法收集垃圾的时候会产生许多的内存碎片 ( 即不连续的内存空间 )，此后需要为较大的对象分配内存空间时，若无法找到足够的连续的内存空间，就会提前触发一次 GC 的收集动作。
>
> + 永久代（**PermGen**）
>
>   永久代是Hotspot虚拟机特有的概念，是方法区的一种实现，别的JVM都没有这个东西。在Java 8中，永久代被彻底移除，取而代之的是另一块与堆不相连的本地内存——元空间（**Metaspace**）。 
>
>   > [元空间优秀参考地址](https://www.jianshu.com/p/a6f19189ec62)
>   >
>   > 元空间和永久代的区别：
>   >
>   > 存储位置不同，永久代物理是是堆的一部分，和新生代，老年代地址是连续的，而元空间属于本地内存；存储内容不同，元空间存储类的元信息，静态变量和常量池等并入堆中。相当于永久代的数据被分到了堆和元空间中。



## Java虚拟机栈

Java虚拟机栈(Java virtual Machine stack) ,早期也叫Java栈。**每个线程在创建时都会创建一个虚拟机栈,其内部保存一个个的栈帧(stack Frame) ,对应着一次次的Java方法调用**。

> **栈是运行时的单位,而堆是存储的单位**。即: 栈解决程序的运行问题,即程序如何执行,或者说如何处理数据。堆解决的是数据存储的问题,即数据怎么放、放在哪儿。

### 虚拟机栈的特点

- 线程私有
- 生命周期生命周期和线程一致
- 主管Java程序的运行,它保存方法的局部变量(8种基本数据类型、对象的引用地址)、部分结果,并参与方法的调用和返回。

- JVM直接对Java栈的操作只有两个：每个方法执行伴随着进栈(入栈、压栈) 和 执行结束后的出栈工作
- 对于栈来说不存在垃圾回收问题GC，但存在内存移出问题OOM

栈可能出现的异常

Java虚拟机规范允许**Java栈的大小是动态的或者是固定不变的**。

- 如果采用**固定大小**的Java虚拟机栈,那每一个线程的Java虚拟机栈容量可以在线程创建的时候独立选定。如果线程请求分配的**栈容量超过Java虚拟机栈允许的最大容量**, Java虚拟机将会抛出一个**stackoverflowError**异常。（递归操作不当容易发生stackoverflowError异常）
- 如果Java虚拟机栈可以**动态扩展**,并且在尝试扩展的时候无法申请到足够的内存,或者在创建新的线程时**没有足够的内存去创建对应的虚拟机栈**,那Java虚拟机将会抛出一个**outofMemoryError**异常。

> 扩展
>
> JVM关于栈大小的设置：
>
> -Xms128m -Xmx512m -XX:PermSize=128M -XX:MaxPermSize=256m
>
> `-Xms<size>`: 初始化堆大小
>
> `-Xmx<size>`: 允许使用的最大堆大小
>
> `-Xss`: 选项来设置线程的最大栈空间
>
> `-XX:PermSize`：表示非堆区初始内存分配大小，其缩写为permanent size（持久化内存）
>
> `-XX:MaxPermSize`：表示对非堆区分配的内存的最大上限。
>
> > 注意： 一定要慎重的考虑一下自身软件所需要的非堆区内存大小，因为此处内存是不会被java垃圾回收机制进行处理的地方

**栈帧的内部结构**

![stackframe.png](https://gitee.com/itzlg/mypictures/raw/master/img/image-20200711234258769.png)



+ 局部变量表（Local Variables）

  局部变量表也被称之为局部变量数组或本地变量表。

  主要用于存储方法参数和定义在方法体内的局部变量，这些数据类型包括各类基本数据类型、对象引用(reference) ,以及returnAddress类型。

  **局部变量表中的变量只在当前方法调用中有效**。**当方法调用结束后,随着方法栈帧的销毁,局部变量表也会随之销毁**。

  > 扩展：
  >
  > 1. 由于局部变量表是建立在线程的栈上,是线程的私有数据,因此**不存在数据安全问题**。
  > 2. 局部变量表所需的容量大小是在编译期确定下来的,并保存在方法的Code属性的`maximum local variables`数据项中。在方法运行期间是不会改变局部变量表的大小的。
  > 3. 对一个函数而言,它的参数和局部变量越多,使得局部变量表膨胀,它的栈帧就越大，导致栈所允许的套嵌方法调用次数减少。
  >
  > **静态变量与局部变量对比**
  >
  > 参数表分配完毕后，会根据方法体内定义的变量顺序和作用域进行分配。类变量在系统初始化时有两次：1.prepare阶段默认赋值；2.initial阶段显示赋值。局部变量不存在系统初始化，一旦定义必须显示初始化，否则无法使用。
  >
  > 
  >
  > **局部变量表中的变量也是重要的垃圾回收根节点,只要被局部变量表中直接或间接引用的对象都不会被回收**。



### 操作数栈

**操作数栈,也可以称之为**表达式栈(Expression stack)。在方法执行过程中,根据字节码指令,往栈中写入数据或提取数据，即入栈(push)/出栈(pop)**。其是一个**后进先出(Last-In-First-Out)的栈。

主要用于保存计算过程的中间结果,同时作为计算过程中变量临时的存储空间。

> 操作数栈伴随着新的栈帧创建而创建出来,默认操作数栈是空的。
>
> **Java虚拟机的解释引擎是基于栈的执行引擎,其中的栈指的就是操作数栈**。



### 动态链接

**每一个栈帧内部都包含一个指向运行时常量池中该栈帧所属方法的引用**。包含这个引用的目的就是为了支持当前方法的代码能够实现**动态链接**(Dynamic Linking) 

在Java源文件被编译到字节码文件中时,**所有的变量和方法引用都作为符号引用(symbolic Reference)保存在class文件的常量池里**。比如:描述一个方法调用了另外的其他方法时,就是通过常量池中指向方法的符号引用来表示的,那么**动态链接的作用就是为了将这些符号引用转换为调用方法的直接引用**。

![dynamiclingk.png](https://gitee.com/itzlg/mypictures/raw/master/img/image-20200711211254048.png)

> 扩展：
>
> 常量池
>
> 主要为了提供一些符号和常量，便于指令的识别。
>
> 1. 静态常量池
>
>    静态常量池也称为Class文件中的常量池，主要用于存放两大类常量：字面量(Literal)和符号引用量(Symbolic References)**
>
>    字面量相当于Java语言层面常量的概念，如文本字符串，声明为final的常量值等，
>
>    符号引用则属于编译原理方面的概念，包括了如下三种类型的常量：
>
>    1. 和接口的全限定名
>    2. 字段名称和描述符
>    3. 方法名称和描述符
>
> 2. 方法区中的运行时常量池
>
>    运行时常量池相对于CLass文件常量池的另外一个重要特征是具备动态性，Java语言并不要求常量一定只有编译期才能产生，也就是并非预置入CLass文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用比较多的就是String类的intern()方法。
>
>    
>
> 常量池的好处：
>
> 常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。
>
> + 节省内存空间：常量池中所有相同的字符串常量被合并，只占用一个空间。
> + 节省运行时间：比较字符串时，==比equals()快。
>
> 
>
> 对于8种基本数据类型大部分都有自己的封装类，其中Byte,Short,Integer,Long,Character,Boolean都实现了常量池技术 `Float`和`Double`就没有实现常量池。



### 方法的调用

在JVM中,将符号引用转换为调用方法的直接引用与方法的绑定机制相关。

- **静态链接**:当一个字节码文件被装载进JVM内部时,如果**被调用的目标方法在编译期可知且运行期保持不变时**。这种情况下将调用方法的符号引用转换为直接引用的过程称之为静态链接。其对应的是早期绑定。
- **动态链接**：如果被调用的方法在编译期无法被确定下来,也就是说,**只能够在程序运行期将调用方法的符号引用转换为直接引用**,由于这种引用转换过程具备动态性,因此也就被称之为动态链接。其对应的是晚期绑定。

- **早期绑定**：指被调用的目标方法在编译器可知，且运行期保持不变是，可将这个方法与所属类型进行绑定。
- **晚期绑定**：指被调用的目标方法在编译器无法被确定下来，只能够在程序运行期根据实际的类型绑定相关的方法。

虚方法与非虚方法：

- **非虚方法**：如果方法在编译期就确定了具体的调用版本,这个版本在运行时是不可变的。（不能被重写的方法：静态方法、私有方法、final方法、实例构造器、没有重写的父类方法）
- **虚方法**：非虚方法之外的方法。（例如多态，父类引用指向子类对象）



> 扩展：
>
> 动态类型语言和静态类型语言：Java是静态类型语言，JS，Python等是动态类型语言。
>
> - 动态类型语言和静态类型语言两者的区别就在于**对类型的检查是在编译期还是在运行期**,满足前者就是静态类型语言,反之是动态类型语言。
> - 静态类型语言是判断变量自身的类型信息;动态类型语言是判断变量值的类型信息,变量没有类型信息,变量值才有类型信息,这是动态语言的一个重要特征。





###  方法返回地址

**存放调用该方法的pc寄存器的值**

**方法返回地址：存放调用该方法的pc寄存器的值**。

一个方法的结束,有两种方式：正常执行完成和出现未处理的异常,非正常退出。无论通过哪种方式退出,在方法退出后都返回到该方法被调用的位置。**方法正常退出时,调用者的pc计数器的值作为返回地址,即调用该方法的指令的下一条指令的地址**。而通过异常退出的,返回地址是要通过异常表来确定,栈帧中一般不会保存这部分信息。

**本质上方法的退出就是当前栈帧出栈的过程**。此时,**需要恢复上层方法的局部变量表、操作数栈、将返回值压入调用者栈帧的操作数栈、设置PC寄存器值等**,让调用者方法继续执行下去。

> 正常完成出口和异常完成出口的区别在于：
>
> 通过异常完成出口退出的不会给他的上层调用者产生任何的返回值.





栈的运行原理

![images.jpg](https://gitee.com/itzlg/mypictures/raw/master/img/image-20200711130500960.png)

流程描述：

1. JVM直接对Java栈的操作只有两个,就是对栈帧的**压栈和出栈**,遵循“先进后出” / "后进先出”原则
2. 在一条活动线程中,一个时间点上,只会有一个活动的栈帧。即只有当前正在执行的方法的栈帧(栈顶栈帧)是有效的,这个栈帧被称为**当前栈帧**(current Frame) ,与当前栈帧相对应的方法就是**当前方法**(currentMethod) ,定义这个方法的类就是**当前类**(current Class)。不同线程中所包含的栈帧是不允许存在相互引用的,即不可能在一个栈帧之中引用另外一个线程的栈帧
3. 执行引擎运行的所有字节码指令只针对当前栈帧进行操作。
4. 如果在该方法中调用了其他方法，则会创建新的栈帧，并入栈，同时操作该新的栈帧。
5. 栈帧被弹出（出栈），一种式正常返回return、一种式抛出异常



## 本地方法栈

 本地方法栈与虚拟机栈基本类似，区别在于虚拟机栈为虚拟机执行的java方法服务，而本地方法栈则是为Native方法服务。







## 垃圾回收

### java中的引用

+ 强引用

  强引用不会被GC回收，并且在java.lang.ref里也没有实际的对应类型，平时工作接触的最多的就是强引用。Object obj = new Object();这里的obj引用便是一个强引用。当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。

+ 软引用

  如果一个对象只具有软引用，那就类似于可有可物的生活用品。如果内存空间足够，垃圾回收器就不会回收它，如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。

+ 弱引用

  弱引用（weak reference）在强度上弱于软引用，通过类WeakReference来表示。它的作用是引用一个对象，但是并不阻止该对象被回收。如果使用一个强引用的话，只要该引用存在，那么被引用的对象是不能被回收的。弱引用则没有这个问题。

  在垃圾回收器运行的时候，如果一个对象的所有引用都是弱引用的话，该对象会被回收。

  弱引用的作用在于解决强引用所带来的对象之间在存活时间上的耦合关系。弱引用最常见的用处是在集合类中，尤其在哈希表中。哈希表的接口允许使用任何Java对象作为键来使用。当一个键值对被放入到哈希表中之后，哈希表对象本身就有了对这些键和值对象的引用。如果这种引用是强引用的话，那么只要哈希表对象本身还存活，其中所包含的键和值对象是不会被回收的。如果某个存活时间很长的哈希表中包含的键值对很多，最终就有可能消耗掉JVM中全部的内存。

  对于这种情况的解决办法就是使用弱引用来引用这些对象，这样哈希表中的键和值对象都能被垃圾回收。Java中提供了[WeakHashMap](http://download.oracle.com/javase/6/docs/api/java/util/WeakHashMap.html)来满足这一常见需求。

  > 扩展：
  >
  > `ThreadLocal`提供了线程独有的局部变量，可以在整个线程存活的过程中随时取用，极大地方便了一些逻辑的实现。
  >
  > Threadlocal可能发生内存泄漏：详细可参考 [ThreadLocal传送门](https://www.cnblogs.com/aspirant/p/8991010.html)
  >
  > 原因：ThreadLocal的原理：每个Thread内部维护着一个ThreadLocalMap，它是一个Map。这个映射表的Key是一个弱引用对象，其实就是ThreadLocal本身，Value是真正存的线程变量Object。
  >
  > (在threadlocal的生命周期中,都存在这些引用. 看下图: 实线代表强引用,虚线代表弱引用.)
  >
  > ![threadlocal原理图](https://images2018.cnblogs.com/blog/137084/201805/137084-20180504154502152-1165477841.jpg)
  >
  > 每个thread中都存在一个map, map的类型是`ThreadLocal.ThreadLocalMap. Map`中的key为一个threadlocal实例. 这个Map的确使用了弱引用,不过弱引用只是针对key. **每个key都弱引用指向threadlocal。** 当把threadlocal实例置为null以后，没有任何强引用指向threadlocal实例,所以threadlocal将会被gc回收. 
  >
  > 但是,我们的value却不能回收,因为存在一条从current thread连接过来的强引用. 只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收。
  >
  > 
  >
  > 结论：
  >
  > 只要这个线程对象被gc回收，就不会出现内存泄露，**但是如果使用线程池的时候，线程结束是不会销毁的，会再次使用的。就可能出现内存泄露**。
  >
  > 
  >
  > 扩展：
  >
  > + Java为了最小化减少内存泄露的可能性和影响，在ThreadLocal的get,set的时候都会清除线程Map里所有key为null的value。但是如果你使用了线程池，分配了数据但是又不调用get,set方法，那么内存泄漏依旧会存在。
  >
  >   所有最好的做法就是，使用之后，手动remove
  >
  > + JVM利用设置ThreadLocalMap的Key为弱引用，就是来避免内存泄露。
  >
  > 



+ 虚引用：

  在介绍幽灵引用之前，要先介绍Java提供的对象终止化机制（finalization）。在Object类里面有个finalize方法，其设计的初衷是在一个对象被真正回收之前，可以用来执行一些清理的工作。因为Java并没有提供类似C++的析构函数一样的机制，就通过 finalize方法来实现。但是问题在于垃圾回收器的运行时间是不固定的，所以这些清理工作的实际运行时间也是不能预知的。幽灵引用（phantom reference）可以解决这个问题。在创建幽灵引用PhantomReference的时候必须要指定一个引用队列。当一个对象的finalize方法已经被调用了之后，这个对象的幽灵引用会被加入到队列中。通过检查该队列里面的内容就知道一个对象是不是已经准备要被回收了。



###  如何判断对象的存活

+ 引用计数器法

  引用计数算法就是在对象中添加了一个引用计数器，当有地方引用这个对象时，引用计数器的值就加1，当引用失效的时候，引用计数器的值就减1。当引用计数器的值为0时，jvm就开始回收这个对象。

  > 引用计数器的缺点：
  >
  > 引用计数算法就是在对象中添加了一个引用计数器，当有地方引用这个对象时，引用计数器的值就加1，当引用失效的时候，引用计数器的值就减1。当引用计数器的值为0时，jvm就开始回收这个对象。

+ 可达性分析法

  定义一个名为"GC Roots"的对象作为起始点，这个"GC Roots"可以有多个，从这些节点开始向下搜索，搜索所走过的路径称为引用链(Reference Chain)，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的，即可以进行垃圾回收。

  > `GC Roots`对象一般包括有：
  > 1.虚拟机栈（栈帧中本地变量表）中引用的对象；
  > 2.方法区中类静态属性引用的对象；
  > 3.方法区中常量引用的对象；
  > 4.本地方法栈中JNI（Native方法）引用的对象。

  ![image](https://img-blog.csdnimg.cn/20200227140703948.png)

### 

### GC

#### Minor GC

- 特点: 发生在新生代上，发生的较频繁，执行速度较快
- 触发条件: Eden 区空间不足/空间分配担保

#### Full GC

- 特点:主要发生在老年代上（新生代也会回收），较少发生，执行速度较慢
- 触发条件:
  - 调用 System.gc()
  - 老年代区域空间不足
  - 空间分配担保失败
  - JDK 1.7 及以前的永久代(方法区)空间不足

### 垃圾回收算法

> 详细带图可参考：[垃圾回收带图](https://www.cnblogs.com/aademeng/articles/11028623.html)

#### 标记-清楚算法

**标记—清除算法是最基础的收集算法**，它分为“**标记**”和“**清除**”两个阶段：首先标记出所需回收的对象，在标记完成后统一回收掉所有被标记的对象，**它的标记过程其实就是前面的可达性分析算法中判定垃圾对象的标记过程**。

![clear](https://upload-images.jianshu.io/upload_images/3251891-25c29a521cfe2a4b.png)

![mark2](https://upload-images.jianshu.io/upload_images/3251891-0bbce21010980518.png)

缺点：

- 标记和清除过程的**效率都不高**

- 标记清除后会产生大量不连续的**内存碎片**，空间碎片太多可能会导致，当程序在以后的运行过程中需要分配较大对象时无法找到足够的连续内存而不得不触发另一次垃圾收集动作

  

#### 复制算法

复制算法是针对标记—清除算法的缺点，在其基础上进行改进而得到的，它将可用内存按容量分为大小相等的两块，每次只使用其中的一块，**当这一块的内存用完了，就将还存活着的对象复制到另外一块内存上面，然后再把已使用过的内存空间一次清理掉**。

![copy](https://upload-images.jianshu.io/upload_images/3251891-977ed6107c0476b7.png)

![xopy2](https://upload-images.jianshu.io/upload_images/3251891-af88a9c36b9338d4.png)

优点：

- 每次只对一块内存进行回收，运行高效
- 只需移动栈顶指针，按顺序分配内存即可，实现简单
- 内存回收时不用考虑内存碎片的出现

缺点:

+ 可一次性分配的**最大内存缩小了一半**

  

#### 标记—整理算法（Mark-Compact）

该算法标记的过程与标记—清除算法中的标记过程一样，但对标记后出的垃圾对象的处理情况有所不同，它不是直接对可回收对象进行清理，**而是让所有的对象都向一端移动，然后直接清理掉端边界以外的内存**。

![markcompact](https://upload-images.jianshu.io/upload_images/3251891-7383aa69926fa5c3.png)

回收后：

![mark2](https://upload-images.jianshu.io/upload_images/3251891-b1070ff58ce46e24.png)





#### 分代收集

当前商业虚拟机的垃圾收集都采用分代收集，它**根据对象的存活周期的不同将内存划分为几块**，一般是把Java堆分为**新生代**（年轻代）和**老年代**。

年轻代又进一步划分为：Eden(伊甸园)，Survivor(幸存区)，Survivor又包含from  to区。

![image.png](https://i.loli.net/2020/07/26/3yflcICEvFZL8nk.png)

每次创建的对象都会在年轻代的Eden中诞生，随着MinorGC的通过可达性算法，会把存活下来的对象，通过**复制算法** 将其复制到Survivor中的from或者to区，同时将from或者to区的数据的分代年龄+1，同时回收未存活的对象。如果分代年龄达到15，则会将该对象复制到老年代中。如果新生代的内存不足，也会将对象负责到老年代，如果老年代内存也紧张，会触发FullGC，对新生代和老年代进行垃圾回收。

> 扩展：
>
> Major GC 清理OldGen（老年代），一般使用标记清理或者标记整理的算法进行回收。
>
> STW(Stop the word)：垃圾回收的时候，会暂停所有的工作线程



| GC算法   | 优点           | 缺点               | 存活对象移动 | 内存碎片 | 适用场景 |
| -------- | -------------- | ------------------ | ------------ | -------- | -------- |
| 标记清除 | 不需要额外空间 | 两次扫描，耗时严重 | N            | Y        | 老年代   |
| 复制     | 没有标记和清除 | 需要额外空间       | Y            | N        | 新生代   |
| 标记整理 | 没有内存碎片   | 需要移动对象的成本 | Y            | N        | 老年代   |



### 垃圾收集器

[参考文章](https://www.cnblogs.com/cxxjohnson/p/8625713.html)

+ serial收集器

  STW，单线程，新生代

+ ParNew收集器

  多线程，缩短STW

+ Parallel Scavenge

  新生



> 扩展使用java自带的`jconsole.exe`可以图形化展现java JVM的内存占用，堆内存大小占用等情况。



