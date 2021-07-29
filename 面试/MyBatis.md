## Mybatis 定义

- Mybatis是一个半ORM（对象关系映射）框架，内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力处理加载驱动，创建连接，创建statement等繁杂的过程。程序员直接编写原生态的SQL，可以严格控制SQL执行性能，灵活度高。
- Mybatis可以使用XML或者注解来配置和映射原生信息，将POJO映射成数据库中的记录，避免了几乎所有的JDBC代码和手动设置参数以及获取结果集
- 通过XML文件或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中SQL的动态参数进行映射生成最终执行的SQL语句，最后由Mybatis执行SQL并将结果集映射为java对象并返回

## Mybatis 定义

1. 基于SQL编程，相当灵活。
2. 与JDBC相比，减少了50%以上代码量，消除了JDBC大量冗余代码，不需要手动开关连接
3. 与各种数据库兼容
4. 与Spring很好的集成
5. 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护

## Mybatis缺点

1. 编写sql工作量大
2. SQL依赖数据库，导致数据库移植性差

## Mybatis和Hibernate区别

- Mybatis不完全是一个ORM框架，因为Mybatis需要程序员自己编写SQL语句
- Mybatis直接编写原生态sql
- Hibernate对象/关系映射能力强，数据库无关性好

## #{}和 ${}区别

#{}是预编译处理，${}是字符串替换

在处理#{}时，会将SQL中的#{}替换为？，调用PreparedStatement的set赋值

在处理${}时，会把${}替换为变量值

使用#{}可以防止SQL注入

## 实体类属性名和表中字段名不一样时，怎么办

+ 通过查询的SQL语句定义字段名的别名，让字段名的别名和实体类的属性名一致
+ 通过\<resultMap>映射字段名和实体类属性名的一一对应关系

## DAO接口的工作原理

​		Dao接口即Mapper接口。接口的全限名，就是映射文件中namespace的值。接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给SQL的参数。

​		Mapper接口没有实现类，当调用接口方法时，接口全限名  + 方法名拼接字符串作为key，可唯一定位一个MapperStatement。在Mybatis中，每一个\<select>、\<insert>、\<update>、\<delete>都会被解析为一个MapperStatement对象。

## Dao接口里的方法，参数不同时，能否被重载。

不能，因为使用的是全限名  + 方法名。

## Mybatis分页

使用RowBounds对象进行分页。针对ResultSet结果结果集执行的内存分页，而非物理分页。

## Mybatis批量插入

1. 创建简单的insert语句

       <insert id=”insertname”>
            insert into names (name) values (#{value})
       </insert>

2. 执行批量插入

       list<string> names = new arraylist();
       names.add(“fred”);
       names.add(“barney”);
       names.add(“betty”);
       names.add(“wilma”);
       // 注意这里 executortype.batch
       sqlsession sqlsession = sqlsessionfactory.opensession(executortype.batch);
       try {
        namemapper mapper = sqlsession.getmapper(namemapper.class);
        for (string name : names) {
            mapper.insertname(name);
        }
        sqlsession.commit();
       }catch(Exception e){
        e.printStackTrace();
        sqlSession.rollback(); 
        throw e; 
       }
        finally {
            sqlsession.close();
       }

## 获取自动生成的（主）键值

insert方法总是返回一个int，这个值代表的是插入的行数

如果是自增方式，自动生成的键值在insert执行完成后可以被设置到传入的参数对象中。

## 在mapper中传递多个参数

1. dao层接口：``` User  selectUser(String userName, String area);```

   xml文件：

   ```xml
   <select id="insertUser" resultMap="BaseResultMap">
   	select * from t_user  where username = #{0} and area = #{1}
   </select>
   ```

2. 使用param注释

   dao层接口：```User  selectUser(@Param("userName")String userName, @Param("area")String area);```

   xml文件：

   ```xml
   <select>
   	select * from t_user where userName = #{userName} and area = #{area}
   </select>
   ```

3. 多个参数封装成map

## Mybatis动态SQL有什么用？执行原理？哪些动态SQL

Mybatis动态SQL可以在XML映射文件中，以标签的形式编写动态SQL，执行原理是根据表达式的值，完成逻辑判断，并动态拼接SQL的功能。

9个动态SQL标签：trim|where| set| foreach|if|choose|when| otherwise|bind

## XML 映射文件中，除了常见的select|insert|update|delete标签之外，还有哪些标签

\<resultMap> 、\<parameterMap>、\<sql>、\<include>、\<selectKey>

## Mybatis实现一对一有几种方式

联合查询：几个表联合查询，只查询一次，通过在resultMap里配置association结点配置一对一的类就可以完成

嵌套查询：先查一个表，根据这个表里面的结果的外键id，去另一个表查询数据，也是通过association配置，但另一个表的查询通过select属性配置

## Mybatis延迟加载

延迟加载条件：resultMap可以实现高级映射（使用association、collection）

延迟加载好处：先从单表查询，如果需要再从关联表进行查询，大大提高数据库性能，因为单表查询要比多表查询要快

开启延迟加载：

- lazyLoadingEnabled：全局性设置懒加载。如果为false，所有相关联的都会被初始化。
- aggressiveLazyLoading：当设置为true时，懒加载的对象可能被任何懒属性全部加载，否则，每个属性都按需加载，默认为true

## Mybatis一级、二级缓存

一级缓存：基于PerpetualCache的HashMap本地缓存，存储作用域为Session。当session flush/close后，一级缓存会被全部清空。默认开启一级缓存。

二级缓存：机制与一级缓存相同，基于Perpetual的HashMap本地缓存。其作用域为Mapper(namespace)，并且可以自定义数据源，如Ecache。默认关闭。



## 使用Mybatis的mapper接口调用时有哪些要求

- Mapper接口方法名与mapper.xml中定义的每个SQL的id相同。
- Mapper接口参数类型与mapper.xml中定义的每个SQL的parameterType的类型相同。
- Mapper接口方法的输出参数类型和mapper.xml中定义的每个SQL的resultType类型相同
- Mapper.xml文件中的namespace即是mapper接口的类路径



