# SpringMVC

## 一、SpringMVC简介（9.13）

### 1、什么是MVC

MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分
M: Model, 模型层,指工程中的JavaBean,作用是处理数据
JavaBean分为两类:

- 类称为实体类Bean:专门存储业务数据的，如Student.- User等
- 一类称为业务处理Bean: 指Service或Dao对象，专门]用于处理业务逻辑和数据访问。

V: View, 视图层，指工程中的html或jsp等页面,作用是与用户进行交互,展示数据
C: Controller,控制层，指工程中的servlet,作用是接收请求和响应浏览器
MVC的工作流程:
用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Mode|层处理请
求，处理完毕将结果返回到Controller, Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终
响应给浏览器

2、SpringMVC的特点

- Spring家族原生产品，与IOC容器等基础设施无缝对接

- **基于原生的Servlet**,通过了功能强大的**前端控制器DispatcherServlet**,对请求和响应进行统一处理

- 表述层各细分领域需要解决的问题全方位覆盖,提供全面解决方案

- **代码清新简洁**，大幅度提升开发效率

- 内部组件化程度高，可插拔式组件即插即用，想要什么功能配置相应组件即可

- **性能卓著**，尤其适合现代大型、超大型互联网项目要求



## 二、创建SpringMVC项目

### 1、创建Maven工程

设置打包方式为war包，Maven依赖如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.csii.jwh.mvc</groupId>
    <artifactId>springmvc-demo1</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--    打包方式为war包      -->
    <packaging>war</packaging>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--        springMVC      -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.12.RELEASE</version>
        </dependency>
        
        <!--        日志      -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        
        <!--        ServletAPI      -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        
        <!--        spring5和thymeleaf整合包       -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.12.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```



### 2、配置web.xml

==位于webapp/WEB-INF下==

如果创建使用Maven的webapp模板，则会自动生成。否则手动创建：

项目结构 --> 模块 --> 新增web模块 --> 添加部署描述符，指定web.xml位置

![image-20210913113749979](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210913113749979.png)

#### a.默认配置方式

此配置作用下，SpringMVC的配置文件默认位于WEB-INF下，默认名称为`<servlet-name> -servlet.xml`,例如，以下配置所对应SpringMVC的配置文件位于WEB-INF下，文件名为springMVC-servlet.xml

```xml
<!--配置SpringMVC的前端控制器，对浏览器发送的请求统一处理-->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<!--  
设置springMVC的核心控制器所能处理的请求的请求路径
/所匹配的请求可以是/login或.html或.js或.css方式的请求路径
但是/不能匹配.jsp请求路径的请求
  -->
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--所能处理的请求路径-->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### b.扩展配置方式

**在默认方式的基础上**，可通过init-param标签设置SpringMVC配置文件的位置和名称，通过load-on-startup标签设置 SpringMVC前端控制器DispatcherServlet的初始化时间

```xml
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置SpringMVC配置文件的位置和名称-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--将前端控制器DispatcherServlet的初始化提前到服务器启动时-->
    <load-on-startup>1</load-on-startup>
</servlet>
```



### 3、创建请求控制器

由于前端控制器对浏览器发送的请求进行了统一的处理， 但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即<u>请求控制器</u>

请求控制器中每一个控制请求的方法称作<u>控制器方法</u>

SpringMVC的控制器是由POJO（普通的类）担任，使用@Controller标记，将其标记为控制层组件，才能识别

**springMVC配置文件**

==属于Spring配置文件，位于Resources目录下==

```xml
<context:component-scan base-package="com.csii.jwh.controller"/>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">

                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>

                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8"/>
                </bean>
            </property>
        </bean>
    </property>
</bean>
```



### 4、测试：实现对首页的访问以及到其他页面的跳转

> 处理一个请求：找到**控制器**，通过请求映射，来**匹配到当前请求**，找到其**控制器方法**，**返回视图名称**，通过SpringMVC配置文件中配置的**视图解析器**，加上前缀和后缀，得到页面

![image-20210913163841335](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210913163841335.png)

控制器：

```java
// @RequestMapping注解：处理请求和控制器方法之间的映射关系
// @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径
@Controller
public class HelloController {

    // "/" --> WEB-INF/templates/index.html
    @RequestMapping("/")
    public String index() {
        return "index";
    }
    
    @RequestMapping("/target")
    public  String toTarget(){
        return "target";
    }
}
```

index.html：

```html
<!DOCTYPE html>
<html lang="en"
      xmln:th="http://www.thymeleaf.org"
      xmlns:xmln="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<a th:href="@{/target}">跳转至target</a>
</body>
</html>
```

> 注：
>
> 1. html头必须声明：`xmln:th="http://www.thymeleaf.org"`
>
> 2. 浏览器解析访问地址时，**绝对路径**的“/”代表localhost:8080，没有带**上下文路径**（即下图所示，Tomcat中配置），此时，使用**thymeleaf**进行解析，会自动帮我们加上上下文路径，固定书写格式：`th:href="@{绝对路径}"`
>
>    th:href="@{/target}" ==》 localhost:8080/springmvc/target
>
>    href="/target" ==》 localhost:8080/target
>
>    ![image-20210913164707964](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210913164707964.png)
>
> 3. 控制器中RequestMapping中的值与请求地址**必须一致**（“/target”）
>
> 4. 配置Tomcat服务器时，添加Artifact为![image-20210914114656635](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210914114656635.png)，如果没有此选项，则说明在pom.xml中未配置packaging为war(打包方式)，如果服务器配置有问题，体现为**404**



### 5、总结

浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面



## 三、@RequestMapping注解

### 1、@RequestMapping注解的功能

@RequestMapping注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。

### 2、@RequestMapping注解的位置

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```java
@Controller
@RequestMapping("/test")
public class RequestMappingController {
	//此时请求映射所映射的请求的请求路径为：/test/testRequestMapping
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping(){
        return "success";
    }
}
```

### 3、@RequestMapping注解的value属性

@RequestMapping注解的value属性通过请求的请求地址匹配请求映射

@RequestMapping注解的value属性是一个**字符串数组**，表示该请求映射能**够匹配多个请求地址**所对应的请求

@RequestMapping注解的value属性**必须设置**，至少通过请求地址匹配请求映射

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"}
)
public String testRequestMapping(){
    return "success";
}
```

### 4、@RequestMapping注解的method属性

@RequestMapping注解的method属性通过请求的请求方式（get或post）匹配请求映射

@RequestMapping注解的method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配多种请求方式的请求

若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported

如果没有method属性，则不以method为条件，get、post都可

```xml
<a th:href="@{/test}">测试@RequestMapping的method属性-->post</a><br>
<form th:action="@{/test}" method="post">
    <input type="submit">
</form>
```

```java
@RequestMapping(
    value = {"/testRequestMapping", "/test"},
    //支持GET和POST
    method = {RequestMethod.GET, RequestMethod.POST}
)
public String testRequestMapping(){
    return "success";
}
```

> 注：
>
> 1. 对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解
>
>    处理get请求的映射-->@GetMapping
>
>    处理post请求的映射-->@PostMapping
>
>    处理put请求的映射-->@PutMapping
>
>    处理delete请求的映射-->@DeleteMapping
>
> 2. 常用的请求方式有get，post，put，delete
>
>    但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理
>
>    若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter，涉及到RESTful

### 5、SpringMVC支持ant风格的路径

RequestMapping的value中：

?：表示任意的单个字符

*：表示任意的0个或多个字符

\**：表示任意的一层或多层目录

注意：在使用\**时，只能使用/**/xxx的方式

### 8、SpringMVC支持路径中的占位符（重点）

原始方式：/deleteUser?id=1

rest方式：/deleteUser/1

SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的@RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据，在通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参

```xml
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
@RequestMapping(value = {"/target/{username}","/sss/{uname}"}) //target能成,sss不能成
public String toTarget(@PathVariable("username") String uname) {
    System.out.println("username:" + uname);
    return "target";
}
```



# 四、SpringMVC获取请求参数

### 1、通过ServletAPI获取

将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请求的请求报文的对象

```java
@RequestMapping("/testParam")
public String testParam(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username:"+username+",password:"+password);
    return "success";
}
```

### 2、通过控制器方法的形参获取请求参数（9.15）

```java
@RequestMapping(value = "/target")
public String toTarget(String username,int password,String[] chooses) {
    System.out.println("username:" + username);
    System.out.println("password:" + password);
    System.out.println("chooses:" + Arrays.toString(chooses));
    return "target";
}
```

```xml
<form th:action="@{/target}" method="post">
    <p>username:<label>
        <input type="text" width="50px" name="username">
    </label></p>
    <p>password:<label>
        <input type="password" width="50px" name="password">
    </label></p>
    <input type="checkbox" id="vehicle1" name="chooses" value="Bike">
    <label for="vehicle1"> I have a bike</label><br>
    <input type="checkbox" id="vehicle2" name="chooses" value="Car">
    <label for="vehicle2"> I have a car</label><br>
    <input type="checkbox" id="vehicle3" name="chooses" value="Boat">
    <label for="vehicle3"> I have a boat</label><br>
    <input type="submit">
</form>
```

> 注：
>
> 若请求所传输的请求参数中有**多个同名的请求参数**，此时可以在控制器方法的形参中设置**字符串数组**或者**字符串类型的形参**接收此请求参数
>
> 若使用字符串数组类型的形参，此参数的数组中包含了每一个数据
>
> 若使用字符串类型的形参，此参数的值为每个数据中间使用**逗号拼接**的结果

### 3、@RequestParam

@RequestParam是将请求参数和控制器方法的形参创建映射关系

```java
@RequestMapping(value = "/target")
public String toTarget(
    @RequestParam("uname") String username,  //html的uname映射为username
    int password) {
    System.out.println("username:" + username);
    System.out.println("password:" + password);
    return "target";
}
```

@RequestParam注解一共有三个属性：

value：指定为形参赋值的请求参数的参数名

required：设置是否必须传输此请求参数，默认值为true

若设置为true时，则当前请求必须传输value所指定的请求参数，**若没有传输该请求参数**，且没有设置defaultValue属性，则页面**报错400**：Required String parameter 'xxx' is not present；若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

### 4、通过实体类获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实体类中的属性名一致，那么请求参数就会为此属性赋值

```html
<form th:action="@{/testpojo}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    性别：<input type="radio" name="sex" value="男">男<input type="radio" name="sex" value="女">女<br>
    年龄：<input type="text" name="age"><br>
    邮箱：<input type="text" name="email"><br>
    <input type="submit">
</form>
```

```java
@RequestMapping("/testpojo")
public String testPOJO(User user){
    System.out.println(user);
    return "success";
}
//最终结果-->User{id=null, username='张三', password='123', age=23, sex='男', email='123@qq.com'}
```

### 5、解决获取请求参数的乱码问题

解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器CharacterEncodingFilter，但是必须在web.xml中进行注册

```xml
<!--配置springMVC的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 注：
>
> SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效



## 五、RestFul风格

### RestFul 风格

**概念**

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**功能**

资源：互联网所有的事物都可以被抽象为资源

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。

分别对应 添加、 删除、修改、查询。

**传统方式操作资源**  ：通过不同的参数来实现不同的效果！方法单一，post 和 get

​	http://127.0.0.1/item/queryItem.action?id=1 查询,GET

​	http://127.0.0.1/item/saveItem.action 新增,POST

​	http://127.0.0.1/item/updateItem.action 更新,POST

​	http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST

**使用RESTful操作资源** ：可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！

​	http://127.0.0.1/item/1 查询,GET

​	http://127.0.0.1/item 新增,POST

​	http://127.0.0.1/item 更新,PUT

​	http://127.0.0.1/item/1 删除,DELETE



### 测试

在Spring MVC中可以使用  @PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上。

```java
@Controller
public class RestFulController {
   //映射访问路径
   @RequestMapping("/commit/{p1}/{p2}")
   public String index(@PathVariable int p1, @PathVariable int p2, Model model){
       int result = p1+p2;
       //Spring MVC会自动实例化一个Model对象用于向视图中传值
       model.addAttribute("msg", "结果："+result);
       //返回视图位置
       return "test";
  }
}
```



### 使用路径变量的好处

* 使路径变得更加简洁；

* 获得参数更加方便，框架会自动进行类型转换。

* 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败。


SpringMVC 的 @RequestMapping 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH。

**所有的地址栏请求默认都会是 HTTP GET 类型的。**

方法级别的注解变体有如下几个：组合注解

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

@GetMapping 是一个组合注解，平时使用的会比较多！

它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。



## 六、重定向和转发
