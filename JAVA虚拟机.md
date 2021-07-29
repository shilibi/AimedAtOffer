JAVA虚拟机

Write once Run anywhere

 .java 文件通过 javac  编译成.class 字节码文件，java虚拟机将字节码文件翻译成操作系统可以执行的命令，操作系统与硬件打交道

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313115159048.png" alt="image-20200313115159048" style="zoom: 50%;" />

java 运行时数据区：

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313115456062.png" alt="image-20200313115456062" style="zoom:50%;" />

程序计数器：在if或者for循环中计数执行的次数，每个线程都有一个单独的计数器且互不干扰（线程私有      ）。

###### 程序计数器不会发生内存泄露(唯一没有OutOfMemoryError的区域)

**堆**：创建出来（new)的所有对象都在堆中分配空间和数组。不一定是连续的物理空间。可能泄露内存。

**虚拟机栈** : 线程私有，生命周期与线程相同，描述的是java方法的内存值执行模型

long double 在栈中占两个局部变量大小，其余占一个。 按照方法加载顺序进栈，执行完毕后出栈。因此先进后出。

**本地方法栈**：会发生内存泄露

**方法区**：常量、静态变量在编译时就能确认。可能发生内存泄漏。

```java
//在运行过程中添加到常量池中
String str1 = new String("hello");
String str2 = new String("hello");
System.out.println(str1.intern() == str2.intern());//输出结果为true
```



​		当字符串调用intern()方法时，它会在常量池中找有没有hello这个对象，如果有就返回。如果没有，就在常量池中创建hello这个对象。运行时常量池，具有动态性。

强制转换优先级高于+-*/ 

-Xmx10m：最大内存10M;

###### 直接内存

#### 内存异常情况演示：

###### 堆内存：

```java
List<String> strings = new ArrayList<>();
while(true){
    strings.add(new String(""));
}
```

​		报错：OutOfMemoryError : java heap space

###### 栈内存：

```java
public static void main(String[] args){
    int rabbitNum = getRabbitNum(20);
    System.out.println(rabbitNum);
}
public static int getRabbitNum(int month){
    return getRabbitNum(month - 1) + getRabbitNum(month - 2);//没有结束的标记
}
```

​		报错： StackOverflowError 栈溢出,请求栈的深度已经超过了



## 内存回收机制

判断对象已死的算法：

1、引用计数器算法

​	引用计数器算法简单概括为:给对象添加一个引用计数器,每当有一个地方引用该对象时,计数器+1,当引用失效时,计数器-1,任何时刻,当计数器为0的时候,该对象不再被引用。客观的说,引用计数器的实现简单,判定效率也高,大部分场景下是一个不错的选择。但是,当前主流的JVM均没有釆用标记清除算法,原因在于,它很难解决对象之间互相循环调用的情况

```java
Student stu1 = new Student();
Student stu2 = new Student();
stu1 = stu2;
stu2 = stu1;
```

**↑↑↑此处存疑**

2、可达性分析算法

​	在主流的商用程序语言(如C#,Java)的主流实现中,都是通过可达性分析来判断对象是否存活,这个算法的思想就是通过一系列的成为" GC Roots"的对象作为起始点,从这些节点开始向下搜索,搜索所走过的路径成为引用链,当一个对象到 GC Roots"没有任何引開链相连,则证明此对象是不可用的。

​	在Java中,可以作为 GC Roots的对象包括下面几种:

1. 虚拟机栈中引用的对象;
2. 方法区中类静态属性引用的对象;
3. 方法区中的常量引用的对象;
4. 本地方法栈中JNI(即一般说的 Native方法)的引用的对象

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313125959313.png" alt="image-20200313125959313" style="zoom: 50%;" />

## 引用

无论是通过引用计数器判断的引用数量,还是通过可达性分析判断出的引用链是否可达,判定对象是否存活都跟引用有关。在JDκ1.2以前,引用被定义为当一个 reference类型的数据代表的是另外一块内存的起始地址,该类型的数据被称之为引用,这种定义很纯粹,但是也很狭隘,一个对象在这种定义下只有被引用和没有被引两种状态。对于描述一些食之无味,弃之可惜的对象就显得无能为力。我们希望能描述这类对象,当内存足够的时候,将它存放在内存中,当内存空间进行垃圾回收后显得还是内存紧张时,可以回收这部分对象,很多系统的缓存功能都符合这样的应用场景。因此在JDK12以后对引用进行重新的扩充，分为强引用,软引用,弱引用,虚引用4种,这四种引用的强度依次递减。

##### 强引用

强引用是在代码中普遍存在的,类似于 Object ot= new object0,只要强引用一直存在,垃圾收集器就永远不会回收被引用的对象。

##### 软引用

软引用用来描述一些还有用但并非必须的对象,对于软引用关联着的对象,当内存溢出异常发生之前,通过垃圾回收进行二次回收。如果次回收完成之后,系统内存依然不够,才会抛出内存溢出异常,在k12以后用 SoftReference类来实现软引用。

##### 弱引用

弱引用也是用来描述非必须对象的,但是它的强度相比于软引用来说更弱些,它仅仅能生存到下一次垃圾回收之前。当垃圾收集时,无论内存是否足够,弱引用的对象都要被回收,在dk1.2以后用 WeakReference类来实现弱引用。

##### 虚引用

虚引用是最弱的一种引用关系,一个对象是否有虚引用的存在,完全不会对其生存时间构成影响,也无法通过一个虚引用来获取一个实例对象。为一个对象设置弱引用的唯一目的就是该对象在垃圾回收时受到一个系统通知,Jdk1,2以后用 PhantomReference实现虚引用。

#### 标记清除算法

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313130724622.png" alt="image-20200313130724622" style="zoom:50%;" />

​	先标记，再清除

​	问题：

​		1、回收之后内存不处于连续的状态，即内存碎片。当需要分配一大块内存时，内存空间足够，但是却会发生分配失败的情况。

​		2、标记和清除效率不高。

#### 标记整理算法：

先标记，分为回收对象和存活对象，然后将存活对象放在连续的内存空间中，其余的内存空间进行清理。

![image-20200313131154472](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313131154472.png)

#### 复制算法：

预留50%内存空间，将存活对象复制到预留空间，清空左侧使用区域，使之成为预留区域。

问题:空间利用率太低。

![image-20200313134450685](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313134450685.png)

#### 分代垃圾回收

​	将内存空间分为年轻代，老年代。

​	年轻区又分为Eden（伊甸园）区，新生代都在伊甸园诞生，Survivor（幸存）区。Survivor区分为from区和to区。年轻代都存放朝生夕死的对象。伊甸区：from区：to区 = 8:1:1

 	老年区存放经常用，不容易被回收的对象，有价值的在老年代。 

过程：

​	将Eden区过一段时间后仍然存在的对象复制到Survivor区的From区，然后清空Eden区，from区到to区此时采用了复制算法。带有年龄标记。Minor GC :stop the world---》STW，在进行垃圾回收的时候，所有的工作线程、用户线程全部停止。



或：

1.分代分为年轻代和老年代,年轻代里头又分为Eden区和 Survivor区,通常默认的比例为8:1:1,每次只保留10%的空间用作预留区域,然后将90%的空间可以用作新生对象。

2.每一次垃圾回收之后,存活的对象年龄对应+1,当经历15次还依然存活的对象,我们让它直接进入到老年代

3.另外—种进入到老年代的方式是内存担保机制,也就是当新生代的空间不够的时候,对象直接进入到老年代

4.新生代的垃圾回收叫 Minor go,老年代的叫FG

###### 10%的区域不够了怎么办？

​	内存担保机制。如果某个对象年龄标记为15岁，那么就将from区/to区直接进入到老年代。

###### 如果年轻代和老年代的空间都不够了怎么办？

​	Full GC 机制。对年轻代和老年代统一进行垃圾回收。耗时较长。	

**原话**：分代收集算法:当前商用的垃圾收集器都采用的是分代垃圾回收,这种算法没有什么新的思想,只是根据对象的存活周期将内存分为几块,一般是将java堆分为新生代和老年代,这样就可以根据各个代的对象特点选用最适当的回收算法。在新生代每次垃圾回收都有大量的对象死去,只有少量存活,这样就适合采用复制算法。只需要付出少量的对象复制成本就可以完成垃圾回收而老年代因为存活率高,没有其他内存进行分配担保,就必须使用标记清理或者标记整理进行回收。

## 垃圾收集器

#### Serial收集器：

​	单线程,stw。 Pause the world???没法实现边运行边收集，因为是收集不完的。想象下嗑瓜子皮？？

​	GC线程时间越来越短、越来越少，但是不会消失。

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313141500310.png" alt="image-20200313141500310" style="zoom: 50%;" />

​	Serial收集器是最基本,发晨最悠久的收集器在JDK1.31之前是拟机新生代垃圾回收的选择。这个收集器是个单线程的。它的单线程的意义并不仅仅说明它只会使用一个CPU或者条收集线程去完成收集工作,最重要的是,它进行垃圾收集时,其他江作线程会暂停,直到收集结束。这项工作由虚拟机在后台自动发起和执行的,在用户不可见的情兄下将所有工作线程全部俘頂这对于很多应用程序釆说是不可容忍的。我们可以设想一下.我们的计算机在运行1个小时就要停止5分钟的时候.这是什么慆况?对这种设计,虛拟机设计人员表示的也是非常委屈,因为不可边收集,这边还要不断的产生垃圾对象,这样是清理不完的。所以从1.3—直到现在,虚拟机的开发团队一真为减少因垃圾回收而产生的线程停顿努力着,所出现的虚拟机越来越优秀,但自刳现在,依然没有完全消除。讲区里,貌以Serial收集器已经是食之无味弃之可惜"了,但实际上,它依然是虚拟机在client模式下,新生代默认的垃圾收集器。它有相对干其他垃圾收果器的优,比如由于没有线程之间切换的开销,专心做垃圾收集自然能够收获最高的线程科用双率。在用户面应用背鬚下,一般分配给虚拟机内存不会太大收集几十兆或者两百兆的新生代对象,停顿时同控制在几十毫秒到百毫秒之间,这个是可以接受的,只要不是频繁发生。因此,Serial收集器在 Client模式下,对于新生代来说依然是个很好的选择。

#### ParNew收集器：

​		ParDew收集器其实就是 Serial收集器的多线程版本,除了使用多线程进行垃圾回收之外,其佘可控参数,收集算法,停止工作线程,对象分配原则,回收策略等与 Serial收集器完全致。除了多线程实现垃圾收集之外,其他没有什么太多创新之处,但是它确实 Server模式下的新生代的首选的虚拟机收集器。其中一个重要的原因就是除了 Serial收集器外,只有它能与CMS配合使用。在JDK1.5时期,Hotspot出了—款在强交互应用划时代的收集器CMS,这款收集器是Hotspot第一款真正意义上的并发收集器,第一次实了垃圾回收与工作线程同时工作的可能性,换而言之,你可以边污染,边收集。

​		不过CMS作为老年代的收集器,却无法与1.4中发布的最新的新生代垃圾收集器配合使用,反之只能使用Sera或者 Pardew中的一个。 ParNel收集器可以使用-XX:+ Use ParNewGc强行指定它,或者使用-XX:+ UseConcMarkSweepGO选项后的默认新生代收集器

​		ParNew收集器在单CPU环境下绝对不会有比 Serial收集器更好的效果,甚至由于存在线程交互开销,该收集器在通过超线程技术实现的两个CPU的环境下都不能保证百分之百超越 Serial收集器。当然,随着CPU数量的增加,对于GC时系统的有效资源利用还是很有好处的。在CPU非常多的情况下,可以使用- XX. ParallelGC Threads来限制垃圾回收线程的数量。

​		仍然有STW。

![image-20200313142530658](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313142530658.png)



#### Parallel Scavenge收集器：

​		Parallel Scavenge收集器是个新生代收集器,采用复制算法,又是并行的多线程垃圾收集器。它的关注点与其它收集器的关注点不-样,CMS等收集器的关注点在于缩短垃圾回收时用户线程停止的时间,而 Parallel Scavenge收集器则是达到个可控制的吞吐量,所谓吞吐量就是CPU运行用户线程的时间与CPU运行总时间的比值,即吞叶量=(用户线程工作时间)/(用户线程工作时间+垃圾回收时间),比如虚拟机总共运行100分钟,垃圾收集消耗1分钟,则吞叶量为99%。停顿时间越短越适合与用户交互的程序,良好的响应速度能提高用户体验,但是高吞叶量则可以高效率的利用CPU的时间,尽快完成程序的运算任务,主要适合在后台运算而不需要太多交互的程序。有两个参数控制吞吐量,分别为最大垃圾收集时间:XX: MaxGcPauseMills,直接设置吞吐量的大小:- X GCTimeRatio.XX: +UseAdaptiveSizePolicy自适应策略也是 Parallel Scavenge收集器区别于 Parnew收集器的重要一点

#### Serial Old收集器：

Serial old收集器是serial收集器的老年代版本,它同样是一个单线程收集器,使用标记-整理算法,这个收集器的主要目的也是在与给client模式下使用。如果在 Server模式下,还有两种用途,一种是在jdk5以前的版本中配合 Parallel Scavenge收集器使用,另一种用途作为cMs的备用方案,在并发收集发生 Concurrent mode failure时使用

#### Parallel Old收集器：

Parallel Old收集器是 Parallel Scavenge收集器的老年代版本,使用多线程和标记-整理算法,这个收集器在jdk6中才开始使用的,在此之前 Parallel Scavenge收集器直处于比较尴尬的阶段,原因是,如果新生代采用了 Parallel Scavenge收集器,那么老年代除了 Serial Old之外,别无选择,由于老年代 Serial在服务端的拖累,使得使用了 Parallel Scavenge收集器也未必能达到吞吐量最大化的效果,由于单线程的老年代无法充分利用服务器多CPU的处理能力,在老年代很大而且硬件比较高级的环境中,这种组合的吞吐量甚至不如 Parallel Scavenge收集器+CMs直到 Parallel old收集器出现后,"吞吐量优先收集器咚终于有了名副其实的组合,在注重吞吐量优先和CPU资源敏感的场合,可以采用 Parallel Scavenge收集器+Parallel old收集器

#### CMS收集器：

​		CMS:Concurrent Mark Sweep

CMS收集器是一种以获取最短停顿时间为目标的收集器。从名字( concurrent Mark Sweep)上就可以看出,采用的标记清除算法,它的过程分为4个步骤只有初始标记和重新标记需要暂停用户线程。

​	1.被台标记已一仅仅关联GC Roots能直接关联到的象,速度很快

​	2.并发标记一进行 GC Roots Tracing的过程

​	3.重新标记-为了修正并发标记期间,因用户程序运作而导致标记产生变动的那部分对象的标记记录

​	4.并发清除

是一款边污染，边治理的收集器。

缺点：

​		由于整个过程中耗时最长的并发标记和并发清除过程收集器都能与用户线程起工作,所以总的来说,CMS的内存回收过程与用户线程起并发执行的

CMS收集器的三大缺点：

1.cMs收集器对CPU资源非常敏感

2.无法处理浮动垃圾，需要预留空间

3因为基于标记清除算法,所以会有大量的垃圾碎片产生-xX+ UseCMSCompactAtFull Collection

![image-20200313143852548](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313143852548.png)

#### G1收集器：

​	G1是JDK1.9以后默认的处理器

​	首先,G1的设计原则就是简单可行的性能调优

​	-XX:+UseG1Gc  -xmx32g -XX:MaxGCPauseMillis=200

​	其中XX+UseG1Gc为开启G1垃圾收集器,-Xmx32g设计堆内存的最大内存为32G,- XX:MaxGCPauseMillis=200设置GC的最大暂停时间为200ms。如果我们需要调优,在内存大小一定的情况下,我们只需要修改最大暂停时间即可。

​	1、内存分配

​	2、Young垃圾回收

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313144815847.png" alt="image-20200313144815847" style="zoom:50%;" />

​	3、Mix垃圾回收:三色法

![image-20200313145052606](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313145052606.png)

## 设置参数

![image-20200313145354402](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200313145354402.png)

## 工具

jdk 的bin目录下的jconsole，通过jps获取进程pid，然后通过jconsole 连接到这个pid。

```java
public class HeapTest {
    private byte[] bytes = new byte[64*1024];
    public static void main(String[] args) throws Exception {
        System.in.read();
        List<HeapTest> heapTestList = new ArrayList<>();
        for(int i = 0;i < 1000;i++){
            Thread.sleep(100);
            heapTestList.add(new HeapTest());
        }
        System.gc();
    }
}
```



