# Docker 

开发自运维（DevOps）

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200319203307549.png" alt="image-20200319203307549" style="zoom:50%;" />

你今天的优势会被以后的趋势所取代///勿在浮沙之上筑高台

\> centos 6.5

##### Docker三要素： 

仓库：

镜像：就是只读的模板，镜像可以用来创建Docker容器，一个镜像可以创建很多容器。=== java中的类

容器：就是镜像的一个或者一组实例。容器是用镜像创建的运行实例。java中一个个实例化的对象。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个筒易版的 Linux环境(包括root用户权限、进程空间、用户空间和网络空间等)和运行在其中的应用程序。容器的定义和镜像几乎一模一样,也是一堆层的统一视角,唯一区别在于容器的最上面那一层是可读可写的。

仓库( Repository)是集中存放镜像文件的场所。仓库( Repository)和仓库注册服务器( Registry)是有区别的。仓库注册服务器上往往存放着多个仓库,每个仓库中又包含了多个镜像,每个镜像有不同的标签(tag)。

 仓库分为公开仓库( Public)和私有仓库( Private)两种形式。最大的公开仓库是DockerHub(https://hub.docker.com/)存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等



###### Docker CE 版： Community Edition 社区版

启动docker：**systemctl start docker**

使用**docker  info** 查看docker信息，其中包含加速器信息

![image-20200320112657972](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200320112657972.png)

---

## Docker常用命令

#### 帮助命令

1. docker version
2. docker info
3. docker --help

#### 镜像命令

docker images

docker images -a 显示所有镜像

docker images -q 只显示imageid

docker images -qa 组合使用

docker images --digests 显示镜像的摘要信息

docker images --no-trunc 显示完整的镜像信息

docker  search 某个镜像的名字  

​			   网站https://hub.docker.com

​				docker search[ OPTIONS] 镜像名字

​				docker search -s 30 tomcat 

​		OPTION说明:

​			\-- no- trunc:显示完整的镜像描述命令		

​			~~-s:列出收藏数不小于指定值的镜像。:~~  已经被弃用，新的方式为**--filter=stars=100 ** :laughing:

​		   --automated:只列出 automated build类型的镜像

docker pull tomcat  == docker pull tomcat:latest

![image-20200320134657424](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200320134657424.png)

docker rmi -f 镜像id    **-f**   表示强制删除

docker rmi -f hello-world nginx : 同时删除多个镜像

docker rmi -f $(docker images -q):先查询出来，然后执行删除命令，删除的是刚刚查询出的内容

#### 容器命令

有镜像才能创建容器，这是根本前提

1. 新建并启动容器  **docker run [options] IMAGE 

​			OPTIONS说明(常用):有些是一个减号,有些是两个减号

​			--name="容器新名字"为容器指定一个名称

​			-d:后台运行容器,并返回容器ID,也即启动守护式容器;

​			**-i以交互模式运行容器,通常与同时使用;**

​			**-t:为容器重新分配一个伪输入终端,通常与-i同时使用;**

​			-P:随机端口映射;

​			-p:指定端口映射,有以下四种格式

​			ip: hostPort: containerPort

​			ip: containerPort

​			hostPort: container Port

​			containerPort

1.  docker ps [OPTION] 查看docker当前**当前正在运行**全部进程

   OPTIONS说明(常用)​ 

   -a:列出当前所有正在运行的容器+历史上运行过

   -l:显示最近创建的容器。

   -n:显示最近n个创建的容器。

   **-q:静默模式,只显示容器编号。**

   -no- trunc:不截断输出。

2. 退出容器

   1. exit 容器停止并退出
   2. ctrl + P + Q 容器不停止退出

3. 启动容器  **docker start  容器id**

4. 重启容器 **docker restart 容器id**

5. 停止容器**docker stop 容器id|| 容器名**

6. 强制停止容器 **docker  kill 容器id || 容器名**

7. 删除已经停止的容器   

   1. **docker rm [OPTION]容器id**  
      1. OPTION  ：-f   停止然后删除，即强制删除
   2. 一次性删除多个容器
      1. docker rm -f $(docker ps -a -q) ||   <font color=red>docker stop $(docker ps -q)</font>
      2. docker ps -a -q | xargs docker rm

###### 重要:warning::warning::warning:

1. 使用**docker run -d centos**,以守护方式启动，但是这种方式启动，使用`docker ps`并不会显示刚刚启动的进程。

   1. #使用镜像 centos: lates以后台模式启动一个容器

      docker run -d centos

      问题:然后 docker ps -a进行查看,会发现容器已经退出

      很重要的要说明的一点: Docker容器后台运行,就必须有一个前台进程

      容器运行的命令如果不是那些一直挂起的命令(比如运行top,tai),就是会自动退出的。

      这个是 docker的机制问题,比如你的web容器我们以 ngInX为例,正常情况下,我们配置启动服务只需要启动响应的 service即可。例如service nginx start

      但是,这样做, nginx为后台进程模式运行,就导致 docker前台没有运行的应用,

      这样的容器后台启动后,会立即自杀因为他觉得他没事可做了

      所以,最佳的解决方案是,将你要运行的程序以前台进程的形式运行


可以使用sh脚本方式使服务一直运行：*docker run -d centos /bin/sh -c "while true; do echo hello world;sleep 2;done"*

---

1. 查看容器日志

   1. `docker logs -f -t --tail 容器id `  
      1. -t是加入时间戳 
      2. -f 时随最新的日志打印
      3. --tail数字  显示最后多少条
   
2. 查看容器内运行的进程：*docker top 容器id*

3. 查看容器内部细节  ：*docker inspect 容器id*

4. 进入正在运行的容器并以命令行交互：
   1. *docker exec -it  容器id bashShell* 在容器中打开新的终端，并且可以启动新的进程，bashshell即直接执行命令，但是当前命令行不会进入到容器内。隔docker打centos
   
      其中，**`docker exec -t  容器id /bin/bash* == docker attach 容器id`**
   
   2. *docker attach 容器id*  直接进入容器启动命令的终端，不会启动新的进程
   
5. 从容器内拷贝文件到主机上     *docker cp 容器id：容器内路径 目的主机路径*

---

docker run -it -p 8888:8080 tomcat   使docker中的tomcat以端口8080方式启动，但是对于宿主机来说，tomcat的端口是8888

docker run -it -P  tomcat   使docker中的tomcat以端口8080方式启动，但是对于宿主机来说，tomcat的端口是*随机分配的*

docker run -d -p 6666:8080 tomcat  在端口6666以后台方式启动

---

## Docker 镜像

#### 是什么

镜像是一种轻量级、可执行的独立软件包,用来打包件运行环境和基于运行环境开发的软件,它包含运行某个软件所需的所有内容,包括代码、运行时、库、环境变量和配置文件。

1. UnionFS 联合文件系统

2. Docker镜像加载原理

3. 分层的镜像

4. 为什么Docker镜像要采取分层结构

   最大的一个好处就是-共享资源比如:有多个镜像都从相同的base镜像构建而来,那么宿主机只需在磁盘上保存一份base镜像

   同时内存中也只需加载一份base镜像,就可以为所有容器服务了。而且镜像的每一层都可以被共享。

5. 为什么tomcat这么大（500M+），老千层饼。只有tomcat是可读写，内层封装完成，只能读不能写。

   <img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200320221616191.png" alt="image-20200320221616191" style="zoom:50%;" />

#### 特点

#### Docker镜像commit操作补充

docker commit 提交容器副本使之成为一个新的镜像

docker commit -m=”提交的描述信息“ -a ="作者" 容器id 要创建的目标镜像名：[标签名]

​		**启动时使用 docker run atzx/mytomcat:1.0 **

## Docker 容器数据卷

#### 是什么

先来看看 Docker的理念

*将运用与运行的环境打包形成容器运行,运行可以伴随着容器,但是我们对数据的要求希望是持久化的

*容器之间希望有可能共享数据

Docker容器产生的数据,如果不通过 docker commit生成新的镜像,使得数据做为镜像的一部分保存下来,那么当容器删除后,数据自然也就没有了。

为了能保存数据在 docker中我们使用卷。

#### 能干嘛

卷就是目录或文件,存在于一个或多个容器中,由 docker挂载到容器,但不属于联合文件系统,因此能够绕过 Union File System提供一些用于持续存储或共享数据的特性:

卷的设计目的就是数据的持久化,完全独立于容器的生存周期,因此 Docker不会在容器删除时删除其挂载的数据卷特点:

1:数据卷可在容器之间共享或重用数据

2:卷中的更改可以直接生效

3:数据卷中的更改不会包含在镜像的更新中

4:数据卷的生命周期一直持续到没有容器使用它为止



*容器的持久化*

*容器间继承+共享数据*

#### 数据卷

容器内添加: 

1. 直接命令添加：docker run -it -v /myVolume:/dataVolume[<font color = red>:ro</font>] centos
2. dockerFile添加   
3. 



#### 数据卷容器

docker run -it --name yourname  centos  设置容器名字为yourname





执行以下命令：

```
 docker run -it --name doc1  mycentos 
 
		cd dataVolumeContainer2 
		vi doc1.txt

 docker run -it --name doc2--volumes-from doc1 mycentos 
 		cd dataVolumeContainer
 		ls 
```

执行完第八行会看到doc1.txt，此时如果早doc2容器中创建一个doc2.txt，那么在doc1容器中也可以看到

1. 若执行

```
docker run -it --name doc3--volumes-from doc2 mycentos 
```

那么也可以在doc3中看到文档，这些文档是共享的，即doc1 doc2 doc3的dataVolumeContainer2都是一样的

​	2.若删除doc01，doc02修改后doc03可以访问

​	3.新建doc04继承自doc03，删除doc03后，doc04一样可以访问

结论：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止

## DockerFile文件解析

scratch == java中的object  ，所有类的祖先类

CMD  和entrypoint 区别：cmd可以有多个命令，但只有最后一个生效，cmd会被docker run之后的参数替换，而entrypoint可以执行多个命令

<font color=red>倒序执行</font> 先执行FROM scratch



CMD 会被docker run之后的参数替换掉



ENTRYPOINT 不会被替换，docker run之后的参数会被当做参数传递给ENTRYPOINT，之后形成新的命令组合

<font color=green>使用docker build时，如不加-f 那么就会默认使用当前目录下的Dockefile</font>




