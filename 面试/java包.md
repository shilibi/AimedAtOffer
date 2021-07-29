com.ouc.bean  || com.ouc.entities   JavaBean实体类，存放与数据库表对应的实体类，如Student、User、Teacher 等等，即Model  层

com.ouc.controller    控制层，用于接收来自前端的请求，在这里一般调用com.ouc.service方法。

com.ouc.service        服务层，定义接口

com.ouc.service.impl    服务层的实现层，这里实现com.ouc.service定义的接口

com.ouc.utils             工具类，将一些常用的方法抽取出来放在这里

com.ouc.dao              这里定义数据库查询方法

com.ouc.aspect         切面的，没用过

com.ouc.pojo             好像是定义一些数据库不存在的JavaBean