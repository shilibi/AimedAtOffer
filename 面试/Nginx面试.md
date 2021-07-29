

### 什么是Nginx

Ngine X，是一款免费的、自由的、开源的、高性能HTTP服务器和反向代理服务器，也是一个IMAP，POP3，SMTP代理服务器，Nginx以高性能、稳定性、丰富的功能、简单的配置和低资源消耗而闻名。



Nginx本身就可以托管网站（类似于tomcat），进行http服务处理，也可以作为反向代理服务器、负载均衡服务器和HTTP缓存。

Nginx解决了服务器C10K（一秒内连接客户端的数目为10K）问题。它的设计不像传统服务器一样使用线程处理请求，而是一个更加高级的机制——事件驱动机制，是一种异步事件驱动结构

### Nginx特性

跨平台

配置异常简单

非阻塞、高并发连接

Nginx代理和后端Web服务器无需长连接

Nginx接收用户请求是异步的，即先将用户请求全部接收下来，然后一次性发送给后端Web服务器，极大减轻后端Web服务器压力

发送响应报文时，是边接受来自后端web服务器的数据，边发送给客户端

网络依赖性低，理论上只要能够ping通，就可以实施负载均衡，而且可以有效区分内网，外网流量。

支持内置服务器检测，Nginx能够根据应用服务器处理页面返回的状态码、超时信息等检测服务器是否出现故障，并及时返回错误的请求重新提交到其他节点上

内存消耗小、成本低廉、节省带宽、稳定性高

### Nginx与Apache不同点

![640?wx_fmt=png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/bcPwoCALib9JbKiaiaTvvWZCEUW4AdPzzKIopFHNSu6q4PiaOtUib64HAy7FByibbOeU1ciawpUyZfPG53t4sceibGWzpg/640?wx_fmt=png)

### Nginx处理HTTP请求

Nginx结合多进程机制和异步机制，异步机制使用的是异步非阻塞方式

- 多进程机制

  ​		每当服务器收到一个客户端，就有服务器主进程（master process） 生成一个子进程（worker process ） 与客户端建立连接进行交互，直到连接断开，子进程就结束了。

  ​		好处：1、各个进程相互独立，不需要加锁，减少了使用锁对性能造成影响，降低编程复杂度，降低开发成本。2、采用独立进程，可以让进程互相之间不会影响，如果一个进程发生异常退出，其他进程正常工作，master会启动新的进程确保服务不会中断，将风险降到最低。

  ​		缺点：生成子进程在资源和时间上会有一定的开销。当有大量请求时，会导致系统性能下降。

- 异步非阻塞机制

  ​		每个工作进程使用异步非阻塞方式，可以处理多个客户端请求。

  ​		当某个工作进程接收到客户端请求以后，调用IO进行处理，如果不能立即得到结果，就去处理其他请求（即非阻塞），而客户端也无需等待响应，可以去处理其他事情，即异步。

  ​		当IO返回时，就会通知此工作进程；进程得到通知，暂时 挂起当前处理 事务去响应客户端请求。

### Nginx中，如何使用未定义的服务器名称来阻止处理请求

将请求删除的服务器定义为

```
server{
	listen 80;
	server_name"";
	return 444;
}
```

这里，服务器名被保留为一个空的字符串，它将在没有“主机”头字段的情况下匹配请求，而一个特殊的Nginx的非标准代码444被返回，从而终止连接

### 使用“反向代理服务器”的优点是什么

可以隐藏源服务器的存在和特征。它充当互联网云和web服务器之间的中间层。安全方面来说是好的。

### Nginx服务器的最佳用途

在网络上部署动态HTTP内容，使用SCGI、WSGI应用程序服务器、用于脚本的FastCGI处理程序。还可以作为负载均衡器。

### 是否有可能将Nginx的错误替换为502错误、503？

502 = 错误网关

503 = 服务器超载

有可能，需要确保fastcgi_intercept_errors被设置为on，并使用错误页面指令

![640?](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/gUFHIUJJ6iay23sEXiaRGraGvdj7G5yy6ib18waolmZ4ia4z150Baw2uBIkRickyuAXIYhQpDmuxNic4RzmccYOaJSdA/640?)



### 在nginx中，解释如何在URL中保留双斜线?

使用merge_slashes_off

### ngx_http_upstream_module的作用

用于定义可通过fastcgi传递、proxy传递、uwsgi传递、memcached传递和scgi传递执行来引用的服务器组。

### C10K问题

C10k问题是指无法同时处理大量客户端（10000）的网络套接字

### stub_status和sub_filter指令作用

stub_status：用于了解Nginx当前状态，如当前的活动连接，接受和处理当前读、写、等待连接的总数

sub_filter: 用于搜索和替换响应中的内容，并快速修复陈旧的数据

### Nginx是否支持将请求压缩到上游

使用Nginx模块gunzip将请求压缩到上游。

如何在Nginx获得当前时间

必须使用SSI模块，$date_gmt和$date_local变量





