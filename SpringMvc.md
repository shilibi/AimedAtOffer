# 二、参数绑定及自定义类型转换

## 请求参数绑定入门

```html
<body>
    <a href="/param/testParam?username=hehe"> 请求参数</a>
</body>
```

```java
 @RequestMapping(path = "/testRequstMapping",params = "username=heihei",headers = {"Accept"})
    public String  testRequstMapping(){
        System.out.println("测试requestMapping");
        return "success";
    }
```

通过？进行连接



## 请求参数绑定实体类型（Bean)

param.jsp(通过封装的类型加 “.“进行封装)

```jsp
    <form action="user/addAccount" method="post">
        userName:<input name="userName" type="text" >
        password:<input name="passWord" type="text" >
        money:<input name="money" type="text" >
        uname:<input name="user.uname" type="text" >
        age:<input name="user.age" type="text" >
        <input value="submit" type="submit" >
    </form>

```

addAccount.java

```
 @RequestMapping(path = "/addAccount")
    public String addAccount(Account account){
        System.out.println("测试requestMapping");
        System.out.println(account.toString());
        return "success";
    }
```



## 请求参数绑定集合类型

```jsp
<form action="user/addAccount" method="post">
    userName:<input name="userName" type="text" >
    password:<input name="passWord" type="text" >
    money:<input name="money" type="text" >

    uname:<input name="list[0].uname" type="text" >
    age:<input name="list[0].age" type="text" >

    uname:<input name="map[one].uname" type="text" >
    age:<input name="map[one].age" type="text" >


    <input value="submit" type="submit" >	
</form>
```

## 自定义类型转换器演示异常

​	页面提交的任何数据，都是字符串类型的

	## 自定义类型转换器代码编写

通过实现 "Converter"接口

```java
public class StringToDateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String s) {
        if(s == null){
            throw  new RuntimeException("请检查！");
        }
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        try {
            return df.parse(s);
        } catch (ParseException e) {
            throw  new RuntimeException("数据类型转换出现错误！");
        }
    }
}
```

在配置文件中配置

```xml
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="com.zx.util.StringToDateConverter"></bean>
            </set>
        </property>
    </bean>
    <!--在类型转换中使之生效-->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
```

## 获取Servlet原生API



# 三、SpringMVC常用注解

## RequestParam注解

anno.jsp

```jsp
    <a href="/anno/testRequestParam?myName=张晓"> 点击测试testServlet</a>
```

AnnoController.java

```java
    @RequestMapping("/testRequsetParam")
    public String testRequsetParam(@RequestParam(name = "myName") String userName){
        System.out.println(userName);
        return "success";
    }
```

请求中的myName 需要与@RequestParam(name="myName")一致

required属性 默认为true，如果真的加@RequestParam,必须传一个符合条件的类似于“myName”的值	 

## RequestBody注解 （用于ajax异步）	 

用于获取请求体内容，直接使用得到键值对结构的数据

anno.jsp

```java
    <form action="anno/testRequestBody" method="post">
        uname:<input name="username" type="text" >
        age:<input name="age" type="text" >
        <input value="submit" type="submit" >
    </form>
```

AnnoController.java

```java
    @RequestMapping("/testRequestBody")
    public String testRequestBody(@RequestBody String body){
        System.out.println(body);
        return "success";
    }
```

注：

1. 此时貌似有中文乱码问题，待解决

2. String body 的body一定不能和input中的name重复，否则会认为是@RequestParam

   

## PathVariable 注解

请求地址都一样，根据不同的请求方式	 最终让不同的方法去执行 

![image-20191102104417258](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191102104417258.png)

 anno.jsp

```jsp
    <a href="/anno/testPathVariable/100"> 点击测试testPathVariable</a>
```

AnnoController.java

```java
    @RequestMapping("/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable(value = "sid") String id){
        System.out.println(id);
        return "success";
    }
```

通过@PathVariable（value="sid"）属性设置，在前端传值的时候直接/100就可进行传值



## RequestHeader

AnnoController.java

```java
@RequestMapping("/testRequestHeader")
    public String testRequestHeader(@RequestHeader(value = "Accept") String header){
        System.out.println(header);
        return "success";
    }
```

anno.jsp

```jsp
    <a href="/anno/testRequestHeader"> 点击测试testPathVariable</a>
```

## CookieValue

anno.jsp

```jsp
    <a href="/anno/testCookieValue"> testCookieValue</a>	
```

AnnoController.java

```java
    @RequestMapping("/testCookieValue")
    public String testCookieValue(@CookieValue(value = "JSESSIONID") String cookieValue){
        System.out.println(cookieValue);
        return "success";
    }
```

## ModelAttribute

anno.jsp

```jsp
    <form action="anno/testModelAttribute" method="post">
        uname:<input name="username" type="text" >
        age:<input name="age" type="text" >
        <input value="submit" type="submit" >
    </form>
```



AnnoController.java

```java
   @RequestMapping("/testModelAttribute")
    public String testModelAttribute(){
        System.out.println("testModelAttribute执行了...");
        return "success";
    }

    @ModelAttribute
    public void shouUser(){
        System.out.println("showUser执行了...");
    }
```

执行结果：

![image-20191102113246345](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191102113246345.png)

@ModelAttribute修饰的方法会优先于控制器执行

应用场景：当表单提交数据不是完整的实体类数据时，保证没有提交数据的字段使用数据库原来的数据

#### 有返回值时，例：

AnnoController.java

```java
   @RequestMapping("/testModelAttribute")
    public String testModelAttribute(User u){//此时对数据库数据进行了更新，如果输入88，则此时u.getAge为88;
        System.out.println("testModelAttribute执行了...");
        System.out.println(u.toString());
        return "success";
    }

    @ModelAttribute
    public User shouUser(String uname){
        System.out.println("showUser执行了...");
        User u = new User();
        u.setAge(10);
        u.setUname(uname);
        u.setDate(new Date());//此时是数据库获取的数据
        return u;
    }
```

#### 无返回值时，例：

anno.jsp

```jsp
    <form action="anno/testModelAttribute" method="post">
        uname:<input name="uname" type="text" >
        age:<input name="age" type="text" >
        <input value="submit" type="submit" >
    </form>	
```

AnnoController.java

```java
    @RequestMapping("/testModelAttribute")
    public String testModelAttribute(@ModelAttribute("abc") User u){
        System.out.println("testModelAttribute执行了...");
        System.out.println(u.toString());
        return "success";
    }

	@ModelAttribute
    public void showUser(String uname, Map<String,User> map){
        System.out.println("showUser执行了...");
        User uer = new User();
        uer.setAge(10);
        uer.setUname(uname);
        uer.setDate(new Date());
        map.put("abc",uer);
    }
```

通过设置一个形参Map<String,<T>> map ,将新对象放入map中，在控制器中通过@ModelAttribute（value=“avb”）获取到新对象

## SessionAttributes（只能作用在类上）

用于多次执行控制器方法见的参数共享

#### request域：

```java
    @RequestMapping("/testSessionAttributes")
    public String testSessionAttributes(Model model){
        System.out.println("testSessionAttributes...");
        model.addAttribute("msg","张晓");
        return "success";
    }
```

在success.jsp页面通过

```jsp
${requestScope}
```

获取

#### session域存值

在类前加上@SessionAttributes

```java
@SessionAttributes(value = {"msg"})//把msg存到了session域
@Controller
@RequestMapping("/anno")
public class AnnoController {
    @RequestMapping("/testSessionAttributes")
    public String testSessionAttributes(Model model){
        System.out.println("testSessionAttributes...");
        model.addAttribute("msg","张晓");
        return "success";
    }
}
```



success.jsp

```jsp
    ${msg}<!--只获取msg-->
    ${sessionScope}<!--获取整个session域-->
```

#### session域取值

```java
 @RequestMapping("/getSessionAttributes")
    public String getSessionAttributes(ModelMap model){
        System.out.println("getSessionAttributes...");
        String msg = (String) model.get("msg");
        System.out.println("msg:"+msg );
        return "success";
}
```

此处注意为ModelMap并非Model

#### session域删除值

```java
@RequestMapping("/delSessionAttributes")
    public String delSessionAttributes(SessionStatus status){
        System.out.println("delSessionAttributes...");
        status.setComplete();//置空
        return "success";
    }
```



# 四、SpringMVC返回值类型及响应数据类型

### 响应之返回值为String类型

java通过model.addAttribute(String name,Object obj)进行存放，jsp页面通过${name.属性}进行调用，注意：jsp一定要设置 isELIgnored="false" ，否则读不出来

UserController.java

```java
    @RequestMapping("testString")
    public String testString(Model m){
        System.out.println("testString执行了...");
        User u = new User("zx","qwerty",20);
        m.addAttribute("user",u);
        return "success";
    }
```

success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    success
    ${user.userName}
    ${user.age}
    ${user.password}
</body>
</html>
```



### 响应之返回值为void类型

```jsp
    <a href="user/testVoid">testVoid</a>
```

默认为寻找一下路径的文件

```
/WEB-INF/pages/user/testVoid.jsp
```

可通过：

```java
  @RequestMapping("/testVoid")
    public void testVoid(HttpServletResponse response, HttpServletRequest request) throws Exception {
        System.out.println("testVoid...");
        //1、编写请求转发的程序
        //request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);
        //2、重定向
       // response.sendRedirect(request.getContextPath()+"/index.jsp");
        //3、直接进行响应
        //设置中文乱码
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        response.getWriter().print("张晓");
        return;
    }
```

等三种方式解决

### 响应之返回值是ModelAndView类型

```java
    @RequestMapping("testModelAndView")
    public ModelAndView testModelAndView(){
        System.out.println("testString执行了...");
        ModelAndView mv = new ModelAndView();
        User u = new User("zmg","123568",20);
        mv.addObject("user",u);
        mv.setViewName("success");//也是通过视图解析器进行解析
        return mv;
    }
```

返回值为String类型的底层也是ModelAndView；

### 响应之使用forward和redirect进行页面跳转 

```java
    @RequestMapping("testForwardOrRedirect")
    public String testForwardOrRedirect(Model m){
        System.out.println("testForwardOrRedirect...");
        //return "forward:/WEB-INF/pages/success.jsp";
        return "redirect:/index.jsp";
    }
```

forward/redirect为固定语法，不需要加项目名，因为框架已经封装好了项目名

### ResponseBody响应json数据

为什么按钮不会弹窗？

答：因为拦截器把所有的都拦截了，需要在springmvc.xml下配置

```xml
<!-- 设置静态资源不过滤 -->
<mvc:resources location="/css/" mapping="/css/**"/> <!-- 样式 -->
<mvc:resources location="/images/" mapping="/images/**"/> <!-- 图片 -->
<mvc:resources location="/js/" mapping="/js/**"/> <!-- javascript -->
```

UserController.java

```java
    @RequestMapping("testAjax")
    public @ResponseBody User testAjax(@RequestBody User user){
        //不加@ResponseBody，返回的是一个json数据，通过@ResponseBody,把返回的user转换成一个json串，直接转，转完直接响应。
        System.out.println("testAjax...");
        user.setAge(88);
        user.setUserName("张冠希");
        System.out.println(user.toString());
        return user;
    }

```

response.jsp

```jsp
<script>
        $(function () {
           $("#btn").click(function () {
                $.ajax({
                    url:"user/testAjax",
                    contentType:"application/json;charset=UTF-8",
                    data:'{"userName":"hehe","password":"1234","age":33}',//与java bean中的属性名一致
                    dataType:"json",
                    type:"post",
                    success:function (data) {
                        //data为服务器端响应的json数据
                        alert(data);
                        alert(data.userName);
                        alert(data.age);
                    }
                });
           });
        });
    </script>
```



# 五、SpringMVC实现文件上传

## 文件上传之上传原理分析和搭建环境 

文件上传的必要前提

![image-20191103085808034](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103085808034.png)



## 传统方式上传文件

UserController.java

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("fileUpLoad1")
    public String fileUpLoad1(HttpServletRequest request) throws Exception {
        System.out.println("文件上传");
        //使用fileupload组件完成文件上传
        //上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        //判断路径是否存在
        File file = new File(path);
        if(!file.exists()){
            //创建该文件夹
            file.mkdirs();
        }
        //解析request对象，获取上传文件项
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        //解析request
        List<FileItem> fileItems = upload.parseRequest(request);
        for (FileItem fileItem:fileItems) {
            //进行判断，当前item对象是否是上传文件项
            if(fileItem.isFormField()){
                //说明普通表单项
            }else{
                //说明上传文件项
                //获取上传文件的名称
                String fileName = fileItem.getName();
                //把文件的名称设置成唯一值，uuid
                String replace = UUID.randomUUID().toString().replace("-", "");
                fileName = replace+"_"+fileName;
                //完成文件上传
                fileItem.write(new File(path,fileName));
                //删除临时文件
                fileItem.delete();
            }
        }

        return "success";
    }
}
```

index.jsp

```jsp
   <form action="/user/fileUpLoad1" method="post" enctype="multipart/form-data">
        浏览...<input type="file" name="upload"><br>
        <input type="submit" value="上传">
    </form
```



## springMVC 上传文件

![image-20191103102124968](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103102124968.png)

## 跨服务器上传分析和搭建环境

![image-20191103103107196](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103103107196.png)

搭建另外一个tomcat:![image-20191103115636986](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103115636986.png)

UserController.java

```java
  @RequestMapping("fileUpLoad3")
    public String fileUpLoad3(MultipartFile upload) throws Exception {
        System.out.println("跨服务器上传");
        //定义上传文件服务器路径
        String path = "http://localhost:9090/uploads/";
        String fileName = upload.getOriginalFilename();
        //把文件的名称设置成唯一值，uuid
        String replace = UUID.randomUUID().toString().replace("-", "");
        fileName = replace + "_" + fileName;
        //创建客户端对象
        Client client = Client.create();
        //和图片服务器进行连接
        WebResource resource = client.resource(path + fileName);
        //上传文件
        resource.put(upload.getBytes());
        return "success";
    }
```

报错403原因：

1、没有uploads这个文件

2、端口不对

# 六、异常

![image-20191103133220317](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103133220317.png)





## 自定义异常

1、编写自定义异常类（做提示信息）

```java
public class exceptionSysException {
    private String massage;

    public exceptionSysException(String massage) {
        this.massage = massage;
    }

    public exceptionSysException() {
    }

    public String getMassage() {
        return massage;
    }

    public void setMassage(String massage) {
        this.massage = massage;
    }
}

```



2、编写异常处理器

```java
public class SysExceptionResolver implements HandlerExceptionResolver {


    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e1) {
        SysException e = null;
        if (e1  instanceof  SysException){
                e = (SysException) e1;
        }else{
            e = new SysException("系统正在维护。。。");
        }
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg",e.getMassage());
        mv.setViewName("error");
        return mv;
    }
}
```

3、配置异常处理器（跳转到提示页面）

```xml
    <bean id="sysExceptionResolver" class="com.zx.exception.SysExceptionResolver"></bean>

```



## 自定义拦截器



![image-20191103153910367](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103153910367.png)

### 第一种拦截器

1、编写拦截器类，实现HandlerInterceptor接口

UserController.java

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/testInterceptor")
    public String testInterceptor(){
        System.out.println("testInterceptor执行了");
        return  "success";
    }
}
```

MyInterceptor.java

```java
public class MyInterceptor1 implements HandlerInterceptor {

     /*
        预处理：controller方法执行前
        return true:放行，执行下一个拦截器，若果没有，执行Controller里的方法
        return false：不放行
      */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器执行了");
        return true;
    }
}
```

当返回值为false的时候，Controller里的方法不会执行；但是可以通过：

```java
request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
```

进行转发。



2、配置拦截器

```xml
    <!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/user/*"/>
<!--
            <mvc:exclude-mapping path="/**"/>
-->
            <bean class="com.zx.interceptor.MyInterceptor1"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

当只有<mvc:mapping>时，只拦截配置里的

当只有<mvc:exclude-mapping>时，除了配置里的都拦。

可以进行一些逻辑判断。例:判断用户是否登陆，如果登陆，放行；如果没有登陆，跳转到登陆页面。

### 第二种拦截器

```java
 /*
    后处理方法，controller方法执行后，success.jsp执行前
     */
    public  void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
        System.out.println("后拦截器执行了2222");
        //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);//指定新的跳转，不会跳向success.jsp
    }
```



### 第三种拦截器

```java
    /*
    success.jsp页面执行后，该方法执行
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
        System.out.println("最后2222");
    }
```

可以释放一下资源

注：

1、如果有两个相同的拦截器，那么执行顺序如下：

![image-20191103161123105](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191103161123105.png)

拦截器执行的顺序与配置文件中的先后顺序相对应。

2、配置第二个拦截的时候：

```xml
    <!--配置拦截器器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <!--  <mvc:exclude-mapping path="/**"/>-->
            <bean class="com.zx.interceptor.MyInterceptor2"></bean>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/user/*"/>
            <!--  <mvc:exclude-mapping path="/**"/>-->
            <bean class="com.zx.interceptor.MyInterceptor1"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

不是直接加bean，而是新加一个<mvc：interceptor>