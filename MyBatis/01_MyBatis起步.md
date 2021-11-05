# 01_MyBatis起步

# 框架：框架的学习（运用）最简单、照着步骤做就可以

# 学习框架：先照着步骤做出来、再问为什么

## 一、MyBatis介绍

```
MyBatis 是支持普通 SQL 查询,存储过程和高级映射的优秀持久层框架。MyBatis 消除 了几乎所有的 JDBC 代码和参数的手工设置以及结果集的检索。MyBatis 使用简单的 XML 或注解用于配置和原始映射,将接口和 Java 的 POJOs(Plan Old Java Objects,普通的 Java 对象)映射成数据库中的记录。 简化了访问数据库操作
```

## 二、使用步骤

### 1、官网下载jar（mybatis-3.4.6.jar）：http://www.mybatis.org/mybatis-3/zh/index.html

或者直接使用maven依赖

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.6</version>
</dependency>
```

### 2、创建测试类、从 XML 中构建 SqlSessionFactory

```java
package org.lanqiao.mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.lanqiao.mybatis.bean.Student;

import java.io.InputStream;
import java.util.List;

public class Demo {
    public static void main(String[] args)throws Exception {
        String resource = "mybatis.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();

    }
}
```

### 3、添加Mybatis的配置文件 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.property"/>
    <typeAliases>
        <package name="org.lanqia.mybatis.bean"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/lanqiao/mybatis/mapper/StudentMapper.xml"></mapper>
    </mappers>
</configuration>
```

### 4、添加Mybatis配置文件依赖的属性文件

```
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://124.70.142.213:3306/test?useSSL=false&useUnicode=true&characterEncoding=utf-8
username=root
password=qwe123!@#
```

### 4、添加Mybatis配置文件依赖的映射文件【StudentMapper.xml】

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.lanqiao.mybatis.dao.StudentDao">
    <select id="getStudentAll" resultType="student">
        select * from student
    </select>
</mapper>
```

### 5、添加映射文件对应的实体类

```java
package org.lanqiao.mybatis.bean;
@Alias("student")
public class Student {
    private int id;
    private String name;
    private String pwd;
    private String img;
    private int classid;
	//构造函数和get、set方法等省略
}
```

### 6、单纯的使用【mapper.xml】映射文件测试

```java
package org.lanqiao.mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.lanqiao.mybatis.bean.Student;

import java.io.InputStream;
import java.util.List;

public class Demo {
    public static void main(String[] args)throws Exception {
        String resource = "mybatis.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();

        List<Object> objects = sqlSession.selectList("org.lanqiao.mybatis.dao.StudentDao.getStudentAll");
        for (Object info:objects){
            System.out.println(info);
        }

        System.out.println("over");
    }
}
```

### 7、亦可使用【mapper.xml映射文件+接口】形式配合使用

```java
//包名+接口名、方法名、务必和mapper的配置保持一致
//namespace="包名类名"
//select id="方法名"
//resultType 返回值类型的完全限定名
//<mapper namespace="org.lanqiao.mybatis.dao.StudentDao">
//    <select id="getStudentAll" resultType="org.lanqiao.mybatis.bean.Student">
//        select * from student
//    </select>
//</mapper>
package org.lanqiao.mybatis.dao;

import org.lanqiao.mybatis.bean.Student;
import java.util.List;

public interface StudentDao {
    public List<Student> getStudentAll();
}

//调用方式
StudentDao dao = session.getMapper(StudentDao.class);
List<Student> selectList = dao.getStudentAll();
```

### 8、亦可只使用【接口+注解方式】、完全去掉mapper.xml配置

```java
package org.lanqiao.mybatis.dao;

import org.apache.ibatis.annotations.Select;
import org.lanqiao.mybatis.bean.Student;
import java.util.List;

public interface StudentDao {
    @Select("select * from student")
    public List<Student> getStudentAll();
}
```

**但需要在配置文件添加接口的包扫描**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <mappers>
       	<!-- 纯Mapper和mapper+接口形式 -->
    	<mapper resource="org/lanqiao/mapper/StudentMapper.xml"/>
    	
    	<!-- 这两种都只能针对注解 -->
    	<mapper class="org.lanqiao.dao.StudentDao"/>
       	<package name="org.lanqiao.dao"/>
        <package name="org.lanqiao.mybatis.dao"/>
    </mappers>
</configuration>
```

### 9、小结：

#### 三种使用mybatis方式、根据实际情况选择

1、完全使用【**mapper.xml映射文件**】方式：【**不推荐**】，仅仅用来做演示mybatis学习过程、理解其工作原理即可

2、【**接口+mapper.xml映射****】方式：【**中大型项目推荐**】这种方式是mybatis最核心、功能最齐全、最强大的方式，但并不怎么好用，其优点是：“把所有sql语句统一管理，对大项目而言来说：方便以后维护，但开发的便捷性却一般”

3、完全使用【**接口+注解**】方法：【**中小型项目推荐**】





> 常见错误：
>
> 1. `Mapped Statements collection does not contain value for com.csii.jwh.mapper.StudentMapper`
>
>    这个问题可能有如下原因：
>
>    1. namespace不对
>
>    2. 配置中没有引入mapper的xml文件
>
>    3. 逻辑代码中引用不全，要详细到方法，如：session.selectList("com.csii.jwh.mapper.StudentMapper.getStudentAll");
>
>    4. idea的扫描机制，导致src/main/java下的xml文件不会被扫描到(Maven静态资源过滤问题)，Maven中添加配置：
>
>       ```
>       <build>
>           <!--扫描xml文件-->
>           <resources>
>               <resource>
>                   <directory>src/main/java</directory>
>                   <includes>
>                       <include>**/*.xml</include>
>                   </includes>
>                   <filtering>true</filtering>
>               </resource>
>           </resources>
>       </build>
>       ```

