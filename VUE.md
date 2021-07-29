VUE

#### 解决闪动问题：

1、提供样式

```html
<style type="text/css">
[v-cloak]{
 display:none
}
</style>
```

2、在插值表达式所在的标签中添加v-cloak

```html
<div id="app" v-cloak>{{msg}}</div>
```

原理：

先通过样式隐藏内容，然后在内存中进行值的替换，最后将内容显示出来

