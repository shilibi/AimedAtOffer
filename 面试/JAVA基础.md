JAVA基础



## java 命令

编译java程序：通过**javac java文件名+后缀**，例如**javac scout.java**



运行java程序：通过 **java java的程序名但不加后缀**，例如 **java scout**

## 基本数据类型

**基本数据类型是没有静态方法的，但是基本数据类型的包装类却有**

byte：8位 一个字节 初始为0，包装类型 Byte初始值为Null

boolean：8位 一个字节

short:16位 两个字节

char：16位 两个字节  存储的Unicode码，不仅可以存储ASCII，也可以存汉字

 int：32位 四个字节

float:32位 四个字节 

 long：64位 八个字节 

double：64位 八个字节（浮点默认double）精度比float高

强制转换优先级高于+-*/



### 关键字 || 保留字  || 显示常量值

true，false，null不是关键字，也不是保留字，只是显示常量值。

const 和goto是java的保留字。

java中所有的关键字都是小写。

## 构造函数

假设A类有构造方法A(int i),在其他构造方法中可以通过this(i)进行 调用

只要没有定义任何构造函数，JVM都会为类生成一个默认构造函数

子类不能继承父类构造方法，只能调用父类的构造方法（通过super.构造函数）

构造函数不能被继承，构造方法只能被显式或隐式的调用。

super和this()都只能位于构造器的第一行，而且不能同时使用，这是因为会造成初始化两次，this()用于调用重载的构造器，super用于调用父类被子类重写的方法

**子类的构造方法总是先调用父类的构造方法，如果子类的构造方法没有明显指明使用父类的哪个构造方法，子类就调用父类不带参数的构造方法。父类如果没有无参的构造函数，那么子类需要在自己的构造函数中显示调用父类的构造函数（super（Obj obj））**

## 类

**USES-A：**依赖关系，A类会用到B类，这种关系具有偶然性，临时性。但B类的变化会影响A类。这种在代码中的体现为：A类方法中的参数包含了B类。

**关联关系：**A类会用到B类，这是一种强依赖关系，是长期的并非偶然。在代码中的表现为：A类的成员变量中含有B类。

**HAS-A：**聚合关系，拥有关系，是**关联关系**的一种特例，是整体和部分的关系。比如鸟群和鸟的关系是聚合关系，鸟群中每个部分都是鸟。

**IS-A：**表示继承。父类与子类，这个就不解释了。

类中声明的变量有默认初始值；方法中声明的变量没有默认初始值，必须在定义时初始化，否则在访问该变量时会出错。

## 内部类

匿名内部类不可以定义构造器

内部类的类名一般与文件名不同

1. 静态内部类：
  2. 静态内部类本身可以访问外部的静态资源，包括静态私有资源。但是不能访问非静态资源，可以不依赖外部类实例而实例化。
3. 成员内部类：
  4. 成员内部类本身可以访问外部的所有资源，但是自身不能定义静态资源，因为其实例化本身就还依赖着外部类。
5. 局部内部类：
  6. 局部内部类就像一个局部方法，不能被访问修饰符修饰，也不能被static修饰。
  7. 局部内部类只能访问所在代码块或者方法中被定义为final的局部变量。
8. 匿名内部类：
  9. 没有类名的内部类，不能使用class，extends和implements，没有构造方法。
  10. 多用于GUI中的事件处理。
  11. 不能定义静态资源
  12. 只能创建一个匿名内部类实例。
  13. 一个匿名内部类一定是在new后面的，这个匿名类必须继承一个父类或者实现一个接口。
14. 匿名内部类是局部内部类的特殊形式，所以局部内部类的所有限制对匿名内部类也有效。

### 成员内部类访问规则

成员内部类不是静态的：外部类名.内部类名 对象名 = new 外部类名().new 内部类名();
成员内部类是静态的：外部类名.内部类名 对象名 = new 外部类名.内部类名();

![img](https://uploadfiles.nowcoder.com/images/20190412/242025553_1555060831843_BA9669C5826A238ACEC0BD86755FA5DB)

---

编译具有内部类的Cow和CowLeg，会在文件所在路径生成两个class文件，一个Cow.class，一个Cow$CowLeg.class

如果外部类成员变量、内部类成员变量与内部类里方法的局部变量同名，可以使用this，外部类类名.this作为限定来区分。

非静态内部类对象和外部类对象关系：

非静态内部类对象必须寄生在外部类对象中，而外部类对象不必一定有非静态内部类寄生其中。也就是说，有非静态内部类，肯定有它所寄生的外部类；而外部类对象存在时，外部类对象不一定寄生了非静态内部类对象。



不允许在非静态内部类中定义静态内部成员。



接口内部类只能是静态内部类。



在外部类以外使用非静态内部类：

```java
Out.In in = new Out().new In("测试在外部类以外使用非静态内部类");
```

或：

```java
//使用OutterClass.InnerClass形式定义内部类变量
Out.In in;
//创建外部类实例，非静态内部类实例将寄生在该实例中
Out out = new Out();
//通过外部类实例和new 来调用内部类构造器创建非静态内部类实例；
in = out.new In("测试在外部类以外使用非静态内部类")
```





## 初始化顺序

1. 父类的静态代码块
2. 子类的静态代码块
3. 父类的普通代码块
4. 父类的构造方法
5. 子类的普通代码块
6. 子类的构造方法

## 基本数据类型

byte范围：-128 ~ 127 

不能将null赋值给基本数据类型，可以赋值给引用变量

## 遍历

Iterator：调用remove即可安全删除对象，可以避免ConcurrentModifiedException

## 数组

数组是一个连续的存储结构。

JAVA中只有一维数组，二维数组是一维数组对一维数组的引用。

二维数组的创建：

```java
float f[][] = new float[6][6];
float []f[] = new float[6][6];
float [][]f = new float[6][6];
float [][]f = new float[6][];
```

数组名和方括号位置可以随意设置，但是必须定义二维数组中一维数组的个数，也就是必须给等号后的第一个方括号赋值。

复制的效率System.arraycopy>clone>Arrays.copyOf>for循环。

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200823115956741.png" alt="image-20200823115956741" style="zoom: 67%;" />

object对象的equals方法，比较两个数组对象的地址，返回的肯定是false；相当于equals；而使用Arrays.equals方法比较两个数组时，比较的是内容。

## 运算符

&：不具有短路功能，会检测每一个条件

&& :具有短路功能，如果第一个条件为false，就不会检测后边的条件

| ：不具有短路功能，会检测每一个条件，有true就返回true

|| ：具有短路功能，如果第一个为true，直接返回true，不会检测后边的条件

^:两个数转为二进制，从高位开始比较，相同为0，不同为1

​		例：17和5    17:0001 0001      

​                              5:0000 0101

​                        17^5:0001 0100        =  20

<<表示左移位

\>>表示带符号右移位

\>>>表示无符号右移

## 修饰符

### 访问权限修饰符

对外部类来说，只有public  和default，外部类放在，只有可见和不可见

对内部类来说，有public>protected>默认(包访问权限)>private，因为protected除了可以被同一包访问，还可以被包外的子类所访问，default不能修饰变量。

### 非访问权限修饰符

可见性：线程之间的可见性，一个线程修改的状态对另一个线程是可见的。

原子性：原子是世界上最小的单位，具有不可分割性

有序性：持有同一个对象锁的两个同步块只能串行执行。

1. static 创建类方法和类变量。方便在没有创建对象的情况下进行调用(方法/变量)。因此不依附于任何对象。不使用this关键字
2. final修饰类、方法、变量，修饰的类不能被继承，修饰的方法不能被继承类重新定义（不能被重写），可以被继承和重载，修饰的变量为常量。不能修饰abstract。当修饰的成员变量为基本数据类型时，赋值后无法改变；**当修饰的成员变量为引用数据类型时，赋值后地址无法改变，但内容可以改变。** 赋值的三种方式：1、声明时赋值2、构造器赋值3、初始代码块赋值。类中的 final 方法可以被子类继承，但是不能被子类修改。声明 final 方法的主要目的是防止该方法的内容被修改。String、StringBuffer、StringBuilder都是final修饰的。
3. abstract 创建抽象类和方法，不能修饰变量；不能声明为final和static；任何继承抽象类的子类必须实现抽象类的所有方法，除非该子类也是抽象类。如果一个类包含若干抽象方法，该类必须声明为抽象类。抽象类可以不包含抽象方法。抽象方法以分号结尾：public abstract sample();  **修饰的方法不能实现**
4. synchronized 多线程同步，在运行前，对所属的方法加锁，防止其他线程访问。运行结束后解锁。保证可见性、原子性、有序性。
5. volatile 修饰的成员变量在每次被线程访问时，强制每次从共享内存中读取该成员变量的值。当成员变量值发生变化时，会强制写回到共享内存中。保证可见性、有序性，不保证原子性。
6. transient：将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会被序列化。

## 字符串

如果int x=20, y=5，则语句System.out.println(x+y +""+(x+y)+y);  的输出结果是（）

小括号优先级最高，先执行（x+y），为25，然后顺序执行，字符串前的按照原格式加，为25，然后拼接""，字符串后的按字符串相加，为25 + “” + 25 + 5 = 25255

25255

## 封装多态继承&&重载

### 继承

子类重写继承方法时，不能降低访问权限，如果父类是protected，子类只能是public或者protected，不能是friendly和orivate。关于抛出的异常：父类抛出异常的范围要比子类抛出异常范围要大，也就是子类抛出的异常必须是父类异常的自异常，或者子类抛出的异常要比父类抛出的异常要少。

子类将继承父类的所有的数据域和方法

Java只支持单继承，实现多重继承三种方式：（1）直接实现多个接口 （2）扩展(extends)一个类然后实现一个或多个接口 （3）通过内部类去继承其他类

子类被创建时，首先会在内存中创建一个父类对象，然后在父类对象外部放上子类独有的属性，两者合起来形成一个子类的对象。所以所谓的继承使子类拥有父类所有的属性和方法可以这么理解，子类对象确实拥有父类对象中所有的属性和方法，但父类对象的私有属性和方法，子类无法访问，只是拥有，但是不能使用。所以子类对象是绝对大于父类对象的。所谓的子类对象只继承父类非私有的属性及方法的说法是错误的。可以继承，但是无法访问。

### 多态

多态是指针对一消息，不同的对象可以以适合自身的方式加以相应。

**继承是多态的根源，重写是多态的表现形式**

## 自动装箱自动拆箱

##### 自动装箱

由于**JAVA 5** 之前定义Integer对象只能通过 

```java
Integer i = new  Integer(10)
```

方式进行，但在JAVA 5时，可以通过 

```java
Integer i = 5;
```

进行直接定义，这个将int基本类型的数据转换为Integer对象的过程就叫装箱

##### 自动拆箱

```java
Integer i = 5;
int k = i;
```

Line 1 中定义了一个Integer对象，实现了自动装箱

Line 2  由于i是一个Integer对象，但是k是一个int基本数据类型，赋值的时候将Integer对象的值拆出来，得到数字，然后赋值给k

##### 实现



###### Integer

装箱时自动调用Integer.valueOf(int)方法

拆箱时自动调用Integer.intValue方法

如果两个Integer对象值介于[-128,127]之间，那么这两个Integer对象使用 == 比较是相等的，因为返回的是已经存在的对象的引用。

如果不介于[-128,127]之间，那么这两个值相等的对象是新创建出来的。两个新创建的对象使用 == 比较 返回结果肯定是false。

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200720220457026.png" alt="image-20200720220457026" style="zoom: 50%;" />

**注：Integer与Short、Byte、Character、Long相同**

###### Double

如果是两个Double包装类型，使用 ==  比较，返回值都是false，因为Double是无限的。

**注：Double，Float包装相同，返回都是**

######  Boolean

两个Boolean包装类型，只要值相同，那么返回都是true；

![image-20200720223058937](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200720223058937.png)

其中TRUE和FALSE都是static final对象。相同对象进行比较，返回值为true

**注：如果 == 两侧存在表达式，那么比较的是数值，因为会触发自动拆箱过程**

```java
public class Main {
    public static void main(String[] args) {
         
        Integer a = 1;
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;
        Long h = 2L;
         
        System.out.println(c==d);//true
        System.out.println(e==f);//false
        System.out.println(c==(a+b));//true
        System.out.println(c.equals(a+b));//true
        System.out.println(g==(a+b));//true
        System.out.println(g.equals(a+b));//false
        System.out.println(g.equals(a+h));//true
    }
}
```

Line 18 因为a、b都为Integer，但g为Long包装类型，值不相等；

Line 19 a+h 先触发自动拆箱，int + long = long，再触发自动装箱，equals都为long，因此返回为true。

---

## 集合

LinkedList 实现了List接口

AbstractSet实现了Set接口

HashSet继承AbstractSet类，同时也实现了Set。

collection类型的集合（ArrayList和LinkedList）只能装入对象类型的数据

线程安全：

- Vector  
- Stack 
- enumeration（当一个Java类第一次被真正使用到的时候静态资源被初始化，Java类的加载和初始化过程都是线程安全的，所以创建一个enumeration类型是线程安全的）
- HashTable 
- StringBuffer  
- ConcurrentHashMap

线程不安全：

- ArrayList ：
- LinkedList：
- HashMap：
- HashSet：
- TreeMap：
- TreeSet：
- StringBulider：

java.util.Collection是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口的意义是为各种具体的集合提供最大化的同意操作方式。

java.util.Collections是一个包装类，它包含有各种有关集合操作的静态多态方法。因为构造器私有，所以不能实例化。它包含有各种有关集合操作的静态方法。

## ThreadLocalMap

ThreadLocalMap使用**开放定址法**处理散列冲突，HashMap使用分离链表法。因为ThreadLocalMap散列值分布十分均匀，很少出现冲突，此外ThreadLocalMap经常需要清除无用对象，使用纯数组更方便。

### Arraylist

ArrayList list = new ArrayList(20); 不会进行扩容，输入多少初始化时就会是多少。

#### 成员变量

```java
    private static final int DEFAULT_CAPACITY = 10;
    private static final Object[] EMPTY_ELEMENTDATA = {};
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    transient Object[] elementData; // non-private to simplify nested class access
    private int size;
```

释义：

**DEFAULT_CAPACITY**:默认容量，即实例化对象，但不指定容量时的默认值。

**EMPTY_ELEMENTDATA**：空的数组实例，以便需要时调用，例如**clear方法**。有参构造时（初始长度为0或传入的集合为非null的空集合），将**EMPTY_ELEMENTDATA**赋值给*elementData*。

**DEFAULTCAPACITY_EMPTY_ELEMENTDATA**：空数组实例，当使用默认构造器实例化时返回该数组。将该数组赋值给*elementData*。

**elementData**：ArrayList存放的数据就存在这里。

**size**：数组的长度

#### 构造方法

```java
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

**public ArrayList(int initialCapacity)**:有参构造器，传入初始值数组大小。大于0时，创建一个新的objec数组赋值给*elementData*。当等于0时，返回上文提到的**EMPTY_ELEMENTDATA**。当小于0时，抛出异常。

**public ArrayList()**：构造一个初始容量为10的空列表，返回的是**DEFAULTCAPACITY_EMPTY_ELEMENTDATA**空数组。

**public ArrayList(Collection<? extends E> c)**：将集合c的数据复制进当前对象。

#### 方法

**add(Object obj)**:将obj添加进list中

**add(Integer i, Object obj)**:向list的第i个位置添加obj，i之后的元素后移

**remove(Integer i)**:移除下标为i的元素，i之后的元素左移。

**calculateCapacity&&ensureCapacityInternal&&ensureExplicitCapacity**:三个一起看，因为ensureCapacityInternal调用了calculateCapacity。

首先判断该实例对象的elementData数组是不是等于默认生成的空数组，如果是，判断当前容量和10哪个大，哪个大就返回哪一个（这样是为了排除自定义实例化的对象数组比默认值10大的情况）。如果不是，就返回当前容量。返回的容量就是扩容后的最小容量。如果当前数组长度比最小还要小，就进行扩容，执行grow方法。

```java
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
 private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```

**ensureExplicitCapacity**：其中有一个快速报错机制，也就是modCount++；

**grow**:分为三种情况

1、使用默认构造器，第一次添加数据，minCapacity为默认10。

2、指定默认容量为0，minCapacity为1，也就是每次扩容都会加1，直到为10后才会以1.5倍扩容。

3、当扩容大于ArrayList定义数组长度最大值，那么就会抛出异常。

```java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

![img](https://uploadfiles.nowcoder.com/images/20160729/6316247_1469798881617_02E5E9CC442F264C8E3D2C934651A39F)

<img src="http://uploadfiles.nowcoder.com/images/20150904/458054_1441352361202_2B9A4774B9C5C489A9E9564854FFCD6C" alt="img" style="zoom: 50%;" />

<img src="http://uploadfiles.nowcoder.com/images/20150928/458054_1443399626006_AA40C8919CE65157D5F251E7622A75FB" alt="img" style="zoom:50%;" />

![image-20200806233120020](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200806233120020.png)

```java
public static void main(String[] args) {
		List<A> a;
		List list;
		list = a;   //A对，因为List就是List<?>，代表最大的范围，A只是其中的一个点，肯定被包含在内
		List<B> b;
		a = b;      //B错，点之间不能相互赋值
		List<?> qm;
		List<Object> o;
		qm = o;     //C对，List<?>代表最大的范围，List<Object>只是一个点，肯定被包含在内
		List<D> d;
		List<? extends B> downB;
		downB = d;  //D对，List<? extends B>代表小于等于B的范围，List<D>是一个点，在其中
		List<?extends A> downA;
		a = downA;  //E错，范围不能赋值给点
		a = o;      //F错，List<Object>只是一个点
		downA = downB;  //G对，小于等于A的范围包含小于等于B的范围，因为B本来就比A小，B时A的子类嘛
	}
```

### HashMap HashTable

#### HashMap

public class HashMap extends AbstractMap implements Map

缺省情况下是非同步的。

null可以为键，但只能有一个；可以有一个或多个值为null，若通过get返回的值为空，有两种情况：1、没有该键2、该键对应的值为null，因此不能通过判断get是否为空来确认HashMap中是否有该键，而应用containsKey来判断。

遍历上使用Iterator

哈希值的使用：重新计算hash值

扩容：初始值为16，一定是2的指数。

#### HashTable

public class Hashtable extends Dictionary implements Map

方法同步

key value都不允许为空

遍历上使用了Iterator 和Enumeration

哈希值的使用：直接使用对象的hashCode

扩容：hash数组默认大小为11，增加的方式是old * 2 + 1

---

## 类加载器

#### 1、类的加载

将编译器编译的class文件读取到JVM中，将其放在运行时数据区的方法区中，然后在堆中创建一个java.lang.Class对象，用来封装类在方法区内的数据结构

1）、什么时候启动类加载器？

可以在预加载时启动。若预加载时遇到.class文件缺失、错误，类加载器需要在首次使用时报错，如果未被使用，就不会报错。（Web中，如果有错误，不会在启动/编译时报错，而是点某个功能报错）

2）、从何处加载class文件？

本地磁盘、网上加载的class文件、数据库、压缩文件（zip，jar）、从其他文件中生成。

#### 2、类加载过程

类从加载到JVM到卸载出JVM，一共七步：加载、验证、准备、解析、初始化、使用、卸载。

因此类加载过程为：验证、准备、解析、初始化、使用

**加载**

1. 根据全类名获取其二进制字节流
2. 将字节流所代表的静态存储结构转为方法区运行时的数据结构
3. 在堆中生成代表这个类的class对象。

**验证**

验证文件格式、元数据、字节码、符号引用

**准备**

为类变量分配内存并设置初始值

**解析**

将常量池中的符号引用转为直接引用。

**初始化**

主要为类的静态变量赋予正确的初始值。

#### 3、三种类加载器

Bootstrap ClassLoader、Extension ClassLoader、App ClassLoader（System App Class Loader）

#### 4、双亲委派机制

如果子类收到类加载请求，不会自己加载，而是会抛给父类加载，如果父类还有父类，那么继续向上抛，直到最顶层的类加载器，如果最顶层类加载器可以完成，那么就完成；如果完成不了，就子类自己解决。

#### 5、其他

类加载器保证线程安全

装载一个不存在的类，由于使用的是双亲委派，因此会报错：java.lang.SecurityException

自定义类加载器实现继承ClassLoader后重写了findClass方法加载执行路径上的class

一个类，由不同的类加载器实例加载的话，会在方法区产生两个不同的类，彼此不可见，并且在堆中生成不同Class实例。

##  数据库

1、Statement对象用于执行不带参数的简单SQL语句。 

2、Prepared Statement 对象用于执行预编译SQL语句。 

3、Callable Statement对象用于执行对存储过程的调用。

原子性：事务是一组不可分割的操作单元，这组单元要么同时成功，要么同时失败（事务管理系统实现）

一致性：事务前后的数据完整性保持一致（完整性子系统执行测试任务）

隔离性：多个用户的事务之间不要相互影响，要相互隔离（并发控制子系统实现）

持久性：一个事务一旦提交，那么它对数据库产生的影响就是永久的不可逆的，如果后边再回滚，都不会影响已提交的事务（恢复管理子系统实现）

ResultSet跟普通的数组不同，索引从1开始而不是从0开始

## 泛型

**泛型仅仅是java的语法糖，它不会影响java虚拟机生成的汇编代码，在编译阶段，虚拟机就会把泛型的类型擦除，还原成没有泛型的代码，顶多编译速度稍微慢一些，执行速度是完全没有什么区别的.**

**1，类型安全。** 泛型的主要目标是提高 Java 程序的类型安全。通过知道使用泛型定义的变量的类型限制，编译器可以在一个高得多的程度上验证类型假设。没有泛型，这些假设就只存在于程序员的头脑中（或者如果幸运的话，还存在于代码注释中）。

**2，消除强制类型转换。** 泛型的一个附带好处是，消除源代码中的许多强制类型转换。这使得代码更加可读，并且减少了出错机会。

**3，潜在的性能收益。** 泛型为较大的优化带来可能。在泛型的初始实现中，编译器将强制类型转换（没有泛型的话，程序员会指定这些强制类型转换）插入生成的字节码中。但是更多类型信息可用于编译器这一事实，为未来版本的 JVM 的优化带来可能。由于泛型的实现方式，支持泛型（几乎）不需要 JVM 或类文件更改。所有工作都在编译器中完成，编译器生成类似于没有泛型（和强制类型转换）时所写的代码，只是更能确保类型安全而已。**PS：**

## 接口

接口包含方法声明和变量声明，接口默认方法是abstract public（只能是public），接口只写函数声明符合语法规则。变量默认是用public final static 修饰，意思是它是静态常量，常量不管在接口中还是类中不许在声明时初始化。

接口中只有变量定义，没有变量声明。

接口支持多继承。

抽象类中的方法是可以有方法体的。JDK1.8之后，接口中的方法也可以有方法体，用default关键字修饰方法。

类可以实现多个接口，接口可以继承（或扩展）多个接口

---

非抽象类实现接口，两同两小一大

方法名相同，参数类型相同

子类返回类型小于等于父类方法返回类型，
子类抛出异常小于等于父类方法抛出异常，
子类访问权限大于等于父类方法访问权限。

---

JDK1.8之前，接口不能有静态方法，抽象类中因为有普通方法，因此也可以有静态方法。

JDK1.8及以后，抽象类可以有静态方法，同时接口可以定义静态方法。

JDK1.7中，接口只包含抽象方法，使用public abstract 修饰

```java
public interface Demo{
    public abstract void method();
}
```

JDK 1.8，接口新加了默认方法和静态方法： 

​	默认方法：使用default修饰。在接口的实现类中，可以直接调用或重写。

​	静态方法：使用static修饰，通过接口直接调用

JDK1.9中，接口新增了private方法，使用private修饰，私有方法供接口内的默认方法和静态方法调用。

### 接口和抽象类的区别

接口不能有构造函数、普通成员变量，抽象类可以有构造函数和普通成员变量；

一个类可以实现多个接口，但只能继承一个抽象类

## 文件读写

能够读写文件的是数据流（OutputStream和InputStream）

flush（）函数强制将缓冲区中的字符流、字节流等输出，目的是如果输出流输出到缓冲区完成后，缓冲区并没有填满，那么缓冲区将会一直等待被填满。所以在关闭输出流之前要调用flush（）。OutputStream有该方法。

Character流与Byte流的区别是 
A) 每次读入的字节数不同 B) 前者带有缓冲，后者没有
C) 前者是字符读写，后者是字节读写 D) 二者没有区别，可以互换使用

stream结尾的都是字节流，reader和writer结尾的都是字符流，区别：读写的时候一个按照字节读写，一个按照字符读写。若需要按行处理，比如比较特定字符，处理某一行数据使用字符流。而只读文件，和文件内容无关的，一般选择字节流。

**字节流（继承自InputStream OutputStream）**

InputStream：是字节输入流的抽象基类，InputStream作为基类，给它的基类定义了几个通用的函数。
FileInputStream :用来操作文件输入流，它除了可以使用基类定义的函数外，还实现了基类的read()函数
BufferedInputStream(BufferedInputStream不是InputStream的直接实现类，是FileInputStream的子类) 

**字符流(继承自InputStreamReader  OutputStreamWriter)**

字符输入流：

Reader

InputStreamReader (byte->char 桥梁）

BufferedReader (常用)

FileReader





字符输出流：

Writer:字符输出流的抽象基类

OutputStreamWriter (char->byte 桥梁）：可以直接往流中写字符串数据，会根据字符编码方式把字符数据编程字节数据写给输出流

BufferedWriter：利用缓冲区提高写的效率。

FileWriter：与OutputStreamWriter类似

 

按照流是否直接与特定的地方（如磁盘、内存、设备等）相连，分为节点流和处理流两类。节点流的前缀都是名词，如File，String，Char等；处理流的前缀都是动词，如input，print，bufferd等等

- 节点流：可以从或向一个特定的地方（节点）读写数据。如FileReader.
- 处理流：是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写。如BufferedReader.处理流的构造方法总是要带一个其他的流对象做参数。一个流对象经过其他流的多次包装，称为流的链接。

**JAVA常用的节点流：**

- 文 件 FileInputStream FileOutputStrean FileReader FileWriter 文件进行处理的节点流。
- 字符串 StringReader StringWriter 对字符串进行处理的节点流。
- 数 组 ByteArrayInputStream ByteArrayOutputStreamCharArrayReader CharArrayWriter 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）。
- 管 道 PipedInputStream PipedOutputStream PipedReaderPipedWriter对管道进行处理的节点流。

**常用处理流（关闭处理流使用关闭里面的节点流）**

- 缓冲流：BufferedInputStrean BufferedOutputStream BufferedReader BufferedWriter 增加缓冲功能，避免频繁读写硬盘。

- 转换流：InputStreamReader OutputStreamReader 实现字节流和字符流之间的转换。
- 数据流 DataInputStream DataOutputStream 等-提供将基础数据类型写入到文件中，或者读取出来.

**流的关闭顺序**

1. 一般情况下是：先打开的后关闭，后打开的先关闭
2. 另一种情况：看依赖关系，如果流a依赖流b，应该先关闭流a，再关闭流b。例如，处理流a依赖节点流b，应该先关闭处理流a，再关闭节点流b
3. 可以只关闭处理流，不用关闭节点流。处理流关闭的时候，会调用其处理的节点流的关闭方法。

## 多线程 

### 创建线程对象两种方式：

1.继承Thread类，重写run方法；

2.实现Runnable接口，实现run方法 

---

volatile：是线程同步的轻量级实现，性能比synchronized好。但只能修饰变量，多线程不会发生阻塞，保证数据可见性、有序性，不保证原子性，用于解决变量在多个线程之前的可见性

synchronized：可以修饰方法和代码块，可能发生阻塞，保证数据可见性、有序性和原子性，解决多线程之间访问资源的同步性。

yield：高风亮节，我不上，跟我同级别的先上。

**同步器是一些使线程能够等待另一个线程的对象，允许它们协调动作。最常用的同步器是CountDownLatch和Semaphore，不常用的是Barrier 和Exchanger**

启动新线程使用 start，而run相当于调用方法，不是启动新线程，run方法是线程执行的入口。

**非静态锁的是实力的对象：多个非静态方法同时加了synchronized，互不影响**

**静态锁的是类：多个静态方法加了synchronized，如果其中一个方法被锁了，其他的静态方法是拿不到锁的，没法执行，只有等该方法执行结束，其它静态方法才能拿到锁**

### 线程局部存储TLS

解决多线程中的对同一变量的访问冲突的一种技术

TLS会为每一个线程维护一个和该线程绑定的变量的副本

Java平台的java.lang.ThreadLocal是TLS技术的一种实现

同一全局变量或者静态变量每个线程访问的是同一变量，多个线程同时访存同一全局变量或者静态变量时会导致冲突，尤其是多个线程同时需要修改这一变量时，通过TLS机制，为每一个使用该全局变量的线程都提供一个变量值的副本，每一个线程均可以独立地改变自己的副本，而不会和其它线程的副本冲突。

### 线程状态

![image-20200812215538817](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200812215538817.png)

![image-20200812215525256](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200812215525256.png)

![7](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200825161212692.png)

### sleep 、wait 区别

1. sleep 不会释放锁资源，wait会
2. wait只能通过notify唤醒，注意：只是唤醒，还需要给他分配资源才能执行
3. sleep可以在任何地方使用，而wait只能在同步方法或者同步块中使用

yield只是给了其他同等级线程执行的机会，很有可能在某个方法yield后，CPU又执行了这个方法。

### 类、接口和方法

Semaphore：类，控制某个资源可被同时访问的个数;

ReentrantLock：类，具有与使用synchronized方法和语句所访问的隐式监视器锁相同的一些基本行为和语义，但功能更强大。

Future：接口，异步计算的结果

CountDownLatch：类，在一个线程中等待多个线程完成任务的类。

CopyOnWriteArrayList：适用于写少读多的并发场景

ReadWriteLock：读写锁，要求写与写互斥，读与写互斥，读与读之间可以并发执行，在读多写少的情况下可以提高效率。

ConcurrentHashMap：同步的HashMap，读写都加锁。

### 多线程与集合

hashmap在单线程中使用大大提高效率，在多线程情况下使用HashTable保证安全。HashTable使用synchronized关键字实现安全机制。但synchronized是对整个hash表进行锁定，即让线程独享整张hash表，安全同时造成浪费。concurrentHashMap采用分段式加锁机制确保安全。而JDK1.8后，concurrentHashMap取消了segment分段锁。采用cas和synchronized保证并发安全，数据结构和hashmap1.8类似，数组+链表或红黑二叉树。

### 线程的通知与唤醒

Object.wait/notify/notifyAll

Condition.await/signal/signalAll:出现于JDK1.5。用来替代传统的Objet的wait（），notify（）实现线程间的协作，相比使用Object的wait()，notify(),使用Condition的await（）和signal（）这种方式实现线程间协作更加安全和高校。

## 异常

java异常都继承自类Throwable，Throwable子类有Error 和 Exception，其中Exception分为编译时异常和运行时异常。编译时异常是未雨绸缪，防范，需要显示处理。运行时异常是程序员问题造成，并不强制进行显示处理。

throws是写在方法上，申明要抛出的异常。

throw是抛出异常

finally：表示总是执行。但是：1、在try中调用 System.exit(0)强制退出程序，finally不执行。2、在进入try前，出现了异常，finally不执行。 finally会撤销之前的return 语句，继续执行最后finally中的代码。

finalize：是在java.lang.Object定义的。每一个对象都有这么一个方法。在gc启动，被回收时调用。一般不需要事先finalize，需要实现finalize，如对象被回收时释放一些资源。使用finalize还要注意一件事儿，调用super.finalize()，一个对象的finalize()只会被调用一次。而finalize被调用不意为对象被回收，有可能调用finalize后，对象不需要被回收了，当真正需要回收时，因为前边调用过一次，不会调用finalize()，容易产生问题，不推荐使用finalize()。它和析构函数不一样。

函数调用是入栈出栈，栈在寄存器之下速度最快，空间小。而异常是存在堆中，异常的内存开销大。

当程序执行到try{}语句中的return方法时，它会干这么一件事，将要返回的结果存储到一个临时栈中，然后程序不会立即返回，而是去执行finally{}中的程序， 在执行`a = 2`时，程序仅仅是覆盖了a的值，但不会去更新临时栈中的那个要返回的值 。执行完之后，就会通知主程序“finally的程序执行完毕，可以请求返回了”，这时，就会将临时栈中的值取出来返回。这下应该清楚了，要返回的值是保存至临时栈中的。

finally块中的return语句会覆盖try块中的return返回

## 反射

反射涉及到的Class类位于java.lang包下

通过反射可以动态的实现一个接口，形成一个新的类，并可以用这个类创建对象，调用对象方法

通过反射，可以突破Java语言提供的对象成员、类成员的保护机制，访问一般方式不能访问的成员

Java的反射机制会给内存带来额外的开销。例如对永生堆的要求比不通过反射要求的更多

选项反射的本质就是从字节码中查找，动态获取类的整容结构，包括属性，构造器，动态调用对象的方法，而不是修剪类



## 垃圾回收

两个基本的java垃圾回收算法：复制算法和标记清理算法

| 收集器            | 新生代                     | 老年代               |
| ----------------- | -------------------------- | -------------------- |
| Serial New        | 针对新生代，复制           | -                    |
| Parallel New      | 新生代复制                 | 老年代标记整理       |
| Parallel Scavenge | 针对新生代，复制收集       | -                    |
| Serial Old        | 新生代复制                 | 老年代标记整理       |
| Parallel Old      | -                          | 针对老年代，标记整理 |
| CMS               | 基于标记清理               |                      |
| G1                | 整体标记整理，局部采用复制 |                      |

新生代：复制算法

老年代：标记整理算法

方法调用时，会创建栈帧在栈中，调用完是程序自动出栈释放，而不是gc释放

## JDBC

java连接数据库JDBC使用了桥接模式。

## JVM

相互引用不会导致无法回收。

**Java的跨平台特性是因为JVM的存在， 它可以执行.class字节码文件，而不是.java源代码**

1、jps：查看本机java进程信息。

2、jstack：打印线程的**栈**信息，制作线程dump文件。

3、jmap：打印内存映射，制作**堆**dump文件

4、jstat：性能监控工具

5、jhat：内存分析工具

6、jcosole：简易的可视化控制台

7、jvisualvm：功能强大的控制台

<img src="https://uploadfiles.nowcoder.com/images/20190314/242025553_1552556718666_11CD8DF4C9693369E94F5F84238EBBC6" alt="img" style="zoom: 200%;" />

Xms 起始内存

Xmx 最大内存

Xmn 新生代内存

Xss 栈大小。 就是创建线程后，分配给每一个线程的内存大小

-XX:NewRatio=n:设置年轻代和年老代的比值。如:为3，表示年轻代与年老代比值为1：3，年轻代占整个年轻代年老代和的1/4

-XX:SurvivorRatio=n:年轻代中Eden区与两个Survivor区的比值。注意Survivor区有两个。如：3，表示Eden：Survivor=3：2，一个Survivor区占整个年轻代的1/5

-XX:MaxPermSize=n:设置持久代大小

收集器设置
-XX:+UseSerialGC:设置串行收集器
-XX:+UseParallelGC:设置并行收集器
-XX:+UseParalledlOldGC:设置并行年老代收集器
-XX:+UseConcMarkSweepGC:设置并发收集器
垃圾回收统计信息
-XX:+PrintGC
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps
-Xloggc:filename
并行收集器设置
-XX:ParallelGCThreads=n:设置并行收集器收集时使用的CPU数。并行收集线程数。
-XX:MaxGCPauseMillis=n:设置并行收集最大暂停时间
-XX:GCTimeRatio=n:设置垃圾回收时间占程序运行时间的百分比。公式为1/(1+n)
并发收集器设置
-XX:+CMSIncrementalMode:设置为增量模式。适用于单CPU情况。
-XX:ParallelGCThreads=n:设置并发收集器年轻代收集方式为并行收集时，使用的CPU数。并行收集线程数。



off-heap叫做堆外内存，将你的对象从堆中脱离出来序列化，然后存储在一大块内存中，这就像它存储到磁盘上一样，但它仍然在RAM中。对象在这种状态下不能直接使用，它们必须首先反序列化，也不受垃圾收集。序列化和反序列化将会影响部分性能（所以可以考虑使用FST-serialization）使用堆外内存能够降低GC导致的暂停。堆外内存不受垃圾收集器管理，也不属于老年代，新生代。

###  内存溢出

除了程序计数器外，其余运行区域都可能发生内存溢出

#### 报错

java.lang.OutOfMemoryError: PermGen space （Permanent Generation space）增加-XX:MaxPermSize这个参数的值的话，这个问题通常会得到解决。

java.lang.OutOfMemoryError: Requested array size exceeds VM limit当你正准备创建一个超过虚拟机允许的大小的数组时，这条错误将会出现

java.lang.OutOfMemoryError: Java heap space 一般情况下解决这个问题最快的方法就是通过-Xmx参数来增加堆的大小

java.lang.OutOfMemoryError: nativeGetNewTLA 当虚拟机不能分配新的线程本地空间时错误信息，此错误是线程申请一个新的TLA时产生的，一般只会发生在jRockit虚拟机。

---

## 其他

System是java.lang中的一个类，out是System内的一个成员变量，这个变量是一个java.io.PrintStream类的对象，println呢就是一个方法了。 

字符界面接收用户输入，导的包为java.io

String 没有 encode和decode方法。

将GBK->UTF-8:bytep[] src,dst;   **dst = new String(src,"GBK").getBytes*("UTF-8");**

static没有this

### 引用传递和值传递

1 基本类型和基本类型变量被当作参数传递给方法时，是值传递。

在方法实体中，无法给原变量重新赋值，也无法改变它的值。

2 对象和引用型变量被当作参数传递给方法时，是引用传递。

在方法实体中，无法给原变量重新赋值，但是可以改变它所指向对象的属性。

结论：1.值传递不可以改变原变量的内容和地址；

​      2.引用传递不可以改变原变量的地址，但可以改变原变量的内容；

### 导包

导包只能导到当前层，不能导入包里面包中的类。

---

转载别人的一篇博客

A、在方法中，修改一个基础类型的参数永远不会影响原始参数值。
B、在方法中，改变一个对象参数的引用永远不会影响到原始引用。然而，它会在堆中创建了一个全新的对象。（译者注：指的是包装类和immutable对象）

C、在方法中，修改一个对象的属性会影响原始对象参数。

D、在方法中，修改集合和Maps会影响原始集合参数。

## Java函数

### Math函数：

floor: 求小于参数的最大整数。返回double类型-----n. 地板，地面

​     例如：Math.floor(-4.2) = -5.0

\-----------------------------------------------------------

ceil:  求大于参数的最小整数。返回double类型-----vt. 装天花板；

​     例如：Math.ceil(5.6) = 6.0

If the argument value is less than zero but greater than -1.0, then the result is negative zero

如果参数小于零，但是大于-1，返回值为-0

注：If the argument is NaN or an infinity or positive zero or negative zero, then the result is the same as the argument

如果参数是NaN或无穷或正零或负零，返回值与参数相同。

\-----------------------------------------------------------

round: 对小数进行四舍五入后的结果。返回int类型

​     例如：Math.round(-4.6) = -5

Math.cos为计算弧度的余弦值，Math.toRadians函数讲角度转换为弧度,toDegrees()是将弧度转换为角度

---

### String函数

String split 这个方法默认返回一个数组， * 如果没有找到分隔符， * 会把整个字符串当成一个长度为1的字符串数组 * 返回到结果

---

RMI采用TCP/IP协议

## Web

### webService:

Webservice是跨平台，跨语言的远程调用技术;它的通信机制实质就是xml数据交换;它采用了soap协议（简单对象协议）进行通信

### servlet

![image-20200806114153130](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200806114153130.png)HttpServlet容器响应Web客户请求流程如下：

1）Web客户向Servlet容器发出Http请求；

2）Servlet容器解析Web客户的Http请求；

3）Servlet容器创建一个HttpRequest对象，在这个对象中封装Http请求信息；

4）Servlet容器创建一个HttpResponse对象；

5）Servlet容器调用HttpServlet的service方法，这个方法中会根据request的Method来判断具体是执行doGet还是doPost，把HttpRequest和HttpResponse对象作为service方法的参数传给HttpServlet对象；

6）HttpServlet调用HttpRequest的有关方法，获取HTTP请求信息；

7）HttpServlet调用HttpResponse的有关方法，生成响应数据；

8）Servlet容器把HttpServlet的响应结果传给Web客户。

doGet() 或 doPost() 是创建HttpServlet时需要覆盖的方法.

---

实现会话跟踪技术：URL重写、隐藏表单域、Cookie、Session。

HttpServletResponse完成：设置http头标，设置cookie，设置返回数据类型，输出返回数据

读取路径信息是HttpServletRequest做的

GenericServlet类：抽象类，定义一个通用的、独立于底层协议的Servlet。

大多数Servlet通过从GenericServlet或HttpServlet类进行扩展来实现

ServletConfig接口定义了在Servlet初始化的过程中由Servlet容器传递给Servlet得配置信息对象

HttpServletRequest接口扩展ServletRequest接口，为HTTP Servlet提供HTTP请求信息

​		

---

### weblogic

weblogic中开发消息Bean时的persistent与non-persisten的差别：

- **persistent方式的MDB可以保证消息传递的可靠性**,也就是如果EJB容器出现问题而JMS服务器依然会将消息在此MDB可用的时候发送过来。
- **non－persistent方式的消息将被丢弃**。

## web容器

web容器：给处于其中的应用程序组件（JSP，SERVLET）提供一个环境，使 JSP,SERVLET直接更容器中的环境变量接**互，不必关注其它系统问题。主要有WEB服务器来实现。例如：TOMCAT,WEBLOGIC,WEBSPHERE等。该容器提供的接口严格遵守J2EE规范中的WEB APPLICATION 标准。我们把遵守以上标准的WEB服务器就叫做J2EE中的WEB容器。

EJB容器：Enterprise java bean 容器。更具有行业领域特色。他提供给运行在其中的组件EJB各种管理功能。只要满足J2EE规范的EJB放入该容器，马上就会被容器进行高效率的管理。并且可以通过现成的接口来获得系统级别的服务。例如邮件服务、事务管理。

JNDI：（Java Naming & Directory Interface）JAVA命名目录服务。主要提供的功能是：提供一个目录系，让其它各地的应用程序在其上面留下自己的索引，从而满足快速查找和定位分布式应用程序的功能。

JMS：（Java Message Service）JAVA消息服务。主要实现各个应用程序之间的通讯。包括点对点和广播。

JTA：（Java Transaction API）JAVA事务服务。提供各种分布式事务服务。应用程序只需调用其提供的接口即可。

JAF：（Java Action FrameWork）JAVA安全认证框架。提供一些安全控制方面的框架。让开发者通过各种部署和自定义实现自己的个性安全控制策略。

RMI/IIOP:（Remote Method Invocation /internet对象请求中介协议）他们主要用于通过远程调用服务。例如，远程有一台计算机上运行一个程序，它提供股票分析服务，我们可以在本地计算机上实现对其直接调用。当然这是要通过一定的规范才能在异构的系统之间进行通信。RMI是JAVA特有的。

## 正则表达式

\d   匹配一个数字字符。等价于 [0-9]。
\D   匹配一个非数字字符。等价于 [^0-9]。
\f    匹配一个换页符。等价于 \x0c 和 \cL。
\n   匹配一个换行符。等价于 \x0a 和 \cJ。
\r    匹配一个回车符。等价于 \x0d 和 \cM。
\s   匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。
\S   匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。
\t    匹配一个制表符。等价于 \x09 和 \cI。
\v   匹配一个垂直制表符。等价于 \x0b 和 \cK。
\w   匹配字母、数字、下划线。等价于'[A-Za-z0-9_]'。
\W  匹配非字母、数字、下划线。等价于 '[^A-Za-z0-9_]'。

## 编码

Java一律采用Unicode编码方式，每个字符无论中文还是英文字符都占用2个字节。

不同的编码之间是可以转换，通常流程如下：将字符串S以其自身编码方式分解为字节数组，再将字节数组以你想要输出的编码方式重新编码为字符串。

Java虚拟机中通常使用UTF-16的方式保存一个字符

ResourceBundle能够依据Local的不同，选择性的读取与Local对应后缀的properties文件，以达到国际化的目的。

