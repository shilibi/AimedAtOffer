

替换文字：  ***:%/s/原字符/新字符***



将gem改成阿里云镜像```gem sources -a http://mirrors.aliyun.com/rubygems/ ```



查看连接到linux服务器的ip地址  netstat -an



VMware 共享文件夹/mnt/hgfs无法显示的问题：**vmhgfs-fuse .host:/ /mnt/hgfs**
（安装open vm tools后执行）



永久关闭Linux防火墙：***systemctl disable firewalld.service***



查看系统版本  **uname -r**   ///   **cat /etc/redhat-release**



查看 JAVA安装目录：**echo $JAVA_HOME**



windows解决远程桌面登录后闪退cmd下：mstsc -admin

win+shift +s windows10自带截图

查看tomcat版本:  **cd /usr/local/tomcat/bin **  ---------> **./version.sh**



筛选端口号： netstat -nltp | grep \<端口号>



杀死进程 ： kill  \<pid>



export CATALINA_OPTS="-Dcom.sun.management.jmxremote
  -Dcom.sun.management.jmxremote.port=1099 
 -Dcom.sun.management.jmxremote.ssl=false 
 -Dcom.sun.management.jmxremote.authenticate=false 
 -Djava.rmi.server.hostname=192.168.21.190" 
 export JAVA_OPTS="-Dcom.sun.management.jmxremote=
 -Dcom.sun.management.jmxremote.port=1099
 -Dcom.sun.management.jmxremote.ssl=false
 -Dcom.sun.management.jmxremote.authenticate=false"











选择文件，编辑模式进入：

[root@localhost bin]# vi redis.conf

进入后：

按下”/“键，这时在状态栏（也就是屏幕左下脚）就出现了 “/” 然后输入你要查找的关键字敲回车就可以了。

![img](https://img.php.cn/upload/image/536/571/781/1580712606362445.jpg)

找到相关文字以后：

（1）按下小写n，向下查找 

（2）按下大写N，向上查找







![image-20200318163852139](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200318163852139.png)

![image-20200319194852751](C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200319194852751.png)

使用apachectl restart 重启httpd服务器





