# mybatis 九大动态标签详解

##### 一、if：你们能判断，我也能判断！

```java
<select id="count" resultType="java.lang.Integer">
	select count(*) from user where <if test="id != null">id = #{id}</if> and username = 'xiaoming'
</select>
```

* 如果传入的 id 不为 null， 那么才会 SQL 才会拼接 id = #\{id\}
* 如果传入的 id 为 null，那么最终的 SQL 语句就变成了 select count\(\*\) from user where and username = ‘xiaoming’。这语句就会有问题，这时候 where 标签就该隆重登场了

##### 二、where：有了我，SQL 语句拼接条件神马的都是浮云！

```java
<select id="count" resultType="java.lang.Integer">
	select count(*) from user
	<where>
		<if test="id != null">id = #{id}</if>
		and username = 'xiaoming'
    </where>
 </select>
```

* where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入 WHERE 子句
* 若语句的开头为 AND、OR，where 元素也会将它们去除。还可以通过 trim 标签去自定义这种处理规则

##### 三、trim：我的地盘，我做主！

###### trim 标签一般用于拼接、去除 SQL 的前缀、后缀

###### trim 标签中的属性

|      属性       |   描述   |
| :-------------: | :------: |
|     prefix      | 拼接前缀 |
|     suffix      | 拼接后缀 |
| prefixOverrides | 去除前缀 |
| suffixOverrides | 去除后缀 |

```java
<select id="count" result="java.lang.Integer">
	select count(*) from user
	<trim prefix ="where" prefixOverrides="and | or">
		<if test="id != null">id = #{id}</if>
		<if test="username != null"> and username = #{username}</if>
	</trim>
</select>
```

* 如果 id 或者 username 有一个不为空，则在语句前加入 where。如果 where 后面紧随 and 或 or 就会自动会去除
* 如果 id 或者 username 都为空，则不拼接任何东西

##### 四、set： 信我，不出错！

```java
<update id="UPDATE" parameterType="User">
	update user
    <set>
    	<if test="name != null">name = #{name},</if> 
        <if test="password != null">password = #{password},</if> 
        <if test="age != null">age = #{age},</if> 
   	</set>
</update>
```

* 三个 if 至少有一个不为空。会在前面加上 set，自动去除尾部多余的逗号

##### 五、foreach: 你有 for，我有 foreach

###### foreach 标签中的属性

|    属性    |              描述              |
| :--------: | :----------------------------: |
|   index    |              下标              |
|    item    |          每个元素名称          |
|    open    |        该语句以什么开始        |
|   close    |        该语句以什么结尾        |
| separator  | 在每次迭代之间以什么作为分隔符 |
| collection |            参数类型            |

`foreach的属性有个写错了，collection不是参数类型，而是指定参数名，或者说是指定是取哪个参数值，而不是指定参数的类型，你这个接口如果有多个传参，指定类型而不是指定参数名肯定不行，如果传入的是个List类型的参数，mapper层用\@Para\(“idList”\)List\<String> list ，那这里应该是填collection="idList" 而不是list这个类型`

###### collection：

 *    如果参数类型为 List，则该值为 list

```java
<select id="count" resultType="java.lang.Integer">
	select count(*) from user where id in
  	<foreach collection="list" item="item" index="index" open="(" separator="," close=")">
        #{item}
  	</foreach>
</select>
```

 *    如果参数类型为数组，则该值为 array

```java
<select id="count" resultType="java.lang.Integer">
	select * from user where id inarray
  	<foreach collection="array" item="item" index="index" open="(" separator="," close=")">
        #{item}
  	</foreach>
</select>
```

* 如果参数类型为 Map，则参数类型为 Map 的 Key

##### 六、choose: 我选择了你，你选择了我！

```java
<select id="count" resultType="Blog">
	select count(*) from user
  	<choose>
    	<when test="id != null">
      		and id = #{id}
    	</when>
    	<when test="username != null">
      		and username = #{username}
    	</when>
    	<otherwise>
      		and age = 18
    	</otherwise>
  	</choose>
</select>
```

* 当 id 和 username 都不为空的时候， 那么选择二选一（前者优先）
* 如果都为空，那么就选择 otherwise 中的
* 如果 id 和 username 只有一个不为空，那么就选择不为空的那个

##### 七、sql：相当于 Java 中的代码提重，需要配合 include 使用

```java
<sql id="table"> user </sql>
```

##### 八、include：相当于 Java 中的方法调用

```java
<select id="count" resultType="java.lang.Integer">
	select count(*) from <include refid=“table（sql 标签中的 id 值）” />
</select>
```

##### 九、bind：对数据进行再加工

```java
<select id="count" resultType="java.lang.Integer">
	select count(*) from user
	<where>
		<if test="name != null">
			<bind name="name" value="'%' + username + '%'"
			name = #{name}
		</if>
</select>
```


