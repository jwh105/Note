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
  @ComponentScan("com.csii.jwh")
  ```

SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面



### 1、引导加载自动配置类

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {}
```

* **@SpringBootConfiguration**

  代表当前是一个 <u>核心配置类</u>
  其中包含有@Configuration，代表当前是一个配置类

* **@ComponentScan**

  指定扫描哪些，Spring注解

* **@EnableAutoConfiguration**

  ```java
  @AutoConfigurationPackage
  @Import({AutoConfigurationImportSelector.class})
  public @interface EnableAutoConfiguration {}
  ```

  * **@AutoConfigurationPackage**

    标签名直译为：自动配置包，指定了默认的包规则。

    ```java
    @Import({Registrar.class})
    public @interface AutoConfigurationPackage {
        String[] basePackages() default {};
    
        Class<?>[] basePackageClasses() default {};
    }
    ```

    * **@Import({Registrar.class})**

      利用`Registrar`，通过`registerBeanDefinitions()`方法进行批量注入

      将指定的一个包下的所有组件导入进MainApplication所在包下

  * **@Import({AutoConfigurationImportSelector.class})**

    利用``getAutoConfigurationEntry(annotationMetadata)``;给容器中批量导入一些组件
    调用``List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)``获取到所有需要导入到容器中的配置类
    利用工厂加载 `Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader);`得到所有的组件:

    1. 从`META-INF/spring.factories`位置来加载一个文件。

    2. 默认扫描当前系统里面所有`META-INF/spring.factories`位置的文件
       `spring-boot-autoconfigure-2.3.4.RELEASE.jar`包里面也有`META-INF/spring.factories`

       ```xml
       # Auto Configure
       org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
       org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
       org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
       org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
       .............
       ```

       虽然一次性加载了全部，但是`xxxxAutoConfiguration`会按照条件装配规则（使用`@Conditional`），进行按需配置，例如：

       ```java
       @Configuration(
           proxyBeanMethods = false
       )
       @ConditionalOnProperty(
           prefix = "spring.aop",  //配置文件中是否存在spring.aop这个配置
           name = "auto",        //spring.aop=auto
           havingValue = "true",
           matchIfMissing = true   //就算你没配，我也认为你配了
       )
       public class AopAutoConfiguration {
           public AopAutoConfiguration() {
           }
       	...
       }
       ```

       

## 三、SpringBoot底层注解

### 1、@Configuration创建配置类

- 基本使用
  - Full模式与Lite模式
  - 示例

```java
/**
 * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
 * 2、配置类本身也是组件
 * 3、proxyBeanMethods：代理bean的方法
 * Full(proxyBeanMethods = true)（保证每个@Bean方法被调用多少次返回的组件都是单实例的）（默认）
 * Lite(proxyBeanMethods = false)（每个@Bean方法被调用多少次返回的组件都是新创建的）
 */
@Configuration(proxyBeanMethods = true) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {

    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user() {
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(pet());
        return zhangsan;
    }

    @Bean("tomcatPet")
    public Pet pet() {
        return new Pet();
    }
}
```

测试：

```java
@SpringBootApplication
public class Springboot01Application {

    public static void main(String[] args) {
        //1、返回我们IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(Springboot01Application.class, args);

        //2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name+"-------");
        }

        //3、从容器中获取组件
        Pet tom01 = run.getBean("tomcatPet",Pet.class);
        System.out.println(tom01);
        Pet tom02 = run.getBean("tomcatPet", Pet.class);
        System.out.println(tom02);
        System.out.println("组件："+(tom01 == tom02));

        //4、com.atguigu.boot.config.MyConfig$$EnhancerBySpringCGLIB$$51f1e1ca@1654a892
        MyConfig bean = run.getBean(MyConfig.class);
        System.out.println(bean);

        //如果@Configuration(proxyBeanMethods = true)代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有。
        //保持组件单实例
        User user = bean.user();
        User user1 = bean.user();
        System.out.println(user == user1);

        User user01 = run.getBean("user", User.class);
        Pet tom = run.getBean("tomcatPet", Pet.class);

        //@Configuration(proxyBeanMethods = true)的情况下。user的宠物和直接得到的宠物不是同一个
        System.out.println("用户的宠物："+(user01.getPet() == tom));
    }
}
```

- 配置 类组件之间**无依赖关系**用Lite模式加速容器启动过程，减少判断容器中是否有该组件
- 配置 类组件之间**有依赖关系**，方法会被调用得到之前单实例组件，用Full模式（默认），每次判断

### 2、@import导入组件

@Import({User.class, DBHelper.class})给容器中**自动创建出这两个类型的组件**、默认组件的名字就是**全类名**，如果在类上使用了@compoent，则组建的名字是小写的类名

```java
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
}
```

测试类：

```java
//1、返回我们IOC容器
ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

//...

//5、获取组件
String[] beanNamesForType = run.getBeanNamesForType(User.class);

for (String s : beanNamesForType) {
    System.out.println(s);
}

DBHelper bean1 = run.getBean(DBHelper.class);
System.out.println(bean1);
```

![image-20210922162333973](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210922162333973.png)

### 3、@Conditional条件装配

条件装配：满足Conditional指定的条件，则进行组件注入

![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/UL920VHDA%7BMA_NQW%5DVR1F%5BU.png)

见名知意，如：@ConditionalOnMissingBean，当容器中没有某组件时启用

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnMissingBean(name = "tom")//没有tom名字的Bean时，MyConfig类的Bean才能生效。
public class MyConfig {

    @Bean
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom22")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}

public static void main(String[] args) {
    //1、返回我们IOC容器
    ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

    //2、查看容器里面的组件
    String[] names = run.getBeanDefinitionNames();
    for (String name : names) {
        System.out.println(name);
    }

    //是否存在名为tom的组件
    boolean tom = run.containsBean("tom");
    System.out.println("容器中Tom组件："+tom);//false

    boolean user01 = run.containsBean("user01");
    System.out.println("容器中user01组件："+user01);//true

    boolean tom22 = run.containsBean("tom22");
    System.out.println("容器中tom22组件："+tom22);//true

}
```

### 4、@ImportResource导入Spring配置文件

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {
...
}
```

### 5、@ConfigurationProperties配置绑定

将property中的属性绑定到JavaBean里边去

传统方式：

```java
public class getProperties {
     public static void main(String[] args) throws FileNotFoundException, IOException {
         Properties pps = new Properties();
         pps.load(new FileInputStream("a.properties"));
         Enumeration enum1 = pps.propertyNames();//得到配置文件的名字
         while(enum1.hasMoreElements()) {
             String strKey = (String) enum1.nextElement();
             String strValue = pps.getProperty(strKey);
             System.out.println(strKey + "=" + strValue);
             //封装到JavaBean。
         }
     }
 }
```

#### 方式一：@ConfigurationProperties + @Component

application.properties：

```properties
student.name=jwh
student.id=55123
student.classid=51284
```

Bean：

```java
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
    private String name;
    private int id;
    private int classid;

    /*
     *get/set/toString以及一些构造方法
     */
}
```

测试：

如果在测试代码中，可直接通过getBean获取到，如果在Controller中使用，需要@Autowired自动装配一下

```java
@Autowired
Student student1;

@RequestMapping("/student")
public Student student(){
    return student1;
}
```

#### 方式二：@ConfigurationProperties+@EnableConfigurationProperties

```java
@ConfigurationProperties(prefix = "student")
public class Student {
	/*
	***
	*/
}
```

```java
@EnableConfigurationProperties(Student.class)
//1、开启属性配置功能
//2、把Student自动注入到容器中
public class MyConfig {
    /*
    ***
    */
}
```

> ### 疑问：
>
> ![image-20210922180106741](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210922180106741.png)
>
> 控制台输出：
>
> ![image-20210922180116092](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210922180116092.png)
>
> **为什么查询不到有这个组件，但是通过getBeanNamesForType获取能获取到？**
>
> **解决：**根据getBeanNamesForTypes得到的组件名称并不是像往常那样的student，而是  `类名小写-包名` 的组合，可以保存字符串来使用，经测试可以通过getBean获取到实例。

## 四、SpringBoot怎么用？

* 引入场景依赖
  * 官方文档
* 查看自动配置了哪些（选做）
  * 自己分析，引入场景对应的自动配置一般都生效了
  * 配置文件中debug=true开启自动配置报告。
    * Negative（不生效）
    * Positive（生效）
* 是否需要修改
  * 参照文档修改配置项
    * 官方文档
    * 自己分析。xxxxProperties绑定了配置文件的哪些。
  * 自定义加入或者替换组件
    * @Bean、@Component…
  * 自定义器 XXXXXCustomizer；
  * …

## 五、配置文件-YAML

适合用于做以数据为中心的配置文件

### 1、基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，单引号’’、双引号""表示字符串内容会被 转义、不转义

### 2、数据类型

- 字面量：单个的、不可再分的值。date、boolean、string、number、null

```yaml
k: v
```

- 对象：键值对的集合。map、hash、object

```yaml
#行内写法(可以不要空格，但有也不报错)：  

k: {k1:v1,k2:v2,k3:v3}

#或

k: 
  k1: v1
  k2: v2
  k3: v3
```

- 数组：一组按次序排列的值。array、list、queue、set

```yaml
#行内写法：  

k: [v1,v2,v3]

#或者

k:
 - v1
 - v2
 - v3
```

**示例：**

```java
@Data
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
    private List<List<Integer>> xxx;
}

@Data
@Component
@ConfigurationProperties(prefix = "pet")
public class Pet {
    private String name;
    private Double weight;
}
```

用yaml绑定(命名为application.yml):

```yaml
#Person
person:
  username: "jwh"
  boss: true
  birth: 1999/11/28 23:33:32
  age: 22
  pet:
    name: "小汪"
    weight: 20
  interests: [ "吃饭","睡觉","打豆豆" ]
  animal:
    - jerry
    - tom
  score: { "chinese": 97,"math": [ 98,92 ] ,"english": 99 }
  salarys:
    - 2200.34
    - 4600.52
  allpets:
    dog: [ { name: "小黑",weight: "25" },{ name: "小白",weight: "33" } ]
    cat:
      - { name: "小喵",weight: 55 }
      - { name: "三十二",weight: 16 }
  xxx:
    - - 12
      - 13
      - 14
    - - 22
      - 23
      - 24
```

> **注：**
>
> 1. 注意空格，虽然有的`:`不加空格不报错，但最好还是都加上
> 2. 注意套娃，理清层次关系
> 3. 分清`中括号`和`花括号`的用法，其中Set是同List一样使用`中括号`和`-`的
> 4. 关于字符串，单引号会转义，双引号不会转义

### 3、自定义类绑定的配置提示

默认是不会有提示的，可添加新的依赖来实现提示

![image-20210926172608495](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210926172608495.png)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

<!-- 下面插件作用是工程打包时，不将spring-boot-configuration-processor打进包内，让其只在编码的时候有用 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-configuration-processor</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 六、Web场景开发

### 1、静态资源目录

只要静态资源放在类路径下： called /static (or /public or /resources or /META-INF/resources

访问 ： 当前项目根路径/ + 静态资源名

原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面。

也可以改变默认的静态资源路径，/static，/public,/resources, /META-INF/resources失效

**修改静态资源路径：**

```yaml
spring:
  resources:
    static-locations: [classpath:/hhh]
```

访问地址：http://localhost:8080/adada.jpg

**静态资源访问前缀：**

```
spring:
  mvc:
    static-path-pattern: /boot/res/**
```

访问地址：http://localhost:8080/boot/res/adada.jpg

### 2、webjar

可用jar方式添加css，js等资源文件，

https://www.webjars.org/

例如，添加jquery

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.5.1</version>
</dependency>
```

访问地址：[http://localhost:8080/webjars/**jquery/3.5.1/jquery.js**](http://localhost:8080/webjars/jquery/3.5.1/jquery.js) 后面地址要按照依赖里面的包路径。

### 3、欢迎页

- 静态资源路径下 index.html，会自动识别为欢迎页面。
  - 可以配置静态资源路径
  - 但是不可以配置静态资源的访问前缀。否则导致 index.html不能被默认访问

```yaml
spring:
  resources:
    static-locations: [classpath:/hhh]
#  mvc:
#    static-path-pattern: /boot/res/**  这个配置会是欢迎页失效
```

### 4、自定义Favicon

指网页标签上的小图标。

favicon.ico 放在静态资源目录下即可。

*同样，访问前缀的配置会使favicon.ico失效*
