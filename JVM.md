# 一、类加载全过程

1、加载

![image-20191109103109599](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191109103109599.png)

2、链接

![image-20191109102626771](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191109102626771.png)

我在1米处，她在101米处，直接引用就是在我上边100米处

符号引用比较抽象，直接饮用比较具体



3、初始化	

![image-20191109102633401](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191109102633401.png)

```java
	 public static void main(String[] args) {
        A a = new A();
        System.out.println(A.length);//A长度为200，是从上往下合并
    }
```

A.java

```java
class A {
    //先执行类加载器
    public static int length = 100;
    static {
        System.out.println("静态初始化块A");
        length = 200;
    }
    public A() {
        System.out.println("创建A的对象");
    }
}
```

此处A.length 为200，因为静态代码块是从上往下执行，先赋值100，然后赋值200。

![image-20191111100115379](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191111100115379.png)

首先，会加载Demo01。常量池中有Demo01类名，变量名：a。同时在堆里也会形成一个反射对象（2），通过反射对象获得类的结构。然后，加载类A的信息，同时也会在堆中形成一个对象，代表A类（4）。类加载完成后，连接成功，初始化的时候会调用这个方法。 会把这两个合并到`clinit`方法，结束后才代表初始化完成才会创建我们对象。完了会执行main方法里边的代码。main方法执行的时候会在栈中形成mian方法栈帧（5），一个方法对应一个栈帧。

如果main方法中调用了其他方法，会在栈帧中继续往里加。一开始a为null，然后new 一个对象，因为new A()也是一个对象。所以会将A的构造方法压进栈中进行调用（6）。调用的结果是在堆中生成了一个A对象（7）。会将A的对象赋值（地址）给a。a就不为空了，拥有了对象的地址。然后打印`A.width`，直接写类名会引用方法区的数据。

`静态变量 == 静态属性 == 静态域`

## 初始化时机—-静态初始化块执行顺序问题

```java
public class Demo01 {
    static {
        System.out.println("静态初始化Demo01");
    }

    public static void main(String[] args) {
        System.out.println("Demo01的main方法");
    }
}
```

执行结果：

```java
静态初始化Demo01
Demo01的main方法
```



先执行static中的输出，因为main方法毕竟是方法调用。只有Demo01加载完成以后才会调用main方法。

顺序：先加载类——》加载连接——》初始化——》调用初始化块——》开始使用Demo01类——》使用才会调用main方法



A.java

```java
class A {
    //先执行类加载器
    public A() {
        System.out.println("创建A的对象");
    }
    //将以下两个合并到一起。
    public static int length = 100;
    static {
        System.out.println("静态初始化块A");
        length = 200;
    }
}
```

### 父类未被加载

A_Father.java

```java
class  A_Father{
    static {
        System.out.println("静态初始化A_Father");
    }
}
```



main.java

```java
 public static void main(String[] args) {
        A a = new A();
    }
```

执行结果：

```
静态初始化A_Father
静态初始化块A
创建A的对象
```

先初始化A_Father，然后初始化A类，然后执行main方法。

### 创建两次对象

```java
 public static void main(String[] args) {
        System.out.println("Demo01的main方法");
        A a = new A();
        System.out.println(A.length);
        A a2 = new A();
    }
```

输出结果：

```
Demo01的main方法
静态初始化A_Father
静态初始化块A
创建A的对象
200
创建A的对象
```

类只会初始化一次.

## 类的主动引用和被动引用

![image-20191111104715702](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191111104715702.png)

类使用时才会加载。

### 主动引用：

```java
 new A();
//或
System.out.println(A.length);
```

这时会初始化。

### 被动引用：

1、A.java

```java
    public static final int MAX = 100;
```

当在main方法中执行

```java
        System.out.println(A.MAX);
```

的时候，不会被初始化。因为常量是放在常量池中的。 可以通过常量池直接定义，是被动引用。并不会初始化所在的类。

2、数组定义类引用，不会触发此类的初始化。

3、如果访问一个子类的父类的静态属性，那么子类也不会被初始化。

B.java

```java
class B extends A{
    static  {
        System.out.println("静态初始化B");
    }
}
```

main

```java
        System.out.println(B.length);
```

执行结果：

```java
静态初始化A_Father
静态初始化块A
200
```

### 总结

主动：

```java
        //  new A();
        // System.out.println(A.length);
        // Class.forName("com.zx.classLoader.A");
```

被动

```java
        //  System.out.println(A.MAX);
        //A[] as = new A[10];
        // System.out.println(B.length);
```

# 二、深入类加载器

## 1、类加载器原理

![image-20191111113113428](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191111113113428.png)

## 2、类加载器树状结构

![image-20191111113157843](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191111113157843.png)

**不是继承，而是组合实现的，即将parent设置为该类的private属性。**

除了引导类加载器使用C来写，其他的都是使用JAVA写的

![image-20191112104747383](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112104747383.png)

获取类加载器：

```java
 public static void main(String[] args) {
        System.out.println(ClassLoader.getSystemClassLoader());
        System.out.println(ClassLoader.getSystemClassLoader().getParent());
       System.out.println(ClassLoader.getSystemClassLoader().getParent().getParent());
    }
```

当前为应用程序加载器，应用程序加载器为扩展类加载器，因为启动类加载器是由原生代码实现的，所以在JAVA中获取不到，因此为null。

执行结果：

```java
sun.misc.Launcher$AppClassLoader@18b4aac2
sun.misc.Launcher$ExtClassLoader@1540e19d
null
```

## 3、双亲委托（代理）机制

![image-20191112105556683](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112105556683.png)

父类加载器优先加载。保证安全。

例：如果自己定义一个java.lang.String要求加载，首先交给自定义加载器进行加载，然后自定义加载传给应用程序加载……直到传给启动类加载器，启动类加载器发现核心包中有java.lang.String，则启动类加载器加载成功，扩展类、应用程序类以及自定义类都不会加载。那么自定义的java.lang.String永远不会加载。始终加载核心包里类。起到很好的保护作用。

## 4、自定义类加载器

 ![image-20191112112148408](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112112148408.png)

自定义类加载器：

```java
package com.zx.classLoader;

import java.io.ByteArrayOutputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class FileSystemClassLoader extends ClassLoader {
    private String rootDir;

    public FileSystemClassLoader(String rootDir) {
        this.rootDir = rootDir;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        Class<?> c = findLoadedClass(name);
        //应该要先查询有没有加载过这个类。如果已经加载，则直接返回加载好的类，如果没有，则加载新的类。
        if(c!=null){
            return  c;
        }else{
            ClassLoader parent = this.getParent();
            try{
                c = parent.loadClass(name);//委派给父类加载。
            }catch (Exception e){
                e.printStackTrace();
            }
            if(c!=null){
                return  c;
            }else {
                byte[] classData = getClassData(name);
                if(classData == null){
                    throw  new ClassNotFoundException();
                }else{
                    c = defineClass(name,classData,0,classData.length);
                }
            }
        }
        return super.findClass(name);
    }

    private byte[] getClassData(String classname) {
        String path = rootDir +"/" + classname.replace(".","/")+".class";
        //IOUtils ,可以使用它将流中的数据转换成字节数组。
        InputStream is = null;
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        try{
            is = new FileInputStream(path);
            byte[] buffer = new byte[1024];
            int temp = 0;
            while((temp = is.read(buffer))!= -1){
                baos.write(buffer,0,temp);
            }
            return  baos.toByteArray();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            try{
                if(is!= null){
                    is.close();
                }
            }catch (IOException e){
                e.printStackTrace();
            }
            try {
                if(baos != null){
                    baos.close();
                }
            }catch (Exception e){
                e.printStackTrace();
            }
        }
        return  baos.toByteArray();
    }
}
```

##### 加密解密加载器

Encrypt.java

```java
public class EncryptUtil {
    public static void main(String[] args) {
        encrupt("c:/video/HelloWorld.class","c:/video/temp/HelloWorld.class");
    }
    public static void encrupt(String src,String dest){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try{
            fis = new FileInputStream(src);
            fos = new FileOutputStream(dest);
            int temp = -1;
            while ((temp = fis.read()) != -1){
                fos.write(temp^0xff);
            }
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                if(fis != null){
                        fis.close();
                    }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if(fos != null){
                        fos.close();
                    }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

Decrypted.java

```java
public class DecruptClassLoader extends ClassLoader {
    private String rootDir;

    public DecruptClassLoader(String rootDir) {
        this.rootDir = rootDir;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        Class<?> c = findLoadedClass(name);
        //应该要先查询有没有加载过这个类。如果已经加载，则直接返回加载好的类，如果没有，则加载新的类。
        if(c!=null){
            return  c;
        }else{
            ClassLoader parent = this.getParent();
            try{
                c = parent.loadClass(name);//委派给父类加载。
            }catch (Exception e){
                e.printStackTrace();
            }
            if(c!=null){
                return  c;
            }else {
                byte[] classData = getClassData(name);
                if(classData == null){
                    throw  new ClassNotFoundException();
                }else{
                    c = defineClass(name,classData,0,classData.length);
                }
            }
        }
        return c;
    }

    private byte[] getClassData(String classname) {
        String path = rootDir +"/" + classname.replace(".","/")+".class";
        //IOUtils ,可以使用它将流中的数据转换成字节数组。
        InputStream is = null;
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        try{
            is = new FileInputStream(path);
            int temp = -1;
            while ((temp = is.read()) != -1){
                baos.write(temp^0xff);
            }
            return  baos.toByteArray();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            try{
                if(is!= null){
                    is.close();
                }
            }catch (IOException e){
                e.printStackTrace();
            }
            try {
                if(baos != null){
                    baos.close();
                }
            }catch (Exception e){
                e.printStackTrace();
            }
        }
        return  baos.toByteArray();
    }
}
```

main.java

```java
		DecruptClassLoader loader1  = new DecruptClassLoader("c:/video/temp");
        Class<?> c = loader1.loadClass("HelloWorld");
        System.out.println(c);
```



## 5、线程上下文类加载器

 ![image-20191112195329753](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112195329753.png)



## 6、服务器类加载原理和OSGI介绍



![image-20191112195854513](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112195854513.png)



![](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191112200049672.png)

# 三、内部类

![image-20191113150521480](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191113150521480.png)

![image-20191113152651957](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191113152651957.png)











































