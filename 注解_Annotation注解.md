## 注解入门

![image-20191107144456956](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107144456956.png)



定义注解方式：

![image-20191107145444845](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107145444845.png)

## 内置注解

![image-20191107145626355](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107145626355.png)



### SuppressWarning注解

![image-20191107150247346](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107150247346.png)

**String[]** :参数类型

**value()**:参数名，抑制多种警告：   **@SuppressWarnings(value={"unchecked","deprecation"})**

![image-20191107150438380](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107150438380.png)



### Target注解

​		表示应用于哪些地方

## 自定义注解、元注解

![image-20191107151000094](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107151000094.png)

```java
@Target(ElementType.METHOD)
public @interface MyAnnotation01 {
    /*
        如果不设置默认值，使用注解会报错
     */
    String studentName() default  "";
    int age() default  0;
    int id() default  -1;//String indexOf("abc") -1

    String[] schools() default {"Peking University","OUC"};
}
```

如果注解中只有一个参数，一般将这个参数定义为value。在使用时只有一个value的情况可以省略不写。



注意：

​		--注解元素必须要有值，定义注解元素时，经常使用空字符串、0作为默认值。

​		--也经常使用负数（比如-1）表示不存在的含义。

**元注解：**![image-20191107151055254](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107151055254.png)

#### 1、@Target注解：

![image-20191107151254770](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107151254770.png)

#### 2、@Retention注解

![image-20191107151639302](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107151639302.png)

# 二、反射机制读取注解

![image-20191107154829976](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107154829976.png)

### 1、首先创建一个类:

```java
package com.ouc;

@MyTable("tb_student")
public class Student {

    @MyFiled(columnName = "id",length = 10,type = "int")
    private int id;
    @MyFiled(columnName = "sname",length = 10,type = "varchar")
    private String  studentName;
    @MyFiled(columnName = "age",length = 3,type = "int")
    private int age;
}

```

### 2、根据所需的项创建两个对应的注解：

```java
@Target({ElementType.FIELD})//属性
@Retention(RetentionPolicy.RUNTIME)
public @interface MyFiled {
    String columnName();
    String type();
    int length();
}
```

```java
@Target({ElementType.TYPE})//类
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTable {
    String value();
}

```

### 3、将注解加在类中对应的位置（如1）

### 4、解析注解以及类

```java
  try{
            Class clazz= Class.forName("com.ouc.Student");
            //获得类的所有有效注解
            Annotation[] annotations = clazz.getAnnotations();
            for (Annotation anno:
                 annotations) {
                System.out.println(anno);
            }
            //获得类的指定注解
            MyTable myTable = (MyTable) clazz.getAnnotation(MyTable.class);
            System.out.println(myTable.value());

            //获得类的属性的注解
            Field studentName = clazz.getDeclaredField("studentName");
            MyFiled myFiled = studentName.getAnnotation(MyFiled.class);
            System.out.println(myFiled.columnName()+"--"+myFiled.type()+"--"+myFiled.length());

            //拼接Sql语句，使用JDBC发送语句
        }catch (Exception e){
            e.printStackTrace();
        }
```

1. 获得类的所有有效注解

   ```java
   Class clazz = Class.forName("com.ouc.Student");//输入类名
   Annotation[] anno = clazz.getAnnotations();
   ```

2. 获得类指定的注解

   ```java
   MyTable myTable = (MyTable)clazz.getAnnotation(MyTable.class);//输入注解类型
   ```

3. 获得类的属性的注解

   ```java
   Filed studentName = clazz.getDeclaredField("studentName")；//属性名
   MyFiled myFiled = studentName.getAnnotation(MyFiled.class);
   ```

# 三、JAVA动态性之反射机制

## 反射机制



![image-20191107172016499](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107172016499.png)

![](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107172212569.png)

![image-20191107190600612](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107190600612.png)

==com.bjsxt.test.User==为字符串，因此可以传入不同的类

## Class对象获取

 ![image-20191107190936792](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107190936792.png)

> 同一个类，只会被加载一次。加载一次之后会形成一个class对象。一个类只有一个对象。
>
> ```java
> try {
>             Class clazz = Class.forName("com.zx.bean.User");
>             System.out.println(clazz.hashCode());
> 
>             Class clazz2 = Class.forName("com.zx.bean.User");
>             System.out.println(clazz2.hashCode());
>             //两次hash值一样，表示每次都是同一个对象。
>         } catch (Exception e) {
>             e.printStackTrace();
>         }
> ```

​		对象是表示或封装一些数据。一个类被加载后，JVM会创建一个对应该类的Class对象，类的整个结构信息会被放到对应的Class对象中。

​		这个Class对象就像一面镜子，通过这面镜子可以看到类的全部信息。

```java
        int[] arr01 = new int[10];
        int[] arr02 = new int[30];
        System.out.println(arr01.getClass().hashCode());
        System.out.println(arr02.getClass().hashCode());
```

输出的相同，表明为同一个对象，和长度无关，和维数以及类型有关。

```java
that is shared by all arrays with the same element type and number* of dimensions. 
```

### 获取Class类的对象：

1. 运用getClass();
2. 运用Class.forName()；
3. 运用.class语法。

### 反射机制的常见作用

1. 动态加载类、动态获取类的信息（属性、方法、构造器）
2. 动态构造对象
3. 动态调用类和对象的任意方法、构造器
4. 动态调用和处理属性
5. 获取泛型信息
6. 处理注解



## 动态操作-构造器-方法-属性

### 获取类的名字

```java
            String path = "com.zx.bean.User";
            Class clazz = Class.forName(path);
            System.out.println(clazz.getName());//com.zx.bean.User
            System.out.println(clazz.getSimpleName());//User
```

### 获得属性信息

1. ` Field[] fields = clazz.getFields(); `   ——————》只能获得public的field
2. ` Field[] fields = clazz.getDeclaredFields();` ——————》获得所有的Field
3. `Field f = clazz.getDeclaredField("uname")`——————》获取指定的Field

### 获得方法信息

1. `Method[] methods = clazz.getMethods(); `   ——————》只能获得public的field
2. `  Method[] declaredMethods = clazz.getDeclaredMethods();` ——————》获得所有的Field
3. `Method getUname = clazz.getDeclaredMethod("getUname", null);`——————》获取指定的Field，后边的null可以填写不同参数以应对方法重载。

### 获得构造器信息

1. `Constructor[] constructors = clazz.getConstructors();`
2. `Constructor[] declaredConstructors = clazz.getDeclaredConstructors();`
3. `Constructor user = clazz.getDeclaredConstructor(null);`//无参构造器
4. `Constructor user1 = clazz.getDeclaredConstructor(int.class,int.class,String.class);`//有参构造器

### 动态操作构造器

##### 通过反射API调用构造方法

​	通过反射API调用构造方法构造对象

```java
 try {
            String path = "com.zx.bean.User";
            Class<User> clazz = (Class<User>) Class.forName(path);

            //通过反射API调用构造方法，构造对象
            User u = clazz.newInstance();//其实是调用了User的无参构造方法，因此在javabean中必须加一个无参的构造器
            System.out.println(u);
            Constructor<User> c = clazz.getDeclaredConstructor(int.class,int.class,String.class);
            User u1 = c.newInstance(1001,1,"张晓1122");
            System.out.println(u1.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }
```

##### 通过反射API调用普通方法

```java
            User u2 = clazz.newInstance();
            Method method = clazz.getDeclaredMethod("setUname", String.class);
            method.invoke(u2,"zZZ");
            System.out.println(u2.getUname());
```

其中第二三行代码相当于执行了一个 `u2.setUname("zZZ");`，好处是可以动态调用，方法名作为参数可以传过来，数据也可以作为参数传过来。

##### 通过反射API操作属性

```java
        User u3 = clazz.newInstance();
        Field f = clazz.getDeclaredField("uname");
        f.setAccessible(true);
        f.set(u3,"qqq");
        System.out.println(u3.getUname());
        System.out.println(f.get(u3));//通过反射读取属性的值
```

如果不写`f.setAccessible(true)`，就不能访问private属性。写上以后表示这个属性不需要做安全检查了，可以直接访问。

## 提高反射效率-操作泛型-操作注解

### 反射机制性能问题

![image-20191107214207169](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107214207169.png)



### 反射操作注解

```java
Class clazz= Class.forName("com.ouc.Student");
            //获得类的所有有效注解
            Annotation[] annotations = clazz.getAnnotations();
            for (Annotation anno:
                 annotations) {
                System.out.println(anno);
            }
            //获得类的指定注解
            MyTable myTable = (MyTable) clazz.getAnnotation(MyTable.class);
            System.out.println(myTable.value());

            //获得类的属性的注解
            Field studentName = clazz.getDeclaredField("studentName");
            MyFiled myFiled = studentName.getAnnotation(MyFiled.class);
            System.out.println(myFiled.columnName()+"--"+myFiled.type()+"--"+myFiled.length());

            //拼接ql语句，使用JDBC发送语句
```

### 反射操作泛型

![image-20191107215139617](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191107215139617.png)

两个方法：

```java
 public void  test01(Map<String,User> map, List<User> list){
        System.out.println("Demo04.test01()");
    }
    public Map<Integer,User> test02(){
        System.out.println("Demo04.test02()");
        return null;
    }
```

获得指定方法参数泛型信息

```java
 Method m = Demo04.class.getMethod("test01", Map.class, List.class);
            Type[] t = m.getGenericParameterTypes();
            for (Type paramType:  t) {
                System.out.println("#"+ paramType);
                if(paramType instanceof ParameterizedType){
                    Type[] genericTypes = ((ParameterizedType) paramType).getActualTypeArguments();//获得真正的参数类型
                    for (Type genericType:
                         genericTypes ) {
                        System.out.println("泛型类型"+genericType);
                    }
                }
            }
```

获得指定方法返回值泛型信息

```java
 Method m2 = Demo04.class.getMethod("test02",null);
            Type returnType = m2.getGenericReturnType();
            if(returnType instanceof ParameterizedType){
                Type[] genericTypes = ((ParameterizedType) returnType).getActualTypeArguments();//获得真正的参数类型
                for (Type genericType:
                        genericTypes ) {
                    System.out.println("泛型类型"+genericType);
                }
            }
```

# 四、动态编译

![image-20191108145548274](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191108145548274.png)

![image-20191108145603079](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191108145603079.png)

### 通过JavaCompiler动态编译：

```java
JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
int run = compiler.run(null, null, null, "C:/video/HelloWorld.java");
System.out.println(run == 0?"编译成功！":"编译失败");
```

**实现编译String类型数据：**通过IO流操作，将字符串存储成一个临时文件（Hi.java）,然后调用动态编译方法



### 通过getRuntime()动态执行代码

此处注意安全问题！！

1、通过Runtime.getRuntime()运行启动新的进程运行

```java
        Runtime run1 = Runtime.getRuntime();
        Process exec = run1.exec("java -cp c:/video HelloWorld");//此时已经执行完成了

		//以下是获得System.out.println的输出的信息
        InputStream in = exec.getInputStream();
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(in));
        String info = null;
        while((info = bufferedReader.readLine())!= null){
        System.out.println(info);
        }
```

2、通过反射运行编译好的类

```
  try{
            URL[] urls = new URL[]{new URL("file:/"+"C:/video/")};
            URLClassLoader urlClassLoader = new URLClassLoader(urls);
            //调用加载类的main方法
            Class helloWorld = urlClassLoader.loadClass("HelloWorld");
            Method m = helloWorld.getMethod("main",String[].class);
            //由于可变参数是JDK5.0以后才有的，下面代码会编译成：m.invoke(null,"aa","bb")就会发生参数个数不匹配的问题
            //因此必须加上（Object）转型，避免这个问题
            m.invoke(null,(Object)new String[]{"aa","bb"});

        }catch (Exception e){
            e.printStackTrace();
        }
```

# 五、脚本引擎执行JavaScript代码

![image-20191108171550769](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191108171550769.png)

![image-20191108172443456](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191108172443456.png)

#### 定义脚本 

```java
  public static void main(String[] args) throws  Exception {
        //获得脚本引擎对象
        ScriptEngineManager sem = new ScriptEngineManager();
        ScriptEngine engine = sem.getEngineByName("javascript");

        //定义变量，存储   到引擎上下文中
        engine.put("msg","zxzxzxz");
        String str = "var user = {name:'zx',age:18};";
        str += "print(user.name);";
        engine.eval(str);
    }
```





#### 定义函数：

```java
        //定义函数
        engine.eval("function add(a,b) {var sum = a+b;return sum;}");
        //取得调用接口
        Invocable jsInvoke = (Invocable) engine;
		//执行脚本中定义的方法
        Object add = jsInvoke.invokeFunction("add", new Object[]{13, 20});
        System.out.println(add);
```



#### 使用其他包中的Java类