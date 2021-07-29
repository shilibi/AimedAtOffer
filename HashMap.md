## 一、基本信息

1. HashMap继承自AbstractMap（AbstractMap实现了Map接口），实现了Map，Clonable和Serializable接口。
2. HashMap是无序的，并且是线程不安全的，因此多环境推荐使用ConcurrentHashMap。
3. 允许使用null值作为键，又因为map键值唯一，因此null值作为键只能是唯一的。
4. HashMap扩容很耗时。
5. Hash表装填因子定义为： α = 表中填入的记录数/哈希表的长度。α越小，发生冲突概率越小，但是会浪费空间；α越大，发生冲突概率变大。
6. Hash表在第一次使用的时候初始化，并且必须重置大小。当分配空间时，长度总是2的n次方。
7. HashMap有四个构造方法：
   1. HashMap(int initialCapacity, float loadFactor):参数为初始化容量和加载因子
   2. HashMap(int initialCapacity)
   3. HashMap()：默认加载因子0.75，最大容量为16，因此默认容量是0.75*16 = 12。(JDK1.8 ->HashMap.java-Line472)
   4. HashMap(Map<? extends K, ? extends V> m)：可以直接放入一个map
8. HashMap除了7.4这种构造方法外，其余三种都是在第一次放入数据的时候进行初始化，而不是在创建的时候初始化，盲猜目的是为了尽可能减少内存<font color='red'>？</font>

## 二、HashMap构成原理

首先，思考下数组有什么优点？查询简单，缺点就是插入和删除麻烦。链表则相反，插入删除简单，而查询麻烦。有没有办法将这两个数据结构优点合二为一？

真有。这就是Entry。

HashMap是使用Entry数组存储Key-Value的基于数组和链表的。什么是Entry？就是一个二维表，这个表横坐标是数组，纵坐标是链表，和Hash表很相似。

Entry数组将链表和数组的优点合二为一，它只在链表（红色）中存放数据，数组中（黑色）存放索引。查询时先在散列，然后数组中进行查找，根据数组的位置在数组对应的链表中进行查询，由于链表较短，因此查询速度较快，理论上如果散列的足够平均，如果哈希表长度为n，那么查询速度将提高到纯链表的n倍。若是插入和删除，同样的需要先散列，确定所在数组的位置，然后进行相应的操作，又因为是在链表上进行的操作，因此速度也很快。

<font color='red'>注意：不仅仅链表中存有数据，数组中也有数据。待补充</font>

![image-20200525223046148](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200525223046148.png)

在JDK1.8中，HashMap还使用了红黑树来替代链表（只有当链表长度过长的时候才会替代）。

## 三、主要方法：

#### 1、put：

一般的put方法首先调用了putVal方法，其中对key值进行了哈希计算，将onlyIfAbsent值（若为true，不会修改已经存在的数据）设置为false，将evict值（若为false，hash表将处于创建模式中）设置为true。

```java
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

1. 判断该HashMap是否为空，如果是空的，就进行初始化hash表；
2. 判断散列后的数组结点是够为空，如果为空，就将值赋值给这个节点；
3. 如果首结点不为空，说明有冲突，查看冲突的链表中是否存在这个键，如果不存在，那么就进行插入：
   1. 插入的时候需要进行判断，判断这个是链表还是红黑树，若为红黑树，那么就执行putTreeVal方法；若为链表，生成新的节点，放在头结点后边，插入完成后判断链表长度是否到达转换为红黑树的阈值，若是，将链表转换为红黑树
4. 如果存在，那么就将newValue替换掉oldValue；
5. 最后查看容量是否到达了threshold（默认8）,如果达到了，就进行二倍扩容。

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    	//创建一个node数组和一个node结点，node结点实际上实现了Map
        Node<K,V>[] tab; Node<K,V> p; int n, i;
    	//若该table为空，也就是未初始化，那么调用resize方法，resize方法可以初始化或者扩大一倍表容量。将初始化的长度赋值给n。
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
	    //若tab[i]这个节点为空，那么就创建tab[i]这个节点，并将数据存在这个节点上
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            //当tab[i]这个节点不为空时
            Node<K,V> e; K k;
            //第一个node的hash值就是要加入的hash
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //若为红黑树
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //若为链表
                for (int binCount = 0; ; ++binCount) {
                    //找到最后一个节点
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //如果长度超过8就会转换为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //如果e不为空，就替换原有的value值
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
    	//如果容量大于阈值，那么就二倍扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

#### 2、扩容

扩容原理：由已知得，假定扩容前的size为k，那么扩容后的size就是2k，扩容时先创建一个size为2k的HashMap，然后将原来的kv值全部**复制**进去，因此是一个较耗资源的操作。

源码：



```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
	    //1、获取原表的容量
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
    	//2、若原表不为空，判断原容量是否一定到达最大值，若是，则提高扩容阈值到Integer的最大值
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            //3、若未达到最大值，判断将原容量扩容两倍后的数是否小于最大容量以及旧的容量是否大于默认的初始化容量，然后将扩容阈值提高成原来两倍，之所以判断是否大于等于初始容量，是因为需要确认是扩容，而不是初始化？？？
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
               		 newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            //进入这个if条件是使用的7.1和7.2的构造方法。带参的构造中initialCapacity（初始容量值）不管是输入几都会通过 “this.threshold = tableSizeFor(initialCapacity);”此方法计算出接近initialCapacity参数的2^n来作为初始化容量（初始化容量==oldThr）
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            //进入这个if说明进入了L8没有进入L9&&L14，但进入了L18（否则就会进入L21，而若进21，则newThr必然不是0）。
            //说明该map创建时使用的是有参构造器。
            float ft = (float)newCap * loadFactor;
            // 判断容量是否到达最大，若最大，则将阈值设置为Integer.MAX_VALUE;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
    	//如果旧表空，直接返回newTab
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {//复制数据到新的表中
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {//若该节点不为空，将值赋给e，并清空原数据
                    oldTab[j] = null;
                    if (e.next == null)//若e没有下一个结点，直接赋值给新表
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)//如果是红黑树，拆分然后赋值
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        //如果是链表且有多个节点，将原链表拆分成两个链表,并将两个链表的值赋给新表
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

#### 3、删除

判断是否为空表，如果是空表，则返回null；不是空表则判断元素对应桶第一个位置是不是目标元素，如果是那就将目标元素的next赋给tab[i],如果不是，那么就判断是不是红黑树，如果是，调用getTreeNode,如果是链表，遍历链表找到目标元素。遍历同时通过p记录链表的前指针，当进行移除的时候，只需要将p.next = node.next即可。



## 附录：

1、位运算符：

1. &运算：若a = 128(10000000),b = 129(10000001),则a&b = 128(10000000),即只有同位置的数<font color='red'>都为1</font>时，结果才是1

   用途:

   1. 清零。使之与一个全为零的数做与运算
   2. 取指定位：假设取1011 1011后四位，使之与00001111做与运算，可得00001011

2. | 运算：两个数至少有一个1，或运算后结果就是1。

3. ^异或运算：若两个数不一样，则结果为1； 0^1 = 1, 0^0 = 0;

4. ~取反运算：

5. << : 左移运算符，num << 1,相当于num乘以2

6. \>>:右移运算符，num >> 1,相当于num除以2

7. \>>>无符号右移，忽略符号位，空位都以0补齐

2、Integer最大值：A constant holding the maximum value an {@code int} can have, 2<sup>31</sup>-1.

​			     最小值：-2<sup>31</sup>

# HashMap面试必问的6个点，你知道几个？

## **一、HashMap的实现原理?**

此题可以组成如下连环炮来问

- 你看过HashMap源码嘛，知道原理嘛?
- 为什么用数组+链表？
- hash冲突你还知道哪些解决办法？
- 我用LinkedList代替数组结构可以么?
- 既然是可以的,为什么HashMap不用LinkedList,而选用数组?



### **1.你看过HashMap源码嘛，知道原理嘛?**



针对这个问题，嗯，当然是必须看过HashMap源码。至于原理，下面那张图很清楚了:

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc143e55685?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



HashMap采用Entry数组来存储key-value对，每一个键值对组成了一个Entry实体，Entry类实际上是一个单向的链表结构，它具有Next指针，可以连接下一个Entry实体。

只是在JDK1.8中，链表长度大于8的时候，链表会转成红黑树！



### **2.为什么用数组+链表？**



数组是用来确定桶的位置，利用元素的key的hash值对数组长度取模得到.

链表是用来解决hash冲突问题，当出现hash值一样的情形，就在数组上的对应位置形成一条链表。ps:这里的hash值并不是指hashcode，而是将hashcode高低十六位异或过的。至于为什么要这么做，继续往下看。



### **3.hash冲突你还知道哪些解决办法？**



比较出名的有四种(1)开放定址法(2)链地址法(3)再哈希法(4)公共溢出区域法

ps:大家有兴趣拓展的，自己去搜一下就懂了，这个就不拓展了！



### **4.我用LinkedList代替数组结构可以么?**



这里我稍微说明一下，此题的意思是，源码中是这样的

```
Entry[] table = new Entry[capacity];
```



ps：Entry就是一个链表节点。

那我用下面这样表示

```
List<Entry> table = new LinkedList<Entry>(); 
```



是否可行?

答案很明显，必须是可以的。



### **5.既然是可以的,为什么HashMap不用LinkedList,而选用数组?**



因为用数组效率最高！

在HashMap中，定位桶的位置是利用元素的key的哈希值对数组长度取模得到。此时，我们已得到桶的位置。显然数组的查找效率比LinkedList大。



那ArrayList，底层也是数组，查找也快啊，为啥不用ArrayList?



(烟哥写到这里的时候，不禁觉得自己真有想法，自己把自己问死了，还好我灵机一动想出了答案)

因为采用基本数组结构，扩容机制可以自己定义，HashMap中数组扩容刚好是2的次幂，在做取模运算的效率高。

而ArrayList的扩容机制是1.5倍扩容，那ArrayList为什么是1.5倍扩容这就不在本文说明了。



## 二、HashMap在什么条件下扩容?

此题可以组成如下连环炮来问

- HashMap在什么条件下扩容?
- 为什么扩容是2的n次幂?
- 为什么为什么要先高16位异或低16位再取模运算?



### **1.HashMap在什么条件下扩容?**



如果bucket满了(超过load factor*current capacity)，就要resize。

load factor为0.75，为了最大程度避免哈希冲突

current capacity为当前数组大小。



### **2.为什么扩容是2的次幂?**



HashMap为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就在把数据存到哪个链表中的算法；这个算法实际就是取模，hash%length。

但是，大家都知道这种运算不如位移运算快。

因此，源码中做了优化hash&(length-1)。

也就是说hash%length==hash&(length-1)

那为什么是2的n次方呢？

因为2的n次方实际就是1后面n个0，2的n次方-1，实际就是n个1。

例如长度为8时候，3&(8-1)=3 2&(8-1)=2 ，不同位置上，不碰撞。

而长度为5的时候，3&(5-1)=0 2&(5-1)=0，都在0上，出现碰撞了。

所以，保证容积是2的n次方，是为了保证在做(length-1)的时候，每一位都能&1 ，也就是和1111……1111111进行与运算。



### **3.为什么为什么要先高16位异或低16位再取模运算?**



我先晒一下，jdk1.8里的hash方法。1.7的比较复杂，咱就不看了。

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc13e133aee?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



hashmap这么做，只是为了降低hash冲突的几率。

打个比方，当我们的length为16的时候，哈希码(字符串“abcabcabcabcabc”的key对应的哈希码)对(16-1)与操作，对于多个key生成的hashCode，只要哈希码的后4位为0，不论不论高位怎么变化，最终的结果均为0。

如下图所示

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc14085899a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

而加上高16位异或低16位的“扰动函数”后，结果如下

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc140938fa4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



可以看到: 扰动函数优化前：1954974080 % 16 = 1954974080 & (16 - 1) = 0 扰动函数优化后：1955003654 % 16 = 1955003654 & (16 - 1) = 6 很显然，减少了碰撞的几率。



## 三、讲讲hashmap的get/put的过程?

此题可以组成如下连环炮来问

- 知道hashmap中put元素的过程是什么样么?
- 知道hashmap中get元素的过程是什么样么？
- 你还知道哪些hash算法？
- 说说String中hashcode的实现?(此题很多大厂问过)



### **1.知道hashmap中put元素的过程是什么样么?**



对key的hashCode()做hash运算，计算index;

如果没碰撞直接放到bucket里；

如果碰撞了，以链表的形式存在buckets后；

如果碰撞导致链表过长(大于等于TREEIFY_THRESHOLD)，就把链表转换成红黑树(JDK1.8中的改动)；

如果节点已经存在就替换old value(保证key的唯一性)

如果bucket满了(超过load factor*current capacity)，就要resize。



### **2.知道hashmap中get元素的过程是什么样么?**



对key的hashCode()做hash运算，计算index;

如果在bucket里的第一个节点里直接命中，则直接返回；

如果有冲突，则通过key.equals(k)去查找对应的Entry;

- 若为树，则在树中通过key.equals(k)查找，O(logn)；
- 若为链表，则在链表中通过key.equals(k)查找，O(n)。



### **3.你还知道哪些hash算法？**



先说一下hash算法干嘛的，Hash函数是指把一个大范围映射到一个小范围。把大范围映射到一个小范围的目的往往是为了节省空间，使得数据容易保存。

比较出名的有MurmurHash、MD4、MD5等等



### **4.说说String中hashcode的实现?(此题频率很高)**



```
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;
        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
复制代码
```

String类中的hashCode计算方法还是比较简单的，就是以31为权，每一位为字符的ASCII值进行运算，用自然溢出来等效取模。

哈希计算公式可以计为**s[0]31^(n-1) + s[1]31^(n-2) + … + s[n-1]**

那为什么以31为质数呢?

主要是因为31是一个奇质数，所以31*i=32*i-i=(i<<5)-i，这种位移与减法结合的计算相比一般的运算快很多。



## 四、为什么hashmap的在链表元素数量超过8时改为红黑树?

此题可以组成如下连环炮来问

- 知道jdk1.8中hashmap改了啥么?
- 为什么在解决hash冲突的时候，不直接用红黑树?而选择先用链表，再转红黑树?
- 我不用红黑树，用二叉查找树可以么?
- 那为什么阀值是8呢?
- 当链表转为红黑树后，什么时候退化为链表?





### **1.知道jdk1.8中hashmap改了啥么?**



- 由**数组+链表**的结构改为**数组+链表+红黑树**。
- 优化了高位运算的hash算法：h^(h>>>16)
- 扩容后，元素要么是在原位置，要么是在原位置再移动2次幂的位置，且链表顺序不变。

最后一条是重点，因为最后一条的变动，hashmap在1.8中，不会在出现死循环问题。



### **2.为什么在解决hash冲突的时候，不直接用红黑树?而选择先用链表，再转红黑树?**



因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。

当元素小于8个当时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于8个的时候，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

因此，如果一开始就用红黑树结构，元素太少，新增效率又比较慢，无疑这是浪费性能的。



### **3.我不用红黑树，用二叉查找树可以么?**

可以。但是二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。

### **4.那为什么阀值是8呢?**

不知道，等jdk作者来回答。

这道题，网上能找到的答案都是扯淡。

我随便贴一个牛客网的答案，如下图所示

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc143f749a8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

看出bug没？交点是6.64？交点分明是4，好么。

log4=2，4/2=2。

jdk作者选择8，一定经过了严格的运算，觉得在长度为8的时候，与其保证链表结构的查找开销，不如转换为红黑树，改为维持其平衡开销。

### **5.当链表转为红黑树后，什么时候退化为链表?**

为6的时候退转为链表。中间有个差值7可以防止链表和树之间频繁的转换。假设一下，如果设计成链表个数超过8则链表转换成树结构，链表个数小于8则树结构转换成链表，如果一个HashMap不停的插入、删除元素，链表个数在8左右徘徊，就会频繁的发生树转链表、链表转树，效率会很低。

## 五、HashMap的并发问题?

此题可以组成如下连环炮来问

- HashMap在并发编程环境下有什么问题啊?
- 在jdk1.8中还有这些问题么?
- 你一般怎么解决这些问题的？

HashMap在并发编程环境下有什么问题啊?

- (1)多线程扩容，引起的死循环问题
- (2)多线程put的时候可能导致元素丢失
- (3)put非null元素后get出来的却是null

在jdk1.8中还有这些问题么?

在jdk1.8中，死循环问题已经解决。其他两个问题还是存在。

你一般怎么解决这些问题的？

比如ConcurrentHashmap，Hashtable等线程安全等集合类。

## 六、你一般用什么作为HashMap的key?

此题可以组成如下连环炮来问

- 健可以为Null值么?
- 你一般用什么作为HashMap的key?
- 我用可变类当HashMap的key有什么问题?
- 如果让你实现一个自定义的class作为HashMap的key该如何实现？



### **1.健可以为Null值么?**



必须可以，key为null的时候，hash算法最后的值以0来计算，也就是放在数组的第一个位置。

![img](https://user-gold-cdn.xitu.io/2019/8/21/16cb3dc143ad2079?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### **2.你一般用什么作为HashMap的key?**

一般用Integer、String这种不可变类当HashMap当key，而且String最为常用。

- (1)因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。
- (2)因为获取对象的时候要用到equals()和hashCode()方法，那么键对象正确的重写这两个方法是非常重要的,这些类已经很规范的覆写了hashCode()以及equals()方法。

### **3.我用可变类当HashMap的key有什么问题?**

hashcode可能发生改变，导致put进去的值，无法get出，如下所示

```
HashMap<List<String>, Object> changeMap = new HashMap<>();
List<String> list = new ArrayList<>();
list.add("hello");
Object objectValue = new Object();
changeMap.put(list, objectValue);
System.out.println(changeMap.get(list));
list.add("hello world");//hashcode发生了改变
System.out.println(changeMap.get(list));
复制代码
```

输出值如下

```
java.lang.Object@74a14482
null
复制代码
```

### **4.如果让你实现一个自定义的class作为HashMap的key该如何实现？**

此题考察两个知识点

- 重写hashcode和equals方法注意什么?
- 如何设计一个不变类

针对问题一，记住下面四个原则即可

(1)两个对象相等，hashcode一定相等

(2)两个对象不等，hashcode不一定不等

(3)hashcode相等，两个对象不一定相等

(4)hashcode不等，两个对象一定不等

针对问题二，记住如何写一个不可变类

(1)类添加final修饰符，保证类不被继承。

如果类可以被继承会破坏类的不可变性机制，只要继承类覆盖父类的方法并且继承类可以改变成员变量值，那么一旦子类以父类的形式出现时，不能保证当前类是否可变。

(2)保证所有成员变量必须私有，并且加上final修饰

通过这种方式保证成员变量不可改变。但只做到这一步还不够，因为如果是对象成员变量有可能再外部改变其值。所以第4点弥补这个不足。

(3)不提供改变成员变量的方法，包括setter

避免通过其他接口改变成员变量的值，破坏不可变特性。

(4)通过构造器初始化所有成员，进行深拷贝(deep copy)

如果构造器传入的对象直接赋值给成员变量，还是可以通过对传入对象的修改进而导致改变内部变量的值。例如：

```
public final class ImmutableDemo {  
    private final int[] myArray;  
    public ImmutableDemo(int[] array) {  
        this.myArray = array; // wrong  
    }  
}
复制代码
```

这种方式不能保证不可变性，myArray和array指向同一块内存地址，用户可以在ImmutableDemo之外通过修改array对象的值来改变myArray内部的值。

为了保证内部的值不被修改，可以采用深度copy来创建一个新内存保存传入的值。正确做法：

```
public final class MyImmutableDemo {  
    private final int[] myArray;  
    public MyImmutableDemo(int[] array) {  
        this.myArray = array.clone();   
    }   
}
复制代码
```

(5)在getter方法中，不要直接返回对象本身，而是克隆对象，并返回对象的拷贝

这种做法也是防止对象外泄，防止通过getter获得内部可变成员对象后对成员变量直接操作，导致成员变量发生改变。

