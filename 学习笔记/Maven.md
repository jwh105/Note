# Maven（8.25）

## 1 简介

Maven主要服务于基于java平台的项目构建，依赖管理和项目信息管理。

Maven是一个跨平台的项目管理工具，它是使用java开发的，它要依赖于jdk1.6及以上.

Maven主要有两大功能：管理依赖、项目构建。依赖指的就是jar包。

### 1.1 四大特性

#### 1.1.1 依赖管理系统

​		Maven为Java世界引入了一个新的依赖管理系统jar包管理，升级时修改配置文件即可。在Java中, 可以用groupld、artifactld、version组成的Coordination (坐标) 唯一标识一个依赖。

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13.1</version>
    <scope>test</scope>
</dependency>
```

**坐标属性的理解**

​		Maven坐标为各种组件引入了秩序，任何-一个组件都必须明确定义自己的坐标。

**groupld**

​		定义当前Maven项目隶属的实际项目-公司名称。(jar包所在仓库路径) 由于Maven中模块的概念， 因此一
个实际项目往往会被划分为很多模块。比如spring是一 个实际项目 ,其对应的Maven模块会有很多，如spring-
core,spring-webmvc等。

**artifactld**

​		该元素定义实际项目中的一个Maven模块-项目名，推荐的做法是使用实际项目 名称作为artifactld的前缀。
比如: spring-bean, spring-webmvc等。

**version**

​		该元素定义Maven项目当前所处的版本。



#### 1.1.2 多模块构建

#### 1.1.3 一致的项目结构

在以前，不同的开发平台创建的项目，目录结构不同，在Maven中，一致了

#### 1.1.4 一致的构建模型以及插件机制



## 2 Maven的安装配置和目录结构

### 2.1 Maven安装配置

#### 2.1.1 JDK版本

​		必须为JDK1.7及以上

#### 2.1.2 下载Maven

​		[Maven – Download Apache Maven](http://maven.apache.org/download.cgi)

#### 2.1.3 配置环境变量

​		解压后把Maven的根目录配置到系统环境变量中MAVEN_ HOME,将bin目录配置到path变量中

​		测试：mvn -v



### 2.2 目录结构

* src
  * -main

    > -java        —— 存放项目的.java文件
    >  -resources   —— 存放项目资源文件，如spring, hibernate配置文件

  * -test

    > -java        ——存放所有测试.java文件，如JUnit测试类
    >  -resources   —— 测试资源文件

- target —— 目标文件输出位置例如.class、.jar、.war文件
- pom.xml ——maven项目核心配置文件

### 2.3 配置

**全局配置**：在maven安装目录的conf里面有一个settings.xml文件，这个文件就是maven的全局配置文件。默认在系统的用户目录下的m2/repository中，该目录是本地仓库的目录。

**用户配置**：用户配置文件的地址：~/.m2/settings.xml，该文件默认是没有，需要将全局配置文件拷贝一份到该目录下。 建议：改成自己的项目专有仓库。



## 3 常用命令

Maven的命令要在pom.xml所在目录中去执行

- mvn compile  -- 编译
- mvn clean  -- 清除命令，清除已经编译好的class文件，具体说清除的是target整个目录
- mvn test -- 测试命令，该命令会将test目录中的源码进行编译
- mvn package -- 打包
- mvn install --安装命令，会将打好的包，安装到本地仓库
- mvn exec:java -Dexec.mainClass=“com.xxxx.demo.Hello” --执行main方法

还可以有组合命令：

- mvn clean compile
- mvn clean install
- ....



## IDEA编辑器集成Maven

设置时选择 ==新项目的设置==，这样否则可能是仅针对当前项目的设置

![image-20210825160526467](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210825160526467.png)

setting.xml修改为自己配置过的（阿里云+本地仓库）
