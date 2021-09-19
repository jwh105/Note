# SpringBoot

**Spring Boot的主要优点：**

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

## 一、创建一个简单的SpringBoot项目

### 1、使用IDEA创建

1. 选择spring initalizr ， 可以看到默认就是去官网的快速构建工具那里实现
2. 填写项目信息
3. 选择初始化的组件（初学勾选 Web 即可）
4. 填写项目路径
5. 等待项目构建成功



### 2、项目结构分析

通过上面步骤完成了基础项目的创建。就会自动生成以下文件。

1. 程序的主启动类
2. 一个 application.properties 配置文件
3. 一个 测试类
4. 一个 pom.xml



**pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!--    父依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.csii.jwh</groupId>
    <artifactId>springboot-01</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-01</name>
    <description>springboot-01</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--        启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <!--        web场景启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--        springboot单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <!--                打包插件-->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>


            <!--
                在工作中,很多情况下我们打包是不想执行测试用例的
                可能是测试用例不完事,或是测试用例会影响数据库数据
                跳过测试用例执
                -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <!--跳过项目运行测试用例-->
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```



### 3、编写一个http接口

1、在主程序的同级目录下，新建一个controller包，一定要在同级目录下，否则识别不到

2、在包中新建一个HelloController类

```java
package com.csii.jwh.springboot01.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@ResponseBody
//@RestController 等价于@Controller + @ResponseBody，否则的话，访问会404
@RequestMapping("/hello")
public class HelloWorldController {

    @RequestMapping("/helloworld")
    public String helloWorld() {
        System.out.println("Hello,world!!");
        return "Hello，world!!";
    }
}
```

**编写完成后，从主程序运行**

```java
//标志这个类是一个SpringBoot应用
@SpringBootApplication
public class Springboot01Application {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01Application.class, args);
    }

}
```

![image-20210916155336113](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210916155336113.png)

> 注：
>
> Controller，注解需要使用@RestController 或者 @Controller + @ResponseBody才能打印出返回值，否则404



## 二、SpringBoot自动装配原理

**pom.xml**

* spring-boot-dependencies：核心依赖在父工程中
* 在引入一-些SpringBoot依赖的时候，不需要指定版本，就因为有这些版本仓库



**启动器**

```xml启动器:说白了就是Springboot的启动场景;
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

* **springboot-boot-starter-xxx**：就是spring-boot的场景启动器

* **spring-boot-starter-web**：帮我们导入了web模块正常运行所依赖的组件；

  SpringBoot将所有的功能场景都抽取出来，做成一个个的starter （启动器），只需要在项目中引入这些starter即可，所有相关的依赖都会导入进来 ， 我们要用什么功能就导入什么样的场景启动器即可 ；我们未来也可以自己自定义 starter；



**主程序**

```java
//标志这个类是一个SpringBoot应用
@SpringBootApplication
public class Springboot01Application {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01Application.class, args);
    }

}
```



**自动配好**Tomcat

* 引入Tomcat依赖。
* 配置Tomcat

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<version>2.3.4.RELEASE</version>
	<scope>compile</scope>
</dependency>
```



**默认的包结构**

主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
无需以前的包扫描配置
想要改变扫描路径：

* @SpringBootApplication(scanBasePackages=“com.lun”)

* @ComponentScan 指定扫描路径

  ```java
  @SpringBootApplication
  等同于
  @SpringBootConfiguration
  @EnableAutoConfiguration
  @ComponentScan("com.lun")
  ```



SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面

