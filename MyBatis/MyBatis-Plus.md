# MyBatis-Plus

## 快速开始

[快速开始 | MyBatis-Plus (baomidou.com)](https://baomidou.com/guide/quick-start.html)

1. 创建SpringBoot项目，添加依赖

```properties
   <dependency>
       <groupId>com.baomidou</groupId>
       <artifactId>mybatis-plus-boot-starter</artifactId>
       <version>3.4.3</version>
   </dependency>
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
   </dependency>
```

不需要添加mybatis和mybatis-spring了，mybatis-plus帮你做了

2. 创建mapper接口，实现BaseMapper接口

```java
//有别于传统的写一个xml并绑定，plus中只需要继承基本的类 BaseMapper 即可
@Repository //表示为持久层
public interface Student1Mapper extends BaseMapper<Student> {
}
```

3. 仅需如此，就可以进行一些简单的CRUD操作，测试代码如下：

```java
@SpringBootTest
class Mybatisplus01ApplicationTests {

    @Autowired
    private Student1Mapper student1Mapper;

    @Test
    void contextLoads() {
        //参数wrapper 是一个条件构造器
        List<Student> list = student1Mapper.selectList(null);
        for (Student student : list) {
            System.out.println(student);
        }
    }
}
```

## 配置日志

```properties
#配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![image-20211008170137272](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20211008170137272.png)

## 插入的测试

[分布式系统唯一ID生成方案汇总 - nick hao - 博客园 (cnblogs.com)](https://www.cnblogs.com/haoxinyue/p/5208136.html)

### @TableId注解（主键生成策略）

|        值         |                             描述                             |
| :---------------: | :----------------------------------------------------------: |
|       AUTO        |                         数据库ID自增                         |
|       NONE        | 无状态,该类型为未设置主键类型(注解里等于跟随全局,全局里约等于 INPUT) |
|       INPUT       |                    insert前自行set主键值                     |
|     ASSIGN_ID     | 分配ID(主键类型为Number(Long和Integer)或String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法) |
|    ASSIGN_UUID    | 分配UUID,主键类型为String(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextUUID`(默认default方法) |
|   ~~ID_WORKER~~   |     分布式全局唯一ID 长整型类型(please use `ASSIGN_ID`)      |
|     ~~UUID~~      |           32位UUID字符串(please use `ASSIGN_UUID`)           |
| ~~ID_WORKER_STR~~ |     分布式全局唯一ID 字符串类型(please use `ASSIGN_ID`)      |

```java
public class Student {
//   雪花算法实现
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;
    private String name;
//   针对字符串，有32位，不可排序且存储量大，实际中不会这样使用，因为这个注解针对的是主键
//   @TableId(type = IdType.ASSIGN_UUID)
    private String pwd;
}
```

```java
@Test
void test01(){
    Student student = new Student();
//   student.setId(2333);
    student.setName("jwh");
//   student.setPwd("23152");
    studentMapper.insert(student);
}
```

## 更新的测试

```java
@Test
void test02() {
    Student student = new Student();
    student.setId(7L);
    student.setPwd("jwkkhxh");
    int i = studentMapper.updateById(student);
    System.out.println(i);
}
```

> 需注意在实体类中TableId注解需正确标注在主键上，如果主键错了会导致失败，更新数为0

## 自动填充功能

### @TableField注解

|       属性       |             类型             | 必须指定 |          默认值          |                             描述                             |
| :--------------: | :--------------------------: | :------: | :----------------------: | :----------------------------------------------------------: |
|      value       |            String            |    否    |            ""            |                         数据库字段名                         |
|        el        |            String            |    否    |            ""            | 映射为原生 `#{ ... }` 逻辑,相当于写在 xml 里的 `#{ ... }` 部分 |
|      exist       |           boolean            |    否    |           true           |                      是否为数据库表字段                      |
|    condition     |            String            |    否    |            ""            | 字段 `where` 实体查询比较条件,有值设置则按设置的值为准,没有则为默认全局的 `%s=#{%s}`,[参考(opens new window)](https://github.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-annotation/src/main/java/com/baomidou/mybatisplus/annotation/SqlCondition.java) |
|      update      |            String            |    否    |            ""            | 字段 `update set` 部分注入, 例如：update="%s+1"：表示更新时会set version=version+1(该属性优先级高于 `el` 属性) |
|  insertStrategy  |             Enum             |    N     |         DEFAULT          | 举例：NOT_NULL: `insert into table_a(<if test="columnProperty != null">column</if>) values (<if test="columnProperty != null">#{columnProperty}</if>)` |
|  updateStrategy  |             Enum             |    N     |         DEFAULT          | 举例：IGNORED: `update table_a set column=#{columnProperty}` |
|  whereStrategy   |             Enum             |    N     |         DEFAULT          | 举例：NOT_EMPTY: `where <if test="columnProperty != null and columnProperty!=''">column=#{columnProperty}</if>` |
|     **fill**     |             Enum             |    否    |    FieldFill.DEFAULT     |                     **字段自动填充策略**                     |
|      select      |           boolean            |    否    |           true           |                     是否进行 select 查询                     |
| keepGlobalFormat |           boolean            |    否    |          false           |              是否保持使用全局的 format 进行处理              |
|     jdbcType     |           JdbcType           |    否    |    JdbcType.UNDEFINED    |           JDBC类型 (该默认值不代表会按照该值生效)            |
|   typeHandler    | Class<? extends TypeHandler> |    否    | UnknownTypeHandler.class |          类型处理器 (该默认值不代表会按照该值生效)           |
|   numericScale   |            String            |    否    |            ""            |                    指定小数点后保留的位数                    |

#### FieldFill

|      值       |         描述         |
| :-----------: | :------------------: |
|    DEFAULT    |      默认不处理      |
|    INSERT     |    插入时填充字段    |
|    UPDATE     |    更新时填充字段    |
| INSERT_UPDATE | 插入和更新时填充字段 |

### 实现步骤：

1. 在实体类上标记出填充字段

```java
public class Student {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String name;
    private String pwd;
    
    //在实体类上先标记出填充字段，接下来需定义一个处理器handler
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
}
```

2. 创建处理器handler

```java
//需要实现MetaObjectHandler并重写其方法
@Component   //一定记住需要加入容器
@Slf4j
public class StudentHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("我要开始插入数据咯~~");
        //在执行插入操作时对一下两个字段进行更新
        this.strictInsertFill(metaObject, "createTime", Date.class, new Date());
        this.strictUpdateFill(metaObject, "updateTime", Date.class, new Date());
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("我要开始更新数据咯！！");
        //在进行更新操作时，对一下字段进行更新
        this.strictUpdateFill(metaObject, "updateTime", Date.class, new Date());
    }
}
```

> 注：Handler中的数据类型注意要与实体类中的相同！！

## 乐观锁插件

**当要更新一条记录的时候，希望这条记录没有被别人更新**
乐观锁实现方式：

- 取出记录时，获取当前version
- 更新时，带上这个version
- 执行更新时， set version = newVersion where version = oldVersion
- 如果version不对，就更新失败

### 实现步骤

1. 加入新的字段version，初始默认值为1
2. 实体类中添加该属性，并使用@Version标记

```java
@Version
private Integer version;
```

3. 创建配置类，在配置类中配置该插件

```java
@Configuration
@MapperScan("com.jwh.mp")
public class MybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

4. 测试代码

```java
@Test
void testOptimisticLockerSuccess(){
    Student student = studentMapper.selectById(1446665743172132870L);
    System.out.println(student);
    student.setPwd("qweqwe111");
    int i = studentMapper.updateById(student);  //查询得到version变为2
    System.out.println(i);
}

@Test
void testOptimisticLockerFail(){
    Student student1 = studentMapper.selectById(1446665743172132870L);
    System.out.println(student1);
    student1.setPwd("qweqwe222");

    Student student2 = studentMapper.selectById(1446665743172132870L);
    System.out.println(student2);
    student2.setPwd("qweqwe333");

    //这一步更新成功，version变为3
    int j = studentMapper.updateById(student2);
    System.out.println(j);

    //version改变，更新失败，因为在此之前数据已经被抢先进行更新过了，无法覆盖
    int i = studentMapper.updateById(student1);
    System.out.println(i);
}
```

## 分页查询插件

**编写配置类**

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor(){
    MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();

    //乐观锁
    mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    //分页查询
    mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));

    return mybatisPlusInterceptor;
}
```

**直接就可以使用啦！**

```java
@Test
//分页查询插件
void test04(){
    Page<Student> page = new Page<>(2, 5);
    Page<Student> studentPage = studentMapper.selectPage(page, null);
    page.getRecords().forEach(System.out::println);
}
```

## 遇到问题

### 1.异常Invalid bound statement (not found)

检查pom.xml是否有误，引入的依赖是`mybatis-plus-boot-starter`，不是`mybatis-plus`

