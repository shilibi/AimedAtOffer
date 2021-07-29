### 什么是Spring MVC？

Spring MVC是一个基于Java的实现了MVC设计模式的请求驱动类型的轻量级Web框架，通过把Model，View，Controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合



### Spring MVC流程

1. 用户发送请求到前端控制器DispatchServlet
2. DispatchServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handle
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器一并返回给DispatchServlet
4. DispatchServlet调用HandlerAdapter处理器适配器
5. HandlerAdapter经过适配器调用具体适配器
6. Handler执行完成返回ModelAndView
7. HandlerAdapter将Handler执行结果ModelAndView返回给DispatchServlet
8. DispatchServlet将ModelAndView传给ViewResolver视图解析器进行解析
9. ViewResolver解析后返回具体View
10. DispatchServlet对View进行渲染视图（将模型数据填充到视图中）
11. DispatchServlet相应用户

![img](https://img-blog.csdn.net/20180708224853769?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E3NDUyMzM3MDA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Spring MVC的优点

1. 支持各种视图技术，不仅仅局限于JSP
2. 与Spring框架集成
3. 清晰的角色分配：前端控制器（DispatchServlet），请求到处理器映射（handlerMapping），处理器适配器（HandlerAdapter)，视图解析器（ViewResolver）
4. 支持各种请求资源的映射策略

## Spring MVC解决GET||POST中文乱码问题

#### GET

- 每次请求对URL进行编码

  如：Location.href = "/encodeURI"("http://localhost/test/s?name=张晓&sex=男")

- 在服务器端配置URL编码格式，修改tomcat 的server.xml：增加URIEncoding="UTF-8"这一句，重启tomcat

#### POST

- 在request解析数据时设置编码格式：request.setCharacterEncoding("UTF-8");

- 使用SpringMVC编码过滤器：在Web.xml中增加如下配置，它的位置一定要是第一个执行的过滤器

  ```java
  <filter>
      <filter-name>charsetFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
      </init-param>
      <init-param>
          <param-name>forceEncoding</param-name>
          <param-value>true</param-value>
      </init-param>
  </filter>
  ```

  

## Spring MVC 设置转发和重定向

- 在返回值前加 "forward:"进行转发
- 在返回值前加 "redirect:"进行重定向

## Spring MVC 和ajax相互调用

通过Jackson框架可以把Java里面的对象直接转换为js可以识别的json对象

- 加入Jackson.jar
- 在配置文件中配置json映射
- 在接受Ajax方法方法里可以直接返回Object，List等。但方法前要加上@ResponseBody注解

## Spring MVC 处理异常

可以将异常抛给Spring框架，由Spring框架进行处理，自定义实现Spring全局异常解析器HandlerExceptionResolver，在异常处理器中添加视图页面即可。

## SpringMVC控制器

SpringMVC控制器是单例模式，所以在多线程访问的时候有线程安全问题，不要用同步，因为会影响性能。解决方案是在控制器里不写字段。

## 拦截get方式提交的方法

在@RequestMapping注解里边加上method=RequestMethod.GET

## 把ModelMap里的数据放到Session里

在类上面加上@SessionAttributes注解，里边包含的字符串就是要放入session里面的key

## SpringMVC用什么对象从后台向前台传递数据

通过ModelMap对象，可以在这个对象里用PUT方法，把对象加到里面，前台通过EL表达式得到。

## Spring MVC中有个类把视图和数据合并在一起了，叫什么

ModelAndView



### @RestController  = @Controller + @ResponseBody





## Restful 请求风格

GET：从服务器取出资源（一项或多项）

POST：在服务器新建一个资源

PUT：在服务器更新资源（客户端提供改变后的完整资源）

PATCH：在服务器更新资源（客户端提供改变的属性）

DELETE：从服务器删除资源

## mvc:default-servlet-handler标签作用

由于在web.xml中会将前端映射的请求设置成'/'，即：

![image-20201010205816144](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20201010205816144.png)



如果使用'/'，则表示拦截所有的请求，包括静态资源的请求。这就容易导致404错误。（No mapping fount for Http request with ……）

为了解决这个问题，Spring提供了default-servlet -handler 标签。在WEB 容器启动时，会在上下文定义一个DefaultServletHttpRequestHandler，会对DispatchServlet请求进行处。1）如果做了映射，那么就直接进行处理2）如果没有映射，那么就交给WEB应用服务器默认的Servlet进行处理，从而找到静态资源。如果还找不到，才会报404.