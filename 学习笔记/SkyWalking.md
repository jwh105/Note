# SkyWalking

[Apache SkyWalking 官网](http://skywalking.apache.org/)

[SkyWalking 官方demo](http://demo.skywalking.apache.org/)

[SkyWalking 下载](http://skywalking.apache.org/downloads/)

[SkyWalking 文档](https://skywalking.apache.org/docs/main/v8.4.0/readme/)

[SkyWalking 中文文档](https://skyapm.github.io/document-cn-translation-of-skywalking/)

[Elasticsearch下载](https://www.elastic.co/cn/downloads/past-releases#elasticsearch)

## 快速入门

### 介绍

skywalking是分布式系统的应用 程序性能监视工具，专为微服务、云原生架构和基于容器（Docker、K8s、Mesos）架构而设计。它是一款优秀的 APM（Application Performance Management）工具，包括了分布式追踪、性能指标分析、应用和服务依赖分析等。

### 功能特性

1. 多种监控手段，可以通过语言探针和service mesh获得监控的数据；
2. 支持多种语言自动探针，包括 Java，.NET Core 和 Node.JS；
3. 轻量高效，无需大数据平台和大量的服务器资源；
4. 模块化，UI、存储、集群管理都有多种机制可选；
5. 支持告警；
6. 优秀的可视化解决方案；

### 环境搭建部署

![image-20211101144628559](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20211101144628559.png)

* skywalking agent和业务系统绑定在一起，负责收集各种监控数据
* Skywalking oapservice是负责处理监控数据的，比如接受skywalking agent的监控数据，并存储在数据库中; 接受skywalking webapp的前端请求，从数据库查询数据，并返回数据给前端。Skywalking oapservice通常以集群的形式存在。
* skywalking webapp，前端界面，用于展示数据。
* 用于存储监控数据的数据库，比如mysql、elasticsearch等。

### 目录结构

●webapp: UI前端(web 监控页面)的jar包和配置文件，==在此处yml中修改SkyWalking的访问端口==；
●oap-libs: 后台应用的jar包,以及它的依赖jar包，里边有一个server- starter-*.jar就是启动程序;
●config: 启动后台应用程序的配置文件，是使用的各种配置
●bin:各种启动脚本，一般使用脚本startup.*来启动web页面和对应的后台应用;
	●oapService.*: 默认使用的后台程序的启动脚本; (使用的是默认模式启动, 还支持其他模式，各模式区别见启动模式)
	●oapServicelnit.*: 使用init模式启动;在此模式下，OAP服务器启动以执行初始化工作,然后退出
	●oapServiceNolnit.*: 使用no init模式启动;在此模式下，OAP服务器不进行初始化。
	●webappService.*: UI前端的启动脚本;
	●startup.*:组合脚本,同时启动oapService.*: webappService.*脚本;
●agent:
	●skywalking-agentjar. 代理服务jar包
	●config: 代理服务启动时使用的配置文件
	●plugins:包含多个插件，代理服务启动时会加载改目录下的所有插件(实际是各种jar包)
	●optional-plugins: 可选插件,当需要支持某种功能时，比如SpringCloud Gateway,则需要把对应的jar包拷贝到plugins目录下;

## 环境搭建

1. Linux下，创建目录，并将elasticsearch和skywalking安装包放进去，注意两者版本对应

   `mkdir /usr/local/skywalking`

   ![img](https://img-blog.csdnimg.cn/20200327162619441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW50eWl0,size_16,color_FFFFFF,t_70)

   ​                                                      Elasticsearch与JDK版本对应关系

2. 首先安装elasticsearch

   `tar -zxvf elasticsearch-7.15.1-linux-x86_64.tar.gz`

     修改Linux系统的限制配置，将文件创建数修改为65536个。 

    1. ==修改系统中允许应用最多创建多少文件等的限制权限。Linux默认来说，一般限制应用最多 创建的文件是65535个。但是ES至少需要65536的文件创建数的权限。== 
    2. ==修改系统中允许用户启动的进程开启多少个线程。默认的Linux限制root用户开启的进程可 以开启任意数量的线程，其他用户开启的进程可以开启1024个线程。必须修改限制数为 4096+。因为ES至少需要4096的线程池预备。==

    ```shell
    vi /etc/security/limits.conf 
    
    #新增如下内容在limits.conf文件中
     es soft nofile 65536
     es hard nofile 65536
     es soft nproc 4096
     es hard nproc 4096
    ```

    修改系统控制权限，ElasticSearch需要开辟一个65536字节以上空间的虚拟内存。Linux默认不允许任 何用户和应用程序直接开辟这么大的虚拟内存。

   ```shell
   vi /etc/sysctl.conf
   #新增如下内容在sysctl.conf文件中，当前用户拥有的内存权限大小
   vm.max_map_count=262144
   #让系统控制权限配置生效
   sysctl -p
   ```

   建一个用户，用于ElasticSearch启动。

   ==ES在5.x版本之后，强制要求在linux中不能使用root用户启动ES进程。所以必须使用其他用户启动ES进程才可以。==

   ```shell
   #创建用户
   useradd es
   #修改上述用户的密码
   passwd es
   #修改elasicsearch目录的拥有者
   chown -R es elasticsearch-6.4.0
   ```

3. 使用es用户启动elasticsearch

   ```shell
   #切换用户
   su es
   #到ElasticSearch的bin目录下
   cd bin/
   #后台启动，回车后不会有任何信息提示
   ./elasticsearch -d
   ```

   测试：

   ```shell
   curl http://localhost:9200
   ```

   显示如下信息则说明启动成功

   ```shell
   {
     "name" : "t1J3djT",
     "cluster_name" : "elasticsearch",
     "cluster_uuid" : "mHpibWWWQhaq-krBJyHkBA",
     "version" : {
       "number" : "6.8.10",
       "build_flavor" : "default",
       "build_type" : "tar",
       "build_hash" : "537cb22",
       "build_date" : "2020-05-28T14:47:19.882936Z",
       "build_snapshot" : false,
       "lucene_version" : "7.7.3",
       "minimum_wire_compatibility_version" : "5.6.0",
       "minimum_index_compatibility_version" : "5.0.0"
     },
     "tagline" : "You Know, for Search"
   }
   ```

   

4. 切换回root用户，安装skywalking,解压后进入config/application.yml选择使用什么数据库

   ```yml
   storage:
     selector: ${SW_STORAGE:elasticsearch}
     elasticsearch:
       nameSpace: ${SW_NAMESPACE:"es_cluster"}
    ......
   #老版本需要开关注释
   ```


5. 进入webapp/webapp.yml修改UI配置

   ```yml
   #默认启动端口为8080
   server:
     port: 8848
   collector:
     path: /graphql
     ribbon:
       ReadTimeout: 10000
       #OAP服务，如果有多个用逗号隔开
       listOfServers: 127.0.0.1:12800
   ```

6. 启动skywalking，bin目录下

   ```shell
   # 启动OAP程序
   oapService.sh
   # 启动UI程序
   webappService.sh
   
   ## 或统一进行启动
   start.sh
   ```

## SkyWalking基础

### agent的使用

Agent探针支持 JDK 1.6 - 12的版本，Agent探针所有的文件在Skywalking的agent文件夹下。文件目录如下：

```
+-- agent
	+-- activations
		apm-toolkit-log4j-1.x-activation.jar
		apm-toolkit-log4j-2.x-activation.jar
		apm-toolkit-logback-1.x-activation.jar
		...
	//配置文件
	+-- config
		agent.config
	//组件的所有插件
	+-- plugins
		apm-dubbo-plugin.jar
		apm-feign-default-http-9.x.jar
		apm-httpClient-4.x-plugin.jar
		.....
	//可选插件(会影响整体性能，要启用需转移至plugins目录)
	+-- optional-plugins
		apm-gson-2.x-plugin.jar
		.....
	+-- bootstrap-plugins
		jdk-http-plugin.jar
		.....
	+-- logs
	skywalking-agent.jar
```

修改agent探针中的应用名：

```shell
cd /usr/local/skywalking/apache-skywalking-apm-bin/agent/config
vi agent.config

# 配置中找到
# The service name in UI
agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}
# Your_ApplicationName改成想要的名字，如：skywalking_tomcat
```

### Linux下Tomcat

解压Tomcat，启动和关闭命令，bin目录下：

```shell
./startup.sh
./shutdown.sh
```

要引入SkyWalking，在启动前需修改bin/catalina.sh中的参数，在文件顶部添加（指向SkyWalking的agent目录下的jar包）：

```shell
TALINA_OPTS="$CATALINA_OPTS -javaagent:/usr/local/skywalking/apache-skywalking-apm-bin/agent/skywalking-agent.jar"; export CATALINA_OPTS
```

在conf/server.xml中修改访问端口

### Windows下Tomcat

Windows下只需要修改 tomcat目录/bin/catalina.bat 文件的第一行为（同样指向SkyWalking的agent目录下的jar包）：

```shell
set "CATALINA_OPTS=-javaagent:/path/to/skywalking-agent/skywalking-agent.jar"
```

### Springboot中使用

为防止与Tomcat发生冲突，将agent复制了一份

```shell
cp -r agent agent_boot
```

修改agent_boot/agent.config

```shell
# 配置中找到
# The service name in UI
agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}
# Your_ApplicationName改成想要的名字，如：skywalking_tomcat
```

使用命令启动SpringBoot项目（jar）

```shell
java -javaagent:/usr/local/skywalking/apache-skywalking-apm-bin/agent_boot/skywalking-agent.jar -Dserver.port=8082 -jar skywalking_springboot.jar &
```

> 使用jar包启动的项目如果需要集成skywalking，需要添加-javaagent参数，参数值为agent的jar 包位置。 
>
> -Dserver.port参数用于指定端口号，防止与tomcat冲突。 
>
> 末尾添加 & 后台运行模式启动Spring Boot项目。

