

Maven中dependencyManagement 和 dependencies区别

dependencyManagement 一般位于父项目中，定义了一个依赖，声明子类如果使用这个可以使用哪个版本，只负责定义，不负责实现,同时子类也不需要声明版本号，其相当于一个接口。好处是全工程的jar包统一，如果需要修改，只需要改父项目的即可。

子类也可以定义自己的依赖版本号。



