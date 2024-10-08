## 1. Final 有什么用？

被final修饰的类不可以被继承 

被final修饰的方法不可以被重写 

被final修饰的变量不可以被改变，

**被final修饰不可变的是变量的引用，而不是引用指向的内容， 引用指向的内容是可以改变的**

## 2. 什么是重载（Overload）和重写（Override） ?

重载：发生在同一个类中，方法名相同参数列表不同（参数类型不同、个数不同、顺序不同），与 方法返回值和访问修饰符无关，**即重载的方法不能根据返回类型进行区分** 

重写：发生在父子类中，方法名、参数列表必须相同，返回值小于等于父类，抛出的异常小于等于 父类，访问修饰符大于等于父类（里氏代换原则）；如果父类方法访问修饰符为private则子类中 就能是重写。

## 3. 重载的方法能否根据返回类型进行区分？

方法重载不可以根据返回类型区分

## 4. == 和 equals 的区别是什么

**== :** 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象。(基本数 据类型 == 比较的是值，引用数据类型 == 比较的是内存地址)

**equals() :** 它的作用也是判断两个对象是否相等。

## 5. 什么是反射机制？

JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任 意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法 的功能称为java语言的反射机制。

## 6. 反射机制优缺点

优点： 运行期类型的判断，动态加载类，提高代码灵活度。

 缺点： 性能瓶颈：反射相当于一系列解释操作，通知 JVM 要做的事情，性能比直接的java代码要 慢很多

## 7.  在你进行项目开发的过程中有没有用到过反射 

在我们的项目中经常会使用反射 + 自定义注解的方式去实现一些功能  , 例如 : 

1. 在前后端交互的时候, 后端Long类型返回前端后会产生精度丢失 , 我们的处理方式就是在服务端, 通过配置修改Jackson的序列化规则, 将一些Long类型字段转化为字符串返回给前端, 这个时候我们自定义了一个@IdEncrpt注解 , 通过反射获取类的属性, 判断属性上是否添加了@IdEncrpt注解, 如果添加了 , 就会通过反射获取属性值, 转化为字符串
2. 在整合EMQ的时候 , 为了能够方便的接收订阅消息, 我们自定义了一个@Topic注解 , 作用在类上 , 之后我们通过反射获取类的字节码,  并且获取类上的@Topic注解, 读取到里面定义的主题 , 通过策略模式将不同主题的消息分发到不同的处理器中
3. 除了上述之外, 在我们项目开发中经常使用的一些框架, 例如 : Mybatis , Spring , SpringMVC 等, 以及一些常用的工具库 common-utils , hutool工具库等都大量使用到了反射机制



## 8. String和StringBuffer、StringBuilder的区别是什么？

**可变性 :**  String类中使用字符数组保存字符串，所以string对象是不可变 的。

StringBuilder与StringBuffer这两种对象都是可变的。 

**线程安全性 :**  String中的对象是不可变的，也就可以理解为常量，线程安全。*StringBuffer*对 **每个方法**加了*同步锁*或者对调用的方法加了同步锁，所以是线程安全的。*StringBuilder*并没有对方法进行加同步锁，所以是非线程安全的。

**性能 :**  每次对String 类型进行改变的时候，都会生成一个新的String对象，然后将指针指向新的String 对 象。StringBuffer每次都会对StringBuffer对象本身进行操作，而不是生成新的对象并改变对象引 用。

StirngBuilder 相比使用StringBuffer而言效率更高

**更多信息参考：**
>[String、StringBuilder、StringBuffer和StringJoiner的区别以及使用_stringbuilder和stringjoiner的区别-CSDN博客](https://blog.csdn.net/weixin_64975807/article/details/135819585)



## 9. java常见的集合类有哪些

Map接口和Collection接口是所有集合框架的父接口： 

1. Collection接口的子接口包括：Set接口和List接口 
2. Map接口的实现类主要有：HashMap、TreeMap、Hashtable、ConcurrentHashMap以及 Properties等 
3. Set接口的实现类主要有：HashSet、TreeSet、LinkedHashSet等 
4. List接口的实现类主要有：ArrayList、LinkedList、Stack以及Vector等

![image-20220518162421503](assets/image-20220518162421503.png)



## 10. 常用的线程安全的类有哪些 ? 

      1. Vector：就比Arraylist多了个 synchronized （线程安全），因为效率较低，现在已经不太建议使 用。
    
      2. hashTable：就比hashMap多了个synchronized (线程安全)，不建议使用。
    
      3. ConcurrentHashMap：是Java5中支持高并发、高吞吐量的线程安全HashMap实现



## 11. ArrayList 和 LinkedList 的区别是什么？

1. 数据结构实现：ArrayList 是动态数组的数据结构实现，而 LinkedList 是双向链表的数据结构实 现。 

2. 随机访问效率：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是线性的数 据存储方式，所以需要移动指针从前往后依次查找。 

3. 增加和删除效率：在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为 ArrayList 增删操作要影响数组内的其他数据的下标。 

4. 内存空间占用：LinkedList 比 ArrayList 更占内存，因为 LinkedList 的节点除了存储数据，还存储 了两个引用，一个指向前一个元素，一个指向后一个元素。 

5. 线程安全：ArrayList 和 LinkedList 都是不同步的，也就是不保证线程安全； 

## 12. 说一下HashMap的实现原理？

HashMap的数据结构： HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。 

HashMap 基于 Hash 算法实现的 

1. 当我们往HashMap中put元素时，利用key的hashCode重新hash计算出当前对象的元素在数 组中的下标 
2. 存储时，如果出现hash值相同的key，此时有两种情况。
   1. 如果key相同，则覆盖原始值；
   2. 如果key不同（出现冲突），则将当前的key-value放入链表中 
3. 获取时，直接找到hash值对应的下标，在进一步判断key是否相同，从而找到对应值。



**HashMap JDK1.8之前**

JDK1.8之前采用的是拉链法。拉链法：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

![image-20210824162508405](assets/image-20210824162508405-1629793509954.png)

**HashMap JDK1.8之后**

相比于之前的版本，jdk1.8在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8） 时，将链表转化为红黑树，以减少搜索时间。扩容 resize( ) 时，红黑树拆分成的 树的结点数小于等于临界值6个，则退化成链表。

![image-20210824162536987](assets/image-20210824162536987-1629793538980.png)



## 13. HashMap的put方法的具体流程？

![image-20210824163053878](assets/image-20210824163053878.png)



> 1. 判断键值对数组table[i]是否为空或为null，否则执行resize()进行扩容；
> 2. 根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向 ⑥，如果table[i]不为空，转向③； 
> 3. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向④，这里的相同指的 是hashCode以及equals；
> 4. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值 对，否则转向5；
> 5. 遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操 作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可； 
> 6. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩 容。

## 14. 讲一讲HashMap的扩容机制

1. 在jdk1.8中，resize方法是在hashmap中的键值对大于阀值(0.75)时或者初始化时，就调用resize方法进 行扩容；
2. 每次扩展的时候，都是扩展2倍； 
3. 扩展后Node对象的位置要么在原位置，要么移动到原偏移量两倍的位置。

> 在putVal()中，我们看到在这个函数里面使用到了2次resize()方法，resize()方法表示的在进行第一 次初始化时会对其进行扩容，或者当该数组的实际大小大于其临界值值(第一次为12) , 这个时候在扩 容的同时也会伴随的桶上面的元素进行重新分发，这也是JDK1.8版本的一个优化的地方，在1.7 中，扩容之后需要重新去计算其Hash值，根据Hash值对其进行分发，但在1.8版本中，则是根据 在同一个桶的位置中进行判断(e.hash & oldCap)是否为0，重新进行hash分配后，该元素的位置 要么停留在原始位置，要么移动到原始位置+增加的数组大小这个位置上



## 15. ConcurrentHashMap 底层具体实现知道吗？

ConcurrentHashMap 是一种线程安全的高效Map集合

**底层数据结构：**

- JDK1.7的 ConcurrentHashMap 底层采用 分段的数组+链表 实现，

- JDK1.8 采用的数据结构跟HashMap1.8的结构一样，数组+链表/红黑二叉树。

  

**JDK1.7**

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段 数据时，其他段的数据也能被其他线程访问。

在JDK1.7中，ConcurrentHashMap采用Segment + HashEntry的方式进行实现

![image-20210824164717055](assets/image-20210824164717055.png)

一个 ConcurrentHashMap 里包含一个 Segment 数组。Segment 的结构和HashMap类似，是一 种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构 的元素，每个 Segment 守护着一个HashEntry数组里的元素，当对 HashEntry 数组的数据进行修 改时，必须首先获得对应的 Segment的锁。

Segment 是一种可重入的锁 ReentrantLock，每个 Segment 守护一个HashEntry 数组里得元 素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment 锁。



**JDK1.8**

在JDK1.8中，放弃了Segment臃肿的设计，取而代之的是采用Node + CAS + Synchronized来保 证并发安全进行实现，synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲 突，就不会产生并发 , 效率得到提升

![image-20210824164853535](assets/image-20210824164853535.png)



## 16. 创建线程的四种方式

1. 继承 Thread 类；

2. 实现 Runnable 接口；

3. 实现 Callable 接口；

4. 使用匿名内部类方式

## 17. runnable 和 callable 有什么区别

- Runnable 接口 run 方法无返回值；Callable 接口 call 方法有返回值，是个泛型，和Future、 FutureTask配合可以用来获取异步执行的结果 
- Runnable 接口 run 方法只能抛出运行时异常，且无法捕获处理；Callable 接口 call 方法允许抛出 异常，可以获取异常信息 注：Callalbe接口支持返回执行结果，需要调用FutureTask.get()得到， 此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞。



## 18. 加锁的方式有哪些 ?

使用synchronized关键字

使用Lock锁

synchronized和Lock有什么区别 ? 

> 首先synchronized是Java内置关键字，在JVM层面，Lock是个Java类；
>
>  synchronized 可以给类、方法、代码块加锁；而 lock 只能给代码块加锁。 
>
> synchronized 不需要手动获取锁和释放锁，使用简单，发生异常会自动释放锁，不会造成死锁； 而 lock 需要自己加锁和释放锁，如果使用不当没有 unLock()去释放锁就会造成死锁。 
>
> 通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到。



## 19. 如果你提交任务时，线程池队列已满，这时会发生什么

有俩种可能： 

1. 如果使用的是无界队列 LinkedBlockingQueue，也就是无界队列的话，没关系，继续添加任务到 阻塞队列中等待执行，因为 LinkedBlockingQueue 可以近乎认为是一个无穷大的队列，可以无限存放 任务 
2. 如果使用的是有界队列比如 ArrayBlockingQueue，任务首先会被添加到ArrayBlockingQueue 中ArrayBlockingQueue 满了，会根据maximumPoolSize 的值增加线程数量，如果增加了线程数量 还是处理不过来，ArrayBlockingQueue 继续满，那么则会使用拒绝策略RejectedExecutionHandler 处理满了的任务，默认是 AbortPolicy



## 19. 在你们的项目中有没有使用到线程池

我们的项目中很多地方使用了线程池 , 使用的场景经常有如下几种情况

1. 业务层处理分多个业务线 , 多条业务线的优先级有高有低 , 使用异步线程池执行优先级较低的业务

   比如: 搜索历史记录的异步保存 , 用户行为数据异步入库

2. 任务很多很重 , 比如说 : 现在有1000w数据需要进行统计运算 (10个线程 每个线程计算100w数据 , 计算完毕之后把10个线程结算结果合并即可) 

   每天晚上计算运营统计数据  , 售货机补货数据计算

## 20. 你了解的线程池的种类有哪些 ?

1. newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回 收空闲线程，若无可回收，则新建线程。 

2. newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列 中等待。 

3. newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。 

4. newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任 务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

   

## 21. 线程池的核心参数有哪些 ? 

corePoolSize 核心线程数量 

maximumPoolSize 最大线程数量 

keepAliveTime 线程保持时间，N个时间单位 

unit 时间单位（比如秒，分） 

workQueue 阻塞队列 

threadFactory 线程工厂 

handler 线程池拒绝策略





## 22. 你们项目中使用线程池, 核心线程数如何配置 ? 

1. IO密集型任务 : 核心线程数的数量  约等于   CPU核心数 * 2-3倍
2. 计算密集型任务 :  核心线程数 约等于 CPU核心数+1



## 23.线程池的执行原理知道嘛

![image-20220518165902129](assets/image-20220518165902129.png)

提交一个任务到线程池中，线程池的处理流程如下：

1. 判断线程池里的核心线程是否都在执行任务，如果不是（核心线程空闲或者还有核心线程没有被创建）则创建一个新的工作线程来执行任务。如果核心线程都在执行任务，则进入下个 流程。 
2. 线程池判断工作队列是否已满，如果工作队列没有满，则将新提交的任务存储在这个工作队 列里。如果工作队列满了，则进入下个流程。 
3. 判断线程池里的线程是否都处于工作状态，如果没有，则创建一个新的工作线程来执行任 务。如果已经满了，则交给饱和策略来处理这个任务。



## 24. 新建 T1、T2、T3 三个线程，如何保证它们按顺序执行？

用 join 方法



## 25. 讲一讲JVM的组成

![image-20220518170240888](assets/image-20220518170240888.png)



JVM包含两个子系统和两个组件:  

- 两个子系统为Class loader(类装载)、Execution engine(执行引 擎)； 
- 两个组件为Runtime data area(运行时数据区)、Native Interface(本地接口)。 
  - Class loader(类装载)：根据给定的全限定名类名(如：java.lang.Object)来装载class文件到 Runtime data area中的method area。 
  - Execution engine（执行引擎）：执行classes中的指令。
  - Native Interface(本地接口)：与native libraries交互，是其它编程语言交互的接口。 
  - Runtime data area(运行时数据区域)：这就是我们常说的JVM的内存。

## 26. JAVA代码在JVM是怎么执行的

首先通过编译器把 Java 代码转换成字节码，类加载器（ClassLoader）再把字节码加载到 内存中，将其放在运行时数据区（Runtime data area）的方法区内，而字节码文件只是 JVM 的一 套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎 （Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要 调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。



## 27. 说一下 JVM 运行时数据区

Java 虚拟机在执行 Java 程序的过程中会把它所管理的内存区域划分为若干个不同的数据区域。这 些区域都有各自的用途，以及创建和销毁的时间，有些区域随着虚拟机进程的启动而存在，有些区 域则是依赖线程的启动和结束而建立和销毁。Java 虚拟机所管理的内存被划分为如下几个区域

![image-20210824170002762](assets/image-20210824170002762-1629795603621.png)

**程序计数器（Program Counter Register）**：当前线程所执行的字节码的行号指示器，字节码解 析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳 转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成； 为什么要线程计数器？因为线程是不具备记忆功能

 **Java 虚拟机栈（Java Virtual Machine Stacks）：**每个方法在执行的同时都会在Java 虚拟机栈中创 建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息； 栈帧就是Java虚拟机栈中的下一个单位 

**本地方法栈（Native Method Stack）：**与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的； Native 关键字修饰的方法是看不到的，Native 方法的源码大部分都是 C和C++ 的代码

 **Java 堆（Java Heap）：**Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的**对象实例**都在这里分配内存； 

**方法区（Methed Area）：**用于存储已被虚拟机加载的**类信息、常量、静态变量、即时编译后的 代码**等数据。



## 28. 堆栈的区别是什么？

![image-20220518170436053](assets/image-20220518170436053.png)



## 29. 什么是类加载器，类加载器有哪些?

主要有一下四种类加载器: 

1. 启动类加载器(Bootstrap ClassLoader)用来加载java核心类库，无法被java程序直接引用。
2. 扩展类加载器(extensions class loader):它用来加载 Java 的扩展库。Java 虚拟机的实现会提 供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。
3. 系统类加载器（system class loader）：它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它。
4. 用户自定义类加载器，通过继承 java.lang.ClassLoader类的方式实现

## 30. 什么是双亲委派模型？

双亲委派模型：如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这 个请求委派给父类加载器去完成，每一层的类加载器都是如此，这样所有的加载请求都会被传送到 顶层的启动类加载器中，只有当父加载无法完成加载请求（它的搜索范围中没找到所需的类）时， 子加载器才会尝试去加载类。 

**总结就是： 当一个类收到了类加载请求时，不会自己先去加载这个类，而是将其委派给父类，由父类去加 载，如果此时父类不能加载，反馈给子类，由子类去完成类的加载。**



## 31. 对于java的Stream流有使用过嘛 , 讲一讲stream流中的常用方法

![image-20220518171249899](assets/image-20220518171249899.png)



## 32. jdk8有哪些新特性

1. 函数式方法
2. 方法引用
3. stream流
4. optional
5. .....

