如果识别为Springboot应用，控制台会显示彩色字样。



如果application.properties和application.yml都有，优先使用application.properties

#### 读取yml配置文件的例子：

首先创建两个javaBean对象，Person 和 Dog 类：

```java
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private List<Object> lists;
    private Map<String,Object> maps;
    private Dog dog;
    //忽略get set 和toString方法
}

public class Dog {

    private String type;
    private String name;
}
```



编写yml文件：

```yml
person:
  name: zhangxiao
  lists:
    - 1
    - 2
    - 3
  maps:
    yinhang: 1000
    xianjin: 10000000
  dog:
    type: alysika
    name: hu
```

或使用properties方式：

```properties
person.name=zhangsan
person.maps.k1=123
person.maps.k2=456
person.lists=1,2,3
person.dog.name=titi
person.dog.type=alysika
```

注意properties乱码问题，选中右下角的UTF-8并勾选Transparent native-to-ascaii conversion

![image-20200418174204822](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200418174204822.png)



导入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

启动一次main程序，使依赖生效。然后在测试中添加如下代码：

```java
@RunWith(SpringRunner.class)//使用Spring的测试方式
@SpringBootTest
public class ConfigApplicationTests {
    @Autowired//将Person组件自动装配IOC容器
    Person person;
    @Test
    public void contextLoads() {
        System.out.println(person);
    }
}
```

#### 加载指定的配置文件

@PropertySource(value="classpath:person.properties") 其中 person.properties位于 resources文件夹下

#### 配置文件占位符

```properties
person.name=张晓${random.uuid}
person.maps.k1=123
person.maps.k2=456
person.lists=1,2,3
# 如果把person.name 注掉，那么就会报错，因为在Person中需要保证每个属性在这里边都有
# 通过${person.name} 获取之前配置的值
person.dog.name=${person.name}titi 
# 获取之前配置的值，如果获取不到，那么就是用默认的值 张思睿
person.dog.type=${personHello:张思睿}_alysika
```

#### 切换环境

1、通过配置多个profile文件，文件名可以使 application-{profile}.properties/yml 默认使用 application.properties

2、通过多文档块方式

```properties
server:
  port: 8088
spring:
  profiles:
    active: dev
---
# 通过spring：  profiles设置名称    
server:
  port: 8089
spring:
  profiles:prod
---
server:
  port: 8090
spring:
  profiles: dev
```

​		1、 通过 spring: profiles: active 选择生产环境

​		2、通过命令行 --spring.profiles.active=dev 方式

​		![image-20200418215926084](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200418215926084.png)

​		3、直接通过命令行 java -jar application.jar --spring.profiles.active=dev 即拼接的方式选择生产环境

​		4、通过设置虚拟机参数： -Dspring.profiles.active=dev 激活

![image-20200418220608756](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200418220608756.png)

#### 配置文件的加载位置

​	file:./config/  

​	file:./                           ()

​	classpath:/config/       (即resources/config路径下)

​	classpath:/                  (即resources路径下)

​	以上优先级<font color="red">由高到低</font>，sb会从这四个位置全部加载主配置文件；会形成互补配置。   





