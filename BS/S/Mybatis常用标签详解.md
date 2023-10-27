# Mybatis常用标签详解

> [MyBatis](https://so.csdn.net/so/search?q=MyBatis&spm=1001.2101.3001.7020) 的真正强大在于它的语句映射，这是它的魔力所在。由于它的异常强大，映射器的 XML文件就显得相对简单。如果拿它跟具有相同功能的 JDBC 代码进行对比，你会立即发现省掉了将近 95\% 的代码。MyBatis致力于减少使用成本，让用户能更专注于 SQL 代码。

## 命名空间

在之前版本的 MyBatis 中，**命名空间（Namespaces）** 的作用并不大，是可选的。 但现在，随着命名空间越发重要，你必须指定命名空间。命名空间的作用：

* 利用更长的全限定名来将不同的语句隔离开来，同时也实现了你上面见到的接口绑定。
* 只要将命名空间置于合适的Java包命名空间之中，你的代码会变得更加整洁，也有利于你更方便地使用 MyBatis。

**命名解析：** 为了减少输入量，MyBatis 对所有具有名称的配置元素（包括语句，结果映射，缓存等）使用了如下的命名解析规则。

* 全限定名（比如 “com.mypackage.MyMapper.selectAllThings）将被直接用于查找及使用。
* 短名称（比如 “selectAllThings”）如果全局唯一也可以作为一个单独的引用。 如果不唯一，有两个或两个以上的相同名称（比如 “com.foo.selectAllThings” 和 “com.bar.selectAllThings”），那么使用时就会产生“短名称不唯一”的错误，这种情况下就必须使用全限定名。

## 顶级元素

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

* cache：该命名空间的缓存配置
* cache-ref：引用其它命名空间的缓存配置
* resultMap：描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素
* ~~parameterMap：老式风格的参数映射。此元素已被废弃，并可能在将来被移除~~
* sql：可被其它语句引用的可重用语句块
* insert：映射插入语句
* update：映射更新语句
* delete：映射删除语句
* select：映射查询语句

* * *

### select

查询语句是 MyBatis 中最常用的元素之一，光能把数据存到数据库中价值并不大，还要能重新取出来才有用，多数应用也都是查询比修改要频繁。 _MyBatis 的基本原则之一是：_ 在每个插入、更新或删除操作之间，通常会执行多个查询操作。因此，MyBatis 在查询和结果映射做了相当多的改进。select 元素允许你配置很多属性来配置每条语句的行为细节。

```sql
<select
  id="selectUser" 
  parameterType="int"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">  
  //此处是你的sql语句 例如：
  select * from t_user where id = #{id}  
</select>
```

**select属性介绍：**

| 属性          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| id            | 在命名空间中唯一的标识符，可以被用来引用这条语句，与Mapper接口一一对应。 |
| parameterType | 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。一般可以是基本数据类型，String，Integer，POJO，Map，List等 |
| resultType    | 期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 resultType 和 resultMap 之间只能同时使用一个。 |
| resultMap     | 对外部 resultMap 的命名引用。结果映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 resultType 和 resultMap 之间只能同时使用一个。 |
| flushCache    | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false。 |
| useCache      | 将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true。 |
| timeout       | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| fetchSize     | 这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值。 默认值为未设置（unset）（依赖驱动）。 |
| statementType | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| resultSetType | FORWARD\_ONLY，SCROLL\_SENSITIVE, SCROLL\_INSENSITIVE 或 DEFAULT（等价于 unset） 中的一个，默认值为 unset （依赖数据库驱动）。 |
| databaseId    | 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有不带 databaseId 或匹配当前 databaseId 的语句；如果带和不带的语句都有，则不带的会被忽略。 |
| resultOrdered | 这个设置仅针对嵌套结果 select 语句：如果为 true，将会假设包含了嵌套结果集或是分组，当返回一个主结果行时，就不会产生对前面结果集的引用。 这就使得在获取嵌套结果集的时候不至于内存不够用。默认值：false。 |
| resultSets    | 这个设置仅适用于多结果集的情况。它将列出语句执行后返回的结果集并赋予每个结果集一个名称，多个名称之间以逗号分隔。 |

**注意：** 与java对象对应的列不是数据库中表的列名，而是查询后结果集的列名

* * *

### insert, update 和 delete

数据变更语句 insert，update 和 delete 的实现非常接近：

```sql
<insert
  id="insertUser"
  parameterType="com.learn.pojo.User"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">
  //此处是你的sql语句 例如：
  insert into t_user (id,username,password,sex,age) values (#{id},#{username},#{password},#{sex},#{age})
</insert>

<update
  id="updateUser"
  parameterType="com.learn.pojo.User"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
  //此处是你的sql语句 例如：  
  update t_user set password = #{password} where id = #{id}
</update>

<delete
  id="deleteUser"
  parameterType="com.learn.pojo.User"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
  //此处是你的sql语句 例如：
  delete from t_user where id = #{id}
</delete>
```

**insert, update 和 delete属性介绍：**

| 属性             | 描述                                                         |
| :--------------- | :----------------------------------------------------------- |
| id               | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| parameterType    | 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。一般可以是基本数据类型，String，Integer，POJO，Map，List等 |
| parameterMap     | 用于引用外部 parameterMap 的属性，目前已被废弃。请使用行内参数映射和 parameterType 属性。 |
| flushCache       | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：（对 insert、update 和 delete 语句）true。 |
| timeout          | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| statementType    | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| useGeneratedKeys | （仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false。 |
| keyProperty      | （仅适用于 insert 和 update）指定能够唯一识别对象的属性，MyBatis 会使用 getGeneratedKeys 的返回值或 insert 语句的 selectKey 子元素设置它的值，默认值：未设置（unset）。如果生成列不止一个，可以用逗号分隔多个属性名称。 |
| keyColumn        | （仅适用于 insert 和 update）设置生成键值在表中的列名，在某些数据库（像 PostgreSQL）中，当主键列不是表中的第一列的时候，是必须设置的。如果生成列不止一个，可以用逗号分隔多个属性名称。 |
| databaseId       | 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有不带 databaseId 或匹配当前 databaseId 的语句；如果带和不带的语句都有，则不带的会被忽略。 |

**注意：** 带条件的删除语句，不要对表使用别名，否则会报错

* * *

### sql

这个元素可以用来定义可重用的 SQL 代码片段，以便在其它语句中使用。 参数可以静态地（在加载的时候）确定下来，并且可以在不同的 include 元素中定义不同的参数值。例如：

```sql
  <sql id="Base_Column_List" >
    id, user_name, password, sex, age
  </sql>
  //这个 SQL 片段可以在其它语句中使用，例如：
  <select id="selectByPrimaryKey" resultMap="BaseResultMap" parameterType="java.lang.Long">
    select <include refid="Base_Column_List" />
    from t_user 
    where id = #{id,jdbcType=BIGINT}
  </select>
```

也可以在 include 元素的 refid 属性或内部语句中使用属性值，例如：

```sql
<sql id="sometable">
  t_order_${suffix}
</sql>

<sql id="someinclude">
  from
    <include refid="${include_target}"/>
</sql>

<select id="select" resultType="map">
  select
    field1, field2, field3
  <include refid="someinclude">
    <property name="suffix" value="202107"/>
    <property name="include_target" value="sometable"/>
  </include>
</select>
// 最终执行的SQL可能是：
// select field1, field2, field3 from t_order_202107
```

* * *

### resultMap

resultMap 元素是 MyBatis 中最重要最强大的元素。它可以让你从 90\% 的 JDBC ResultSets 数据提取代码中解放出来，并在一些情形下允许你进行一些 JDBC 不支持的操作。实际上，在为一些比如连接的复杂语句编写映射代码的时候，一份 resultMap 能够代替实现同等功能的数千行代码。**ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。**  
如果一个select没有显式的使用resultMap，MyBatis会在幕后自动创建一个ResultMap，再根据属性名来映射列到JavaBean的属性上。如果列名和属性名不能匹配上，可以在select语句中设置列别名来完成匹配。

**简单结果映射：**

```sql
//方式一
<select id="selectUsers" resultType="com.learn.pojo.User">
  select 
    id              as "userId",
    user_name       as "userName",
    password     	as "password",
    sex				as "sex",
    age				as "age"				
  from t_user
  where id = #{id}
  // 如果表中的字段和java对象中的字段完全一样，可以不用起别名，例如password，sex，age可以不用别名，也会自动完成映射
</select>

//方式二（推荐）
<resultMap id="userResultMap" type="com.test.pojo.User">
  <id property="userId" 		column="id" />
  <result property="userName" 	column="user_name"/>
  <result property="password" 	column="password"/>
  <result property="sex" 		column="sex"/>
  <result property="age" 		column="age"/>
</resultMap>
<select id="selectUsers" resultMap="userResultMap">
  select id, user_name, password, sex, age
  from t_user
  where id = #{id}
</select>
```

**高级结果映射：**  
如果这个世界总是这么简单就好了。比如，我们如何映射下面这个语句？

```sql
<!-- 非常复杂的语句 -->
<select id="selectBlogDetails" resultMap="detailedBlogResultMap">
  select
       B.id as blog_id,
       B.title as blog_title,
       B.author_id as blog_author_id,
       A.id as author_id,
       A.username as author_username,
       A.password as author_password,
       A.email as author_email,
       A.bio as author_bio,
       A.favourite_section as author_favourite_section,
       P.id as post_id,
       P.blog_id as post_blog_id,
       P.author_id as post_author_id,
       P.created_on as post_created_on,
       P.section as post_section,
       P.subject as post_subject,
       P.draft as draft,
       P.body as post_body,
       C.id as comment_id,
       C.post_id as comment_post_id,
       C.name as comment_name,
       C.comment as comment_text,
       T.id as tag_id,
       T.name as tag_name
  from Blog B
       left join t_author A on B.author_id = A.id
       left join t_post P on B.id = P.blog_id
       left join t_comment C on P.id = C.post_id
       left join t_post_Tag PT on PT.post_id = P.id
       left join t_tag T on PT.tag_id = T.id
  where B.id = #{id}
</select>
```

你可能想把它映射到一个智能的对象模型，这个对象表示了一篇博客，它由某位作者所写，有很多的博文，每篇博文有零或多条的评论和标签。 我们先来看看下面这个完整的例子，它是一个非常复杂的结果映射（假设作者，博客，博文，评论和标签都是类型别名）。不用紧张，我们会一步一步地来说明。虽然它看起来令人望而生畏，但其实非常简单。

```xml
<!-- 非常复杂的结果映射 -->
<resultMap id="detailedBlogResultMap" type="Blog">
  <constructor>
    <idArg column="blog_id" javaType="int"/>
  </constructor>
  <result property="title" column="blog_title"/>
  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
    <result property="favouriteSection" column="author_favourite_section"/>
  </association>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <association property="author" javaType="Author"/>
    <collection property="comments" ofType="Comment">
      <id property="id" column="comment_id"/>
    </collection>
    <collection property="tags" ofType="Tag" >
      <id property="id" column="tag_id"/>
    </collection>
    <discriminator javaType="int" column="draft">
      <case value="1" resultType="DraftPost"/>
    </discriminator>
  </collection>
</resultMap>
```

**resultMap属性介绍：**

| 属性        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| id          | 当前命名空间中的一个唯一标识，用于标识一个结果映射。         |
| type        | 类的完全限定名, 或者一个类型别名（关于内置的类型别名，可以参考上面的表格）。 |
| autoMapping | 如果设置这个属性，MyBatis 将会为本结果映射开启或者关闭自动映射。 这个属性会覆盖全局的属性 autoMappingBehavior。默认值：未设置（unset）。 |
| extends     | 可以实现存在继承关系的两个类实体的映射。extends填父类的resultMap的id或父类的命名空间.父类的resultMap的Id。 |

**resultMap 子元素介绍：**

| 子元素                                                       | 描述                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------- |
| id                                                           | 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能 |
| result                                                       | 注入到字段或 JavaBean 属性的普通结果                   |
| association                                                  | 一个复杂类型的**关联**；许多结果将包装成这种类型       |
| 嵌套结果映射: 关联可以是 resultMap 元素，或是对其它结果映射的引用 |                                                        |
| collection                                                   | 一个复杂类型的**集合**                                 |
| 嵌套结果映射:集合可以是 resultMap 元素，或是对其它结果映射的引用 |                                                        |
| discriminator                                                | 使用结果值来决定使用哪个resultMap;                     |
| case:基于某些值的结果映射,也是一个结果映射，因此具有相同的结构和元素；或者引用其它的结果映射 |                                                        |
| constructor                                                  | 用于在实例化类时，注入结果到构造方法中                 |
| **idArg：** ID 参数，标记出作为ID的结果可以帮助提高整体性能  |                                                        |
| **arg：** 将被注入到构造方法的一个普通结果                   |                                                        |

**Id 和 Result 的属性**

| 属性        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| property    | 映射到列结果的字段或属性。如果 JavaBean 有这个名字的属性（property），会先使用该属性。否则 MyBatis 将会寻找给定名称的字段（field）。 无论是哪一种情形，你都可以使用常见的点式分隔形式进行复杂属性导航。 比如，你可以这样映射一些简单的东西：“username”，或者映射到一些复杂的东西上：“address.street.number”。 |
| column      | 数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString\(columnName\) 方法的参数一样。 |
| javaType    | 一个 Java 类的全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。 |
| jdbcType    | JDBC 类型，所支持的 JDBC 类型参见这个表格之后的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可以为空值的列指定这个类型。 |
| typeHandler | 我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的全限定名，或者是类型别名。 |

这两者之间的唯一不同是，id 元素对应的属性会被标记为对象的标识符，在比较对象实例时使用。 这样可以提高整体的性能，尤其是进行缓存和嵌套结果映射（也就是连接映射）的时候。剩余的属性和规则和普通的 id 和 result 元素是一样的。

| 属性        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| column      | 数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString\(columnName\) 方法的参数一样。 |
| javaType    | 一个 Java 类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。 |
| jdbcType    | JDBC 类型，所支持的 JDBC 类型参见这个表格之前的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。 |
| typeHandler | 我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。 |
| select      | 用于加载复杂类型属性的映射语句的 ID，它会从 column 属性中指定的列检索数据，作为参数传递给此 select 语句。具体请参考关联元素。 |
| resultMap   | 结果映射的 ID，可以将嵌套的结果集映射到一个合适的对象树中。 它可以作为使用额外 select 语句的替代方案。它可以将多表连接操作的结果映射成一个单一的 ResultSet。这样的 ResultSet 将会将包含重复或部分数据重复的结果集。为了将结果集正确地映射到嵌套的对象树中，MyBatis 允许你 “串联”结果映射，以便解决嵌套结果集的问题。想了解更多内容，请参考下面的关联元素。 |
| name        | 构造方法形参的名字。从 3.4.3 版本开始，通过指定具体的参数名，你可以以任意顺序写入 arg 元素。参看上面的解释。 |

**关联**

```sql
<association property="author" column="blog_author_id" javaType="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
</association>
```

**关联（association）** 元素处理 **“有一个”** 类型的关系。 比如，在我们的示例中，一个博客有一个用户。关联结果映射和其它类型的映射工作方式差不多。 你需要指定目标属性名以及属性的javaType（很多时候 MyBatis 可以自己推断出来），在必要的情况下你还可以设置 JDBC 类型，如果你想覆盖获取结果值的过程，还可以设置类型处理器。

关联的不同之处是，你需要告诉 MyBatis 如何加载关联。MyBatis 有两种不同的方式加载关联：

* 嵌套 Select 查询：通过执行另外一个 SQL 映射语句来加载期望的复杂类型。
* 嵌套结果映射：使用嵌套的结果映射来处理连接结果的重复子集。

**关联的基本属性**  
和普通的结果映射相比，它只在 select 和 resultMap 属性上有所不同。

| 属性        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| property    | 映射到列结果的字段或属性。如果用来匹配的 JavaBean 存在给定名字的属性，那么它将会被使用。否则 MyBatis 将会寻找给定名称的字段。 无论是哪一种情形，你都可以使用通常的点式分隔形式进行复杂属性导航。 比如，你可以这样映射一些简单的东西：“username”，或者映射到一些复杂的东西上：“address.street.number”。 |
| javaType    | 一个 Java 类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。 |
| jdbcType    | JDBC 类型，所支持的 JDBC 类型参见这个表格之前的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。 |
| typeHandler | 我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。 |

**关联的嵌套 Select 查询**

| 属性      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| select    | 用于加载复杂类型属性的映射语句的 ID，它会从 column 属性指定的列中检索数据，作为参数传递给目标 select 语句。 具体请参考下面的例子。注意：在使用复合主键的时候，你可以使用 column="\{prop1=col1,prop2=col2\}" 这样的语法来指定多个传递给嵌套 Select 查询语句的列名。这会使得 prop1 和 prop2 作为参数对象，被设置为对应嵌套 Select 语句的参数。 |
| column    | 数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString\(columnName\) 方法的参数一样。 注意：在使用复合主键的时候，你可以使用 column="\{prop1=col1,prop2=col2\}" 这样的语法来指定多个传递给嵌套 Select 查询语句的列名。这会使得 prop1 和 prop2 作为参数对象，被设置为对应嵌套 Select 语句的参数。 |
| fetchType | 可选的。有效值为 lazy 和 eager。 指定属性后，将在映射中忽略全局配置参数 lazyLoadingEnabled，使用属性的值。 |

一个简单的例子：

```sql
<resultMap id="blogResult" type="Blog">
  <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM t_blog WHERE ID = #{id}
</select>

<select id="selectAuthor" resultType="Author">
  SELECT * FROM t_author WHERE ID = #{id}
</select>
```

我们有两个 select 查询语句：一个用来加载博客（Blog），另外一个用来加载作者（Author），而且博客的结果映射描述了应该使用 selectAuthor 语句加载它的 author 属性。其它所有的属性将会被自动加载，只要它们的列名和属性名相匹配。  
这种方式虽然很简单，但在大型数据集或大型数据表上表现不佳。这个问题被称为\*\*“N+1 查询问题”\*\*。 概括地讲，N+1 查询问题是这样子的：

* 你执行了一个单独的 SQL 语句来获取结果的一个列表（就是“+1”）。
* 对列表返回的每条记录，你执行一个 select 查询语句来为每条记录加载详细信息（就是“N”）。

这个问题会导致成百上千的 SQL 语句被执行。有时候，我们不希望产生这样的后果。  
好消息是，MyBatis 能够对这样的查询进行延迟加载，因此可以将大量语句同时运行的开销分散开来。 然而，如果你加载记录列表之后立刻就遍历列表以获取嵌套的数据，就会触发所有的延迟加载查询，性能可能会变得很糟糕。  
所以还有另外一种方法。

**关联的嵌套结果映射**

| 属性          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| resultMap     | 结果映射的 ID，可以将此关联的嵌套结果集映射到一个合适的对象树中。 它可以作为使用额外 select 语句的替代方案。它可以将多表连接操作的结果映射成一个单一的 ResultSet。这样的 ResultSet 有部分数据是重复的。 为了将结果集正确地映射到嵌套的对象树中, MyBatis 允许你“串联”结果映射，以便解决嵌套结果集的问题。使用嵌套结果映射的一个例子在表格以后。 |
| columnPrefix  | 当连接多个表时，你可能会不得不使用列别名来避免在 ResultSet 中产生重复的列名。指定 columnPrefix 列名前缀允许你将带有这些前缀的列映射到一个外部的结果映射中。 详细说明请参考后面的例子。 |
| notNullColumn | 默认情况下，在至少一个被映射到属性的列不为空时，子对象才会被创建。 你可以在这个属性上指定非空的列来改变默认行为，指定后，Mybatis 将只在这些列非空时才创建一个子对象。可以使用逗号分隔来指定多个列。默认值：未设置（unset）。 |
| autoMapping   | 如果设置这个属性，MyBatis 将会为本结果映射开启或者关闭自动映射。 这个属性会覆盖全局的属性 autoMappingBehavior。注意，本属性对外部的结果映射无效，所以不能搭配 select 或 resultMap 元素使用。默认值：未设置（unset）。 |

例子：

```sql
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  select
    B.id            as blog_id,
    B.title         as blog_title,
    B.author_id     as blog_author_id,
    A.id            as author_id,
    A.username      as author_username,
    A.password      as author_password,
    A.email         as author_email,
    A.bio           as author_bio
  from t_blog B 
  left join t_author A on B.author_id = A.id
  where B.id = #{id}
</select>
```

**集合**  
集合元素和关联元素几乎是一样的，它们相似的程度之高，以致于没有必要再介绍集合元素的相似部分。

```sql
<collection property="posts" ofType="domain.blog.Post">
  <id property="id" column="post_id"/>
  <result property="subject" column="post_subject"/>
  <result property="body" column="post_body"/>
</collection>
```

我们来继续上面的示例，一个博客（Blog）只有一个作者（Author\)。但一个博客有很多文章（Post\)。

**集合的嵌套 Select 查询**

```sql
<resultMap id="blogResult" type="Blog">
  <collection property="posts" javaType="ArrayList" column="id" ofType="Post" select="selectPostsForBlog"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM t_blog WHERE ID = #{id}
</select>

<select id="selectPostsForBlog" resultType="Post">
  SELECT * FROM t_post WHERE BLOG_ID = #{id}
</select>
```

**ofType**这个属性非常重要，它用来将 JavaBean（或字段）属性的类型和集合存储的类型区分开来。上面collection 标签可以这么理解：posts 是一个存储 Post 的 ArrayList 集合，一般情况下，MyBatis 可以推断 javaType 属性，因此可以省略javaType。

**集合的嵌套结果映射**

```sql
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <result property="body" column="post_body"/>
  </collection>
</resultMap>

//以上的resultMap等价于以下两个resultMap：
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post" resultMap="blogPostResult" columnPrefix="post_"/>
</resultMap>
<resultMap id="blogPostResult" type="Post">
  <id property="id" column="id"/>
  <result property="subject" column="subject"/>
  <result property="body" column="body"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  select
  B.id as blog_id,
  B.title as blog_title,
  B.author_id as blog_author_id,
  P.id as post_id,
  P.subject as post_subject,
  P.body as post_body,
  from t_blog B
  left join t_post P on B.id = P.blog_id
  where B.id = #{id}
</select>
```

**集合的多结果集（ResultSet）**

像关联元素那样，我们可以通过执行存储过程实现，它会执行两个查询并返回两个结果集，一个是博客的结果集，另一个是文章的结果集：

```sql
SELECT * FROM t_blog WHERE ID = #{id}

SELECT * FROM t_post WHERE BLOG_ID = #{id}
```

在映射语句中，必须通过 resultSets 属性为每个结果集指定一个名字，多个名字使用逗号隔开。

```sql
<select id="selectBlog" resultSets="blogs,posts" resultMap="blogResult">
  {call getBlogsAndPosts(#{id,jdbcType=INTEGER,mode=IN})}
</select>
```

我们指定 “posts” 集合将会使用存储在 “posts” 结果集中的数据进行填充：

```sql
<resultMap id="blogResult" type="Blog">
  <id property="id" column="id" />
  <result property="title" column="title"/>
  <collection property="posts" ofType="Post" resultSet="posts" column="id" foreignColumn="blog_id">
    <id property="id" column="id"/>
    <result property="subject" column="subject"/>
    <result property="body" column="body"/>
  </collection>
</resultMap>
```

**discriminator\(鉴别器\)**

```sql
<discriminator javaType="int" column="draft">
  <case value="1" resultType="DraftPost"/>
</discriminator>
```

有时候，一个数据库查询可能会返回多个不同的结果集（但总体上还是有一定的联系的）。 鉴别器（discriminator）元素就是被设计来应对这种情况的，另外也能处理其它情况，例如类的继承层次结构。 鉴别器的概念很好理解——它很像 Java 语言中的 switch 语句。  
一个鉴别器的定义需要指定 column 和 javaType 属性。column 指定了 MyBatis 查询被比较值的地方。 而 javaType 用来确保使用正确的相等测试（虽然很多情况下字符串的相等测试都可以工作）。例如：

```sql
<resultMap id="vehicleResult" type="Vehicle">
  <id property="id" column="id" />
  <result property="vin" column="vin"/>
  <result property="year" column="year"/>
  <result property="make" column="make"/>
  <result property="model" column="model"/>
  <result property="color" column="color"/>
  <discriminator javaType="int" column="vehicle_type">
    <case value="1" resultMap="carResult"/>
    <case value="2" resultMap="truckResult"/>
    <case value="3" resultMap="vanResult"/>
    <case value="4" resultMap="suvResult"/>
  </discriminator>
</resultMap>
```

在这个示例中，MyBatis 会从结果集中得到每条记录，然后比较它的 vehicle type 值。 如果它匹配任意一个鉴别器的 case，就会使用这个 case 指定的结果映射。 这个过程是互斥的，也就是说，剩余的结果映射将被忽略（除非它是扩展的，我们将在稍后讨论它）。 如果不能匹配任何一个 case，MyBatis 就只会使用鉴别器块外定义的结果映射。 所以，如果 carResult 的声明如下：

```sql
<resultMap id="carResult" type="Car">
  <result property="doorCount" column="door_count" />
</resultMap>
```

那么只有 doorCount 属性会被加载。这是为了即使鉴别器的 case 之间都能分为完全独立的一组，尽管和父结果映射可能没有什么关系。在上面的例子中，我们当然知道 cars 和 vehicles 之间有关系，也就是 Car 是一个 Vehicle。因此，我们希望剩余的属性也能被加载。而这只需要一个小修改。

```sql
<resultMap id="carResult" type="Car" extends="vehicleResult">
  <result property="doorCount" column="door_count" />
</resultMap>
```

现在 vehicleResult 和 carResult 的属性都会被加载了。  
可能有人又会觉得映射的外部定义有点太冗长了。 因此，对于那些更喜欢简洁的映射风格的人来说，还有另一种语法可以选择。例如：

```sql
<resultMap id="vehicleResult" type="Vehicle">
  <id property="id" column="id" />
  <result property="vin" column="vin"/>
  <result property="year" column="year"/>
  <result property="make" column="make"/>
  <result property="model" column="model"/>
  <result property="color" column="color"/>
  <discriminator javaType="int" column="vehicle_type">
    <case value="1" resultType="carResult">
      <result property="doorCount" column="door_count" />
    </case>
    <case value="2" resultType="truckResult">
      <result property="boxSize" column="box_size" />
      <result property="extendedCab" column="extended_cab" />
    </case>
    <case value="3" resultType="vanResult">
      <result property="powerSlidingDoor" column="power_sliding_door" />
    </case>
    <case value="4" resultType="suvResult">
      <result property="allWheelDrive" column="all_wheel_drive" />
    </case>
  </discriminator>
</resultMap>
```

**注意:** 这些都是结果映射，如果你完全不设置任何的 result 元素，MyBatis 将为你自动匹配列和属性。所以上面的例子大多都要比实际的更复杂。 这也表明，大多数数据库的复杂度都比较高，我们不太可能一直依赖于这种机制。

**auto-mapping**  
在简单的场景下，MyBatis 可以为你自动映射查询结果。但如果遇到复杂的场景，你需要构建一个结果映射。 但是在本节中，你将看到，你可以混合使用这两种策略。让我们深入了解一下自动映射是怎样工作的。  
当自动映射查询结果时，MyBatis 会获取结果中返回的列名并在 Java 类中查找相同名字的属性（忽略大小写）。 这意味着如果发现了 ID 列和 id 属性，MyBatis 会将列 ID 的值赋给 id 属性。  
通常数据库列使用大写字母组成的单词命名，单词间用下划线分隔；而 Java 属性一般遵循驼峰命名法约定。为了在这两种命名方式之间启用自动映射，需要将`mapUnderscoreToCamelCase`设置为 true。  
甚至在提供了结果映射后，自动映射也能工作。在这种情况下，对于每一个结果映射，在 ResultSet 出现的列，如果没有设置手动映射，将被自动映射。在自动映射处理完毕后，再处理手动映射。 在下面的例子中，id 和 userName 列将被自动映射，hashed\_password 列将根据配置进行映射。

```sql
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password
  from some_table
  where id = #{id}
</select>

<resultMap id="userResultMap" type="User">
  <result property="password" column="hashed_password"/>
</resultMap>
```

有三种自动映射等级：

* NONE - 禁用自动映射。仅对手动映射的属性进行映射。
* PARTIAL - 对除在内部定义了嵌套结果映射（也就是连接的属性）以外的属性进行映射
* FULL - 自动映射所有属性。

默认值是 PARTIAL，这是有原因的。当对连接查询的结果使用 FULL 时，连接查询会在同一行中获取多个不同实体的数据，因此可能导致非预期的映射。 下面的例子将展示这种风险：

```sql
<select id="selectBlog" resultMap="blogResult">
  select
    B.id,
    B.title,
    A.username,
  from Blog B left outer join Author A on B.author_id = A.id
  where B.id = #{id}
</select>

<resultMap id="blogResult" type="Blog">
  <association property="author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <result property="username" column="author_username"/>
</resultMap>
```

在该结果映射中，Blog 和 Author 均将被自动映射。但是注意 Author 有一个 id 属性，在 ResultSet 中也有一个名为 id 的列，所以 Author 的 id 将填入 Blog 的 id，这可不是你期望的行为。 所以，要谨慎使用 FULL。  
无论设置的自动映射等级是哪种，你都可以通过在结果映射上设置 autoMapping 属性来为指定的结果映射设置启用/禁用自动映射。

```sql
<resultMap id="userResultMap" type="User" autoMapping="false">
  <result property="password" column="hashed_password"/>
</resultMap>
```

* * *

### cache

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。 为了使它更加强大而且易于配置，我们对 MyBatis 3 中的缓存实现进行了许多改进。  
默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```xml
	<cache/>
```

基本上就是这样。这个简单语句的效果如下:

 *    映射语句文件中的所有 select 语句的结果将会被缓存。
 *    映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
 *    缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
 *    缓存不会定时进行刷新（也就是说，没有刷新间隔）。
 *    缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
 *    缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。  
    **注意：** 缓存只作用于 cache 标签所在的映射文件中的语句。如果你混合使用 Java API 和 XML 映射文件，在共用接口中的语句将不会被默认缓存。你需要使用 \@CacheNamespaceRef 注解指定缓存作用域。这些属性可以通过 cache 元素的属性来修改。比如：

```xml
	<cache
	 eviction="FIFO"
 	 flushInterval="60000"
	 size="512"
	 readOnly="true" />
```

这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。可用的清除策略有：

* LRU – 最近最少使用：移除最长时间不被使用的对象。
* FIFO – 先进先出：按对象进入缓存的顺序来移除它们。
* SOFT – 软引用：基于垃圾回收器状态和软引用规则移除对象。
* WEAK – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

**默认的清除策略是 LRU。**

flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。  
size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。  
readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。 因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。 速度上会慢一些，但是更安全，因此默认值是 false。  
**注意：** 二级缓存是事务性的。这意味着，当 SqlSession 完成并提交时，或是完成并回滚，但没有执行 flushCache=true 的 insert/delete/update 语句时，缓存会获得更新。

**使用自定义缓存**  
除了上述自定义缓存的方式，你也可以通过实现你自己的缓存，或为其他第三方缓存方案创建适配器，来完全覆盖缓存行为。

```xml
	<cache type="com.domain.something.MyCustomCache"/>
```

这个示例展示了如何使用一个自定义的缓存实现。type 属性指定的类必须实现 org.apache.ibatis.cache.Cache 接口，且提供一个接受 String 参数作为 id 的构造器。 这个接口是 MyBatis 框架中许多复杂的接口之一，但是行为却非常简单。

```java
public interface Cache {
  String getId();
  int getSize();
  void putObject(Object key, Object value);
  Object getObject(Object key);
  boolean hasKey(Object key);
  Object removeObject(Object key);
  void clear();
}
```

为了对你的缓存进行配置，只需要简单地在你的缓存实现中添加公有的 JavaBean 属性，然后通过 cache 元素传递属性值，例如，下面的例子将在你的缓存实现上调用一个名为`setCacheFile(String file)`的方法：

```xml
<cache type="com.domain.something.MyCustomCache">
  <property name="cacheFile" value="/tmp/my-custom-cache.tmp"/>
</cache>
```

你可以使用所有简单类型作为 JavaBean 属性的类型，MyBatis 会进行转换。 你也可以使用占位符（如 \$\{cache.file\}），以便替换成在配置文件属性中定义的值。  
从版本 3.4.2 开始，MyBatis 已经支持在所有属性设置完毕之后，调用一个初始化方法。 如果想要使用这个特性，请在你的自定义缓存类里实现 org.apache.ibatis.builder.InitializingObject 接口。

```java
public interface InitializingObject {
  void initialize() throws Exception;
}
```

**注意：** 前面对缓存的配置（如清除策略、可读或可读写等），不能应用于自定义缓存。  
请注意，缓存的配置和缓存实例会被绑定到 SQL 映射文件的命名空间中。 因此，同一命名空间中的所有语句和缓存将通过命名空间绑定在一起。 每条语句可以自定义与缓存交互的方式，或将它们完全排除于缓存之外，这可以通过在每条语句上使用两个简单属性来达成。 默认情况下，语句会这样来配置：

```sql
<select ... flushCache="false" useCache="true"/>
<insert ... flushCache="true"/>
<update ... flushCache="true"/>
<delete ... flushCache="true"/>
```

鉴于这是默认行为，显然你永远不应该以这样的方式显式配置一条语句。但如果你想改变默认的行为，只需要设置 flushCache 和 useCache 属性。比如，某些情况下你可能希望特定 select 语句的结果排除于缓存之外，或希望一条 select 语句清空缓存。类似地，你可能希望某些 update 语句执行时不要刷新缓存。

* * *

### cache-ref

对某一命名空间的语句，只会使用该命名空间的缓存进行缓存或刷新。 但你可能会想要在多个命名空间中共享相同的缓存配置和实例。要实现这种需求，你可以使用 cache-ref 元素来引用另一个缓存。

```xml
	<cache-ref namespace="com.someone.application.data.SomeMapper"/>
```

* * *

## 动态sql

动态 SQL 是 MyBatis 的强大特性之一。如果你使用过 JDBC 或其它类似的框架，你应该能理解根据不同条件拼接 SQL 语句有多痛苦，例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL，可以彻底摆脱这种痛苦。  
使用动态 SQL 并非一件易事，但借助可用于任何 SQL 映射语句中的强大的动态 SQL 语言，MyBatis 显著地提升了这一特性的易用性。如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

* if
* choose 、when、otherwise
* trim 、where、 set
* foreach

### if标签

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。比如：

```sql
<select id="findActiveBlogWithTitleLike" resultType="Blog">
  SELECT * FROM t_blog
  WHERE state = 'ACTIVE'
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

这条语句提供了可选的查找文本功能。如果不传入 “title”，那么所有处于 “ACTIVE” 状态的 blog都会返回；如果传入了 “title” 参数，那么就会对 “title” 一列进行模糊查找并返回对应的 BLOG 结果（细心的读者可能会发现，“title” 的参数值需要包含查找掩码或通配符字符）。  
如果希望通过 “title” 和 “author” 两个参数进行可选搜索该怎么办呢？首先，我想先将语句名称修改成更名副其实的名称；接下来，只需要加入另一个条件即可。

```sql
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM t_blog 
  WHERE state = 'ACTIVE'
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

* * *

### choose、when、otherwise标签

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。  
还是上面的例子，但是策略变为：传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG（这可能是管理员认为，与其返回大量的无意义随机 Blog，还不如返回一些由管理员精选的 Blog）。

```sql
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM t_blog WHERE state = 'ACTIVE'
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

* * *

### trim、where、set标签

前面几个例子已经方便地解决了一个臭名昭著的动态 SQL 问题。现在回到之前的 “if” 示例，这次我们将 “state = ‘ACTIVE’” 设置成动态条件，看看会发生什么。

```sql
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM t_blog
  WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

如果没有匹配的条件会怎么样？最终这条 SQL 会变成这样：`SELECT * FROM t_blog WHERE` ，这会导致查询失败。如果匹配的只是第二个条件又会怎样？这条 SQL 会是这样：`SELECT * FROM t_blog WHERE AND title like 'someTitle'`，这个查询也会失败。这个问题不能简单地用条件元素来解决。这个问题是如此的难以解决，以至于解决过的人不会再想碰到这种问题。MyBatis 有一个简单且适合大多数场景的解决办法。而在其他场景中，可以对其进行自定义以符合需求。而这，只需要一处简单的改动：

```sql
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM t_blog
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

where 元素只会在子元素返回任何内容的情况下才插入“WHERE”子句。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。  
如果 where 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：

```sql
// 语法：
<trim prefix="WHERE" prefixOverrides="AND | OR ">
  ...
</trim>

<trim prefix="WHERE" prefixOverrides="AND">
	<if test="state != null">
	  state = #{state}
	</if> 
	<if test="title != null">
	  AND title like #{title}
	</if>
	<if test="author != null and author.name != null">
	  AND author_name like #{author.name}
	</if>
</trim>
```

prefixOverrides 属性会忽略通过管道符分隔的文本序列（注意此例中的空格是必要的）。上述例子会移除所有 prefixOverrides 属性中指定的内容，并且插入 prefix 属性中指定的内容。

**trim标签中涉及到的属性：**

| 属性            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| prefix          | 给sql语句拼接的前缀                                          |
| suffix          | 给sql语句拼接的后缀                                          |
| prefixOverrides | 去除sql语句前面的关键字或者字符，该关键字或者字符由prefixOverrides属性指定，假设该属性指定为"AND"，当sql语句的开头为"AND"，trim标签将会去除该"AND" |
| suffixOverrides | 去除sql语句后面的关键字或者字符，该关键字或者字符由suffixOverrides属性指定 |

用于动态更新语句的类似解决方案叫做 set。set 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```sql
<update id="updateAuthorIfNecessary">
  update t_author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

这个例子中，set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。  
来看看与 set 元素等价的自定义 trim 元素吧：

```sql
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

**注意：** 我们覆盖了后缀值设置，并且自定义了前缀值。

* * *

### foreach标签

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。比如：

```sql
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID IN
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

foreach 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！  
**注意：** 你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象作为集合参数传递给 foreach。当使用可迭代对象或者数组时，index 是当前迭代的序号，item 的值是本次迭代获取到的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。  
至此，我们已经完成了与 XML 配置及映射文件相关的讨论。下一章将详细探讨 Java API，以便你能充分利用已经创建的映射配置。  
批量插入有个问题, 当拼接的sql语句特别长的时候就会报错。这是因为Mysql服务端对于接收的数据包有大小限制：  
在数据库执行 `show variables like 'max_allowed_packet'` 这条语句可以看到默认大小是4M，如果数据量特别大，就需要修改这个参数或者手动控制批量插入的条数，比如，每1000条插入一次等。

* * *

### script

要在带注解的映射器接口类中使用动态 SQL，可以使用 script 元素。比如：

```java
    @Update({"<script>",
      "update Author",
      "  <set>",
      "    <if test='username != null'>username=#{username},</if>",
      "    <if test='password != null'>password=#{password},</if>",
      "    <if test='email != null'>email=#{email},</if>",
      "    <if test='bio != null'>bio=#{bio}</if>",
      "  </set>",
      "where id=#{id}",
      "</script>"})
    void updateAuthorValues(Author author);
```

* * *

### bind

bind 元素允许你在 OGNL 表达式以外创建一个变量，并将其绑定到当前的上下文。比如：

```sql
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM t_blog
  WHERE title LIKE #{pattern}
</select>
```

* * *

### 多数据库支持

如果配置了 databaseIdProvider，你就可以在动态代码中使用名为 “\_databaseId” 的变量来为不同的数据库构建特定的语句。比如下面的例子：

```sql
<insert id="insert">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    <if test="_databaseId == 'oracle'">
      select seq_users.nextval from dual
    </if>
    <if test="_databaseId == 'db2'">
      select nextval for seq_users from sysibm.sysdummy1"
    </if>
  </selectKey>
  insert into t_users values (#{id}, #{name})
</insert>
```

### 动态 SQL 中的插入脚本语言

MyBatis 从 3.2 版本开始支持插入脚本语言，这允许你插入一种语言驱动，并基于这种语言来编写动态 SQL 查询语句。  
可以通过实现以下接口来插入一种语言：

```sql
public interface LanguageDriver {
  ParameterHandler createParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql);
  SqlSource createSqlSource(Configuration configuration, XNode script, Class<?> parameterType);
  SqlSource createSqlSource(Configuration configuration, String script, Class<?> parameterType);
}
```

实现自定义语言驱动后，你就可以在 mybatis-config.xml 文件中将它设置为默认语言：

```xml
<typeAliases>
  <typeAlias type="org.sample.MyLanguageDriver" alias="myLanguage"/>
</typeAliases>
<settings>
  <setting name="defaultScriptingLanguage" value="myLanguage"/>
</settings>
```

或者，你也可以使用 lang 属性为特定的语句指定语言：

```xml
<select id="selectBlog" lang="myLanguage">
  SELECT * FROM t_blog
</select>
```

或者，在你的 mapper 接口上添加 \@Lang 注解：

```xml
public interface Mapper {
  @Lang(MyLanguageDriver.class)
  @Select("SELECT * FROM t_blog")
  List<Blog> selectBlog();
}
```

* * *

## 扩展知识

### mybatis查询翻页

* **物理分页：** 在查询语句中使用limit , 传参时需要自己构造出查询的起始位置和页大小
* **逻辑分页：** 假分页，本质上是先一次性查出所有数据，然后手动筛选出所需要的当前页的数据
* **分页插件 PageHelper：** 使用姿势请查看 【[MyBatis 分页插件 PageHelper](https://pagehelper.github.io/docs/howtouse/)】

### insert时返回自增id

一般的insert操作返回值为插入的记录数目，mysql有些场景下插入数据会使用自增id，并且要求插入之后获得最新的自增ID，而mybatis已经考虑到了这种场景，在insert成功后，mybatis会将插入的值自动绑定到插入的对象的Id属性中，我们用getId就可以取到最新的id。  
mapper.xml分为两种情况，一种是数据库\(如MySQL,SQLServer\)支持auto-generated key field，另一种是数据库（如Oracle）不支持auto-generated key field的。  
一、数据库\(如MySQL,SQLServer\)支持auto-generated key field的情况：

```sql
方案1（推荐）：
<insert id="addUser" parameterType="User" useGeneratedKeys="true" keyProperty="id">
  insert into t_user(user_name, password) values(#{userName}, #{password})
</insert>

方案2(依赖数据库本身)
<insert id="addUser" parameterType="User">
  // 下面是SQLServer获取最近一次插入记录的主键值的方式
  <selectKey resultType="java.lang.Long" keyProperty="id" order="AFTER">
    select LAST_INSERT_ID as id
  </selectKey>
  insert into t_user(user_name, password) values(#{userName}, #{password})
</insert>
```

二、数据库\(如Oracle\)不支持auto-generated key field的情况：

```sql
<insert id="addUser" parameterType="User">
  <selectKey keyProperty="id" resultType="java.lang.Long" order="BEFORE">
    select CAST(RANDOM * 100000 as INTEGER) a FROM SYSTEM.SYSDUMMY1
  </selectKey>
  insert into t_user(user_name, password) values(#{userName}, #{password})
</insert
```

**注意：** mapper接口返回值依然是成功插入的记录数，但此时主键值已经赋值到领域模型实体的id中了，使用user.getId\(\)即可取到。

### 模糊查询

常用的模糊查询方式有以下4种，推荐使用方式3：

```sql
方式1：此方式需要在调用处手动的去添加“%”通配符，如：String userName = "%阿钟小哥%";
	select * from t_user where user_name like #{userName}

方式2：此方式会引发sql注入的问题
	select * from t_user where user_name like '%{userName}%'

方式3：推荐方式
	select * from t_user where user_name like concat('%',#{userName},'%')

方式4：此方式会引发sql注入的问题
	select * from t_user where user_name like concat('%','${userName}','%')
```

* **#\{ \}：** 是预编译处理，MyBatis在处理#\{ \}时，它会将sql中的#\{ \}替换为？，然后调用PreparedStatement的set方法来赋值，传入字符串后，会在值两边加上单引号，使用占位符的方式提高效率，可以防止sql注入。
* **\$\{\}：** 表示拼接sql串，将接收到参数的内容不加任何修饰拼接在sql中，可能引发sql注入问题。

### 特殊字符转义

在解析XML文件时将5种特殊字符进行转义，分别是\&， \<， >， “， ‘， 当我们不希望语法被转义时就需要进行特殊处理。一般有两种解决方法：  
**方案一：** 使用 \<\!\[CDATA\[ \]\]>标签来包含特殊字符：

```sql
<select id="userInfo" parameterType="com.test.pojo.User" resultMap="user">   
     SELECT id,user_name, password,age,status
     FROM t_user 
     <WHERE>
     AND user_name like concat('%',#{userName},'%')
     AND age <![CDATA[ <= ]]> #{age}  
  </WHERE>
 </select>
```

**注意：** 在CDATA内部的所有内容都会被解析器忽略，保持原貌。所以在Mybatis配置文件中，要尽量缩小 \<\!\[CDATA\[ \]\]>  
的作用范围，避免sql标签无法解析的问题。

**方案二：** 使用XML转义序列来表示特殊字符  
5种特殊字符的转义序列：

| 特殊字符 | 转义序列 |
| -------- | -------- |
| \<       | \&lt;    |
| \>       | \&gt;    |
| \&       | \&amp;   |
| "        | \&quot;  |
| ’        | \&apos;  |

因此，上述sql也可以写成如下：

```sql
<select id="userInfo" parameterType="com.test.pojo.User" resultMap="user">   
     SELECT id,user_name, password,age,status
     FROM t_user 
     <WHERE>
     AND user_name like concat('%',#{userName},'%')
     AND age &lt;= #{age}  
  </WHERE>
 </select>
```

**推荐使用\<\!\[CDATA\[ \]\]>，清晰，简洁**

