# 一、简介

![image-20191120204706590](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20191120204706590.png)

# 二、基础语法

## 1、与html结合方式：

### 1）、内部js

* 定义<script>,标签体内容就是js代码

### 2）、外部js

* 定义<script>，通过src属性引入外部的js文件

  注意：1、<script>可以定义在html任何地方，但是定义的位置会影响执行的顺序。

  ​			2、<script>可以定义多个。

## 2、注释

* 单行注释 ：//注释内容

* 多行注释：

  ```
  /*注释内容*/
  ```

## 3、数据类型

 * 原始数据类型（基本数据类型）
   	* number：数字。整数，小数，NaN（特殊的数字。 not a number ，一个不是数字的数字类型）
      	* string：字符串。字符/字符串  “abc”  “a”  ‘a’
      	* boolean：
              	* null：一个对象为空的占位符
              	* undefined：未定义。如果一个对象没有给初始值，则会被默认赋值为undefined
	* 引用数据类型：对象

## 4、变量

* 变量：一小块存储数据的内存空间
* java语言是强类型语言，js是弱类型语言。
  * 强类型：在开辟变量存储空间时，定义了空间将来存储的数据的数据类型。只能存储固定类型的数据。
  * 弱类型：在开辟变量存储空间时，不定义空间将来存储的数据的数据类型。可以存储任意类型的数据。
* 语法
  * var 变量名 =  
  * 输出到页面上：`document.write(num+"<br>");`
  * var obj;//默认为undefined
  * typeof（）：获取类型

## 5、运算符

* 一元运算符  ++，--，+（正号）-（负号）
  * 注意：在js中，如果运算数不是运算符所要求的类型，那么js引擎就会自动的将运算数进行类型转换
    * 其他类型转number：
      * String 转number:按照字面值转换。如果字面值不是数字，则转成NaN（不是数字的数字）
      * boolean转number：true转为1，false转为0
* 算数运算符 +（加号）- * / % 
* 赋值运算符 =    +=
* 比较运算符  >   <    ==      ===(全等于)
  * 比较方式
    * 1、类型相同：直接比较
      * 字符串：按照字典顺序比较。按位逐一比较，直到得出大小为止。
    * 2、类型不同，先进行类型转换，再比较
      * ====：全等于。再比较之前，先判断类型，如果类型不一样，则直接返回false。
* 逻辑运算符    &&     ||   ！
  * 其他类型转boolean
    * number：0或NaN为假，非0为真
    * string：除了空字符串`（""）`，其他都是true
    * null&undefined:都是false
    * 对象：都是true
* 三元运算符     ？ ：

* 1、语句以；结尾，如果一行只有一条语句则；可以省略（不建议）
* 2、变量定义问题：使用var关键字，也可以不使用。
  * 用：定义的变量是局部变量
  * 不用：定义的变量是全局变量 （不建议）

## 6、流程控制语句

* if else

* switch

  * 在java中，switch语句可以接收的数据类型 ：byte int short char 枚举 string

  * 在js中，什么类型都可以。

    ```js
    <script>
            var a = "abc";
            switch (a) {
                case 1:
                    alert("number");
                    break;
                case "abc":
                    alert("string");
                    break;
                case "true":
                    alert("true");
                    break;
                case null:
                    alert("null");
                    break;
                case undefined:
                    break;
                    alert("undefined");
            }
        </script>
    ```

* while

* do while

* for

# 三、基本对象

* #### Function：函数对象（函数名如果相同，只会覆盖原来方法，不会报错）

  * 1、创建

    * 1、var fun = new Function（形参列表，方法体）

    * 2、function 方法名称（形参列表）{

      ​		方法体；

      }

      ```js
          function f2(a,b) {
         		 alert(a+b);
          }
          f2(2,3);
      ```

    * 3、var 方法名 = function（形参列表）{ 方法体}

  * 2、方法

  * 3、属性

    * length：形参个数

  * 4、特点

    * 1、方法定义时，形参的类型不用写,返回值类型也不用写

    * 2、方法是一个对象，如果定义名称相同的方法，会覆盖

    * 3、在js中，方法的调用只与方法的名称油管，和参数列表无关

      ```js
      function fun2(a,b){
      	alert(a);
      	alert(b);
      }
      fun2(1,2);
      fun2(1);
      fun2();
      fun2(1,2,3)
      ```

    * 4、在方法声明中有一个隐藏的内置对象（数组），arguments，封装所有的 实际参数

    * 5、

      ```js
      //求任意个数的和
       function add() {
                    var sum = 0;
                    for(var i = 0;i < arguments.length;i++){
                        sum+=arguments[i];
                    }
                    return sum;
                }
                var sum =  add(1,2,3);
                alert(sum);
      ```

      

  * 5、调用

    * 方法名称（实际参数列表）

* #### Array：数组

  * 1、创建

    * var arr = new Array(元素列表)

    * var arr = new Array（默认长度）

    * var arr = [元素列表 ]

      ```js
          var arr1 = new Array(1,2,3);
          var arr2 = new Array(2);
          var arr3 = [1,2,3];
          var arr4 =new Array();
      
          document.write(arr1+"<br>");
          document.write(arr2+"<br>");
          document.write(arr3+"<br>");
          document.write(arr4+"<br>");
      ```

  * 2、方法

    * join（参数）：将数组中的元素按照指定的分隔符拼接成字符串

      ```js
              var arr = [1,"arr",true];
              document.write(arr.join("--"));
      ```

      执行结果：
      
      ```js
      1--arr--true
      ```
      
    * push()：向数组末尾添加一个或更多元素，并返回新的长度

  * 3、属性：length数组长度

  * 4、特点

    * 1、js中，数组元素的类型是可变的`var arr = [1,"arr",true];document.write(arr)`
    * 2、js中，数组长度可变的。 

* #### Boolean

* #### Date

  * 1、创建
    * var date  = new Date();
  * 2、方法
    * toLocalString():返回当前date对象对应的时间本地字符串格式
    * getTime():获取毫秒值。返回当前如期对象描述的时间和1970年1月1号零点的毫秒之差

* #### Math 

  * 1、创建
    * 特点：Math对象不用创建，直接使用。Math.方法名()
  * 2、方法：
    * 取1~100之间的整数：var random = Math.floor(Math.random()*100)  + 1;
  * 3、属性:
    * PI:圆周率  
    * random：含头（0）不含尾（1）
    * ceil（x）：对数进行上舍入
    * floor（x)：对数进行下舍入
    * round（x）：把数四舍五入为最接近的整数

* #### Number

* #### String

* #### RegExp：正则表达式对象。

  * 1、正则表达式：定义字符串的组成规则。

    *   1、单个字符:[]

      ​		如：[a] [ab] [a-zA-Z0-9_]

      ​		*特殊符号代表特殊含义的单个字符：

      ​		\d :单个数字字符[0-9]

      ​		\w:单个单词字符[a-zA-z0-9_]

    * 2、量词符号：

      * ？：表示出现0次或1次
      * *：表示出现0次或多次
      * +：出现1次或多次
      * {m,n}:表示 m<= 数量 <= n
        * m如果缺省：{，n}：最多n次
        * n如果缺省：{m,}：最少m次

    * 3、开始结束符号

      * ^：开始
      * $:结束

  * 2、正则对象

    * 1、创建
      * 1、var reg  = new RegExp(“正则表达式”)；`var reg = new RegExp("^\\w{6,12}$");`反斜线！
      * 2、var reg  = /正则表达式/
    * 2、方法
      * test（参数）方法：验证指定的字符串是否符合正则定义的规范

  #### Global

  * 1、特点： 全局对象，这个Global中封装的方法不需要对象就可以直接调用。方法名（）
  * 2、方法：
    * encodeURL():url编码
    * decodeURL():url解码
    * encodeURLComponent():url编码，编码的字符更多
    * decodeURLComponent():url解码
    * parseInt（）:将字符串转为数字
      * 逐一判断每一个字符是否是数字，直到不是数字位置，将前边数字部分转为number
    * isNaN():判断一个值是否是NaN
      * NaN六亲不认，连自己都不认识。NaN参与的==比较 全都为false
    * eval():将JavaScript字符串，转成脚本执行

```js
        var encode = encodeURI("张晓");
        document.write(encode);

        var s = decodeURI(encode);
        document.write(s);
```

# 四、简单DOM

* 功能: 控制html文档内容

* 代码：获取页面标签（元素）对象 Element

  * document.getElementById("id值"):通过元素的id获取元素对象

* 操作Element对象

  * 1、修改属性值：
    * 1、明确获取的对象是哪一个?
    * 2、查看API文档，找其中有哪些属性可以设置  

  例1：

  ```js
  <img id = "light" src = "img/off.gif">
  <script>
  	var light = document.getElementById("light");
  	alert("变！");
  	light.src = "img/on.gif";
  </script>
  ```

  例2：

  ```javascript
  <h1 id = "title"> 世界的美好</h1>
  <script>
  var title = document.getElementById("title");
  alert("变！");
  title.innerHTML = "与你环环相扣";
  <script>
  ```

  

  * 2、修改标签体内容：
    * 属性：innerHTML:
    *   

# 五、简单事件

/例：

```js
<img id="light" src="img/off.gif">
<script>
var light = document.getElementById("light");
    var flag = false;
    light.onclick = function (){
        if(flag){
                light.src = "img/off.gif";
                flag = false;
        }else  {
            light.src = "img/on.gif";
            flag = true;
        }
    }
</script>    
```

# 六、BOM

* 1、概念：	Browser Object Model  浏览器对象模型

* 将浏览器的各个组成部分封装成对象。     		
  
* 2、组成：
  * window ：窗口对象
  
  * Navigator：浏览器对象
  
  * Screen：显示器屏幕对象

  * History：历史记录对象
  
  * Location：地址栏对象
  
    * 1、创建（获取）：
  
      * 1、window.location;
      * 2、location
  
    * 2、方法；
  
      * reload（）：刷新
  
        ```js
            var btn = document.getElementById("btn");
           		 btn.onclick = function () {
           		 location.reload();
            }
        ```
  
      * href：var href = location.href;alert(href);
  
    * 3、属性；
  
* 3、Window 窗口对象：

  * 1、创建

  * 2、方法

    * 1、与弹出窗口有关的方法：
      * alert（）：显示带有一段消息和一个确认按钮的警告框
      * confirm（）：显示带有一段消息以及确认按钮和取消按钮的对话框。
        * 如果用户点击确认按钮，方法返回true，否则返回false；
      * prompt（）：显示可提示用户输入的对话框。
        * 返回值：获取用户输入的值
    * 2、与打开、关闭有关的方法
      * close():关闭浏览器窗口（谁调用我，我关谁）
      * open（）：打开一个新的浏览器窗口，返回值为一个新的window对象

    ```js
     <input id="openBtn" type="button" value="打开窗口">
        <input id="closeBtn" type="button" value="关闭窗口">
        <script>
            var newWindow = null;
            var elementById = document.getElementById("openBtn");
            elementById.onclick = function () {
                newWindow= open("https://www.baidu.com");
            }
            var elementById1 = document.getElementById("closeBtn");
            elementById1.onclick = function () {
                newWindow.close();
            }
      </script>	
    ```

    * 3、与定时器有关的方法
      * setTimeOut() 在指定的毫秒数后调用函数或计算表达式。
        * 参数1：js代码或者方法对象
        * 参数2：毫秒值
      * clearTimeOut() 取消由setTimeOut() 方法设置的timeout。
      * clearInterval()	取消由 setInterval() 设置的 timeout。
      * setInterval()	按照指定的周期（以毫秒计）来调用函数或计算表达式。

  * 3、属性：
  
  * 获取其他DOM对象
  
    * history： var history = history;      var h2 = window.history;
  
    * location
  
      ```js
          <input id = "btn1" type="button" value="百度">
      <script>
          var btn1 = document.getElementById("btn1");
          btn1.onclick = function () {
              location.href = "http://www.baidu.com";
          }
      </script>
      ```
  
    * navigator
  
    * screen
  
  * 获取其他BOM对象
  
  * 4、特点：
    * window 对象不需要创建，可以直接使用window使用。 window.方法名（）；
    * window引用可以省略。方法名（）。
  
* 案例（轮播图）：

  ```js
  <img id = "img"src = "img/banner_1.jpg" width="100%">
      <script>
          var number = 1;
           function fun() {
               number++;
               var img = document.getElementById("img");
               img.src= "img/banner_"+number+".jpg";
               if(number == 3){
                   number = 0;
               }
           }
           setInterval(fun,1000);
      </script>
  ```

* 案例（5秒跳到百度）

  ```js
  <p>
     <span id ="time" >5</span>秒后跳转到百度。。。
  </p>
  <script>
      var second = 5;
      function showTime() {
          second--;
          var time = document.getElementById("time");
          time.innerHTML = second;
          if(second == 0){
              location.href = "http://www.baidu.com";
          }
      }
      setInterval(showTime,1000);
  </script>
  ```

* 4、history对象：历史记录对象
  * 1、创建（获取）
    * 1、window.history
    * 2、history
  * 2、方法
    * back（）：加载history列表中的前一个url
    * forward（）：加载history列表中的下一个url
    * go（参数）：加载history列表中的某个具体页面
      * 正数：前进几个历史记录
      * 负数：后退几个历史记录
  * 3、属性
    * length：返回当前窗口历史列表中的URL数量

# 七、DOM

概念：Document Object Model:文档对象模型

​		将标记语言文档的各个组成部分，封装为对象。可以使用这些对象，对标记语言文档进行CRUD的动态操作。  

W3C DOM 标准被分为 3 个不同的部分：

- 核心 DOM - 针对任何结构化文档的标准模型
  - Document ：文档对象
  - Element：元素对象
  - Attribute：属性对象
  - Text：文本对象
  - Comment：注释对象
  - ！Node：节点对象，其他5个的父对象
- XML DOM - 针对 XML 文档的标准模型
- HTML DOM - 针对 HTML 文档的标准模型

核心DOM模型

* Document：文档对象

  * 1、创建（获取）在html dom模型中可以使用window对象来获取

    * 1、window.document
    * 2、document

  * 2、方法

    * 1、获取Element对象：

      * 1、getElementById():根据id属性值获取元素对象。id属性值一般唯一

      * 2、getElementsByTagName():根据元素名称获取元素对象们。返回值是一个数组。

      * 3、getElementsByClassName():根据Class属性值获取元素对象们。返回值是一个数组。

      * 4、getElementsByName():根据name属性值获取元素对象们。返回值时一个数组。

        ```js
        <body>
            <div id = "div1">div1</div>
            <div id = "div2">div2</div>
            <div id = "div3">div3</div>
        
            <div class="cls1">div4</div>
            <div class="cls1">div5</div>
        
            <input type="text" name = "username">
        <script>
            var divs  = document.getElementsByTagName("div");
          //  alert(divs.length);
            var div = document.getElementsByClassName("cls1");
            //alert(div.length);
            var elementsByName = document.getElementsByName("username");
            alert(elementsByName.length);
        
        </script>
        </body>
        ```

    * 2、创建其他DOM对象

      * createAttribute(name):`var table = document.createElement("table")`
      * createComment()
      * createElement
      * createTextNode()

  * 3、属性

* Element：元素对象

  * 1、获取/创建：通过document来获取和创建

  * 2、方法：

    * 1、removeAttribute():删除属性
    * 2、setAttribute():设置属性

    ```js
      <a>click me</a>
        <input type="button" id="btn_set" value="设置">
    <script>
        var btn = document.getElementById("btn_set");
        btn.onclick = function () {
            var ele = document.getElementsByTagName("a")[0];
            ele.setAttribute("href","https://www.baidu.com");
        }
    </script>
    ```

* Node：节点对象，其他5个的父对象

  * 特点：所有dom对象都可以被认为是一个节点

  * 方法：

    * CRUD dom 树：

      * appendChild():向节点的子节点列表的结尾添加新的子节点
      * removeChild():删除（并返回 ）当前节点的指定子节点
      * replaceChild():永新节点替换一个子节点。

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              div{
                  border: 1px solid red;
              }
              #div1{
                  width: 200px;
                  height: 200px;
              }
              #div2{
                  width: 100px;
                  height: 100px;
              }
              #div3{
                  width: 50px;
                  height: 50px;
                  color: yellow;
              }
          </style>
      </head>
      <body>
      <div id = "div1">
          <div id = "div2"> div2</div>
          div1
      </div>
      <a href="javascript:void(0)" id="del"> click</a><br/>
      <a href="javascript:void(0)" id="add"> 添加子节点</a>
      <input type="button" id="btn" value="remove">
      <script>
          var btn = document.getElementById("del");
          btn.onclick = function () {
             var div1 = document.getElementById("div1");
             var div2 = document.getElementById("div2");
             div1.removeChild(div2);
          }
          var add = document.getElementById("add");
          add.onclick = function () {
              var div1 = document.getElementById("div1");
              var div3 = document.createElement("div");
              div3.setAttribute("id","div3")
              div1.appendChild(div3);
          }
      </script>
      </body>
      ```

  * 属性：

    * parentNode：返回节点的父节点

# 八、HTML DOM

* 1、标签体的设置和获取

  ```js
  document.getElementById("btn_add").onclick = function () {
          var id = document.getElementById("id").value;
          var name = document.getElementById("name").value;
          var gender = document.getElementById("gender").value;
  
          var table = document.getElementsByTagName("table")[0];
          table.innerHTML+= " <tr>\n" +
              "        <td>"+ id+"</td>\n" +
              "        <td>"+ name+"</td>\n" +
              "        <td>"+ gender+"</td>\n" +
              "        <td><a href=\"javascript:void(0)\" onclick=\"delTr(this)\">删除</a></td>\n" +
              "    </tr>";
  
      }
          function delTr(obj) {
          alert(obj);
          var table = obj.parentNode.parentNode.parentNode;
          var tr = obj.parentNode.parentNode;
          table.removeChild(tr);
      }
  ```

* 2、使用html元素对象的属性

* 3、控制样式

  * 1、

    ```js
      var div = document.getElementById("div1");
        div.onclick = function () {
            //修改样式方式1
            div.style.border = "2px solid red";
            //font-size ===> fontSize
            div.style.fontSize = "40px";
        }
    ```

  * 2、提前定义好类选择器的样式，通过元素的className属性来设置 其class属性值

  ```js
      <style>
          .d1{
              border: 1px solid red;
              width: 200px;
              height: 200px;
          }
          .d2{
              border: 3px solid black;
              width: 300px;
              height: 300px;
          }
      </style>
  </head>
  <body>
      <div id = "div1">
          div
      </div>
      <div id = "div2">
          div
      </div>
  <script>
      var div = document.getElementById("div1");
      var div1 = document.getElementById("div2");
      div.onclick = function () {
          //修改样式方式1
          div.style.border = "2px solid red";
          //font-size ===> fontSize
          div.style.fontSize = "40px";
      }
      div1.onclick = function () {
          div1.className = "d1";
      }
  </script>
  ```

# 九、事件监听机制

​	概念：某些组件被执行了某些操作后，触发某些代码的执行。

* 事件：某些操作。如：单击、双击、键盘按下、鼠标移动
* 事件源：组件。如：按钮、文本输入框
* 监听器：代码
* 注册监听：将事件、事件源、监听器结合在一起。当事件源上发生了某个事件，则触发执行某个监听器代码。

常见的事件:

 * 1、点击事件

   	* 1、onclick：单击事件
      	* ondbclick：双击事件

* 2、焦点事件

  * 1、onblur：失去焦点
    * 一般用于表单验证
  * 2、onfocus:元素获得焦点

* 3、加载事件

  * 1、onload：一张页面或一幅图像完成加载。

* 4、鼠标事件

  * onmousedown	鼠标按钮被按下。
    * 定义方法时，定义一个形参，接收event对象。
    * event对象的button属性可以获取鼠标按钮键被点击了。
  * onmousemove	鼠标被移动。
  * onmouseout	鼠标从某元素移开。
  * onmouseover	鼠标移到某元素之上。
  * onmouseup	鼠标按键被松开。

* 5、键盘事件

  | [onkeydown](https://www.w3school.com.cn/jsref/event_onkeydown.asp) | 某个键盘按键被按下。       |
  | ------------------------------------------------------------ | -------------------------- |
  | [onkeypress](https://www.w3school.com.cn/jsref/event_onkeypress.asp) | 某个键盘按键被按下并松开。 |
  | [onkeyup](https://www.w3school.com.cn/jsref/event_onkeyup.asp) | 某个键盘按键被松开。       |

* 6、选择和改变

  * | [onselect](https://www.w3school.com.cn/jsref/event_onselect.asp) | 文本被选中。     |
    | ------------------------------------------------------------ | ---------------- |
    | [onchange](https://www.w3school.com.cn/jsref/event_onchange.asp) | 域的内容被改变。 |

* 7、表单事件

  * 1、onsubmit  确认按钮被点击

    * 可以阻止表单的提交。校验用户名格式是否正确

      ```js
      document.getElementById("form").onsubmit = function (event) {
                  var flag = false;
                  return flag;
      }
      ```

      通过返回值判断是否允许提交。如果为false，表单阻止提交。 

    * 2、通过onclick方法

      ```js
      <form action="#" id="form" onclick=" return checkform();"></form>
      <script>
      		checkform(){
      			return false
      		}
      </script>
      ```

      

  * 2、onreset 重置按钮被点击

# 十、example

```js
 <script>
        window.onload = function (ev) {
            document.getElementById("form").onsubmit = function () {
                return checkUsername() &&checkPassword();
            }
           /* document.getElementById("username").onblur = function () {
                checkUsername();
            }*/
            //或：
            document.getElementById("username").onblur = checkUsername;
            document.getElementById("password").onblur = checkPassword;
        }
        function checkPassword() {
            var password = document.getElementById("password").value;
            var reg_password = /^\w{6,12}$/;
            var flag = reg_password.test(password);
            var s_password = document.getElementById("s_password");
            if(flag){
                s_password.innerHTML = "<img  width='50' height='51' src = 'img/gou.png'/>";
            }else{
                s_password.innerHTML = "密码格式错误";
            }

            return flag;
        }
        function checkUsername() {
            var username = document.getElementById("username").value;
            var reg_username = /^\w{6,12}$/;
            var flag = reg_username.test(username);
            var s_username = document.getElementById("s_username");
            if(flag){
                s_username.innerHTML = "<img  width='50' height='51' src = 'img/gou.png'/>";
            }else{
                s_username.innerHTML = "用户名格式错误";
            }

            return flag;
        }
    </script>
```

