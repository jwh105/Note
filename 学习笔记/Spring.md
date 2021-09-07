# Spring框架（8.23）

## 1 概述

1. Spring是轻量级的开源的JavaEE框架
2. 可以解决企业应用开发的复杂性
3. 有两个核心部分：
   * IOC：控制反转，把创建对象的过程交给Spring进行管理
   * AOP：面向切面：不修改源代码进行功能增强
4. 特点：
   1. 方便解耦，简化开发
   2. AOP编程支持
   3. 方便程序测试
   4. 方便和其它框架进行整合
   5. 方便进行事务操作
   6. 降低API开发难度



**Spring下载地址**：[repo.spring.io](https://repo.spring.io/ui/native/release/org/springframework/spring)

Spring四个基本包：

![image-20210823173030618](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210823173030618.png)

其中commons-logging.jar是用于记录日志的，也是一个必备包











## 2 IOC容器

**什么是IOC？**

1. 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理。
2. 使用IOC目的:为了耦合度降低。



### 2.1 IOC底层原理

XML+工厂模式+反射

**IOC过程**

1. xml配置文件，配置创建对象

   <bean id="dao" class="com.csii.jwh.UserDao"></bean>

2. 有Service类和Dao类，创建工厂类

   ```java
   class UserFactory {
       public static UserDao getDao() {
           String classValue = "com.csii.jwh.UserDao"; //1 xml解析得到class属性值
           Class clazz = Class.forName(classValue); //2 通过反射创建对象
           return (UserDao)clazz.newInstance();
       }
   }
   ```



### 2.2 IOC（接口）

IOC思想基于IOC容器完成，IOC容器底层是<u>对象工厂</u>。

Spring提供IOC容器实现两种方式（两种接口）：

1. BeanFactory：IOC容器，是Spring内部的使用接口，不提供开发人员使用
   *加载配置文件时候不会创建对象，在获取对象(使用)才去创建对象*

2. ApplicationContext：BeanFactory的子接口，提供了更多更强大的功能，一般由开发人员使用
   ***加载配置文件时就会把配置文件里的对象创建***
3. ApplicationContext有实现类：
   - FileSystemXmlApplicationContext（获取我电脑磁盘中的配置文件）
   - ClassPathXmlApplicationContext（根目录就在项目的src下）



### 2.3 IOC操作Bean管理

**什么是Bean管理？**

Bean指的是两个操作：Spring创建对象、Spring注入属性

**Bean管理操作有两种方式？**

基于XML配置文件方式、基于注解方式实现

#### 2.3.1 基于XML配置文件

> 1. **基于XML创建对象**
>
>    ​	**<bean id="dao" class="com.csii.jwh.UserDao"></bean>**
>
>    1. 在spring配置文件中，使用bean标签，标签里面添加对应属性，就可以实现对象创建
>
>    2. bean标签中常用属性：
>
>       * id属性：唯一标识（不能有特殊符号）
>       * class属性：类全路径（包全路径）
>
>    3. 创建对象时，默认执行无参的构造方法
>
> 
>
> 2. **基于XML创建属性**
>
>    DI：依赖注入，利用set方法进行属性注入
>
>    <span id="span2">**方式一：**set方法进行注入</span>
>
>    1. 创建类，创建属性及对应set方法
>    2. 配置配置文件
>
>    ```xml
>    <!--  set方式注入属性  -->
>    <bean id="user" class="com.csii.jwh.User">
>        <property name="name" value="jwh"></property>
>        <property name="age" value="22"></property>
>    </bean>
>    ```
>
>    <span id="span1">**方式二：**构造方法注入</span>（8.24）
>
>    ```xml
>    <!--第一种方式：直接通过参数名赋值-->
>    <bean id="user" class="com.csii.jwh.User">
>        <constructor-arg name="age" value="22"></constructor-arg>
>    </bean>
>    <!--    第二种，通过下标赋值-->
>    <bean id="user" class="com.csii.jwh.User">
>        <constructor-arg index="0" value="jwh"></constructor-arg>
>    </bean>
>    <!--    第三种：通过类型创建（不建议）-->
>    <bean id="user" class="com.csii.jwh.User">
>        <constructor-arg type="int" value="22"></constructor-arg>
>    </bean>
>    ```
>
>    **测试：**
>
>    ```java
>    public void test() {
>        //1. 加载配置文件
>        ApplicationContext context = new ClassPathXmlApplicationContext("ApplicetionContext.xml");
>        //2. 获取配置创建的对象
>        User user = context.getBean("user", User.class);
>        System.out.println(user);
>    }
>    ```
>
> 



## 3 Spring配置说明（8.24）

### 3.1 别名

```xml
<!-- 如果添加了别名，也可以通过别名来获取到对象 -->
<alias name= "user" alias="userNew" />
```

### 3.2 Bean的配置

```xml
<!--id : bean的唯一标识符，也就是相当于对象名
    class : bean 对象所对应的全限定名  包名+类型
    name :也是别名，而且name 可以同时取多个别名，用空格，逗号，分号分隔
-->
<bean id="user" class="com.csii.jwh.User" name="user2 u1,u2;u3">
    <property name="name" value="jwh"></property>
    <property name="age" value="22"></property>
</bean>
```

### 3.3 import

一般用于团队开发使用，可以将多个配置文件导入合并为一个

```xml
<import resource="bean1.xml"/>
<import resource="bean2.xml"/>
<import resource="bean3.xml"/>
```



## 4 依赖注入

### 4.1 构造器注入

见[基于XML创建属性方式二](#span1)

#### 4.1.1 c命名空间注入

通过构造器注入：construct-args

1. 配置文件头部导入头文件约束

```xml
xmlns:c="http://www.springframework.org/schema/c"
```

2. 直接以bean属性通过构造器注入

```xml
<bean id="user" class="com.csii.jwh.User" c:age="32" c:name="金文浩" name="jwh"/>
```



### 4.2 Set方式注入【重】

见[基于XML创建属性方式一](#span2)

各种类型的注入方法：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <import resource="bean1.xml"/>
    <import resource="bean2.xml"/>
    <import resource="bean3.xml"/>

    <bean id="address" class="com.csii.jwh.Address">
        <property name="address" value="贵州遵义"></property>
    </bean>

    <bean id="student" class="com.csii.jwh.Student">
        <!--        普通值注入-->
        <property name="name" value="金文浩"></property>

        <!--        Bean注入-->
        <property name="address" ref="address"></property>

        <!--        数组-->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>水浒传</value>
                <value>三国演义</value>
            </array>
        </property>

        <!--        List集合-->
        <property name="hobbys">
            <list value-type="java.lang.String">
                <value>吃饭</value>
                <value>睡觉</value>
                <value>打豆豆</value>
            </list>
        </property>

        <!--        Map集合-->
        <property name="cards">
            <map>
                <entry key="身份证" value="52212111111111"/>
                <entry key="学生证" value="2017404040404"/>
            </map>
        </property>

        <!--        Set集合-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>GTA5</value>
                <value>H1Z1</value>
            </set>
        </property>

        <!--        null-->
        <property name="wife">
            <null/>
        </property>

        <!--        Properties-->
        <property name="info">
            <props>
                <prop key="aaa">AAA</prop>
            </props>
        </property>
    </bean>
</beans>
```

#### 4.2.1 p命名空间注入

可以直接注入属性的值：property

1. 配置文件头部导入头文件约束

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

2. 可以使用`bean`元素的属性(而不是嵌套`<property/>`元素)来描述bean的属性值

```xml
<bean id="address" class="com.csii.jwh.Address" p:address="贵州遵义"/>
```



## 5 Bean作用域

| Scope                                                        | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [singleton](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-singleton) | (默认)将每个 Spring IoC 容器的单个 bean 定义范围限定为单个对象实例。 |
| [prototype](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-prototype) | 将单个 bean 定义的作用域限定为任意数量的对象实例。           |
| [request](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-request) | 将单个 bean 定义的范围限定为单个 HTTP 请求的生命周期。也就是说，每个 HTTP 请求都有一个在单个 bean 定义后面创建的 bean 实例。仅在可感知网络的 Spring `ApplicationContext`中有效。 |
| [session](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-session) | 将单个 bean 定义的范围限定为 HTTP `Session`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |
| [application](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-application) | 将单个 bean 定义的范围限定为`ServletContext`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |
| [websocket](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/web.html#websocket-stomp-websocket-scope) | 将单个 bean 定义的范围限定为`WebSocket`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |

1. singleton 单例模式（Spring的默认机制）![image-20210824151800251](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210824151800251.png)
2. prototype 原型模式：每次从容器中get的时候，都会产生一个新的对象



## 6 Bean自动装配

- 自动装配是Spring满足bean依赖一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性



在Spring中有三种自动装配的方式

1. 在xml中显式的配置
2. 在java中显式的配置
3. 隐式的自动装配bean【重】

### 6.1 测试：一个人有两个动物

![image-20210824164907100](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210824164907100.png)![image-20210824164934514](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210824164934514.png)

#### 6.1.1 ByName自动装配

```xml
<!--    byName:会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean id!-->
<bean id="people" class="com.csii.jwh.People" autowire="byName">
    <property name="name" value="jwh"></property>
</bean>
```

#### 6.1.2 ByType自动装配

```xml
<!--    byType:会自动在容器上下文中查找，和自己对象属性类型相同的bean（必须保证类型唯一） !-->
<bean id="people" class="com.csii.jwh.People" autowire="byType">
    <property name="name" value="jwh"></property>
</bean>
```

**小结：**

- byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致!
- byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致!

### 6.2 使用注解实现自动装配（spring-aop-5.3.9.jar）

导入context约束，配置注解的支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" <!--这里-->
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context <!--这里-->
        https://www.springframework.org/schema/context/spring-context.xsd"> <!--这里-->

    <context:annotation-config/> <!--这里-->

</beans>
```

寻找规律，其实就是原来的代码将beans改成了context

```java
public class People {
    private String name;
    //定义了Autowired的required属性为false，则该对象可以为nu11，否则不允许为空
    @Autowired(required = false) 
    private Dog dog;
    @Autowired
    private Cat cat;
    
    /*一些方法*/
}
```

直接在属性上写==@Autowired== ，也可以写在set方法上，==Autowired的required属性如果为false，则该对象可以为nu11，否则不允许为空==

> 注：如果未添加依赖 `spring-aop-5.3.9.jar` ，会报错：Unexpected exception parsing XML document from class path resource [beans.xml]



如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候、我们可以使用==@Qualifier(value="xxx")==去配合@Autowired的使用，指定一个唯一的bean对象注入!

```java
public class People {
    private String name;
    @Autowired
    @Qualifier(value = "dog22")
    private Dog dog;
    @Autowired
    private Cat cat;
}
```



**@Resource注解**（不属于Spring）

```java
public class People {
    private String name;
    @Resource(name = "dog22")  //指定名称，自动装配
    private Dog dog;
    @Resource  //名称有一致的或class唯一的，都可以自动装配
    private Cat cat;
}
```

**小结：**

@Resource和@ Autowired的区别:

- 都是用来自动装配的，都可以放在属性字段上
- @Autowired 通过byname的方式实现，而且必须要求这个对象存在! 
- @Resource 默认通过byname的方式实现，如果找不到名字，则通过byType实现! 如果两个都找不到的情况
  下，就报错!





## 7 使用注解开发（8.25）

在Spring4之后，要使用注解开发,必须要保证aop的包导入了

![image-20210825105602871](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210825105602871.png)

使用注解需要导入context约束，增加注解的支持!

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    <context:component-scan base-package="com.csii.jwh"/>

</beans>
```



### 7.1 Bean

@Component：组件，放在类上，说明这个类被Spring管理了，就是bean（等价于xml中的<bean>）

```java
@Component
public class User {
    private String name;
    private int age;
}
```



### 7.2 属性注入

@Value(“xxx”)：写在 ==属性== 或 ==set方法== 上，给属性赋值（相当于<bean>中的<property>）

```java
public class User {
    @Value("金文浩")
    private String name;
    private int age;
    
    @Value("22")
    public void setAge(int age) {
        this.age = age;
    }
}
```



### 7.3 衍生的注解

@Component有几个衍生注解，我们在web开发中，会按照mvc三层架构分层

- dao  【@Repository】
- service  【@Service】
- controller  【@Controller】
  这四个注解功能都是一样的， 都是代表将某个类注册到Spring容器中，装配Bean

```java
@Controller
public class UserController {
}

@Repository
public class UserDao {
}

@Service
public class UserService {
}
```



### 7.4 自动装配

@Autowired：自动装配通过 类型>名字
		如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx")
@Nullable：字段标记了这个注解，说明这个字段可以为null
@Resource：自动装配通过 名字>类型



### 7.5 作用域

@Scope(“xxx”)

```java
@Scope("singleton")		//singleton  单例模式    prototype  原型模式
public class User {
    @Value("金文浩")
    private String name;
    private int age;
}
```



### 7.6 小结

xml与注解:

- xml更加万能，适用于任何场合，维护简单方便
- 注解不是自己类使用不了，维护相对复杂!

xml与注解最佳实践:

- xml用来<u>管理bean</u>
- 注解只负责完成<u>属性的注入</u>
- 我们在使用的过程中，只需要注意一个问题: 必须让注解生效，就需要开启注解的支持

```xml
<context:annotation-config/>
<context:component-scan base-package="com.csii.jwh"/>
```





## 8 Spring依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.12.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.12.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.2.12.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>5.2.12.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.2.12.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## 9 使用Java的方式配置Spring（8.26）

可以完全不使用Spring的xm|配置，全权交给Java来做!
JavaConfig是Spring的一个子项目,在Spring 4之后,它成为了一个核心功能!

1. **Bean**

```java
package com.csii.jwh.bean;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class People {
    @Value("jwh")
    private String name;
    @Autowired
    private Dog dog;
    @Autowired
    private Cat cat;
    
    /*一些方法*/
}
```



2. **JavaConfig**【**重**】

```java
//这个也会Spring容器托管，注册到容器中，因为他本来就是一个@Component
//@Configuration代表这是一个配置类，等同于beans.xm1
@Configuration
@ComponentScan("com.csii.jwh.bean")
@Import(MyConfig_1.class)
public class MyConfig {
    //注册一个bean，就相当于一个bean标签
    //这个方法的名字，就相当于bean标签中的id属性
    //这个方法的返回值，就相当于bean标签中的class属性
    @Bean
    public People people(){
        return new People();
    }
}
```



3. **Test**

```java
@Test
public void test1(){
    //如果完全使用了配置类，就只能通过AnnotationConfigApplicationContext来获取容器，通过配置类的class对象加载!
    ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
    People people = context.getBean("people", People.class);
    people.getCat().shot();
    people.getDog().shot();
    System.out.println(people);
}
```



## 10 代理模式

### 10.1 静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，一般会做一些附属操作
- 客户：访问代理对象的人!



方法步骤：

1. 接口

   ```java
   //租房
   public interface Rent {
       public void rent();
   }
   ```

   

2. 真实角色

   ```java
   //房东
   public class Landlord implements Rent{
       public void rent(){
           System.out.println("房东要出租房屋啦！");
       }
   }
   ```

   

3. 代理角色

   ```java
   //中介
   public class Proxy implements Rent{
       private Landlord landlord;
   
       public Proxy() {
       }
   
       public Proxy(Landlord landlord) {
           this.landlord = landlord;
       }
   
       //中介不仅仅帮房东租房，还有一些附加操作
       @Override
       public void rent() {
           see();
           landlord.rent();
           heTong();
           fare();
       }
   
       public void see(){
           System.out.println("带你看房");
       }
   
       public void heTong(){
           System.out.println("跟你签合同");
       }
   
       public void fare(){
           System.out.println("收中介费");
       }
   }
   ```

   

4. 客户端访问代理角色

   ```java
   //客户
   public class Client {
       public static void main(String[] args) {
           Landlord landlord = new Landlord();
           //代理角色一般会有一些附加的操作
           Proxy proxy = new Proxy(landlord);
           //租房不在面向房东而是代理角色--中介
           proxy.rent();
       }
   }
   ```

   



代理的好处：

- 可以使真实角色的操作更加纯粹!不用去关注一些公共的业务
- 公共也就就交给代理角色!实现了业务的分工! .
- 公共业务发生扩展的时候,方便集中管理!

缺点：

- 一个真实角色，就会产生一个代理角色，代码量翻倍，效率变低



在实际开发中，当一个业务需要添加新的功能时，可以通过代理模式，在不改变原有代码的基础上增加新功能

如：

![image-20210826153159356](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210826153159356.png)

原有业务代码需要增加日志功能

![image-20210826154653797](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210826154653797.png)

通过代理，可以在不修改原有业务代码的基础上添加新功能，可以保证原有业务代码依然专注于自身业务，不会破坏原有结构



### 10.2 动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是我们直接写好的!
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口 -- JDK动态代理
  - 基于类: cglib
  - java字节码实现: javasist

需要了解两个类：Proxy类   InvocationHandler接口

- `Proxy`提供了 *创建动态代理类和实例* 的 静态方法 ，它也是由这些方法创建的所有动态代理类的超类

> public static Object newProxyInstance(ClassLoader loader,
>                               					        类<?>[] interfaces,
>                               					        InvocationHandler h)
>                                throws IllegalArgumentException
>
> 返回指定接口的==代理类的实例==，该接口将方法调用分派给指定的调用处理程序。
>
> **参数** 
>
> `loader` - 类加载器来定义代理类 
>
> `interfaces` - 代理类实现的接口列表 
>
> `h` - 调度方法调用的调用处理函数 

- `InvocationHandler`是由代理实例的*调用处理程序*实现的*接口* 。

  每个代理实例都有一个关联的调用处理程序。  当在代理实例上调用方法时，方法调用将被编码并分派到其调用处理程序的`invoke`方法。

> Object invoke(Object proxy,
>        				  方法 method,
>     				     Object[] args)
>          throws Throwable
>
> 处理代理实例上的方法调用并返回结果。  当在与之关联的代理实例上调用方法时，将在调用处理程序中调用此方法。 
>
> **参数** 
>
> `proxy` - 调用该方法的代理实例 
>
> `method` -所述`方法`对应于调用代理实例上的接口方法的实例。  `方法`对象的声明类将是该方法声明的接口，它可以是代理类继承该方法的代理接口的超级接口。 
>
> `args`  -包含的方法调用传递代理实例的参数值的对象的阵列，或`null`如果接口方法没有参数。  原始类型的参数包含在适当的原始包装器类的实例中，例如`java.lang.Integer`或`java.lang.Boolean`  。

1. 接口

```java
//租房
public interface Rent {
    public void rent();
}
```

2. 真实角色（实现接口）

```java
//房东
public class Landlord implements Rent{
    public void rent(){
        System.out.println("房东要出租房屋啦！");
    }
}
```

3. 处理类（生成代理类，完成一些附加操作）

```java
//这个类会自动生成代理类
public class ProxyInvocationhandler implements InvocationHandler {
    //被代理的接口
    private Rent rent;

    //使用set来注入
    public void setRent(Rent rent) {
        this.rent = rent;
    }

    /*两个构造方法*/

    //生成得到代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), 
                             rent.getClass().getInterfaces(),//代理类实现的接口列表
                             this);
    }

    //处理代理实例，并返回结果，真正执行的操作都在这里
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //动态代理的本质，还是反射
        Object result = method.invoke(rent, args);
        return result;
    }
}
```

4. 客户端测试

```java
public static void main(String[] args) {
    //真实角色
    Landlord landlord = new Landlord();
    //代理角色，现在没有
    ProxyInvocationhandler proxyInvocationhandler = new ProxyInvocationhandler();
    //通过调用程序处理角色，来处理我们要调用的按口对象
    proxyInvocationhandler.setRent(landlord);
	 //生成代理类并转换为 被代理的接口
    Rent proxy = (Rent) proxyInvocationhandler.getProxy();
    proxy.rent();
}
```



由此可写出一个公用的工具类ProxyInvocationHandler

```java
public class ProxyInvocationHandler implements InvocationHandler {
    //被代理的接口
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    public ProxyInvocationHandler() {
    }

    public ProxyInvocationHandler(Object target) {
        this.target = target;
    }

    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }


    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("[DEBUG] 调用了" + method.getName() + "方法");
        Object result = method.invoke(target, args);
        return result;
    }
}
```

![image-20210826180325393](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210826180325393.png)





## 11 AOP（8.27）

### 11.1 什么是AOP

AOP (Aspect Oriented Programming)意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能
的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是
函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![image-20210827094906406](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210827094906406.png)



SpringAOP中，通过Advice定义横切逻辑，Spring中支持5中类型的Advice：

![image-20210827100953831](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210827100953831.png)

即，AOP在不改变原有代码的基础上，去增加新功能



```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```



### 11.2 AOP在Spring中的作用

==提供声明式事务，允许用户自定义切面==

- 横切关注点:跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志,安全,缓存,事务等等....
- 切面(ASPECT) :横切关注点被模块化的特殊对象。即，它是<u>一个类</u>
- 通知(Advice) :切面必须要完成的工作。即，它是类中的<u>一个方法</u>
- 目标(Target) :被通知对象
- 代理(Proxy) :向目标对象应用通知之后创建的对象
- 切入点(PointCut) :切面通知执行的“地点”的定义
- 连接点(JointPoint) :与切入点匹配的执行点



### 11.3 AOP实现方式

#### 11.3.1 实现方式一：Spring接口

1. 编写新添加的功能，这个类要继承一些接口，如：MethodBeforeAdvice
2. applicationcontext.xml中配置aop（需要导入aop约束），`<aop:config>`
   1. 设置切入点`<aop:pointcut>`
   2. 执行环绕增强，相当于指明新添加的功能切入到哪个切入点



**新增加的功能类**

```java
public class Log implements MethodBeforeAdvice {
    //method：对象要执行的方法
    //args：参数列表
    //target：对象
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + target.getClass().getName() + "类的" + method.getName() + "方法");
    }
}
```

**配置aop，设置切入点等**

```xml
<!--    注册bean-->
<bean id="userservice" class="com.csii.jwh.service.UserServiceImpl"/>
<bean id="log" class="com.csii.jwh.log.Log"/>
<bean id="logafterreturn" class="com.csii.jwh.log.LogAfterReturn"/>

<!--    方式一：使用原生Spring API接口-->
<!--    配置aop：需要导入aop约束-->
<aop:config>
    <!--        设置切入点，固定写法expression="execution(* com.csii.jwh.service.*.*(..))"
                                          第一个*：任意返回值 第二个*：包下的任意一个class 第三个*：class下的任意method (..)：0个或多个参数-->
    <aop:pointcut id="pointcut" expression="execution(* com.csii.jwh.service.UserServiceImpl.*(..))"/>

    <!--        执行环绕增强-->
    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="logafterreturn" pointcut-ref="pointcut"/>
</aop:config>
```

**测试**

```java
@Test
public void test1(){
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationcontext.xml");
    //动态代理代理的是接口【重】
    UserService userService = context.getBean("userservice", UserService.class);
    userService.add();
    userService.delete();
    userService.select();
    userService.update();
}
```



#### 11.3.2 实现方式二：自定义类来实现AOP

```java
public class DiyPointCut {
    public void before() {
        System.out.println("=====Before=====");
    }
    public void after() {
        System.out.println("=====After=====");
    }
}
```

```xml
<!--    方式二：自定义-->
<!--    注册自定义的类-->
<bean id="diypointcut" class="com.csii.jwh.diy.DiyPointCut"/>
<aop:config>
    <!--        自定义切面，ref要引用的类-->
    <aop:aspect ref="diypointcut">
        <!--            切入点-->
        <aop:pointcut id="pointcut" expression="execution(* com.csii.jwh.service.*.*(..))"/>
        <!--            通知-->
        <aop:after method="after" pointcut-ref="pointcut"/>
        <aop:before method="before" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```



#### 11.3.3 实现方式三：注解

```xml
<!--    方式三-->
<bean id="annotationpointcut" class="com.csii.jwh.diy.AnnotationPointCut"/>
<!--    开启注解支持-->
<aop:aspectj-autoproxy/>
```

```java
@Aspect //标志这个类是一个切面
public class AnnotationPointCut {
    //切入点
    @After("execution(* com.csii.jwh.service.*.*(..))")
    public void after() {
        System.out.println("===after===");
    }

    @Before("execution(* com.csii.jwh.service.*.*(..))")
    public void before() {
        System.out.println("===before===");
    }
}
```



## 12 整合Mybatis

步骤:

1. 导入相关jar包
   - junit
   - mybatis
   - mysq|数据库
   - spring相关的
   - aop织入
   - mybatis-spring [new]
2. 编写配置文件
3. 测试
