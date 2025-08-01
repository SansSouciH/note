# MyBatis-Plus

> 官方文档：https://baomidou.com/

```xml
<!--mybatis-plus.springboot.version = 3.5.9-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-bom</artifactId>
    <version>${mybatis-plus.springboot.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
    <version>${mybatis-plus.springboot.version}</version>
</dependency>
```

## 常用注解

* `@MapperScan(basePackages = "org.hm.cloud.dao")`

* `@TableName`：指定表名称；类

  * 配合Java转JSON存入数据库开启对象映射：@TableName(value = "tableName", autoResultMap = true)

* `@TableId`：指定表中主键字段信息；主键字段

  * 主键自增长：@TableId(type = IdType.AUTO)，默认为雪花算法生成id，不是自增id
  * 默认雪花id：@TableId(type = ASSIGN_ID)

* `@TableField`：指定表中普通字段信息；普通字段

  * 类字段和数据库字段名不一致：@TableField("field_name")
  * 类字段使用is做变量开头并且是布尔值：@TableField("is_field_name")
  * 类字段名称是SQL关键字时：@TableField("'order'")
  * 类字段名不在数据库中存在时：@TableField(exist = false)
  * **将Java对象转换为JSON存入数据库**：@TableField(typeHandler = JacksonTypeHandler.class)

* `@EnumValue`：将枚举类型存入数据库时转换为数据库字段类型，添加在枚举字段上

  * 在配置文件中配置：

    ```yaml
    mybatis-plus:
    	configuration:
    		default-enum-type-handler: com.baomidou.mybatisplus.core.handlers.MybatisEnumTypeHandler
    ```

## 条件构造

* QueryWrapper
* UpdateWrapper
* LambdaQueryWrapper
* LambdaUpdateWrapper

## 通用功能

### 代码生成

> IDEA的Plugins搜索：MyBatisPlus插件

* Config Database：数据库连接配置信息
* Code Generator：生成代码位置配置信息

### 分页插件

> 添加依赖 => 分页配置类 => 操作分页

**引入依赖**

```xml
<!--3.5.9版本后分页插件需在jsqlparser中引用-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-jsqlparser</artifactId>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
</dependency>
```

**分页配置**

```java
//分页配置类
@Configuration
public class MyBatisConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        //1.创建分页插件
        PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor(DbType.MYSQL);
        paginationInnerInterceptor.setMaxLimit(1000L);
        //2.添加分页插件
        interceptor.addInnerInterceptor(paginationInnerInterceptor);
        return interceptor;
    }
}
```

**分页操作**

```java
//通用分页查询条件类
@Data
public class PageQuery {
    private Integer pageNo = 1;
    private Integer pageSize = 5;
    private String sortBy;
    private Boolean isAsc = true;
    //按照sortBy排序或按照自定义排序
    public <T> Page<T> toMpPage(OrderItem ... items) {
        //分页条件
        Page<T> page = Page.of(pageNo, pageSize);
        if (StringUtils.isNotEmpty(sortBy)) {
            //排序条件
            page.addOrder(new OrderItem().setColumn(sortBy).setAsc(isAsc));
        } else if(Objects.nonNull(items)){
            //默认排序
            page.addOrder(items);
        }
        return page;
    }
    //默认按照修改时间排序
    public <T> Page<T> toMpPageDefaultSortByUpdateTime(){
        return toMpPage(OrderItem.desc("update_time"));
    }
}

//通用分页查询DTO分页数据转VO数据
@Data
public class PageDTO<T> {
    private Long total;
    private Long size;
    private List<T> data;
    
    public static <D, V> PageDTO<V> of(Page<D> page, Class<V> clazz) {
        PageDTO<V> vo = new PageDTO<>();
        vo.setTotal(page.getTotal());
        vo.setSize(page.getSize());
        List<D> records = page.getRecords();
        if (CollectionUtils.isEmpty(records)) {
            vo.setData(Collections.emptyList());
            return vo;
        }
        vo.setData(BeanUtil.copyToList(records, clazz));  //records中的对象字段名和clazz类型对象字段名必须一一对应
        return vo;
    }
    //根据传入的函数式接口转换逻辑来进行DTO到VO的转换
    public static <D, V> PageDTO<V> of(Page<D> page, Function<D, V> convertor) {
        PageDTO<V> vo = new PageDTO<>();
        vo.setTotal(page.getTotal());
        vo.setSize(page.getSize());
        List<D> records = page.getRecords();
        if (CollectionUtils.isEmpty(records)) {
            vo.setData(Collections.emptyList());
            return vo;
        }
        vo.setData(records.stream().map(convertor).collect(Collectors.toList()));
        return vo;
    }
}

/*
使用实例
PageDTO.of(page, user -> {
    UserVO vo = BeanUtil.copyProperties(user, UserVO.class);
    vo.setUsername(...)
});
*/
```

## 常用标签

1. SQL 映射文件中的顶级标签

- **`<mapper>`: 映射文件的根元素**
- **`<resultMap>`: 描述如何从数据库结果集中加载对象**
- **`<sql>`: 可被其他语句引用的可重用 SQL 代码段**
- **`<insert>`: 映射插入语句**
- **`<update>`: 映射更新语句**
- **`<delete>`: 映射删除语句**
- **`<select>`: 映射查询语句**
- `<cache>`: 配置给定命名空间的缓存
- `<cache-ref>`: 引用其他命名空间的缓存配置

2. 动态 SQL 标签

- **`<if>`: 条件判断**
- **`<where>`: 只会在子元素返回任何内容的情况下才插入 WHERE 子句**
- `<choose>`, `<when>`, `<otherwise>`: 类似于 Java 中的 switch-case 结构
- `<trim>`: 格式化标签，可以添加或删除前缀/后缀
- `<set>`: 用于动态包含需要更新的列
- `<foreach>`: 循环遍历集合
- `<bind>`: 创建一个变量并将其绑定到上下文

3. 结果映射相关标签

- **`<id>`: 指定主键列**
- **`<result>`: 指定普通列**
- **`<association>`: 映射单个对象关联**
- **`<collection>`: 映射集合关联**
- `<constructor>`: 用于注入结果到构造方法
- `<discriminator>`: 根据不同的条件使用不同的结果映射

4. 其他常用标签

- **`<include>`: 引用 `<sql>` 标签定义的 SQL 片段**
- `<selectKey>`: 用于获取自动生成的主键值

5. 注解方式常用注解

- **`@Select`: 查询语句注解**
- **`@Insert`: 插入语句注解**
- **`@Update`: 更新语句注解**
- **`@Delete`: 删除语句注解**
- **`@Param`: 参数映射注解**
- `@Results`: 结果映射注解
- `@Result`: 单列映射注解
- `@One`: 一对一关联注解
- `@Many`: 一对多关联注解
- `@Options`: 提供配置选项
- `@SelectProvider`: 动态 SQL 提供类注解
- `@InsertProvider`: 动态插入提供类注解
- `@UpdateProvider`: 动态更新提供类注解
- `@DeleteProvider`: 动态删除提供类注解
