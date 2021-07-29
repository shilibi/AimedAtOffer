

### java new 一个对象过程

1，首先到常量池中找类的带路径全名，然后检查对应的字节码是否已被加载，解析，验证，初始化，如果没有先执行类加载过程(class.forname())。

2，类加载过程完成后，虚拟机会为对象分配内存。分配内存有两种方式，根据使用的垃圾收集器的不同使用不同的分配机制。

（1）指针碰撞，当虚拟机使用复制算法或标记整理算法实现的垃圾收集器时，内存区域都是规整的，这时候使用指针碰撞分配内存，用过的内存放在一边，空闲的内存在另一边，中间用一个指针作为分界点，当需要为新对象分配内存时只需把指针向空闲的一边移动一段与对象大小相等的距离。

（2）空闲列表，当虚拟机使用标记清除算法实现的垃圾收集器时，内存都是碎片化的，那虚拟机就要记录哪块内存是可用的，当需要分配内存时，找一块足够大的内存空间给对象实例，并更新记录。

3，设置对象头信息，如所属类，元数据信息，哈希码，gc分代年龄，等等。

4，调用对象的init()方法,根据传入的属性值给对象属性赋值。

5，在线程栈中新建对象引用，并指向堆中刚刚新建的对象实例。



### Ip地址与数字之间的转换

IP -> 数字

通过String 的 split 按 .  分割成四个数组

通过<< 对每个数组加权，第一段权为2的24次方，第二段权为2的16次方，第三段权为2的8次方，最后一段权为1。

数字 -> IP 地址

右移位操作，右移24位，同时高位补零，得到的数字即为第一段IP

通过与操作符（&）将整数值高8位设为0，右移16位，得到的数字为第二段IP

通过与操作符（&）将整数值高16位设为0，右移8位，得到的数字为第三段IP

通过与操作符（&）将整数值高24位设为0，得到的数字为第四段IP

```java
public class IPUtil {
    /**
     * ip地址转成long型数字
     * 将IP地址转化成整数的方法如下：
     * 1、通过String的split方法按.分隔得到4个长度的数组
     * 2、通过左移位操作（<<）给每一段的数字加权，第一段的权为2的24次方，第二段的权为2的16次方，第三段的权为2的8次方，最后一段的权为1
     * @param strIp
     * @return
     */
    public static long ipToLong(String strIp) {
        String[]ip = strIp.split("\\.");
        return (Long.parseLong(ip[0]) << 24) + (Long.parseLong(ip[1]) << 16) + (Long.parseLong(ip[2]) << 8) + Long.parseLong(ip[3]);
    }

    /**
     * 将十进制整数形式转换成127.0.0.1形式的ip地址
     * 将整数形式的IP地址转化成字符串的方法如下：
     * 1、将整数值进行右移位操作（>>>），右移24位，右移时高位补0，得到的数字即为第一段IP。
     * 2、通过与操作符（&）将整数值的高8位设为0，再右移16位，得到的数字即为第二段IP。
     * 3、通过与操作符吧整数值的高16位设为0，再右移8位，得到的数字即为第三段IP。
     * 4、通过与操作符吧整数值的高24位设为0，得到的数字即为第四段IP。
     * @param longIp
     * @return
     */
    public static String longToIP(long longIp) {
        StringBuffer sb = new StringBuffer("");
        // 直接右移24位
        sb.append(String.valueOf((longIp >>> 24)));
        sb.append(".");
        // 将高8位置0，然后右移16位
        sb.append(String.valueOf((longIp & 0x00FFFFFF) >>> 16));
        sb.append(".");
        // 将高16位置0，然后右移8位
        sb.append(String.valueOf((longIp & 0x0000FFFF) >>> 8));
        sb.append(".");
        // 将高24位置0
        sb.append(String.valueOf((longIp & 0x000000FF)));
        return sb.toString();
    }

    public static void main(String[] args) {
        System.out.println(ipToLong("219.239.110.138"));
        System.out.println(longToIP(18537472));
    }
}
```



