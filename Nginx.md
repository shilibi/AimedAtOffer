# 简介

#### 什么是Nginx？

​		是一个高性能的HTTP和反向代理服务器，特点是占有内存少，并发能力强。支持50000并发数。

支持热部署，支持7*24不间断运行。支持在不间断服务的情况下，对软件版本进行升级。

#### 反向代理：

###### 	正向代理：

​		在客户端（浏览器）配置代理服务器，通过代理服务器进行互联网访问。VPS???

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315191654386.png" alt="image-20200315191654386" style="zoom:50%;" />

###### 	  反向代理：

​			反向代理,其实客户端对代理是无感知的,因为客户端不需要任何配置就可以访问,我们只需要将请求发送到反向代理服务器,由反向代理服务器去选择目标服务器获取数据后,在返回给客户端,此时反向代理服务器和目标服务器对外就是一个服务器,暴露的是代理服务器地址,隐藏了真实服务器IP地址

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315192157596.png" alt="image-20200315192157596" style="zoom: 50%;" />

#### 负载均衡：

​		增加服务器的数量，将请求分发到各个服务器上，将原来请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载发到不同的服务器上，也就是我们所说的负载均衡。

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315193213959.png" alt="image-20200315193213959" style="zoom: 50%;" />

#### 动静分离：

​		将动态和静态页面分别用不同的服务器来解析，加速解析速度，降低原来单个服务器的压力

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315193803027.png" alt="image-20200315193803027" style="zoom:45%;" />

## 常用命令

前提：必须要进入到Nginx目录下  cd  /usr/local/nginx/sbin

1. 查看版本号： ./nginx -v

   ![image-20200315195942880](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315195942880.png)

2. 启动

    	 ``` ./nginx```

3. 关闭

   ​       ```./nginx -s stop``` 强制退出   ```./nginx -s quit```  普通退出

4. 重启

   ​	```./nginx -s reload```

## Nginx 配置文件

位置： /usr/local/nginx/conf/nginx.conf

nginx配置文件有三部分组成

1. 全局块

   ​	配置服务器整体运行的配置指令.

   ​		```		worker_processes  1;```

   ​		这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约  

2. events块

   ```
   events{
   	worker_connections 1024;
   }
   ```

   

   ​	影响 Nginx 服务器与用户的网络连接
   ​	events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 wordprocess 可以同时支持的最大连接数等  

3. http块

   Nginx服务器配置中最频繁的部分

   1. http全局块

      ​		http 全局块配置的指令包括文件引入、 MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。  

   2. server块

      ​		这块和**虚拟主机**有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。
      1、全局 server 块
      最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。
      2、 location 块
      一个 server 块可以配置多个 location 块。
      这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。  

   ## 反向代理1

   1、在linux安装tomcat，使用默认端口号8080

   2、访问过程

   ![image-20200315202814353](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315202814353.png)

   3、具体配置

   ​	1）、在windows系统中的host文件中进行域名和ip对应关系的配置

   ​				路径：**C:\Windows\System32\drivers\etc\hosts**	

   ​	2）、修改nginx.conf的配置文件

   ```
           listen       80;
           server_name  192.168.21.190;//如果访问的这个ip地址
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
           location / {
               root   html;
               proxy_pass http://127.0.0.1:8080;//就转发到这个地址和端口号上
               index  index.html index.htm;
           }
   ```


# 反向代理2

访问http://127.0.0.1:9001/edu/ 直接跳转到127.0.0.1:8080

访问http://127.0.0.1:9001/vod/ 直接跳转到127.0.0.1:8081

第一台：8015、8081、8843,8109

第二台：8025、8082、8943，8209

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315230538654.png" alt="image-20200315230538654" style="zoom: 80%;" />

![image-20200315231234598](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200315231234598.png)

# 负载均衡

在反向代理的基础上，将tomcat8082下的vod改成edu，即保持两个tomcat/webapps里的文件夹名一致。

然后修改nginx.conf，在http块中新增：

```xml
upstream myserver {#即tomcat服务器地址和端口号
		server  192.168.21.190:8081;
		server  192.168.21.190:8082;
}
```

并在http中的server块中修改servername：改成nginx所在的服务器ip地址，在location中新增**proxy_pass   http://myserver**

重启nginx，在浏览器输入nginx ip地址/edu/a.html

完毕。

### 分配服务器策略

1、轮询：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

2、weight：weight代表权，重默认为1，权重越高被分配的客户端越多。

3、ip_hash：在upstream中加上**ip_hash**  ；每个请求按照访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session问题。

4、fair（第三方）：在upstream中加上**fair**  ；按后端服务器的响应时间来分配请求，响应时间短的优先分配。

# nginx动静分离

**动静分离**：将动态请求和静态请求分开，可以理解成Nginx处理静态页面，Tomcat处理动态页面。动静分离目前分为两类：

一种是纯粹把静态文件独立成单独的域名,放在独立的服务器上,也是目前主流推崇的方案;

另外一种方法就是动态跟静态文件混合在一起发布,通过 nginx来分开。

通过location指定不同的后缀名实现不同的请求转发。通过 expires参数设置,可以使浏览器缓存过期时间,减少与服务器之前的请求和流量。具体 Expires定义:是给一个资源设定一个过期时间,也就是说无需去服务端验证,直接通过浏览器自身确认是否过期即可,所以不会产生额外的流量。此种方法非常适合不经常变动的资源。(如果经常更新的文件,不建议使用 Expires来缓存),我这里设置3d,表示在这3天之内访问这个URL,发送个请求,比对服务器该文件最后更新时间没有变化,则不会从服务器抓取,返回状态码304,如果有修改,则直接从服务器重新下载,返回状态码200。

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200316131543678.png" alt="image-20200316131543678" style="zoom:50%;" />

###### 目的：提高访问效率

#### 配置：

在centos根目录下新建两个文件夹：www，image。放入网页和图片，并在nginx/conf下修改nginx.conf为：

![image-20200316145759767](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200316145759767.png)

#### 测试

1、输入http://192.168.21.190/image/  页面显示：

![image-20200316145848391](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200316145848391.png)

原因：配置了autoindex on;它会列出文件夹中的内容。

2、输入http://192.168.21.190/www/a.html

# Nginx高可用集群

1、什么是高可用

![image-20200316151059107](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200316151059107.png)

​	1）、需要两台nginx服务器

​	2）、需要keepalived

​	3）、需要虚拟ip

2、使用yum install keepalived –y 安装keepalived

​	使用rpm -q -a keepalived 查看是否安装成功以及版本号

   安装目录在 /etc/keepalived   内有**keepalived.conf**配置文件

4、完成高可用的配置

## 总览

1xx：通知

2xx：操作成功

3xx：重定向

4xx：客户端错误

5xx：服务器错误

# 1XX:

1. 100("Continue") 重要程度：中等，但（写操作时）很少用。
2. 101("Switching Protocols") 重要程度：非常低。

# 2XX:

1. 200("OK" ) 重要程度：非常高。一般来说，这是客户端希望看到的响应代码。它表示服务器成功执行了客户端所请求的动作，并且在2XX系列里没有其他更适合的响应代码了。
2. 201("Created")重要程度：高。当服务器依照客户端的请求创建了一个新资源时，发送此响应代码。
3. 202("Accepted")重要程度：中等。客户端的请求无法或将不被实时处理。请求稍后会被处理。请求看上去是合法的，但在实际处理它时有出现问题的可能。
4. 203("Non-Authoritative Information")重要程度：非常低。这个响应代码跟200一样，只不过服务器想让客户端知道，有些响应报头并非来自该服务器--他们可能是从客户端先前发送的一个请求里复制的，或者从第三方得到的。
5. 204("No Content")重要程度：高。若服务器拒绝对PUT、POST或者DELETE请求返回任何状态信息或表示，那么通常采用此响应代码。服务器也可以对GET请求返回此响应代码，这表明“客户端请求的资源存在，但其表示是空的”。注意与304("Not Modified")的区别。204常常用在Ajax应用里。服务器通过这个响应代码告诉客户端：客户端的输入已被接受，但客户端不应该改变任何UI元素。
6. 205("Reset Content")重要程度：低。它与204类似，但与204不同的是，它表明客户端应重置数据源的视图或数据结构。假如你在浏览器里提交一个HTML表单，并得到响应代码204，那么表单里的各个字段值不变，可以继续修改它们；但假如得到的响应代码205，那么表单里的各个字段将被重置为它们的初始值。从数据录入方面讲：204适合对单条记录做一系列编辑，而205适于连续输入一组记录。
7. 206("Partial Content")重要程度：对于支持部分GET（partial GET）的服务而言“非常高”，其他情况下“低”。

# 3XX

1. 300("Multiple Choices")重要程度：低。若被请求的资源在服务器端存在多个表示，而服务器不知道客户端想要的是哪一个表示时，发送这个响应代码。或者当客户端没有使用Accept-*报头来指定一个表示，或者客户端所请求的表示不存在时，也发送这个响应代码。在这种情况下，一种选择是，服务器返回一个首选表示，并把响应代码设置为200，不过它也可以返回一个包含该资源各个表示的URI列表，并把响应代码设为300。
2. 301("Moved Permanently")重要程度：中等。服务器知道客户端试图访问的是哪个资源，但它不喜欢客户端用当前URI来请求该资源。它希望客户端记住另一个URI，并在今后的请求中使用那个新的URI。你可以通过这个响应代码来防止由于URI变更而导致老URI失效。
3. 302("Found")重要程度：应该了解，特别是编写客户端时。但我不推荐使用它。这个响应代码市造成大多数重定向方面的混乱的最根本原因。它应该是像307那样被处理。实际上，在HTTP 1.0中，响应代码302的名称是”Moved Temporarily”，不幸的是，在实际生活中，绝大多数客户端拿它像303一样处理。它的不同之处在于当服务器为客户端的PUT，POST或者DELETE请求返回302响应代码时，客户端要怎么做。
           为了消除这一混淆，在HTTP 1.1中，该响应代码被重命名为"Found"，并新加了一个响应代码307。这个响应代码目前仍在广泛使用，但它的含义市混淆的，所以我建议你的服务发送307或者303，而不要发送302.除非你知道正在与一个不能理解303或307的HTTP 1.0客户端交互。
4. 303("See Other")重要程度：高。请求已经被处理，但服务器不是直接返回一个响应文档，而是返回一个响应文档的URI。该响应文档可能是一个静态的状态信息，也可能是一个更有趣的资源。对于后一种情况，303是一种令服务器可以“发送一个资源的表示，而不强迫客户端下载其所有数据”的方式。客户端可以向Location报头里的URI发送GET请求，但它不是必须这么做。
           303响应代码是一种规范化资源URI的好办法。一个资源可以有多个URIs，但每个资源的规范URI只有一个，该资源的所有其他URIs都通过303指向该资源的规范URI，例如：303可以把一个对http://www.example.com/software/current.tar.gz的请求重定向到http://www.example.com/software/1.0.2.tar.gz。
5. 304("Not Modified")重要程度：高。这个响应代码跟204("No Content")类似：响应实体主体都必须为空。但204用于没有主体数据的情况，而304用于有主体数据，但客户端已拥有该数据，没必要重复发送的情况。这个响应代码可用于条件HTTP请求（conditional HTTP request).如果客户端在发送GET请求时附上了一个值为Sunday的If-Modified-Since报头，而客户端所请求的表示在服务器端自星期日（Sunday）以来一直没有改变过，那么服务器可以返回一个304响应。服务器也可以返回一个200响应，但由于客户端已拥有该表示，因此重复发送该表示只会白白浪费宽带。
6. 305("Use Proxy")重要程度：低。这个响应代码用于告诉客户端它需要再发一次请求，但这次要通过一个HTTP代理发送，而不是直接发送给服务器。这个响应代码使用的不多，因为服务器很少在意客户端是否使用某一特定代理。
7. 307("Temporary Redirect")重要程度：高。请求还没有被处理，因为所请求的资源不在本地：它在另一个URI处。客户端应该向那个URI重新发送请求。就GET请求来说，它只是请求得到一个表示，该响应代码跟303没有区别。当服务器希望把客户端重新定向到一个镜像站点时，可以用307来响应GET请求。但对于POST，PUT及DELETE请求，它们希望服务器执行一些操作，307和303有显著区别。对POST，PUT或者DELETE请求响应303表明：操作已经成功执行，但响应实体将不随本响应一起返回，若客户端想要获取响应实体主体，它需要向另一个URI发送GET请求。而307表明：服务器尚未执行操作，客户端需要向Location报头里的那个URI重新提交整个请求。

# 4XX

1. 400("Bad Request")重要程度：高。这是一个通用的客户端错误状态，当其他4XX响应代码不适用时，就采用400。此响应代码通常用于“服务器收到客户端通过PUT或者POST请求提交的表示，表示的格式正确，但服务器不懂它什么意思”的情况。

2. 401("Unauthorized")重要程度：高。客户端试图对一个受保护的资源进行操作，却又没有提供正确的认证证书。客户端提供了错误的证书，或者根本没有提供证书。这里的证书（credential）可以是一个用户名/密码，也可以是一个API key，或者一个认证令牌。客户端常常通过向一个URI发送请求，并查看收到401响应，以获知应该发送哪种证书，以及证书的格式。如果服务器不想让未授权的用户获知某个资源的存在，那么它可以谎报一个404而不是401。这样做的缺点是：客户端需要事先知道服务器接受哪种认证--这将导致HTTP摘要认证无法工作。

3. 402("Payment Required")重要程度：无。除了它的名字外，HTTP标准没有对该响应的其他方面作任何定义。因为目前还没有用于HTTP的微支付系统，所以它被留作将来使用。尽管如此，若存在一个用于HTTP的微支付系统，那么这些系统将首先出现在web服务领域。如果想按请求向用户收费，而且你与用户之间的关系允许这么做的话，那么或许用得上这个响应代码。
   注：该书印于2008年

4. 403("Forbidden")重要程度：中等。客户端请求的结构正确，但是服务器不想处理它。这跟证书不正确的情况不同--若证书不正确，应该发送响应代码401。该响应代码常用于一个资源只允许在特定时间段内访问，
   或者允许特定IP地址的用户访问的情况。403暗示了所请求的资源确实存在。跟401一样，若服务器不想透露此信息，它可以谎报一个404。既然客户端请求的结构正确，那为什么还要把本响应代码放在4XX系列（客户端错误），而不是5XX系列（服务端错误）呢？因为服务器不是根据请求的结构，而是根据请求的其他方（比如说发出请求的时间）作出的决定的。

   实体主体：一个描述拒绝原因的文档（可选）。

5. 404("Not Found")重要程度：高。这也许是最广为人知的HTTP响应代码了。404表明服务器无法把客户端请求的URI转换为一个资源。相比之下，410更有用一些。web服务可以通过404响应告诉客户端所请求的URI是空的，然后客户端就可以通过向该URI发送PUT请求来创建一个新资源了。但是404也有可能是用来掩饰403或者401.

6. 405("Method Not Allowd")重要程度：中等。客户端试图使用一个本资源不支持的HTTP方法。例如：一个资源只支持GET方法，但是客户端使用PUT方法访问。

7. 406("Not Acceptable")重要程度：中等。当客户端对表示有太多要求，以至于服务器无法提供满足要求的表示，服务器可以发送这个响应代码。例如：客户端通过Accept头指定媒体类型为application/json+hic，但是服务器只支持application/json。服务器的另一个选择是：忽略客户端挑剔的要求，返回首选表示，并把响应代码设为200。

8. 407("Proxy Authentication Required")重要程度：低。只有HTTP代理会发送这个响应代码。它跟401类似，唯一区别在于：这里不是无权访问web服务，而是无权访问代理。跟401一样，可能是因为客户端没有提供证书，也可能是客户端提供的证书不正确或不充分。

9. 408("Reqeust Timeout")重要程度：低。假如HTTP客户端与服务器建立链接后，却不发送任何请求（或从不发送表明请求结束的空白行），那么服务器最终应该发送一个408响应代码，并关闭此连接。

10. 410("Gone")重要程度：中等。这个响应代码跟404类似，但它提供的有用信息更多一些。这个响应代码用于服务器知道被请求的URI过去曾指向一个资源，但该资源现在不存在了的情况。服务器不知道
    该资源的新URI，服务器要是知道该URI的话，它就发送响应代码301.410和310一样，都有暗示客户端不应该再请求该URI的意思，不同之处在于：410只是指出该资源不存在，但没有给出该资源的新URI。RFC2616建议“为短期的推广服务，以及属于个人但不继续在服务端运行的资源”采用410.

11. 411("Length Required")重要程度：低到中等。若HTTP请求包含表示，它应该把Content-Length请求报头的值设为该表示的长度（以字节为单位）。对客户端而言，有时这不太方便（例如，当表示是来自其他来源的字节流时）。
    所以HTTP并不要求客户端在每个请求中都提供Content-Length报头。但HTTP服务器可以要求客户端必须设置该报头。服务器可以中断任何没有提供Content-Length报头的请求，并要求客户端重新提交包含Content-Length报头的请求。这个响应代码就是用于中断未提供Content-Lenght报头的请求的。假如客户端提供错误的长度，或发送超过长度的表示，服务器可以中断请求并关闭链接，并返回响应代码413。

12. 412("Precondition Failed")重要程度：中等。客户端在请求报头里指定一些前提条件，并要求服务器只有在满足一定条件的情况下才能处理本请求。若服务器不满足这些条件，就返回此响应代码。If-Unmodified-Since是一个常见的前提条件。客户端可以通过PUT请求来修改一个资源，但它要求，仅在自客户端最后一次获取该资源后该资源未被别人修改过才能执行修改操作。若没有这一前提条件，客户端可能会无意识地覆盖别人做的修改，或者导致409的产生。

13. 413("Request Entity Too Large")重要程度：低到中等。这个响应代码跟411类似，服务器可以用它来中断客户端的请求并关闭连接，而不需要等待请求完成。411用于客户端未指定长度的情况，而413用于客户端发送的表示太大，以至于服务器无法处理。客户端可以先做一个LBYL（look-before-you-leap）请求，以免请求被413中断。若LBYL请求获得响应代码为100，客户端再提交完整的表示。

14. 414("Request-URI Too Long")重要程度：低。HTTP标准并没有对URI长度作出官方限制，但大部分现有的web服务器都对URI长度有一个上限，而web服务可能也一样。导致URI超长的最常见的原因是：表示数据明明是该放在实体主体里的，但客户端却把它放在了URI里。深度嵌套的数据结构也有可能引起URI过长。

15. 415("Unsupported Media Type")重要程度：中等。当客户端在发送表示时采用了一种服务器无法理解的媒体类型，服务器发送此响应代码。比如说，服务器期望的是XML格式，而客户端发送的确实JSON格式。
    如果客户端采用的媒体类型正确，但格式有问题，这时最好返回更通用的400。

16. 416("Requestd Range Not Satisfiable")重要程度：低。当客户端所请求的字节范围超出表示的实际大小时，服务器发送此响应代码。例如：你请求一个表示的1-100字节，但该表示总共只用99字节大小。

17. 17("Expectation Failed")重要程度：中等。此响应代码跟100正好相反。当你用LBYL请求来考察服务器是否会接受你的表示时，如果服务器确认会接受你的表示，那么你将获得响应代码100，否则你将获得417。

# 5XX

5XX系列响应代码在数量上不如4XX系列多，这不是因为服务器错误的几率小，而是因为没有必要如此详细--对于服务器方面的问题，客户端是无能为力的。

1. 500("Internal Server Error")重要程度：高。这是一个通用的服务器错误响应。对于大多数web框架，如果在执行请求处理代码时遇到了异常，它们就发送此响应代码

2. 501("Not Implemented")重要程度：低。客户端试图使用一个服务器不支持的HTTP特性。
   最常见的例子是：客户端试图做一个采用了拓展HTTP方法的请求，而普通web服务器不支持此请求。它跟响应代码405比较相似，405表明客户端所用的方法是一个可识别的方法，但该资源不支持，而501表明服务器根本不能识别该方法

3. 502("Bad Gateway")重要程度：低。只有HTTP代理会发送这个响应代码。它表明代理方面出现问题，或者代理与上行服务器之间出现问题，而不是上行服务器本身有问题。若代理根本无法访问上行服务器，响应代码将是504。

4. 503("Service Unavailable")重要程度：中等到高。此响应代码表明HTTP服务器正常，只是下层web服务服务不能正常工作。最可能的原因是资源不足：服务器突然收到太多请求，以至于无法全部处理。由于此问题多半由客户端反复发送请求造成，因此HTTP服务器可以选择拒绝接受客户端请求而不是接受它，并发送503响应代码。

5. 504("Gateway Timeout")重要程度：低。跟502类似，只有HTTP代理会发送此响应代码。此响应代码表明代理无法连接上行服务器。

6. 505("HTTP Version Not Supported")重要程度： 非常低。当服务器不支持客户端试图使用的HTTP版本时发送此响应代码。

   实体主体：一个描述服务器支持哪些协议的文档。






