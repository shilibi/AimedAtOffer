# Servlet

**定义：** Servlet是运行在web服务器的小型java程序，通常通过HTTP协议接受和处理来自web客户端的请求。

**特点：** 

1）Servlet对象Servlet容器创建，这个容器一般是tomcat。

2）Servlet是一个接口，位于javax.servlet包中

## 1、servlet生命周期

**1.加载和实例化**

**2.初始化**

第一次请求资源时，执行且只执行一次init方法

**3.请求处理**

第二次往后，执行service方法，执行多次，在这个方法内部，根据请求方式不同，对应调用get/post方法。

**4.服务终止**

当Servlet服务器正常关闭时，执行的Destroy方法，只执行一次。

由上可知，先实例化，再初始化创建servlet容器的实例是由Servlet容器完成，创建Servlet实例是在初始化方法init()之前



## 2、客户端与服务器交互流程

1. 客户端发送请求
2. 服务器接收请求，转发给servlet
3. servlet产生实例对象，并调用servlet API方法，对请求进行处理，将处理结果返还给WEB服务器
4. web服务器将来自servlet的处理结果返还给客户端

## 3、sendRedirect() 和forward()区别

sendRedirect是创建新的请求，forward是把请求转发给新的目标上

redirect后，之前请求作用域范围内的对象就失效了，因为会产生新的请求

forward后，之前请求作用域的对象还能访问

sendRedirect比forward慢

## 4、HTTP请求结构

1）、请求行

请求行由请求方法字段、URL字段和HTTP协议版本字段3个字段组成

2）、请求头部

3）、空行

它的作用是通过一个空行，告诉服务器请求头部到此为止。

4）、请求数据

若方法字段是GET，则此项为空，没有数据

若方法字段是POST,则通常来说此处放置的就是要提交的数据

比如要使用POST方法提交一个表单，其中有user字段中数据为“admin”, password字段为123456，那么这里的请求数据就是 user=admin&password=123456，使用&来连接各个字段。

总的来说，HTTP请求报文格式就如下图所示

![image-20200710161715880](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200710161715880.png)

![image-20200710161732592](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200710161732592.png)

## 5、HTTP响应结构

1）状态码(status code)

描述相应状态。如果servlet没有返回状态码，默认返回成功的状态码HTTPServletResponse.SC_OK

2)HTTP 头部（Http Header）

响应头用于描述服务器的基本信息，以及数据的描述，服务器通过这些数据的描述信息，可以通知客户端如何处理等一会儿它回送的数据。

3)主体(Body)

包含响应内容，可以包含HTML代码，图片，等等，主体由紧跟头部的数据字节组成。