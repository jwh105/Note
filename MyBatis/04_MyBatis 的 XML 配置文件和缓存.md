# 04_MyBatis 的 XML 配置文件和缓存

## MyBatis的XML整体介绍

```xml
MyBatis 的 XML 配置文件结构如下: 
configuration 配置 
	properties 属性
	settings 设置
	typeAliases 类型命名
	typeHandlers 类型处理器
	objectFactory 对象工厂
	plugins 插件
	environments 环境 
		environment 环境变量 
		transactionManager 事务管理器
	dataSource 数据源

	databaseIdProviderchinese?
	mappers 映射器
```

## 一、整体配置文件介绍

```xml

<properties resource="jdbc.property">
	<property name="password" value="qwe123!@#"/>
</properties>

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--其他地方可以使用${password}来引用这个变量-->
    <properties resource="jdbc.property">
        <property name="password" value="qwe123!@#"/>
    </properties>
    <!--MyBatis设置信息-->
    <settings>
        <!--启用延迟加载数据、cacheEnabled，lazyLoadingEnabled-->
        <!--
			1、延迟加载：用的时候就查询、不用的时候并不会查询
			2、即使加载：不管你用不用、都会去数据库查询出来
		-->
        <setting name="cacheEnabled" value="true"/>
        <setting name="lazyLoadingEnabled" value="true"/>
        <!--选择日志、选择后需要导入对应的jar包和配置-->
        <setting name="logImpl" value="log4j"/>
    </settings>
    <!--别名扫描包-->
    <typeAliases>
        <package name="org.lanqiao.mybatis.bean"/>
    </typeAliases>
    <!--数据源设置、可以设置多个数据源environment---
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
	<!--Mapper映射文件设置-->
    <mappers>
        <!--XML配置-->
        <mapper resource="org/lanqiao/mybatis/mapper/StudentMapper.xml"/>
        <!--单个接口配置-->
        <mapper class="org.lanqiao.mybatis.dao.StudentDao"/>
        <!--多个接口配置、包扫描模式、一次性配置org.lanqiao.mybatis.dao包下面的所有接口-->
        <package name="org.lanqiao.mybatis.dao"/>
    </mappers>
</configuration>
```

## 二、缓存

### 一级缓存 session级别、【默认开启】、增删改默认刷新一级缓存

```
一级缓存是SqlSession级别的缓存。在操作数据库时需要构造 sqlSession对象，mybatis 使用HashMap 来存储缓存数据。不同的sqlSession之间的缓存数据区域（HashMap），所以是互不影响的
```

### 二级缓存【默认是关闭、不常用】

```
是mapper级别的缓存，多个SqlSession去操作同一个Mapper的sql语句，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的
```

使用步骤

```xml
1、修改mybatis-config.xml的setting设置
	<setting name="cacheEnabled" value="true"/>
2、Mapper.XML：加入：<cache/>
3、缓存的bean要实现序列化接口 Serializable
4、一定要关闭第一个sqlSession

//SqlSession:连接对象Connection、mybatis的session并不是会话，指的就是Sql的Connection
		SqlSession session1 = sqlSessionFactory.openSession();
		SqlSession session2 = sqlSessionFactory.openSession();
		
		//为了使用缓存、sql语句一定要统一规范
		StudentDao dao1=session1.getMapper(StudentDao.class);
		System.out.println("查询一次数据");
		List<Student> list1 = dao1.getStudentAll();
		for (Student student : list1) {
			System.out.println(student);
		}
		session1.close();
		System.out.println("第二次查询数据");
		Thread.sleep(10000);
		StudentDao dao2=session2.getMapper(StudentDao.class);
		System.out.println("查询一次数据");
		List<Student> list2 = dao2.getStudentAll();
		for (Student student : list2) {
			System.out.println(student);
		}
```















