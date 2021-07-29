应用服务器：CPU及内存压力

数据库服务器：IO压力

#### Session存在哪儿？

​	1、存在cookie中

​			不安全、网络负担效率低

​	2、存在文件服务器或数据库里

​			大量IO问题

​	3、session复制

​			session数据冗余、节点越多浪费越大

​	4、缓存数据库

​			完全在内存中，速度快，数据结构简单

客户端向应用服务器发送登录请求，应用服务器向缓存数据库进行交互，查询该用户session是否存在，如果存在，则不经过数据库只通过应用服务器直接返回数据。如果不存在，应用数据库向数据库进行查询，查询的结果存放在缓存服务器中，然后再返回给客户端。



Memcached：一般不持久化;支持简单的key-value模式，键和值只能是字符串；多线程+锁

Redis：支持持久化;支持五种数据类型（String,list,set,hash,zset)：；单线程＋多路IO复用（监视功能），Redis一直工作。速度超快。



MongoDB:最接近关系型数据库的非关系型数据库。可以存图片。



Redis持久化两种方式：半持久化(把修改操作写入追加的记录文件)，全持久化（周期性的把更新的数据写入磁盘）

![image-20200301220933220](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200301220933220.png)



redis-server 启动redis

6379  =  Merz



Redis共有16个数据库，第一个为0号库，第十六个为15号库， 一般只用第一个

Redis中只能存字符串

使用select   \<dbid>切换数据库 ，select  8

通过  redis-cli  --raw显示中文  ，但是没有序号和双引号

允许主机访问redis  修改redis.conf 文件，将61行 ```bind 127.0.0.1``` 注释掉和80行```protected-mode yes```改成```protected-mode no```



设置密码： 修改redis.cong 文件，将479行左右的```#requirepass foobared```注释取消。并将密码foobared改成自己的密码即可。

### 常用命令：

1)、keys  * 查当前所有的键

2)、exists \<key>   判断某个键是否存在。返回值 1|0

3)、type \<key> 查看键的类型

4)、del \<key> 删除某个键

5)、expire \<key> \<seconds> 为键值设置过期时间，单位为秒，过期即删除

6)、ttl \<key> 查看还有多少秒过期     -1 表示永不过期，  -2 表示已过期

7)、dbsize  查看当前数据库的key的数量

8)、Flushdb    清空当前库

9)、Flushall    通杀全部库

<!--以下为操作String字符串的指令-->

10)、get \<key> 查询对应键值

11)、set  \<key>  \<value>  添加键值对

12)、append   \<key>     \<value>    将给定的value追加到原值的末尾

13)、strlen   \<key>   获得值的长度

14)、setnx \<key>      \<value>   只有在key不存在时设置key的值

15)、incr \<key> 将key中存储的数字值增加1，只能对`数字值`操作，如果为空，新增值为1

16)、decr \<key>  将key中存储的数字值减1.只能对`数字值`操作，如果为空，新增值为-1

17)、incrby/decrby   \<key>   <步长>   将key中存储的`数字值`增减，自定义步长

18)、mset  \<key1> \<value1>\<key2> \<value2>……  同时设置一个或者多个键值对

19)、mget \<key1>\<key2>\<key3>同时获取一个或者多个value

20)、msetnx  \<key1> \<value1>\<key2> \<value2> 同时设置一个或者多个键值对，当且仅当所有给定的key都不存在时。

21)、getrange \<key>  \<起始位置>  \<结束位置>  获得值的范围，类似java中的substring，但是包前也包后，截取

22)、 setrange  \<key>    \<起始位置>  \<value>   用 value 覆盖key中存储的字符串值，从起始位置开始

23)、setex \<key>  \<过期时间>  \<value> 设置键值的同时，设置过期时间， 单位秒

24)、getset \<key>     \<value>以新换旧，设置了新值同时获得旧值

<!--以下为List的指令--><!--list  左侧为表头-->

25)、 lpush / rpush  \<key>   \<value1>  \<value1>    从左边或右边插入一个或者多个值

26)、 lpop  / rpop  \<key>   从左边/右边吐出一个值，值在键在，值无 键无

27)、rpoplpush  \<key1>\<key2> 从key1 列表右边吐出一个值，插到key2列表的左侧

28)、lrange \<key>   \<start>  \<stop> 按照索引下标获取元素，从左到右   **lrange key  0  -1查询整个list**

29)、lindex \<key>  \<index> 按照索引下标获得元素 从左到右

30)、 llen \<key>   获得列表的长度

31)、linsert \<key> before \<value>  \<newvalue>  在value的前边插入newvalue 插入值  此处的value为索引，即第几个

32)、lrem \<key> \<n>  \<value> 从左边删除n个value   从左到右。 正数从左往右删，负数从右往左删  0代表删除所有的

注：lpush k1   1 2 3 4 5     rpush  k1  a b c d e。

存储结果为：  5 4 3 2 1  a b c d e 

<!--以下为Set的指令--><!--set String类型的无序集合  自动排重   && 提供了判断成员是否在set集合中的重要接口-->

33)、sadd  \<key>    \<value1>    \<value2>  将一个或者多个member元素加入到集合key当中，已经存在于集合中的member元素将被忽略。

34)、smembers \<key>     取出该集合中的所有值

35)、sismember  \<key>  \<value>    判断集合key中是否存在该value，有返回1 ，没有返回0

37)、scard \<key>   返回该集合的元素个数

38)、srem \<key>  \<value1>  \<value2>   \<value3>  ……   删除集合中的某个元素

39)、spop  \<key>   \< n> 随机从该集合中吐出一个值，会删除   用作抽奖？

40)、srandmember  \<key>   \<n>   随机从该集合中取出n个值，不会从集合中删除

41)、sinter  \<key1> \<key2>   返回两个集合的交集元素

42)、sunion  \<key1> \<key2>   返回两个集合的并集元素

43)、sdiff  \<key1> \<key2>     返回两个集合的差集元素  key1  -  （key1 ∩ key2）

<!--以下为Hash的指令-->

44)、hset   \<key>   \<field>  \<value>   给key集合中的field键赋值value

45)、hget   \<key1>   \<field>    从key集合field取出value

46)、hmset   \<key1>   \<field1>  \<value1>   批量设置hash值

47)、hexist  key \<field>   查看哈希表key中，给定域field是否存在

48)、hkeys  \<key>  列出该hash集合中的所有field

49)、hvals  \<key>  列出该hash集合中的所有value

50)、hgetall  \<key>   获取所有的field 和value

50)、hincrby  \<key>  \<field>  \<increment>  为哈希表key中的域field的值加上增量increment  通过加负数实现减法

51)、hsetnx  \<key>  \<field>  \<value> 将哈希表key中的域field 的值设置为value，当且仅当field不存在。

<!--以下为Zset的指令--><!--没有重复的字符串  论坛帖子点击量   集合成员唯一，评分可重复  以元素为键，分数为值-->

53)、zadd  \<key>  \<score1> \<value1>   \<score2>  \<value2>  将一个或者多个member元素及其score值加入到有序集key当中，①同一元素不同分数会将分数替换掉；②不同元素同一分数可以添加进，按照

54)、zrange \<key>   \<start>  \<stop>  [WITHSCORES]返回有序集\<key>中，下标在\<start>  \<stop>之间的元素，带上WITHSCOPES  可以让分数一起和值返回到结果集。

55)、zrangebyscore  key  min  max     [WITHSCORES]  [LIMIT OFFSET COUNT]  返回有序集key中，所有score介于min和max之间（包含max和min）的成员。有序集成员按score值递增（从小到大）次序排列

56)、zrevrangebyscore key max min    [WITHSCORES]  [LIMIT OFFSET COUNT]   返回有序集key中，所有score介于max和min之间（包含max和min）的成员。有序集成员按score值递减（从大到小）次序排列

57)、zincrby \<key>  \<incremen> \<value> 为元素的score加上增量

58)、zrem  \<key>  \<value> 删除该集合下，指定值的元素

59)、zcount   \<key>  \<min> \<max>  统计该集合，分数区间内的元素个数

60)、zrank \<key>  \<value>  返回该值在集合中的排名，从0开始





Redis事务的本质就是串联多个命令防止别的命令插队，批量执行指令的功能

1. multi：开始事务，执行多个指令
2. exec：执行事务
3. discard：取消事务
4. watch key [key ……] 在执行multi之前，先执行watch，当watch的值发生改变时，如果执行事务，事务将全部取消
5. unwatch：取消watch命令，若在执行watch之后， exec命令或discard先被执行了，就不需要执行unwatch了

编译时异常：取消事务

其中一步错误，不会影响其他事务的执行。

乐观锁：每一次数据变化都有一个版本号，当数据读取时（V1.0），如果符合条件，就进行执行操作，此时需要验证（v1.0）是否和现在的版本一致，如果一致，就执行修改操作，如果不一致，直接失败  。



#### 事务的三个特性

![image-20200304175003649](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200304175003649.png)

模拟并发      ab -n 1000 -c 200 -p /opt/postfile -T  "application/x-www-form-urlencoded"  192.168.1.48:8080/SecKill/doseckill

### Redis持久化之RDB

写时复制，只有在写的时候才复制。父进程和子进程共用一段物理内存，只有进程空间的各段内容要发生变化时，才会将父进程的内容复制一份给子进程。

相对于AOF ，RDB节省空间，恢复速度快，RDB存储的是数据；AOF存储的是指令，恢复时把每条命令执行一遍。

![image-20200305112336506](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305112336506.png)

持久化两种方式

​	1、满足保存策略  30秒写5次 造成最后一次持久化数据丢失  

​	2、正常关闭  shutdown命令  

save  手动保存快照 其他不管，全部阻塞（一般不用）

save vs bgsave  

![image-20200305110744605](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305110744605.png)

![image-20200305111018330](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305111018330.png)

### Redis持久化-AOF

以日志形式来记录每个**写**操作。默认不开启

![image-20200305112440449](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305112440449.png)

![image-20200305112536532](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305112536532.png)

RDB和AOF同时开启，听aof 的



![image-20200305114555965](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305114555965.png)

![image-20200305114950010](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305114950010.png)

##### Rewrite

AOF采用文件追加方式,文件会越来越大为避免出现此种情况新增了重写机制,当AOF文件的大小超过所设定的阈值时,Redis就会启动AOF文件的内容压缩,只保留可以恢复数据的最小指令集可以使用命令 bgrewriteaof

###### 如何实现重写？

AOF文件持续增长而过大时,会fork出一条新进程来将文件重写(也是先写临时文件最后再 rename),遍历新进程的内存中数据,每条记录有一条的Set语句。重写aof文件的操作,并没有读取旧的aof文件,而是将整个内存中的数据库内容用命令的方式重写了一个新的ao文件,这点和快照有点类似。

###### 何时重写

重写虽然可以节约大量磁盘空间,减少恢复时间。但是每次重写还是有一定的负担的,因此设定 Redis要满足一定条件才会进行重写

`auto-aof-rewrite-percentage 100           auto-aof-- min-size 64mb`

系统载入时或者上次重写完毕时,Redis会记录此时AOF大小,设为 base_size,如果Reds的AOF当前大小>= base _size+base_size*100%(默认)且当前大小>=64mb(默认)的情况下,Reds会对AOF进行重写。

###### 优缺点

优点：备份机制更稳健,丢失数据概率更低。可读的日志文本,通过操作AOF稳健,可以处理误操作

缺点：比起RDB占用更多的磁盘空间。恢复备份速度要慢。每次读写都同步的话,有一定的性能压力。存在个别Bug,造成恢复不能。

###### 用哪个好

官方推荐两个都启用。

1. 如果对数据不敏感,可以选单独用RDB。
2. 不建议单独用AOF,因为可能会出现Bug。
3. 如果只是做纯内存缓存,可以都不用。

#### Redis主从复制

一主二从

master 读为主，slave 写为主。无法解决内存压力，因为主从服务器数据一致。只是用来减少读写压力。

用处： 读写分离，性能扩展；容灾快速恢复。

配从（服务器）不配主(服务器)

1. 拷贝多个redis.conf文件include
2. 开启 daemonize yes
3. Pid文件名字 profile·
4. 指定端口port
5. Log文件名字
6. Dump.rdb名字 filename
7. Appendonly关掉或者换名字

info replication 打印主从复制的相关信息

在从服务器中输入  ``` slaveof <ip> <port>``` 成为某个实例的从服务器

从服务器**永远和主服务器数据一致**，不管何时成为主服务器的从服务器。

从机不能写，只能读

主机宕机后，从机保持待机状态。

主机宕机恢复后，主机新增记录，主从机数据仍然一致。



永久主从复制：从redis.conf 中修改  slaveof  ip地址  端口号



##### Redis主从复制

1. 每次从机联通后，都会给主机发送sync指令。
2. 主机立刻进行存盘操作，发送RDB文件，给从机。
3. 从机收到RDB文件后，进行全盘加载。
4. 之后每次主机的写操作，都会立刻发送给从机，从机执行相同的命令

##### 薪火相传

薪火相传

上一个save可以是下一个save的 Master，slave同样可以接收其他slaves的连接和同步请求，那么该 slave作为了链条中下一个的master，可以有效减轻 master的写压力，**去中心化**降低风险。

用 slaved  \<ip>   \<port>

中途变更转向:会清除之前的数据,重新建立拷贝最新的

风险是一旦某个slave宕机,后面的 slave都没法备份

如果 A 是B的主机，B是C的主机，当A宕机时候，B和C都不能写入。

slaveof no one  不作为任何主机的从服务器。

好处： 假设A是主机，B和C是A的从机，当A宕机时，若想恢复服务，需要①将B设置为slaveof no one ② 将C设置为slaveof B。需要两步操作，而薪火相传模式只需要将B设置为slaveof no one 即可。 

##### 哨兵模式 （反客为主的自动版）

在主从复制的基础上（而不是薪火相传）实现的。

 主服务器宕机时，根据投票数自动将从库转为主库

1. 调整为一主二仆模式

2. 自定义的/myredis目录下新建 sentinel. conf文件

3. 在配置文件中填写内容:

   sentinel monitor mymaster 127.0.0.1 6379 1

4. 其中 mymaster为监控对象起的服务器名称,1为至少有多少个哨兵认为主服务器宕机了，才算宕机。

启动哨兵 ：  ```redis-sentinel /opt/myredis/sentinel.conf```







![image-20200305184834450](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305184834450.png)

流言协议：

投票协议：

注：红线处意思为：将down掉的主服务器设置为新主服务器的从服务器。

![image-20200305184632390](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200305184632390.png)



slave-priority  值     (值越小，优先级越高 )  复制到  redis6381.conf里边

# 集群

Redis集群实现了对 Redis的水平扩容,即启动N个 redis节点,将整个数据库分布存储在这N个节点中,每个节点存储总数据的1/N。

Redis集群通过分区( partition)来提供一定程度的可用性( availability)):即使集群中有一部分节点失效或者无淒进行通讯,集群也可以继续处理命令请求。

   开启集群：     ./redis-trib.rb create --replicas 1 192.168.21.190:6379 192.168.21.190:6380 192.168.21.190:6381 192.168.21.190:6389  192.168.21.190:6390 192.168.21.190:6391

​	**redis-cli -c -p 端口号**：以集群方式启动redis客户端

cluster nodes： 查看集群状态





###### redis cluster如何分配这六个节点?

1. 一个集群至少要有三个主节点。
2. 选项- replicas1表示我们希望为集群中的每个主节点创建一个从节点
3. 分配原则尽量保证每个主数据库运行在不同的iP地址,每个从库和主库不在一个iP地址上。

###### 什么是slots

1. 一个 Redis集群包含16384个插槽( hash slot),数据库中的每个键都属于这16384个插槽的其中一个,集群使用公式CRC16(key)%16384来计算键key属于哪个槽,其中CRC16(key)语句用于计算键key的CRC16校验和。
2. 集群中的每个节点负责处理一部分插槽。举个例子,如果一个集群可以有主节点,其中:节点A负责处理0号至5500号插槽。节点B负责处理5501号至11000号插槽。节点C负责处理11001号至16383号插槽。

###### 查询集群中的值

1.  CLUSTER KEYSLOT<key>计算键key应该被放置在哪个槽上。
2. CLUSTER COUNTKEYSINSLOT<slot> 返回槽slot目前包含的键值对数量。
3. CLUSTER GETKEYSINSLOT<slot>< count> 返回 count个slot槽中的键。

###### 故障恢复

1. 如果主节点下线?从节点能否自动升为主节点?

   可以

2. 主节点恢复后,主从关系会如何?

   变成从节点

3. 如果所有某一段插槽的主从节点都当掉, redis服务是否还能继续?

   集群宕机。

4. redis. conf中的参数 cluster-require-full-coverage  默认值yes

    **16384个slot都正常的时候才能对外提供服务**





#### 集群的Jedis开发

```java
public class JedisClusterTest {
    public static void main(String[] args){
        Set<HostAndPort> set =new HashSet< HostAndport>();
        set.add (new HostAndPort( "192. 168.31 211",6379));
        Jediscluster jediscluster=new JedisCluster(set);
        jediscluster.set("k1",v1");
        System.out.printLn(jedisCLuster get("k1"))
    }
}
```

Redis集群提供了以下好处:

1. 实现扩容
2. 分摊压力
3. 无中心配置相对简单

Redis集群的不足

1. 多键操作是不被支持的
2. 多键的 Redis事务是不被支持的。lua脚本不被支持。
3. 由于集群方案出现较晚,很多公司已经采用了其他的集群方案,而代理或者客户端分片的方案想要迁移至 Redis cluster,需要整体迁移而不是逐步过渡,复杂度较大

############################################################################

### 报错：ERR Slot 741 is already busy (Redis::CommandError)

进入插槽对应的主机，依次使用flushall  和reset，然后重建集群

https://blog.csdn.net/qiushisoftware/article/details/78837855

