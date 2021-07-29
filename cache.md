华夏ERP

部署到本地步骤：

1. git clone https://gitee.com/jishenghua/JSH_ERP.git
2. idea中打开
3. 同步maven仓库
4. 运行ErpApplication

本地部署华夏ERP中存在的问题

1. 当同步maven仓库时，发现plugin报错：Cannot resolve plugin org.apache.maven.plugins:maven-assembly-plugin:2.5.1

   原因：idea中maven配置文件和仓库地址不是同一个导致报此错误。

2. 本地idea设置发现和之前不一致

   可能原因：可能是因为在其他机器上登录过，将其他机器的配置给同步了过来，所以导致这个原因。此原因有待进一步验证。

注意事项：

启动完成后，发现启动地址并非localhost，而是http://169.254.98.124/，发现该地址是本机外网ip地址，通过ComputerInfo.getIpAddr()进行获取的。







## Docker file

docker file 相当于自定义镜像。并且可以在其中调用各种命令。

通过docker build -f dockerfile文件目录 构建镜像，可以在本地仓库生成相应的镜像文件。

### dockerfile的四个组成部分

1. 基础镜像信息
2. 维护者信息
3. 镜像操作命令
4. 容器启动命令

### 基础镜像

相当于java中的导包，表示引入了什么原始镜像，后续需要对这些镜像进行进一步的加工。

语法：

```
FROM  基础镜像名称
```

### 维护者信息

表示作者是谁

语法：

```
MAINTAINER 作者名
```

### 镜像操作命令

run：执行命令

add: 将本地文件添加到容器中，tar会自动解压，网络压缩资源不会解压

copy: 类似add，但是不会自动解压，也不能访问网络资源

env: 设置环境变量，支持kv键值对。

expose: 指定与外界的交互端口

volume: 持久化的目录

workdir: 工作目录

### 容器启动命令

cmd: 容器启动时执行的命令



dockerfile 也就是镜像所有的操作打包，相当于java的一个方法，只需要调用这个自定义的docker镜像就可以实现一系列的功能。







## 数据库设计规范

基本原则：在满足最大的长度的情况下，适当使设计时的长度扩大20%-50%。

如一个字段，已知它在实际中最大长度为15，在设计数据库时，可以将长度设置为20-25。

详细规范如下：

1. 手机号使用varchar类型。
2. 编码类型一般为varchar，长度控制在50以内。
3. 注意key-value中可能存放json，长度可以设置的较大一些。
4. 设置状态字段时（如：0表示未开始，1表示开始，2表示结束），设置为tinyint，根据实际情况，长度设置为2或者4
5. 为避免撞库，主表与详细内容表之间不要通过主键关联，而是通过流水号关联。
6. 除非是用在金额上，尽量减少decimal的使用
7. 经纬度字段类型使用varchar，`东经121.506377,北纬31.245105`,经纬度长度14。
8. 小数使用float
9. 当在数据库中使用的是bigint时，生成的是bigdecimal，导出到excel中表示有问题，如（10E000112），导致精度丢失
10. 金额表示为int，如果是10.05，则表示为1005。
11. 接上一条，可以在其中新增一个单位字段，以后可以表示美元等，便于国际化
12. 在每个表中，推荐新增以下五个字段
    1. create_user：创建人
    2. create_time：创建时间
    3. update_user：最后一次更新人
    4. update_time：最后一次更新时间
    5. desc：备注





### 背景：

公司采用Nexus来进行maven仓库管理，但是是部署在服务器上的，外网无法访问。目前想到两个解决办法，一个是通过内网穿透，但是公司路由器连接的还是公司大楼的内网，需要公司大楼开放端口来提供映射。二是在本地搭建nexus，将公司服务器的nexus数据复制到本地。由于是自己使用，所以还是采用第二种在本地搭建的方式。

### 〇、准备工作：

1. 要备份的nexus的账号密码；

2. nexus安装包，最好是与要备份的版本一致；
3. 关闭新nexus。

## 一、导出原数据 && 导入

1. 导入bak文件 

   在Nexus的管理页面选择System->Tasks,然后选择Create task，选择 Admin- Export databases for backup ，在name中输入Task的名字，然后backUp location中输入备份文件的地址，可以填写相对路径（相对于sonatype-work/nexus的地址），使用/ （之前尝试使用windows的绝对路径，报错），然后选择频率Manual，点击保存，回到Tasks页面，选择myBackUp，然后点击run，待执行完毕，将生成的bak文件复制到sonatype-work/nexus下的restore-from-backup即可。

   ![image-20210109204922488](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20210109204922488.png)

2. 导入blobs

   删除新nexus 中sonatype-work/nexus/blobs里的全部内容，将原nexus中blobs的复制进去。

3. 删除原始部分数据

   删除sonatype-work/nexus/db中的accesslog、analytics、audit、component、config、security文件夹。

## 二、重启新nexus

使用 nexus /run 启动nexus完毕



小结：感觉备份还是比较简单的，没之前想象的那么麻烦，很多时候都是自己想多了……但是有的时候就会想的不够，很矛盾……

## idea启动项目报错'@' that cannot start any token. (Do not use @ for indentation)

原因1： 在@字段前后加上 '

```yml
active: '@profileActive@'
```

原因2：未被idea识别，右键pom文件，reimort即可



注：idea 如何确定使用了那个yml？

​	1、 通过 spring: profiles: active 选择生产环境，它的值可以使用注解方式：

```yml
spring:
  profiles:
    active: @profileActive@
    
```

在pom中设置默认

```xml
<profiles>
        <profile>
            <id>dev</id>
            <properties>
                <!-- 环境标识，需要与配置文件的名称相对应 -->
                <profileActive>dev</profileActive>
            </properties>
            <activation>
                <!-- 默认环境 -->
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <profileActive>prod</profileActive>
            </properties>
        </profile>
    </profiles>
```



​	2、通过命令行 --spring.profiles.active=dev 方式

![image-20200418215926084](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200418215926084.png)

​		3、直接通过命令行 java -jar application.jar --spring.profiles.active=dev 即拼接的方式选择生产环境

​		4、通过设置虚拟机参数： -Dspring.profiles.active=dev 激活

![image-20200418220608756](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200418220608756.png)

在application.yml中输入：

```yml
spring:
  profiles:
    active: @profileActive@
```

在pom.xml中输入：

```xml
    <profiles>
        <profile>
            <id>dev</id>
            <properties>
                <!-- 环境标识，需要与配置文件的名称相对应 -->
                <profileActive>dev</profileActive>
            </properties>
            <activation>
                <!-- 默认环境 -->
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <profileActive>prod</profileActive>
            </properties>
        </profile>
    </profiles>

```

# WordPress备份

### 背景：

服务器到期了，貌似有学生免费续费，但是需要服务器到期后才能续费，担心到时候会有各种条件，因此先把博客数据导出来。

WordPress 版本5.3.2

## 步骤

1. 在仪表盘页面，选择工具，导出，所有内容，下载。这个只是包含索引，不包含图片。
2. 在新空间安装同版本的wp，登录后选择导入，开始恢复备份
3. 将之前的图片上传到新的空间。

为了防止数据丢失，还把数据导出来了，同时把wp的整个安装目录压缩下载了下来。

linux 将目录打包成压缩包命令：

```shell
tar -zcvf /www.tar.gz /var/www
```

其中 /www.tar.gz 为压缩包名称，表示存放在根目录下， /var/www表示将 /var/www目录打包





抽象类：

1. 抽象类不能实例化，只能继承
2. 抽象类的继承类需要实现抽象类的所有抽象方法，否则需要定义为抽象类
3. 抽象类中可以有实现的方法
4. 抽象类的引用可以指向子类的实例

接口：

1. 接口中所有方法都是抽象方法，但是1.8以后可以有static 和 default 方法
2. 接口的变量都是常量
3. 接口不能被实例化
4. 接口可以多继承
5. 接口可以多实现
6. 实现接口的类需要实现接口的全部方法，否则需要定义为抽象类
7. 接口的引用指向实现类的实例



















抽象类：

1. 不能被实例化
2. 只能单继承
3. 可以有实现的方法
4. 抽象类的引用可以指向子类的



接口

1. 接口不能被实例化
2. 接口中不能有实现的方法
3. 变量为常量
4. 接口的实现类需要实现接口全部方法，否则只能定义为抽象类
5. 接口可以多实现
6. 接口可以多继承
7. 接口的引用指向实现类的实例



IO 五大模型 同步阻塞  同步非阻塞  IO多路复用  信号驱动  异步IO

Java 常见的三大IO模型

同步阻塞 BIO 

同步非阻塞： 就是应用程序发起read后，内核开始准备数据，准备过程中用户态不断轮询内核态，

IO多路复用：有三个重要部件 selector、channel、buffer 

AIO ：

java 7引入epoll





epoll的水平触发和边缘触发

水平触发：只要满足条件就触发一个事件

边缘触发：状态发生改变就触发一个事件

















抽象类 

1. 不能被实例化
2. 可以有非抽象方法
3. 子类需要实现

接口

不能被实例化

不能有方法的实现，但是JDK1.8

duojicneng 





