1. Spring理解

   ​	让Java开发模块化，并且全面

   ​	Spring通过控制反转降低耦合性，一个对象的依赖通过被动注入而非主动new

   ​	通过代理模式实现了面向切面编程

2. BeanFactory 和ApplicationContext区别：

   1. BeanFactory是Spring最底层的接口，提供了最简单的容器的功能，只提供实例化对象和拿对象的功能；ApplicationContext应用上下文，继承了BeanFactory接口，是Spring的一个更高级别的容器，提供了更多有用的功能：1）、国际化（Message Source）2）、访问资源：如URL和文件，ResourceLoader。3）、载入多个有继承关系上下文，使得每个上下文都专注于一个特定的层次4）、消息发送、响应机制：ApplicationEventPublisher5）、AOP拦截器
   2. 装载Bean区别：BeanFactory:启动时不会实例化。中有容器拿Bean的时候才会实例化；ApplicationContext在启动时就把所有的Bean都实例化，还可以为Bean配置lazy-init=true来让bean延迟实例化

3. Bean的一生

   1. 实例化bean（通过构造方法或者工厂方法）
   2. 设置对象属性（setter)（依赖注入）
   3. 如果Bean实现了BeanNameAware接口，工厂调用Bean的setBeanName方法传递Bean的id
   4. 如果Bean实现了BeanFactoryAware接口，工厂调用Bean的setBeanFactory方法传入工厂自身
   5. 将Bean实例传递给Bean的前置处理器的postProcessBeforeInitialization(Object bean, String beanName)方法
   6. 调用bean的初始化方法
   7. 将Bean实例传给Bean的后置处理器的postProcessAfterInitialization(Object bean, String beanName)方法
   8. 使用bean
   9. 在容器关闭之前，调用bean的销毁方法。

4. Bean作用域

   1. singleton:Spring容器中只存在一个Bean实例，单例模式，是系统默认值

   2. prototype:每次调用都会创建一个Bean，也就是每次getBean()就相当于new Bean()的操作

      ​	prototype的作用域要慎重考虑，因为每次创建和销毁都会造成很大性能开销

      web环境下的作用域

   3. request：每次HTTP请求都会创建一个bean

   4. session：HTTP Session 共享一个Bean实例

   5. global-session:用于portlet容器，因为每个portlet有单独的session，globalsession提供一个全局的http session

5. Spring中单例Beans是否线程安全

   ​		Spring框架并没有对单例bean进行任何多线程的封装处理。关于单例Bean的线程安全和并发问题需要开发者自行解决，但实际上，大部分的Spring bean并没有可变的状态，所以某种程度上Spring的单例bean是线程安全的。如果Bean有多种状态，需要自行保证线程安全，最浅显的办法就是将多态Bean作用域由singleton变为prototype

6. Spring AOP四种方式

   1. 经典的基于代理的AOP
   2. @AspectJ注解驱动的切面
   3. 纯POJO切面
   4. 注入式AspectJ切面

7. Spring 中使用到的

   1. 工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例
   2. 单例模式：Bean默认是单例模式
   3. 代理模式：Spring Aop功能用到了JDK的动态代理和CGLIB字节码生成技术
   4. 模板方法：用于解决代码重复的问题，如RestTemplate，JMSTemplate，JpaTemplate
   5. 观察者模式：定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring中listener的实现---ApplicationListener

8. FileSystemResource和ClassPathResource有何区别

   ​	FileSystemResource在配置文件中读取配置文件

   ​	ClassPathResource在环境变量中读取配置文件

9. AOP中，pointCut为切点，advice为通知，join point 为连接点

10. Join Point 只是切面中可以切的方法，选择的要切的哪些方法就是Point Cut。换句话说，所有在程序中可以被调用的方法都是Join point，只想切两个方法，那么这两个方法就是 Point Cut。Advice是作用到pointcut的方式，可以使用before，after，around（around在before执行之前执行一次，在方法执行结束后再执行一次）

   ​    在一个方法只被一个aspect类拦截时：正常情况、异常情况

   1. <img src="https://img-blog.csdn.net/20160811192425854" alt="one-ok" style="zoom:33%;" />

   2. <img src="https://img-blog.csdn.net/20160811192446479" alt="one-exception" style="zoom:33%;" />

      当一个方法被多个aspect类拦截时，两个切面的执行顺序不定。解决方法：实现org.springframework.case.Ordered接口，实现getOrder()方法(返回值为值)；或者给aspect添加@Order（org.springframework.core.annotation.Order）注解（值越小的切面越先执行）

      多个切面：

      <img src="https://img-blog.csdn.net/20160811193349342" alt="two-ok" style="zoom: 50%;" />

      执行顺序：aspect1的around>aspect1的before>aspect2的around>aspect2的before>方法体>aspect2的around>aspect2的after>aspect2的afterreturning>aspect1的around>aspect1的after>aspect1的afterreturning

      **其中要注意around通知即环绕通知，可以通过proceed()方法控制目标函数是否执行。**

      切面是定义切入点和通知的组合

      把切面应用到目标函数的过程称为织入（weaving）

11. AOP原理：Spring用代理类包裹切面，把它们织入到Spring管理的Bean中。也就是说代理类伪装成目标类，它会截取目标类中方法的调用，让调用者对目标类的调用都先变成调用伪装类，伪装类中就限制行了切面，再把调用转发给真正的目标bean。

    ​	如何伪装不会被调用者发现（过JVM的检查，JAVA是强类型检查）

    1. 实现和目标相同的接口，就逃过了类型检查。兄弟模式，找我办事儿，我弟弟答应下来，并收取了好处（日志等），但是我弟弟无法解决问题，就让我解决问题。但无法解决没有实现接口的问题

    2. 生成子类调用，用子类来做伪装类。父子模式，子类不仅重写了目标类的所有方法，还实现了其他的方法（日志，安全检查，事务等）。与兄弟模式不同的是，子类本身就能解决问题，但是子类解决问题也要收取好处。但有些方法是父类独有的（final），子类无法重写，也就无法解决问题。

       兄弟模式：spring会使用JDK的java.lang.reflect.Proxy类，允许Spring动态生成一个新类实现必要的接口，织入通知，并把对这些接口的任何调用都转发到目标类。

       父子模式：spring会使用CGLIB库生成目标类的一个子类，在创建这个子类的时候，Spring织入通知，并把这个子类的调用委托到目标类。

12. JDK和CGLIB动态

    JDK动态代理：利用拦截器（拦截器必须实现InvocationHandler）加上反射机制生成一个实现代理接口的匿名类。在调用任何具体方法前调用InvokeHandler处理。

    CGLIB动态代理：利用ASM开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

    **何时使用JDK还是CGLIB？**

    1、如果目标对象实现了接口，默认使用JDK的动态代理实现AOP

    2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP

    3、如果目标对象未实现接口，必须使用CGLIB，Spring会自动在JDK代理和CGLIB之间切换

    **强制使用CGLIB实现AOP**

    1、添加CGLIB库（aspect-xxx.jar、aspectweaver-xxx.jar、cglib-nodep-xxx.jar）

    2、在spring配置文件中加入<aop:aspect-autoproxy  proxy-target-class="true">

    **区别**

    1、JDK代理只能针对实现了接口的类生成代理，不能针对类

    2、CGLIB是针对类实现代理，主要是对指定的类生成一个子类覆盖其中的方法，因为采用的是继承，所以最好不要声明为final。对于final，无法继承。

    **使用JDK代理**

    1、实现InvocationHandler

    2、使用Proxy.newProxyInstance产生代理对象

    3、被代理的对象必须要实现接口

    **使用CGLIB**

    CGLIB必须依赖CGLIB的类库，但它需要覆盖其中的方法，是一种继承。

13. Spring框架中有哪些不同类型的事件（Context）

    1. 上下文更新事件（ContextRefreshedEvent）:在调用ConfigurableApplicationContext接口中的refresh被触发
    2. 上下文开始事件（ContextStartedEvent）:当调用CAC接口的start方法开始/重新开始容器时触发
    3. 上下文停止事件（ContextStopEvent）: ……stop()……
    4. 上下文关闭事件（ContextClosedEvent）:当ApplicationContext被关闭时触发。容器被关闭时，其管理的所有Bean都被销毁。
    5. 请求处理事件（RequestHandledEvent）:在web应用中，当一个http请求结束时触发该事件。

14. Spring的IOC设计支持一下功能：

    1. 依赖注入**
    2. 依赖检查
    3. 自动装配
    4. 支持集合
    5. 指定初始化方法和销毁方法
    6. 支持毁掉某些方法

15. IOC容器：主要是完成了对象的创建和依赖的管理注入

    控制反转：把原先代码实现的对象创建、依赖的代码，反转给容器来实现。

    对象和对象的关系：使用xml、properties等语义化配置文件表示

    描述对象关系的文件在哪儿：classpath，filesystem、URL、serverletContext

    如何对不同的配置文件进行解析：需要对不同的配置文件语法，采用不同的解析器

16. Spring循环依赖

    以Setter方式构成的循环依赖，Spring可以自动解决，以构造器方式构成的循环依赖，Spring无法自动解决。Setter注入和构造器注入的区别在创建bean的过程中，Setter注入可以先用无参构造方法返回Bean实例，再注入依赖属性，使用到了Spring的三级缓存，而constructor方法无法返回bean实例，必须先实例化它所依赖的属性，这就会导致死循环。

    解决方法：在使用的比较多的属性上加上@Autowired，在Spring内部处理中，与Setter方法不太一样，但只要先实例化出来，提前暴露，就能解决循环依赖问题。

17. Spring 解决循环依赖：Spring使用了三级缓存解决了循环依赖问题，在populateBean()给属性赋值阶段里Spring会解析属性，并且赋值，当发现A对象依赖了B，就会走getBean方法，但这个时候，可以从缓存中得到。因为在对createBeanInstance对象创建完成后已经放入了缓存中，所以创建B的时候发现依赖A，直接从缓存中拿，此时B创建完，A也创建完，至此Bean创建完成，最后将创建好的Bean放入单例缓存池中。

18. Bean创建的核心三个方法

    1. createBeanInstance：实例话，调用对象的构造方法实例化对象
    2. populateBean:填充属性，主要是Bean的依赖属性进行注入（@Autowired）
    3. initializeBean：回到一些形如initMethod、InitializingBean等方法。

19. 三级缓存：

    1. SingletonObjects：第一级单例缓存池，用于存放完全初始化好的Bean，从该缓存中取出的Bean可以直接使用
    2. earlySingletonObjects：第二级，提前曝光的单例对象Cache，存放原始的Bean对象（尚未填充属性的Bean）
    3. SingletonFactories：第三级单例对象工厂缓存，单例对象工厂的Cache，存放Bean工厂对象。
    
   12. IOC实现机制：工厂模式 + 反射机制

   13. @Autowired和@Resource 区别

          1. @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在
          2. @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的Bean才会按照类型装配注入。

   14. Spring IOC启动过程

          1. 读取bean配置信息
          2. 根据bean注册表实例化Bean
          3. 将Bean实例放到容器中
          4. 使用Bean
       
   15. @Autowired 和@Resource区别

          1. @Autowired 是bytype的，默认要求依赖对象必须存在，如果允许依赖对象为空，可以设置required  = false，如果要求通过@Autowired进行按名称注入，需要添加@Qualifier("beanName")注解
          2. @Resource 默认按照name进行注入的
