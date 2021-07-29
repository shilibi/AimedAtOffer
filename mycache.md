

# java虚拟机

java的好处：Write once ,Run anywhere

java 的编译过程：  .java 文件通过 javac  命令编译成.class 字节码文件，java虚拟机将字节码文件翻译成操作系统可以执行的命令，操作系统与硬件打交道

![image-20200409234401145](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200409234401145.png)

**程序计数器不会发生内存溢出**（[为什么？](http://101.201.57.15/wordpress/?p=83)）

JVM中各个数据区的作用

**堆**：创建出来（new)的所有对象都在堆中分配空间和数组（存放实例对象）。

不一定是连续的物理空间。可能泄露内存。被所有线程共享。

堆是垃圾回收机制管理的主要区域，因此也被称为GC区。

**虚拟机栈** :

 线程私有，生命周期与线程相同，描述的是java方法的内存值执行模型。虚拟机栈存放的其实是一个个的**栈帧**，当线程执行到一个方法时，那么就会把和这个方法有关的数据“打包”成栈帧push进栈，当方法执行完成后，就会出栈。由于方法总是先执行的最后结束，例如main，因此是先进后出，所以采用栈结构。

64位长度的long和double 在栈中占两个局部变量大小，其余占一个。 因此当一个方法执行时，他所需要的空间已经确定了。

ps:StackOverFlowError：程序所申请的深度大于jvm所支持的最大深度，就会发生StackOverFlowError，例如死循环。

**本地方法栈**：与虚拟机栈大同小异，虚拟机栈执行java方法，本地方法栈执行的是本地native方法。

会发生内存泄露



**方法区**：用于存放已经被虚拟机加载的类信息，常量、静态变量、及编译器编译后的代码等数据。

较少进行垃圾回收。

常量、静态变量在编译时就能确认。可能发生内存泄漏。

**运行时常量池**：是方法区的一部分。

程序运行期间，可以手动向常量池添加常量，例如调用String的intern()方法。



String str1 = "abc";

在编译阶段，jvm首先去常量池查看是否存在abc这个常量，如果有，就在栈中开辟一个str1空间，存放“abc”的地址。如果没有，那么就在常量池开辟空间存放abc，然后在栈中开辟一个str1空间，存放“abc”的地址。



String str2 =  new String("abc");

在编译阶段，如果常量池不存在abc，那么在常量池创建abc这个常量。在运行阶段，在堆中创建一个对象str2，然后将常量abc复制一份存到堆空间str2中

---

## 内存分配与回收策略

判断对象已死的算法：

#### 引用计数器算法：

在对象创建的时候给对象添加一个计数器属性，当有一个地方引用它的时候，计数器加一；当引用结束的时候，计数器减一。当计数器为0的时候表示对象不可能被引用。

优点：算法实现简单；效率高；

缺点：难以解决对象间互相引用的情况。

#### 可达性分析算法（根搜索算法）：

将各个对象看作一个个的结点，根据他们之间的调用关系将他们进行连线，从根节点开始往下找，只要找不到的都是属于对象已死的。就算有几个节点成了一棵树，那也算对象已死。如下图,可以看到，obj1、obj2、obj3、obj4都可以通过GC Roots找到，因此他们对象是可用的。虽然obj5、obj6、obj7也成了一棵树，他们三个之间也是有连线，但是，从GC Roots出发，无法找到这三个， 因此这三个对象都是不可用的。

在Java中，可以作为GC Roots的对象包括下面几种： 

- 虚拟机栈中引用的对象； 
- 方法区中类静态属性引用的对象； 
- 方法区中的常量引用的对象； 
- 本地方法栈中JNI（即一般说的Native方法）的引用的对象；

## （非23种之一）简单工厂模式

定义一个工厂类，它可以根据参数的不同返回不同类的实例， 被创建的实例通常都具有共同的父类。

由于在简单工厂中用于创建实例的方法是静态的，因此又称为静态工厂方法。

示例：

```java
public class Factory
{
	public static Product factoryMethod(String type)
	{
		if(type.equalsIgnoreCase(“A"))
		{
			return new ConcreteProductA(); //根据参数创建具体产品
		}
		else if(type.equalsIgnoreCase(“B"))
		{
			return new ConcreteProductB(); //根据参数创建具体产品
		}
		……
	}
	……
}
```

通过向FactoryMethod传入参数，返回不同的实例，但是这个有个问题，就是如果新增一个类，还是需要修改工厂方法。不符合开闭原则。



## 工厂方法模式

工厂模式又称为虚拟构造器模式或多态工厂模式。

工厂方法模式很好的解决了简单工厂方法的缺点，不需要修改具体的工厂方法就可以引进新产品，更加符合开闭原则。

首先介绍一下什么是里氏代换原则：

英文：If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T.  

简单点说，所有引用基类（父类）的地方必须能透明地使用其子类的对象 ，也就是如果从父类改成了子类，代码仍然没有问题，就是里氏代换原则。但是不保证从子类换成父类也没有问题。



由于使用基类对象的地方都可以使用子类对象，因此在程序中尽量使用基类类型来对对象进行定义，而在运行时再确定其子类类型，用子类对象来替换父类对象。  和多态很类似。



父类负责定义创建产品对象的公共接口，而子类负责生成具体的产品对象。



![image-20200413201155268](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200413201155268.png)



在这个模式中，Factory不负责具体的产品创建，只定义子类对象需要实现的接口。子类负责创建具体的实例。

例：

```java
//抽象实体类定义抽象的方法，其中的方法子类必须实现。
public interface Button
{
	public void text();
	public void color();
	public void position();
}
```

```java
//具体的类，其中定义具体的对象的属性
public class CircleButton implements Button
{
	public void text()
	{
		……
	}
	public void color()
	{
		……
	}
	public void position()
	{
		……
	}
}

public class RectangleButton implements Button
{
	public void text()
	{
		……
	}
	public void color()
	{
		……
	}
	public void position()
	{
		……
	}
}
```

```java
//抽象的工厂类，定义对象需要实现的方法
public interface ButtonFactory
{
	public Button createButton();
}
```

```java
//具体的工厂方法，根据创建时的类返回具体的实例
public class CircleButtonFactory implements ButtonFactory
{
	public Button createButton()
	{
		return new CircleButton();
	}
}
public class RectangleButton implements ButtonFactory
{
	public Button createButton()
	{
		return new CircleButton();
	}
}
```

```java
//客户端代码
public Button getButton()
{
    ButtonFactory factory;
    Button button;
    factory=new CircleButtonFactory();
    button =factory.createButton();
    button.text();
    button.color();
    button.position();
    return button;
}
```

注意button和factory就是多态的一种，通过实例化不同的工厂方法来创建不同的Button工厂，然后让工厂生产按钮。

#### 优缺点：

工厂模式方法完全符合开闭原则，如果需要新增不同的产品，只需要新建并继承（实现）一个对应的产品工厂类和产品类即可。

但是工厂方法每次创建的时候都需要创建一对类，增加系统的复杂度，会给系统带来一些额外的开销。也增加了系统的抽象性和理解难度 。

#### 适用环境：

客户端不知道它所需要的对象的类（客户端不需要知道具体产品类的类名，只需要知道所对应的工厂即可，具体产品对象由具体工厂类创建）。
抽象工厂类通过其子类来指定创建哪个对象  。

# 抽象工厂模式

适用于一个工厂生产多个产品对象。又叫工具模式（Kit）

产品等级结构：产品的继承结构。人 ---》 男人-----》中国男人

产品族：指同一工厂生产，但是不是同一类产品。例如：人 ---》 男人-----》黄皮肤男人 和 人 ---》 男人-----》白皮肤男人，此时黄皮肤男人 和白皮肤男人就是一个产品族。

抽象工厂模式中的具体工厂不只是创建一种产品，它负责创建一族产品 。

![image-20200413205620109](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200413205620109.png)

```java
public interface AbstractProductA
{	
	public void algorithmA();
}
```

```java
public class ConcreteProductA1 implements AbstractProductA
{
	public void algorithmA()
	{
		//算法A1
	}
}
```

```java
public interface AbstractProductB
{	
	public void algorithmA();
}
```

```java
public class ConcreteProductB1 implements AbstractProductB
{
	public void algorithmB()
	{
		//算法A1
	}
}
```

```java
public interface Factory
{
	public AbstractProductA createProductA();
	public AbstractProductB createProductB();
}
```

```java
public class ConcreteFactory1 implements Factory
{
    public AbstractProductA createProductA()
    {
    	return new ConcreteProductA1();
    }
    public AbstractProductB createProductB()
    {
  		return new ConcreteProductB1();
    }
}
```

  如果想创建一个实例，需要先创建一个对应的工厂实例，然后通过工厂创建一个需要的实例。但是这个工厂与工厂模式下的工厂不同，这个工厂可以生产不同的实例。

优点：当需要一个新的工厂时，只需要新增一个类并继承自Factory即可。

缺点：如果需要工厂新增一条产品线，需要修改具体工厂类，违背了开闭原则。

### 工厂模式的退化

当抽象工厂模式中每一个具体工厂类只创建一个产品对象，也就是只存在一个产品等级结构时，抽象工厂模式退化成工厂方法模式；
当工厂方法模式中抽象工厂与具体工厂合并，提供一个统一的工厂来创建产品对象，并将创建对象的工厂方法设计为静态方法时，工厂方法模式退化成简单工厂模式。  





场景：当某一个动作执行完毕，在即将执行下一个动作之前，需要在跳转之前进行一系列（如校验1、校验2等）<font color="red">动作</font>，要保证每个<font color=red>动作</font>都有机会被执行，并且这些<font color=red>动作</font>有先后次序之分时；或者需要在以下几种动作中根据条件选一种的情况，可以使用责任链模式。

由下图可知，责任链模式由四部分组成，分别为处理者，基础处理着(可选)，具体处理者，客户端。

处理者（interface）一般用来定义这个处理的方法，其中还包含一个next的指针？

基础处理者需要定义好各个具体处理者之间的关系。

具体处理者需要实现处理者接口定义好的抽象方法。

客户端可以根据逻辑生成链，请求可以发给任何一个处理者，即从接受的那个处理者开始链式地处理请求。

![image-20200425212857176](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200425212857176.png)

例一、日志打印的例子，如果我需要打印一般信息，警告信息以及错误信息，由于每次都是选一种，可以选择责任链模式。

Handler:

```java
public abstract class AbstarctLogger{
    public static int INFO = 1;
    public static int WARNING = 1;
    public static int ERROR = 1;
    
    protected int level;
    
    protected AbstarctLogger nextLogger;
    
    public void setNextLogger(AbstarctLogger abstarctLogger){
        this.nextLogger = abstarctLogger;
    }
    
    public void logMessage(int level,String message){
        if(this.level = level){
            write(massage)
        }else{
            nextlogger.logMessage(level,message);
        }
    }
    abstract protected void write(String message);
}
```

ConcreateHandler：

```java
public class InfoLogger extends AbstractLogger {

   public InfoLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("Info Console::Logger: " + message);
   }
}
public class WarningLogger extends AbstractLogger {

   public WarningLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("Warning Console::Logger: " + message);
   }
}
public class ErrorLogger extends AbstractLogger {

   public ErrorLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("Error Console::Logger: " + message);
   }
}
```

客户端：

```java
public class ChainPatternDemo {
   
   private static AbstractLogger getChainOfLoggers(){
 
      AbstractLogger infoLogger = new InfoLogger(AbstractLogger.INFO);
      AbstractLogger warningLogger = new WarningLogger(AbstractLogger.WARNING);
      AbstractLogger errorLogger = new ErrorLogger(AbstractLogger.ERROR);
 
      infoLogger.setNextLogger(warningLogger);
      warningLogger.setNextLogger(errorLogger);
 
      return infoLogger;  
   }
 
   public static void main(String[] args) {
      AbstractLogger loggerChain = getChainOfLoggers();
 
      loggerChain.logMessage(AbstractLogger.INFO, "This is an information.");
 
      loggerChain.logMessage(AbstractLogger.DEBUG, 
         "This is a warbubg level information.");
 
      loggerChain.logMessage(AbstractLogger.ERROR, 
         "This is an error information.");
   }
}
```

例二、当进行下单操作时，需要进行一系列校验操作，可以将这个操作抽象成一个Handler，而具体的校验则是一个个的concreateHandler。





SqlServer 清空整个数据库（仅数据）

```sql
EXEC sp_MSForEachTable 'ALTER TABLE ? NOCHECK CONSTRAINT ALL'
EXEC sp_MSForEachTable 'ALTER TABLE ? DISABLE TRIGGER ALL'
EXEC sp_MSForEachTable 'DELETE FROM ?'
EXEC sp_MSForEachTable 'ALTER TABLE ? CHECK CONSTRAINT ALL'
EXEC sp_MSForEachTable 'ALTER TABLE ? ENABLE TRIGGER ALL'
EXEC sp_MSFOREACHTABLE 'SELECT * FROM ?'
```



使用JDK1.8支持的stream().sorted，其中Exam是Bean，getExId 为需要排名的属性

```java
exams = exams.stream().sorted(Comparator.comparing(Exam::getExId)).collect(Collectors.toList());
//逆向排序
exams = exams.stream().sorted(Comparator.comparing(Exam::getExId).reversed()).collect(Collectors.toList());
//或 Collections.reverse(exams)
```







错误信息：

```
Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

使用`export HADOOP_ROOT_LOGGER=DEBUG,console`   和`hadoop fs -text /test/data/origz/access.log.gz` 查看日志可以看到以下错误信息：

```
 Failed to load native-hadoop with error: java.lang.UnsatisfiedLinkError: /usr/hadoop/lib/native/libhadoop.so.1.0.0: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by /usr/hadoop/lib/native/libhadoop.so.1.0.0)
```

原因是因为hadoop.so需要的版本是glibc_2.14 ，而目前是2.12，所以需要更新版本为2.14。

1、[点击这里](http://ftp.gnu.org/gnu/glibc/)下载glibc-2.14.tar.bz2、glibc-linuxthreads-2.5.tar.bz2

2、将文件上传到/usr/hadoop/data文件夹中

3、解压glibc-2.14.tar.bz2：``` tar -xjvf glibc-2.14.tar.bz2```

4、进入到 glibc-2.14文件夹中，解压glibc-linuxthreads-2.5.tar.bz2

```shell
cd cd glibc-2.14
tar -xjvf ../glibc-linuxthreads-2.5.tar.bz2
```

5、返回上级目录：

```shell
cd ..
export CFLAGS="-g -O2"
glibc-2.14/configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin --disable-sanity-checks
```

6、执行*make*命令：

```shell
make
```

​		注：如果此时报错：*make: *** 没有指明目标并且找不到 makefile。 停止。* 使用 以下命令安装gcc

```shell
yum install gcc		
```

7、执行make install

```shell
sudo make install
```

8、查看lib版本：

```
ll /lib64/libc.so.6
```

9、重启hadoop

10、设置hadoop日志模式为DEBUG模式，并执行hadoop命令：

```shell
export HADOOP_ROOT_LOGGER=DEBUG,console
hdfs dfs -ls /	 
```





# Parallel Scavenge收集器（新生代）

Parallel Scavenge，翻译过来就是并行收集器，它与吞吐量有密切关系，因此也称为吞吐量收集器（Throughput Collector）

#### 特点：

1. 采用复制算法
2. 并行多线程收集器
3. 关注可控制的吞吐量<sup>[1]</sup>
4. 不能配个CMS等收集器使用<sup>[2]</sup>

#### 两个重要参数：

-XX: MaxGcPauseMills

​	最大垃圾回收时间，即进行垃圾回收时所耗的最大时长。越小则停顿时间越少，但可能导致垃圾收集变得频繁。

-XX:GCTimeRatio：

​	垃圾回收时间占总时间比率，也就是设置吞吐量大小

此外，还有一个参数：

-XX: UseAdaptiveSizePolicy

​	自适应调整策略（GC Ergo nomics）会根据情况自动调节，自适应策略也是 Parallel Scavenge收集器区别于 Parnew收集器的重要一点



注：

1、吞吐量

吞吐量 = （用户线程工作时间）/(用户线程工作时间+垃圾回收时间)，很显然，当吞吐量越大时，意味着用户线程工作时间越长，垃圾回收时间越少，用户体验越佳。

2、为什么此收集器不能配合CMS使用？

Serial/Serial Old/ParNew/CMS都使用了分代式GC框架，使用了这个框架的收集器可以新老代混合使用，但是G1和Parallel Scavenge使用了自己的框架，最重要的是使用了不同框架的垃圾收集器不能配合使用，因此如上所说，不能配合CMS使用。



PageHelper使用

## 1、导入Maven依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.11</version>
</dependency>
```

## 2、整合Spring/Mybatis

以下方式二选一：

```xml
<!-- 
    plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
    properties?, settings?, 
    typeAliases?, typeHandlers?, 
    objectFactory?,objectWrapperFactory?, 
    plugins?, 
    environments?, databaseIdProvider?, mappers?
-->
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
        <property name="param1" value="value1"/>
	</plugin>
</plugins>
```

​																	*mybatis-config.xml↑↑↑*

**<font color='red'>或</font>**

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <!-- 注意其他配置 -->
  <property name="plugins">
    <array>
      <bean class="com.github.pagehelper.PageInterceptor">
        <property name="properties">
          <!--使用下面的方式配置参数，一行配置一个 -->
          <value>
            params=value1
          </value>
        </property>
      </bean>
    </array>
  </property>
</bean>
```

​																	*applicationContext.xml↑↑↑↑↑↑*

## 3、使用

```java
 //Service
   public PageInfo getExamByGrade(String grade,int page,int pageSize) throws IOException {
        PageHelper.startPage(page,pageSize);
        List<Exam> exams = examMapper.selectExamByGrade(grade);
        PageInfo pageInfo = new PageInfo(exams);
        return pageInfo;
    }
```

其中，page参数为第几页，pageSize为每页显示多少数据。紧跟PageHelper.startPage(page,pageSize);后的第一个select语句会被分页，因此，for循环中的多个select语句只有第一个SELECT语句会被执行。

其中exams可以通过pageInfo.getList()获取到。

通过SpringMvc将pageInfo返回到前端页面，可以通过json获取到对应数据。

盲猜原理：先执行一遍查询语句，然后获取其中的特定的查询结果？



```java
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if(root1 == null || root2 == null){
            return false;
        }
        if(root1.val == root2.val){
            if(judge(root1,root2)){
                return true;
            }
        }
        return HasSubtree(root1.left,root2) || HasSubtree(root1.right,root2);
    }
    public boolean judge(TreeNode root1,TreeNode root2){
        if(root2 == null){
            return true;
        }
        if(root1 == null){
            return false;
        }
        if(root1.val == root2.val){
            return judge(root1.left,root2.left) && judge(root1.right,root2.right);
        }
        return false;
    }
}
```

