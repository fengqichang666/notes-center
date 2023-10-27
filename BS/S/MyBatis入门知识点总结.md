# MyBatis入门知识点总结

## [Mybatis](https://so.csdn.net/so/search?q=Mybatis&spm=1001.2101.3001.7020)入门

### 相关概念介绍

SSM = springmvc + spring + **mybatis** 组合框架的一员，是一种持久层框架

持久层主要是完成与数据库的相关操作，数据库访问对象\(**Data Access Object**\)，所以也称为DAO层

框架是一个半成品的软件，需要我们遵守对应的规范去完成开发工作

| 框架类型   | 框架作用                     | 典型代表                        |
| ---------- | ---------------------------- | ------------------------------- |
| 持久性框架 | 专注于解决数据持久化的框架   | mybatis、hibernate、spring jdbc |
| 表现层框架 | 专注于解决与用户交互的框架   | struts2、spring mvc             |
| 全栈框架   | 能在各层都给出解决方案的框架 | spring                          |

### 原始jdbc存在的问题

1.  频繁创建数据库连接、释放资源，影响系统性能
2.  sql写在java文件中\(俗称硬编码，因为每次修改都要重新编译代码\)，不宜于维护
3.  查询操作，需要将结果集手动封装到实体中

#### 解决方案

1.  使用数据库连接池初始化连接资源
2.  将sql语句抽取放在xml配置文件中
3.  使用反射、内省等底层技术，自动将实体和表进行属性和字段的自动映射

### mybatis简介

MyBatis是一个优秀的基于**ORM的半自动轻量级**持久层框架，让开发者们**只关注于SQL本身**，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码

**ORM思想：Object Relational Mapping 对象关系映射**

### 基本应用

 1.     创建数据库及表
 2.     创建工程，导入依赖

```xml
		<!--mybatis坐标-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>

        <!--mysql驱动坐标-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.41</version>
            <scope>runtime</scope>
        </dependency>
        
        <!--单元测试坐标-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- 分页助手 -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>3.7.5</version>
        </dependency>
        <dependency>
            <groupId>com.github.jsqlparser</groupId>
            <artifactId>jsqlparser</artifactId>
            <version>0.9.1</version>
        </dependency>
```

 3.     编写实体类
 4.     编写对象配置文件 xxx**Mapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">

</mapper>
```

 5.     编写**SqlMapConfig.xml**核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--加载properties文件-->
    <properties resource="jdbc.properties"></properties>

    <settings>
        <setting name="lazyLoadTriggerMethods" value="toString()"/>
    </settings>
    <!--批量取别名-->
    <typeAliases>
        <package name="com.hezhe.pojo"/>
    </typeAliases>


    <!--environments: 运行环境-->
    <environments default="development">
        <environment id="development">
            <!--当前的事务事务管理器是JDBC-->
            <transactionManager type="JDBC"></transactionManager>
            <!--数据源信息 POOLED：使用mybatis的连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!--<mapper resource="com/hezhe/mapper/UserMapper.xml"></mapper>-->
        <!--批量加载映射-->
        <package name="com.hezhe.mapper"/>
    </mappers>

</configuration>
```

6.  编写测试代码

### 基础的增\\删\\改\\查应用

使用sqlSession会话对象去调selectList | insert | update | detele

查询的参数为（**namespace.id**），其他均为（**namespace.id**，**param**）

对于修改数据库的操作均需要调用**sqlSession.commit\(\)**

### 核心配置文件概述

 1.     Mybatis核心文件是有强制的层次关系

```xml
<!--顶层-->
<configuration>
    <!--子层需要顺序编写-->
	<properties></properties> (属性，常用于配置数据源配置文件)
	<settings></settings> (设置，可设置延迟加载策略)
    <typeAliases></typeAliases> (类型别名)
	<typeHandlers></typeHandlers> (类型处理器)
    <objectFactory></objectFactory> (对象工厂)
    <plugins></plugins> (插件，分页插件)
    <environments> (重点：环境配置)
    	<environment> (环境变量)
        	<transactionManager></transactionManager> (事务管理器)
            <dataSource></dataSource> (数据源)
        </environment>
    </environments>
    <databaseIdProvider></databaseIdProvider> (数据库厂商标识)
    <mappers></mappers> (映射器)
</configuration>
```

 2.     enviroments标签

```xml
<!--environments: 运行环境-->
<environments default="development">
  <environment id="development">
     <!--当前的事务事务管理器是JDBC-->
     <transactionManager type="JDBC"></transactionManager>
     <!--数据源信息 POOLED：使用mybatis的连接池-->
     <dataSource type="POOLED">
        <property name="driver" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        </dataSource>
   </environment>
</environments>

<!-- 

1. 其中，事务管理器（transactionManager）类型有两种：
	- JDBC：     
	这个配置就是直接使用了JDBC 的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。

	- MANAGED：     
	这个配置几乎没做什么。它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期。     
	例如：		mybatis与spring整合后，事务交给spring容器管理。

2. 其中，数据源（dataSource）常用类型有三种：
	- UNPOOLED：
	这个数据源的实现只是每次被请求时打开和关闭连接。

	- POOLED：
	这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来。

	- JNDI :
	这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用

-->
```

 3.     properties标签

```xml
<!--加载外部的properties文件-->
<properties resource="jdbc.properties"></properties>

<dataSource type="POOLED">
    <!--在此使用-->
	<property name="driver" value="${jdbc.driver}"></property>
</dataSource>
```

 4.     typeAliases标签，用于取别名，方便配置
 5.     mappers标签，用于加载映射

```xml
<!--1. 使用相对于类路径的资源引用，例如：-->
<mapper resource="org/mybatis/builder/userMapper.xml"/>
<!--2. 使用完全限定资源定位符（URL），例如：-->
<mapper url="file:///var/mappers/userMapper.xml"/>  

《下面两种mapper代理开发中使用:暂时了解》
<!--3. 使用映射器接口实现类的完全限定类名，例如： -->
<mapper class="org.mybatis.builder.userMapper"/>
<!--4. 将包内的映射器接口实现全部注册为映射器，例如： -->
<package name="org.mybatis.builder"/>
```

### Mybatis常用[API](https://so.csdn.net/so/search?q=API&spm=1001.2101.3001.7020)

1.  Resources工具类帮助加载核心配置文件

2.  SqlSessionFactoryBuilder.bulid\(\); 构建工厂对象

3.  sqlSessionFactory.openSession\(\[boolean autoCommit\]\); 获取sqlSession会话对象

4.  sqlSession.增\\删\\改\\查\\commit\\rollback

### Mybatis代理开发方式

需要遵循的规范

1.  Mapper.xml映射文件中的namespace与mapper接口的全限定名相同
2.  Mapper接口方法名和Mapper.xml映射文件中定义的每个statement的id相同
3.  Mapper接口方法的输入参数类型和mapper.xml映射文件中定义的每个sql的parameterType的类型相同
4.  Mapper接口方法的输出参数类型和mapper.xml映射文件中定义的每个sql的resultType的类型相同

这样就可以调用 **sqlSession.getMapper\(xxxMapper.class\)** 来获取Mapper代理对象，使用接口中的方法

### 高级查询

#### ResultMap

```xml
<!--实现手动映射封装            
		resultMap                
			id="userResultMap" 此标签唯一标识                
			type="user" 封装后的实体类型           
		<id column="uid" property="id"></id> 表中主键字段封装                
			column="uid" 表中的字段名                
			property="id" user实体的属性名           
		<result column="NAME" property="username"></result> 表中普通字段封装                			column="NAME" 表中的字段名                
			property="username" user实体的属性名                
		补充：如果有查询结果有 字段与属性是对应的，可以省略手动封装 【了解】    
-->
	<resultMap id="articleWithCommentMap" type="article">
        <id column="id" property="id"/>
        <result column="title" property="title"/>
        <result column="content" property="content"/>
    </resultMap>
```

#### 多条件查询

方式一：使用 #\{arg0\}-#\{argn\} 或者 #\{param1\}-#\{paramn\} 获取接口请求参数

方式二：使用注解，接口中引入\@Param\(“name”\)注解，再在配置文件中 #\{name\}获取参数

方式三：使用pojo对象传递参数，配置文件中按照实体类的属性获取参数

#### 模糊查询

```xml
<!--
	List<User> list = userMapper.findByUsername1/2("%王%");
-->

<!--方式1-->
<select id="findByUsername1" parameterType="string" resultType="user">       
    select * from user where username like #{username}    
</select> 

<!--方式2-->
<!--不推荐使用，因为会出现sql注入问题-->    
<select id="findByUsername2" parameterType="string" resultType="user">       
    select * from user where username like '${value}'    
</select>
```

#### \$\{\}与#\{\}的区别

##### #\{\}:表示一个[占位符](https://so.csdn.net/so/search?q=%E5%8D%A0%E4%BD%8D%E7%AC%A6&spm=1001.2101.3001.7020)号

1.  通过#\{\}可以实现preparedStatement向占位符中设置值，自动进行java类型和jdbc类型转换，#\{\}可以有效防止sql注入。
2.  #\{\}可以接收简单类型值或pojo属性值。
3.  **如果parameterType传输单个简单类型值，#\{\}括号中名称随便写。**

##### \$\{\}:表示拼接sql串

1.  通过\$\{\}可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换，会出现sql注入问题。

2.  \$\{\}可以接收简单类型值或pojo属性值。

3.  **如果parameterType传输单个简单类型值，\$\{\}括号中只能是value。**

    – \(补充：TextSqlNode.java 源码可以证明 \)

### 映射文件深入

#### 返回[主键](https://so.csdn.net/so/search?q=%E4%B8%BB%E9%94%AE&spm=1001.2101.3001.7020)

```xml
<!--
	public void save(User user);
-->

<!--方式1：useGeneratedKeys--> 
<!--        
	useGeneratedKeys="true" 声明返回主键        
	keyProperty="id" 把返回主键的值，封装到实体的id属性中 

	注意：只适用于主键自增的数据库，mysql和sqlserver支持，oracle不支持 
-->
<insert id="save" parameterType="user" useGeneratedKeys="true" keyProperty="id">   
    INSERT INTO `user`(username,birthday,sex,address)    
    	values(#{username},#{birthday},#{sex},#{address})
</insert>


<!--方式2：selectKey-->
<!--        
	selectKey 适用范围广，支持所有类型数据库            
	keyColumn="id" 指定主键列名            
	keyProperty="id" 指定主键封装到实体的id属性中            
	resultType="int" 指定主键类型            
	order="AFTER"   设置在sql语句执行前（后），执行此语句
-->
<insert id="save" parameterType="user">    
    <selectKey keyColumn="id" keyProperty="id" resultType="int" order="AFTER">       	
        SELECT LAST_INSERT_ID();    
    </selectKey>   
    INSERT INTO `user`(username,birthday,sex,address)    
    values(#{username},#{birthday},#{sex},#{address})
</insert>
```

#### 动态SQL

1.  where + if test=“表达式”
2.  set + if test=“表达式”
3.  foreach \~
    * collection：代表要遍历的集合元素
    * open：代表语句的开始部分
    * close：代表结束部分
    * item：代表遍历集合的每个元素，生成的变量名
    * sperator：代表分隔符
    * 如果查询条件为普通类型 List集合，collection属性值为：collection 或者 list
    * 如果查询条件为普通类型 Array数组，collection属性值为：array

#### SQL片段

介绍：映射文件中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

```xml
<!--抽取的sql片段-->
<sql id="selectUser">   
    SELECT * FROM `user`
</sql>

<select id="findByList" parameterType="list" resultType="user" >    
    <!--引入sql片段-->    
    <include refid="selectUser"></include>    
    <where>        
        <foreach collection="collection" open="id in(" close=")" item="id" separator=",">           #{id}        
        </foreach>    
    </where>
</select>
```

### Mybatis核心配置文件深入

#### plugins标签：引入第三方插件

##### 分页助手PageHelper的使用

 1.     导入通用PageHelper的坐标

```xml
<!-- 分页助手 -->
<dependency>    
    <groupId>com.github.pagehelper</groupId>    
    <artifactId>pagehelper</artifactId>    
    <version>3.7.5</version>
</dependency>
<dependency>    
    <groupId>com.github.jsqlparser</groupId>    
    <artifactId>jsqlparser</artifactId>    
    <version>0.9.1</version>
</dependency>
```

 2.     在mybatis核心配置文件中配置PageHelper插件

```xml
<!-- 分页助手的插件   -->
<plugin interceptor="com.github.pagehelper.PageHelper">    
    <!-- 指定方言 -->    
    <property name="dialect" value="mysql"/>
</plugin>
```

 3.     使用分页功能，调用API

```java
@Test
public void testPageHelper(){    
    //设置分页参数    
    PageHelper.startPage(1,2);    
    List<User> select = userMapper2.select(null);    
    for(User user : select){        
        System.out.println(user);   
    }
}

//其他分页的数据
PageInfo<User> pageInfo = new PageInfo<User>(select);
System.out.println("总条数："+pageInfo.getTotal());
System.out.println("总页数："+pageInfo.getPages());
System.out.println("当前页："+pageInfo.getPageNum());
System.out.println("每页显示长度："+pageInfo.getPageSize());
System.out.println("是否第一页："+pageInfo.isIsFirstPage());
System.out.println("是否最后一页："+pageInfo.isIsLastPage());
```

### 多表查询

数据库的表及实体类的关系需要对应起来

一对一：子表要有对应的外键，主实体要关联对象的属性，并提供get/set方法

一对多：子表要有对应的外键，主实体要关联对象的集合，并提供get/set方法

多对多：要有关联表提供对应的外键，主实体要关联对象的集合，并提供get/set方法

具体实现：

* 多对一（一对一）配置：使用+做配置
* 一对多配置：使用+做配置
* 多对多配置：使用+做配置
* 多对多的配置跟一对多很相似，难度在于SQL语句的编写。

### 嵌套查询：将[联合查询](https://so.csdn.net/so/search?q=%E8%81%94%E5%90%88%E6%9F%A5%E8%AF%A2&spm=1001.2101.3001.7020)分解成几个sql语句

一对一配置：使用+做配置，通过column条件，执行select

查询一对多配置：使用+做配置，通过column条件，执行select

查询多对多配置：使用+做配置，通过column条件，执行select

查询优点：简化多表查询操作

缺点：执行多次[sql语句](https://so.csdn.net/so/search?q=sql%E8%AF%AD%E5%8F%A5&spm=1001.2101.3001.7020)，浪费数据库性能

### 延迟加载\(懒加载\)

优点：先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快。

缺点：因为只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗时间，所以可能造成用户等待时间变长，造成用户体验下降。

注意点：

```
1. **延迟加载是基于嵌套查询来实现的**
2. **局部配置高于全局配置**
```

##### 具体实现：

 局部配置fetchType属性

 全局配置核心配置文件

```xml
<settings>    
    <!--开启全局延迟加载功能-->        
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

核心配置文件中设置延迟加载策略

```xml
<settings>    
    <!--所有方法都会延迟加载,针对equals\clone\hashCode\toString-->
    <setting name="lazyLoadTriggerMethods" value="toString()"/>
</settings>
```

### Mybatis缓存

缓存的作用：**经常查询一些不经常发生变化的数据，使用缓存来提高查询效率。**

#### 一级缓存：sqlSession级别的缓存

1.  默认开启的
2.  所以在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法
3.  执行SqlSession的CUD操作，或者调用clearCache\(\)、commit\(\)、close\(\)方法，都会清空缓存
4.  可设置\< select flushCache=“true”>来默认查询时清楚缓存

#### 二级缓存：namespace级别的缓存

 1.     二级缓存的开启需要进行配置

```xml
<!--核心配置文件-->
<settings>    
    <!-- 因为cacheEnabled的取值默认就为true，所以这一步可以省略不配置。 
		为true代表开启二级缓存；为false代表不开启二级缓存。    -->    
    <setting name="cacheEnabled" value="true"/>
</settings>

<!--对象映射文件-->
<mapper namespace="com.hezhe.dao.UserMapper">    
    <!--当前映射文件开启二级缓存-->    
    <cache></cache>    
    <!-- 
		<select>标签中设置useCache=”true”代表当前这个statement要使用二级缓存。 
		如果不使用二级缓存可以设置为false        
		注意：            
			针对每次查询都需要最新的数据sql，要设置成useCache="false"，禁用二级缓存。 
	-->    
    <select id="findById" parameterType="int" resultType="user" useCache="true" >       
        SELECT * FROM `user` where id = #{id}    
    </select>
</mapper>
```

2.  多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。

3.  进行多表查询时会产生脏读问题，需要使用第三方的缓存技术解决问题。

4.  mybatis开启了二级缓存后，那么查询顺序：二级缓存–》一级缓存–》数据库

5.  mybatis的缓存，都不需要我们手动存储和获取数据。mybatis自动维护的。

### 常用注解

* \@Insert：实现新增，代替了insert标签

* \@Delete：实现删除，代替了delete标签

* \@Update：实现更新，代替了update标签

* \@Select：实现查询，代替了select标签

* \@Result：实现结果集封装，代替了result标签

* \@Results：可以与\@Result 一起使用，封装多个结果集，代替了resultMap标签

* \@One：实现一对一结果集封装，代替了association标签

  * \@Many：实现一对多结果集封装，代替了collection标签

  注解开发和xml配置优劣分析

    1.  注解开发和xml配置相比，从开发效率来说，注解编写更简单，效率更高。

    2.  从可维护性来说，注解如果要修改，必须修改源码，会导致维护成本增加。xml维护性更强。

  #### 增删改查

```java
public interface UserMapper {        
    @Select("SELECT * FROM `user`")    
    public List<User> findAll(); 
    
    @Insert("INSERT INTO `user`(username,birthday,sex,address) VALUES(#{username},#{birthday},#{sex},#{address})")    
    public void save(User user);    
    
    @Update("UPDATE `user` SET username = #{username},birthday = #{birthday},sex = #{sex},address = #{address} WHERE id = #{id}")    
    public void update(User user);    
    
    @Delete("DELETE FROM `user` where id = #{id}")    
    public void delete(Integer id);
}
```

#### 复杂映射

##### 一对一

```java
public interface OrderMapper {    
    @Select("SELECT * FROM orders")    
    @Results({            
        @Result(id = true, column = "id", property = "id"),            
        @Result(column = "ordertime", property = "ordertime"),            
        @Result(column = "money", property = "money"),            
        @Result(property = "user", javaType = User.class,                    
                column = "uid", one = @One(select = "com.hezhe.mapper.UserMapper.findById", fetchType = FetchType.EAGER))   })    
    public List<Order> 
        findAllWithUser();
}
```

##### 一对多

```java
public interface UserMapper {    
    @Select("SELECT * FROM `user`")    
    @Results({        
        @Result(id = true, column = "id", property = "id"),        
        @Result(column = "brithday", property = "brithday"),        
        @Result(column = "sex", property = "sex"),        
        @Result(column = "address", property = "address"),        
        @Result(property = "orderList", javaType = List.class,                
                column = "id" ,                
                many = @Many(select = "com.hezhe.mapper.OrderMapper.findByUid"))   
    })    
    public List<User> findAllWithOrder();
}
```

##### 多对多

```java
public interface UserMapper {    
    @Select("SELECT * FROM `user`")    
    @Results({        
        @Result(id = true, column = "id", property = "id"),        
        @Result(column = "brithday", property = "brithday"),        
        @Result(column = "sex", property = "sex"),        
        @Result(column = "address", property = "address"),        
        @Result(property = "roleList", javaType = List.class,                
                column = "id" ,                
                many = @Many(select = "com.hezhe.mapper.RoleMapper.findByUid"))   
    })    
    public List<User> findAllWithRole();
}
```

#### 二级缓存

```java
@CacheNamespace
public interface UserMapper {...}
```

#### 延迟加载注解

在一对一或时一对多注解中加上fetchType属性即可
