# @RequestBody与@RequestParam的使用

- @RequestParam 就是从 queryString 获取参数, @RequestBody 就是从请求体里获取参数 ,但是要注意@RequestBody 接收的是json 所以要用实体类或者map接收,即使是一个参数.
- 不使用任何注解的时候,默认同@RequestParam(required = false) 一样 ,而且Controller参数名 要和 queryString 的key 一致,如果不一致就要使用 @RequestParam 了

把 queryString中的数据放到map里面,这时候必须加@RequestParam

把 queryString 中的数据放到实体类 **不需要任何注解, 但是实体类的字段名 必须和 queryString 的key 必须一致**