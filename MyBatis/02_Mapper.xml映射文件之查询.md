# 02_Mapper.xml映射文件之查询01

## 一、Mapper.xml映射文件介绍

```
MyBatis 真正的力量是在映射语句中。这里是奇迹发生的地方。对于所有的力量,SQL 映射的 XML 文件是相当的简单。当然如果你将它们和对等功能的 JDBC 代码来比较,你会 发现映射文件节省了大约 95%的代码量。MyBatis 的构建就是聚焦于 SQL 的,使其远离于 普通的方式。 
```

## 二、功能标签

### 1、select

#### a、普通查询

```xml
 <select id="getStudentAll" resultType="org.lanqiao.mybatis.bean.Student">
     select * from student where id>#{id} and classid=#{classid}
</select>

在方法里面使用@param("id")
```

|  标签属性名   | 作用                                                         |
| :-----------: | ------------------------------------------------------------ |
|      id       | 方法名-->标识名、唯一【不支持方法重载】                      |
| parameterType | 可以不需要【使用@param或直接传递对象】、亦可使用参数完全限定名或别名 |
|  resultType   | 返回值类型完全限定名或别名                                   |
| parameterMap  | 已废弃                                                       |
|   resultMap   | 返回值-->外部 resultMap 的命名引用【不可和resultType共存】   |
|  flushCache   | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false。 |
|  flushCache   | 将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。 |
| statementType | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |

------

#### **b、使用resultMap完成实体类多表关系映射**

#### 多对1关系映射

实体类信息

```java
@Alias("student")
public class Student {
    private int id;
    private String name;
    private String pwd;
    private String img;
    private int classid;
    private Classes cinfo;
}

@Alias("classes")
public class Classes {
    private int id;
    private String name;
}
```

【**推荐**】使用sql语句直接完成

```xml
<select id="getStudentAll" resultType="student">
    select s.id,s.`name`,s.pwd,s.img,s.classid,s.classid as "cinfo.id",c.name as "cinfo.name" from student s , classes c where s.classid=c.id
</select>
```

**【不推荐的两种方式】**

一条查询语句、自己配置映射关系

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.lanqiao.mybatis.dao.StudentDao">
    <resultMap id="studentResultMap" type="student">
        <!--
		id:主键id；
		result:其他属性；
		association：关联对象
			property：属性名
			javaType：属性的数据类型
		-->
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="pwd" column="pwd"/>
        <result property="img" column="img"/>
        <result property="classid" column="classid"/>
        <association property="cinfo" javaType="classes">
            <id property="id" column="cid"/>
            <result property="name" column="cname"/>
        </association>
    </resultMap>

    <select id="getStudentAll" resultMap="studentResultMap">
        select s.id,s.`name`,s.pwd,s.img,s.classid,c.id as "cid",c.name as "cname" from student s , classes c where s.classid=c.id
    </select>
</mapper>
```
n+1查询方式【极度不推荐】

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.lanqiao.mybatis.dao.StudentDao">
    <!--
		id:主键id；
		result:其他属性；
		association：关联对象
			property：属性名
			javaType：属性的数据类型
			select：调用的查询方法
			column：调用select方法需要传递的参数
		-->
    <resultMap id="studentResultMap" type="student">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="pwd" column="pwd"/>
        <result property="img" column="img"/>
        <result property="classid" column="classid"/>
        <association property="cinfo" javaType="classes" column="classid" select="getClassesById">
            <id property="id" column="id"/>
            <result property="name" column="name"/>
        </association>
    </resultMap>

    <select id="getStudentAll" resultMap="studentResultMap">
        select * from student
    </select>

    <select id="getClassesById" resultType="classes">
        select * from classes where id=#{id}
    </select>
</mapper>
```


------

#### 1对多关系映射

实体类信息

```java
@Alias("student")
public class Student {
    private int id;
    private String name;
    private String pwd;
    private String img;
    private int classid;
    private Classes cinfo;
}

@Alias("classes")
public class Classes {
    private int id;
    private String name;
    private Set<Student> set;
}
```

方式一【**并不十分推荐**】使用n+1方式查询

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.lanqiao.mybatis.dao.ClassesDao">
    <resultMap id="classResultMap" type="classes">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <!--
            集合关联对象配置
            property：实体类集合的属性名
            select：查询的sql语句方法名
            ofType：集合里面的数据类型
            column：select方法里面需要传递的外键参数
            <collection property="set" column="id" ofType="student" select="getStudentById"/>
        -->
        <collection property="set" column="id" ofType="student" select="getStudentById"/>
    </resultMap>

    <select id="getAll" resultMap="classResultMap">
        select * from classes
    </select>

    <select id="getStudentById" resultType="student">
        select * from student where classid=#{id}
    </select>
</mapper>
```
方式二【**推荐方式**】一次查询
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.lanqiao.mybatis.dao.ClassesDao">
    <resultMap id="classResultMap" type="classes">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <collection property="set" ofType="student">
            <id property="id" column="sid"/>
            <result property="name" column="sname"/>
            <result property="pwd" column="spwd"/>
            <result property="img" column="simg"/>
            <result property="classid" column="sclassid"/>
        </collection>
    </resultMap>

    <select id="getAll" resultMap="classResultMap">
        select c.*,s.id as "sid",s.name as "sname",s.pwd as "spwd",s.img as "simg",s.classid as "sclassid" from classes c left join student s on c.id=s.classid
    </select>
</mapper>
```

------

#### 多对多本质就是两个1对多的关系映射，故掌握1对多关系映射即掌握多对多关系映射

















































