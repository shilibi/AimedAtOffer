### css：页面美化和布局控制

#### 1、概念：Cascading Style Sheets 层叠样式表

​	层叠：多个样式可以作用在同一个HTML元素上，同时生效

#### 2、好处：

​		①功能比较强大

​		②将内容展示与样式控制分离：1）降低耦合度。解耦。让分工协作更容易。提高开发效率。

#### 3、CSS的使用：css与html结合方式

​		①内联样式

```html 
<div style="color: red">hello</div>
```

​		②内部样式

在head标签内，定义style标签，style标签的标签体内容就是css代码。如：

```html
<style>
	div{
	color:blue;
	}
</style>
```



```html
<div>hello css</div>
```

​		③外部样式

​				1、定义css资源文件

```css
div{
    color: greenyellow;
}
```

​				2、在head标签内，定义link标签，引入外部的资源文件

```
        <link rel="stylesheet" href="css/a.css">
        <div>hello</div>
```

或

```
<style>
	@import "css/a.css"
</style>
<div>hello</div>
```



​		注意：1）1、2、3种方式，css作用范围越来越大。



#### 4、CSS 语法格式

格式：

​		选择器{

​			属性名1：属性值1；

​			属性名2：属性值2；

​					....

}

选择器：筛选具有相似特征的元素。

注意：

每一对属性使用分号隔开。最后一对属性可以不加分号。

#### 5、选择器

筛选具有相似特征的元素。

* 分类

  * 1、基本选择器

    * id选择器

      选择具有具体id属性值的元素，建议在一个html页面中id值唯一

      语法： #id属性值{}

    * 元素选择器

      选择具有相同标签名称的元素。

      语法：标签名称{}

      注意：id选择器优先级高于元素选择器

    * 类选择器

      语法： .class属性值{}

      注意：类选择器优先级高于元素选择器

  * 2、扩展选择器

    * 选择所有元素：

      语法：*{}

    * 并集选择器

      选择器1，选择器2{}

    * 子选择器：筛选选择器1元素下的选择器2元素

      语法：选择器1 选择器2{}

    * 父选择器：筛选选择器2的父元素选择器1

      语法：选择器1 > 选择器2{}

    * 属性选择器：

      ```html
      <style>
      	input[type=]
      </style>
      
      <input typ="text">
      ```

    * 伪类选择器：选择一些元素具有的状态

      ```css
              a:link{
              color: pink;
              }
              a:hover{
              color: green;
              }
              a:active{
              color: yellow;
              }
              a:visited{
              color: red;
              }
      ```

      ```html
      <a href="#" >那我！！</a>
      ```

#### 6、属性

* 字体、文本

  * font-size:字体大小

  * color：文本颜色

  * text-align：对齐方式

  * line-height：行高

    ```css
        <style>
            p{
                color: #FF0000;
                font-size: 40px;
                text-align: center;
                line-height: 200px;
                border: 2px solid green;
            }
        </style>
    ```

    ```html
     <p>122333</p>
    ```

* 背景

  * background:复合属性 

    ```html
    <style>
        background: url("img/logo.jpg") no-repeat center;
    </style>
    ```

    

* 边框

  * border：边框属性：复合属性

    ```css
     border: 2px solid green;
    ```

* 尺寸

  * width:
  * height

* 盒子模型：控制！！！！

  * margin：外边距

  * padding：内边距

    ​		默认情况下内边距会影响整个盒子的大小。

    ​		解决方法：设置盒子属性，让width和height就是最终盒子的大小：

    ```
    box-sizing: border-box;
    ```

  * float:浮动

    * left
    * right