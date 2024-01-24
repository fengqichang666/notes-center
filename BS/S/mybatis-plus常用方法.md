# mybatis-plus

###  BaseMapper 的常用方法：

```
insert(T entity): 插入一条记录。
deleteById(Serializable id): 根据 ID 删除一条记录。
deleteByMap(Map<String, Object> columnMap): 根据 columnMap 条件，删除记录。
delete(Wrapper<T> wrapper): 根据 entity 条件，删除记录。
updateById(T entity): 根据 ID 选择修改。
update(T entity, Wrapper<T> updateWrapper): 根据 UpdateWrapper 条件，修改记录。
selectById(Serializable id): 根据 ID 选择一条记录。
selectBatchIds(Collection<? extends Serializable> idList): 根据 ID 集合，批量查询。
selectByMap(Map<String, Object> columnMap): 根据 columnMap 查询。
selectOne(Wrapper<T> queryWrapper): 根据 Wrapper，查询一条记录。
selectCount(Wrapper<T> queryWrapper): 根据 Wrapper 条件，查询总记录数。
selectList(Wrapper<T> queryWrapper): 根据 entity 条件，查询全部记录。
selectMaps(Wrapper<T> queryWrapper): 根据 Wrapper 条件，查询全部记录返回 List<Map<String, Object>>。
selectObjs(Wrapper<T> queryWrapper): 根据 Wrapper 条件，查询全部记录返回 List<Object>。
```

###  QueryWrapper 的常用方法：

QueryWrapper 通常用于构造查询条件。

```
eq(String column, Object val): 等于 =.
ne(String column, Object val): 不等于 <>.
gt(String column, Object val): 大于 >.
ge(String column, Object val): 大于等于 >=.
lt(String column, Object val): 小于 <.
le(String column, Object val): 小于等于 <=.
like(String column, Object val): LIKE '%value%'.
notLike(String column, Object val): NOT LIKE.
in(String column, Collection<?> value): 字段 IN(value1, value2, ...).
notIn(String column, Collection<?> value): 字段 NOT IN(value1, value2, ...).
orderByDesc(String... columns): 根据字段降序排列。
orderByAsc(String... columns): 根据字段升序排列。
```

### UpdateWrapper 的常用方法：

UpdateWrapper 用于构造更新条件。

```
set(String column, Object val): 设置字段值。
其他方法和 QueryWrapper 类似，用于定义更新的条件。
```

### QueryWrapper eq与LambdaQueryWrapper eq

```

QueryWrapper和LambdaQueryWrapper的eq方法在功能上是相同的，都用于构建等值查询条件。它们的主要区别在于QueryWrapper的eq方法需要手动输入列名，而LambdaQueryWrapper的eq方法使用Lambda表达式自动生成属性名，提供了更好的类型安全性。
在QueryWrapper中，eq方法接受两个参数，第一个参数是数据库表的列名，第二个参数是要匹配的值。例如：

QueryWrapper<User> queryWrapper = new QueryWrapper<>();
queryWrapper.eq("name", "John");

LambdaQueryWrapper通过类型推断来自动生成实体类的属性名，这样可以避免手动输入列名的错误。同时，由于使用了Lambda表达式，编译器可以在编译时检查属性名的正确性，提供了更好的类型安全性。
LambdaQueryWrapper 的 eq 方法主要用于构建等于（=）的查询条件。eq 方法有两个重载版本，其用法如下：
单个字段的等于条件：
	eq(boolean condition, SFunction<T, ?> column, Object val)
	condition: 是否执行该语句的条件。只有当该条件为 true 时，该语句才会被加入查询条件。
	column: 实体类的属性对应的字段名，通常使用 Lambda 表达式如 User::getName。
	val: 你想要匹配的值。
	queryWrapper.eq(true, User::getName, "张三");

没有condition的版本：
	eq(SFunction<T, ?> column, Object val)
	column: 实体类的属性对应的字段名。
	val: 你想要匹配的值。
	queryWrapper.eq(User::getName, "张三");

```

