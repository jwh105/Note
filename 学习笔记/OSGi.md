# OSGi（8.30）

- 开发工具：eclipse



## 什么是OSGi

OSGI技术可以满足：在不同的模块中做到彻底的分离，而不是逻辑意义上的分离，是物理上的分离，也就是说在运行部署之后都可以在不停止服务器的时候直接把某些模块拿下来，其他模块的功能也不受影响。

通过动态加载，也就是“热部署”来启动项目。就是说，我们这个项目把它放在Web容器中之后，我们可以将某些功能给它拿下来，而且拿下来的时候不会对其他模块造成影响。

osgi内嵌web服务器：jetty

jetty：jetty也是一个比较优秀的Web容器，在某些性能方面要比tomcat强大的多(如高并发，长连接)。而且它的整个结构比tomcat轻巧很多

## 项目构建

1. 在Eclipse的新建向导中选择“Plug-in Project”并点击“Next”按钮

2. 在对话框中输入： Project name（项目名称）

   ​							Target Platform（目标平台）an OSGiFramework->Standard（OSGi框架->标准）

3. Next，保持缺省值，勾选Generate an activator, a Java class that controls the plug -in' s life cycle

4. Next，看到模板对话框，选择“Hello OSGi Bundle”模板，点击“Finish”按钮完成该项目



在该插件项目中，生成两个关键的文件： 

1. Activator.java：激活器类，如果需要在Bundle启动或关闭时通知自身，可以新建一个实现BundleActivator接口的类，该类需要遵循以下规则：**该类必须有一个public的、不带参数的构造函数**，这样，OSGi框架就能调用该类的Class.newInstance()方法创建这个BundleActivator对象。
         启动一个Bundle，容器将调用Activator类的start()方法，我们可以在start()方法中执行一些资源初始化的操作，start()方法的唯一参数是一个BundleContext对象，Bundles可以通过该对象和 OSGi框架通讯。
         关闭一个Bundle，容器将调用Activator类中的stop()方法，我们可以在stop()方法中执行一些资源清理任务。

   ```java
   public class Activator implements BundleActivator {
   
   	@Override
   	public void start(BundleContext context) throws Exception {
   		System.out.println("Hello World!!");
   	}
   	
   	@Override
   	public void stop(BundleContext context) throws Exception {
   		System.out.println("Goodbye World!!");
   	}
   
   }
   ```

   

2. MANIFEST.MF：Bundle的部署描述文件，其格式和正常JAR文件包中的MANIFEST.MF文件相同，因此它由一系列的属性及这些属性对应的值组成。OSGi规范规定，您可以使用属性头向容器描述您的Bundle。

   ```
   Manifest-Version: 1.0
   Bundle-ManifestVersion: 2
   Bundle-Name: Osgi_demo
   Bundle-SymbolicName: osgi_demo
   Bundle-Version: 1.0.0.qualifier
   Bundle-Activator: osgi_demo.Activator
   Bundle-RequiredExecutionEnvironment: JavaSE-1.8
   Import-Package: org.osgi.framework;version="1.3.0"
   Automatic-Module-Name: osgi_demo
   ```

   

| 类别         | 命令        | 含义                                            |
| ------------ | ----------- | ----------------------------------------------- |
| 控制框架     | `launch`    | 启动框架                                        |
|              | `shutdown`  | 停止框架                                        |
|              | `close`     | 关闭、退出框架                                  |
|              | `exit`      | 立即退出，相当于 System.exit                    |
|              | `init`      | 卸载所有 bundle（前提是已经 shutdown）          |
|              | `setprop`   | 设置属性，在运行时进行                          |
| 控制 bundle  | `Install`   | 安装                                            |
|              | `uninstall` | 卸载                                            |
|              | `Start`     | 启动                                            |
|              | `Stop`      | 停止                                            |
|              | `Refresh`   | 刷新                                            |
|              | `Update`    | 更新                                            |
| 展示状态     | `Status`    | 展示安装的 bundle 和注册的服务                  |
|              | `ss`        | 展示所有 bundle 的简单状态                      |
|              | `Services`  | 展示注册服务的详细信息                          |
|              | `Packages`  | 展示导入、导出包的状态                          |
|              | `Bundles`   | 展示所有已经安装的 bundles 的状态               |
|              | `Headers`   | 展示 bundles 的头信息，即 MANIFEST.MF 中的内容  |
|              | `Log`       | 展示 LOG 入口信息                               |
| 其它         | `Exec`      | 在另外一个进程中执行一个命令（阻塞状态）        |
|              | `Fork`      | 和 EXEC 不同的是不会引起阻塞                    |
|              | `Gc`        | 促使垃圾回收                                    |
|              | `Getprop`   | 得到属性，或者某个属性                          |
| 控制启动级别 | `Sl`        | 得到某个 bundle 或者整个框架的 start level 信息 |
|              | `Setfwsl`   | 设置框架的 start level                          |
|              | `Setbsl`    | 设置 bundle 的 start level                      |
|              | `setibsl`   | 设置初始化 bundle 的 start level                |

### 注册服务

注册服务，OSGi 框架提供了两种注册方式，都是通过 `BundleContext` 类实现的：

1. `registerService(String,Object,Dictionary)` 注册服务对象 `object` 到接口名 `String` 下，可以携带一个属性字典`Dictionary`；
2. `registerService(String[],Object,Dictionary)` 注册服务对象 `object` 到接口名数组 `String[]` 下，可以携带一个属性字典 `Dictionary`，即一个服务对象可以按照多个接口名字注册，因为类可以实现多个接口；

### 查询服务

查询服务：同样，OSGi 框架提供了两种查询服务的引用 `ServiceReference` 的方法：

1. `getServiceReference(String)`：根据接口的名字得到服务的引用；
2. `getServiceReferences(String,String)`：根据接口名和另外一个过滤器名字对应的过滤器得到服务的引用；
