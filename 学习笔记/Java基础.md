# Java基础

## 数据类型

### 基本数据类型

|   类型名称   | 关键字  | 占用内存 |                  取值范围                  |
| :----------: | :-----: | :------: | :----------------------------------------: |
|    字节型    |  byte   |  1 字节  |                  -128~127                  |
|    短整型    |  short  |  2 字节  |                -32768~32767                |
|     整型     |   int   |  4 字节  |           -2147483648~2147483647           |
|    长整型    |  long   |  8 字节  | -9223372036854775808L~9223372036854775807L |
| 单精度浮点型 |  float  |  4 字节  |        +/-3.4E+38F（6~7 个有效位）         |
| 双精度浮点型 | double  |  8 字节  |         +/-1.8E+308 (15 个有效位）         |
|    字符型    |  char   |  2 字节  |               ISO 单一字符集               |
|    布尔型    | boolean |  1 字节  |               true 或 false                |

### 引用数据类型

除开基本数据类型都为引用数据类型，例如：<u>Class类</u>、<u>Interface接口</u>以及<u>数组</u> [ ]

### 基本数据类型和引用数据类型的区别

|                         基本数据类型                         |                         引用数据类型                         |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                     不牵扯到内存分配问题                     |                      需要开发者开辟空间                      |
| 各自拥有默认值，整型为0，浮点型为0.0，字符型为‘\u0000’，布尔型为false |                         默认值为null                         |
|                      内容存放在**栈**中                      | 内容存放在**堆**中，而<u>栈</u>中存放的是内容在堆的<u>地址</u>，通过变量地址可以找到变量的具体内容 |
|                            值传递                            |                     引用传递（传递地址）                     |

**关于‘\u0000’：**‘\u0000’是char类型的最小值，它转换成整数时为0，’\u0000’与’ ‘是不相等的

**关于存储位置的区别：**

![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/1537987-20181117143815305-1391925376.png)

**关于传递方式的区别：**

```java
//参数按值传递
public class Main{
   public static void main(String[] args){
       int msg = 100;
       System.out.println("调用方法前msg的值：\n"+ msg);    //100
       fun(msg);
       System.out.println("调用方法后msg的值：\n"+ msg);    //100
   }
   public static void fun(int temp){
       temp = 0;
   }
}
```

![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/1537987-20181117152204524-2019844954.png)

```java
//参数引用传递，将实际参数的地址传递给方法
class Book{
    String name;
    double price;
    
    public Book(String name,double price){
        this.name = name;
        this.price = price;
    }
    public void getInfo(){
        System.out.println("图书名称："+ name + "，价格：" + price);
    }

    public void setPrice(double price){
        this.price = price;
    }
}

public class Main{
   public static void main(String[] args){
       Book book = new Book("Java开发指南",66.6);
       book.getInfo();  //图书名称：Java开发指南，价格：66.6
       fun(book);
       book.getInfo();  //图书名称：Java开发指南，价格：99.9
   }

   public static void fun(Book temp){
       temp.setPrice(99.9);
   }
}
```

![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/1537987-20181117160912759-229459932.png)





## 数组

**我的理解：**<u>相同数据类型</u>并按一定<u>顺序排列</u>的集合，使用同一个名字命名，通过编号进行管理

**特点：**

1. 数组是有序排列的
2. 数组的元素可以是基本数据类型，也可以是引用数据类型

3. 创建数组会开辟一整块连续的空间
4. 数组长度一旦确定就不能被修改

**声明和初始化：**

```java
int[] id = new int[]{1,2,3,4,5};//静态初始化
int[] id = new int[5];//动态初始化
```

### 数组在内存中的情况

**内存的简化结构：**

![image-20210806101428887](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210806101428887.png)

```java
int[] arr = new int[]{1,2,3};
String[] arr1= new String[4];
arr1[1]=“刘德华";
arr1[2]=张学友;
arr1= new String[3];
```

![](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210806100953554.png)

​             栈                                                                          堆



### 数据结构（概述）

1. 数据与数据之间的逻辑关系：集合、一对一、一对多、多对多

2. 数据的存储结构：

   线性表：顺序表（如数组）、链表、栈、队列；

   树形结构：二叉树；

   图形结构；

3. 算法（程序=数据结构+算法）：

   算法的5大特征：输入、输出、有穷性、确定性（每一步有确定含义，没有二义）、可行性（每一步清楚且可行、能让用户用纸笔计算出来）

- 排序算法：
  - 选择排序：直接选择排序、<u>堆排序</u>
  - 交换排序：**冒泡排序**、**快速排序**
  - 插入排序：直接插入排序、折半插入排序、Shell排序
  - <u>归并排序</u>
  - 桶式排序
  - 基数排序

* 搜索算法：线性查找、二分查找

  ......



## 面向对象（8.10）

### JVM内存结构

![image-20210810102501853](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210810102501853.png)

### 对象的内存解析

![image-20210810103630471](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210810103630471.png)



### OOP--继承

1. 减少代码冗余，提高封装性
2. 便于功能扩展
3. 为多态的使用提供前提



### OOP--多态

一个事物的多种表现形态，**可以提高代码的*通用性*，也可以让代码变得*更规范***

 对象的多态性：*<u>父类的引用指向子类的对象</u>*

```java
Person p1 = new Man();//左父右子，声明一个父类变量，赋予一个子类对象
```

多态的使用：虚拟方法调用

​	**有了对象的多态性以后，在编译期，只能调用父类中声明的方法，在运行期，则执行在子类中重写的父类方法**

​    **对象的多态性只适用于方法，不适用于属性**

```java
p1.eat(); //调用不报错，运行时则运行Man中重写的eat()
p1.earnMoney(); //调用不了，因为这个方法只存在于子类，Person类中没有这个方法
p1.age;  //调用的是父类Person的age，因为多态性只适用于方法，不适用于属性
```

**多态的前提：**

1. 类的继承关系
2. 方法的重写

```java
package com.csii.jwh;

public class AnimalTest {
    public static void main(String[] args) {
        AnimalTest animalTest = new AnimalTest();
        //此处即为多态的体现，如果没有多态性，那传参只能是Animal，要想传Dog和Cat，就不得不将func()重载
        animalTest.func(new Dog());
        animalTest.func(new Cat());
    }

    void func(Animal animal){
        animal.eat();
        animal.shout();
    }
}

class Animal {
    public void eat() {
        System.out.println("动物：吃");
    }
    public void shout() {
        System.out.println("动物：叫");
    }
}

class Dog extends Animal {
    public void eat() {
        System.out.println("狗吃骨头");
    }
    public void shout() {
        System.out.println("汪汪汪");
    }
}

class Cat extends Animal {
    public void eat() {
        System.out.println("猫吃鱼干");
    }
    public void shout() {
        System.out.println("喵喵喵");
    }
}
```

再比如：

```java
class Driver{
    public void doData(Connection conn){
        //conn = new MySQLConnection();
        //conn = new OracleConnection();
        //在Connection类中去规定操作数据应该有哪些步骤，统一规范，各个数据库厂商根据这个规范来设计自己的代码
        //更规范的步骤去处理数据
        conn.method1();
        conn.method3();
        conn.method2();
    }
}
```

如果没有多态性的话，要操作MySQL就要写MySQL的方法，操作Oracle就要写Oracle的方法，很麻烦

#### 虚拟方法调用

​		子类中定义了与父类同名同参数的方法,在多态情况下，将此时父类的方法称为虚拟方法，父类根据赋给它的不同子类对象，动态调用属于子类的该方法。这样的方法调用在编译期是无法确定的。

**多态性是运行时行为**（不到运行的时候不知道是什么对象的方法）

#### 向下转型

在多态中，内存中实际上加载了子类的属性和方法，但由于变量声明为父类，导致在编译时，只能调用父类中声明的属性和方法，子类特有的则不能调用

```java
//向下转型：使用强制转换//使用强转时，可能出现ClassCastException的异常Person p1 = new Man();Man p2 = (Man)p1;
```



### static关键字

static可以修饰 属性、方法、代码块和内部类

static修饰过后，就变成了公用的了



### 抽象类和抽象方法

abstract关键字，可以用来修饰<u>类</u>和<u>方法</u>

 抽象类：

1. 抽象类不能被实例化

2. 抽象类中一定有<u>构造方法</u>，便于子类实例化调用
3. 在开发中都会给抽象类提供子类，通过子类实例化来完成相关操作

 抽象方法：

1. 抽象方法只有方法的声明，没有方法体

   ```java
   public abstract void func();
   ```

2. 有抽象方法一定是抽象类

3. 只有子类<u>重写</u>父类的<u>所有抽象方法</u>时，子类才可实例化，否则这个子类仍是一个抽象类

### 接口（8.11）

interface关键字

1. JDK7以前，只能定义全局常量和抽象方法
   - 全局常量：public static final，书写时可省略
   - 抽象方法：public abstract
2. JDK8：除了定义全局常量和抽象方法之外，还可以定义静态方法（static）和默认方法（default）
3. 接口中不能定义构造器，结构不可以被实例化
4. 类实现接口，如果实现类覆盖了接口的所有抽象方法，则此实现类就可以实例化，否则这个实现类仍是一个抽象类

### 异常

异常是程序执行中发生的不正常的情况（开发过程中的语法错误和逻辑错误不属于异常）

- Error：java虚拟机无法解决的严重问题

  ```java
  public static void main(String[] args) {
      //栈溢出：java.lang.StackOverflowError
      main(args);
      //堆溢出：java.lang.OutOfMemoryError
      Integer[] integers = new Integer[1024 * 1024 * 1024];
  }
  ```

  Exception：因编程错误或偶然外在因素导致的一般性问题，可通过代码进行处理

  ![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/5982616-88bc2c5c2d6ee0b9.png)

Exception 异常主要分为两类

- 一类是 IOException（I/O 输入输出异常），其中 IOException 及其子类异常又被称作**受检异常**（在编译期间就必须处理的异常）

- 另一类是 RuntimeException（运行时异常），RuntimeException 被称作**非受检异常**

  **常见运行时异常：**

  ![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/5982616-ad31cf5b8e2cbb36.png)

  **常见输入输出异常：**

  ![img](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/5982616-97fef7461bed33c6.png)

#### 异常处理

- 所有异常都必须是 Throwable 的子类。
- 如果希望写一个检查性异常类，则需要继承 Exception 类。
- 如果希望写一个运行时异常类，则需要继承 RuntimeException 类。

##### 异常处理方式一

1. 抛，throw，一旦抛出异常类的对象后，其后的代码不再执行

2. 抓，try...catch...finally，可以多重捕获

 ```java
try{    
    // 程序代码}catch(异常类型1 异常的变量名1){
    // 程序代码}catch(异常类型2 异常的变量名2){
    // 程序代码}catch(异常类型2 异常的变量名2){ 
    // 程序代码}  finally{ 
    //无论如何，一定会执行的代码
}
 ```

```java
try {    
    //String str = new String("abc");  
    //char ch = str.charAt(3);  
    Object object = new String("abc");  
    int i = (int)object;
} catch (StringIndexOutOfBoundsException e) { 
    System.out.println("出现了字符串下标越界异常！");
} catch (ClassCastException e) { 
    System.out.println("出现了类型转换异常！");
}
System.out.println("1111111");
//catch到过后，继续执行之后的代码
```

catch中，通常使用的两种处理方式，获取异常信息：

1. String getMessage()

   System.out.println(e.getMessage());

2. printStackTrace()

   e.printStackTrace();

**finally的使用：**

像数据库连接、输入输出流、网络编程Socket等资源，JVM不会自动回收的，需要手动的进行资源释放，将资源释放的代码放在finally中。

##### 异常处理方式二

throws + 异常类型

try-catch-finally 真正将异常处理了

throws的方式只是将异常抛给了方法的调用者，并没有真正被处理

<u>*子类重写抛出的异常不能比父类的更大，如果父类没有抛异常，子类也不能抛*</u>

#### 自定义异常类

1. 继承RuntimeException或Exception
2. 提供全局常量 serialVersionUID
3. 提供重载的构造器

```java
public class MyException extends RuntimeException{  
    static final long serialversionUID = -7034897193246939L;  
    public MyException(){  
    }    
    public MyException(String msg){   
        super(msg);  
    }
}
//使用if(id > 0){....}else{    throw new MyException("不能输入负数！！");}
```



## 多线程

### 基本概念

**程序：**为完成特定任务、用某种语言编写的一组<u>指令的集合</u>。可理解为一段<u>静态的</u>代码，静态的对象

**进程：**程序运行起来了就是一个正在运行的进程，是<u>动态的</u>，有它自身的产生、存在和消亡的过程——生命周期

*进程作为资源分配的单位，系统在运行时会为每个进程分配不同的内存区域*

**线程：**<u>线程包含在进程中，是程序内部的一条执行路径</u>。

* 若一个进程同一时间并行执行多个线程，就是支持多线程的
* 线程作为调度和执行的单位，每个线程拥有独立的运行栈和程序计数器，线程切换的开销小
* 一个进程中的多个线程*共享*相同的内存单元→它们从同一堆中分配对象,可以访问相同的变量和对象。这就使得线程间通信更简便、高效。但多个线程操作共享的系统资源可能就会带来安全的隐患。

**并行和并发：**

- 并行：多个CPU同时执行多个任务（多个人同时做不同的事）
- 并发：一个CPU同时执行多个任务（秒杀）

### 线程的创建和使用（8.12）

#### 创建方式一：继承Thread类

1. 创建Thread类的子类
2. 重写Thread的run()
3. 创建Thread类的子类对象
4. 通过子类对象调用start()

例：

```java
public class ThreadTest extends Thread {  
    @Override 
    public void run() {  
        super.run();      
        for (int i = 0; i < 100; i++) {  
            System.out.println(Thread.currentThread().getName() + ":" + i);   
        }
    }   
    public static void main(String[] args) {   
        ThreadTest t1 = new ThreadTest();    
        ThreadTest t2 = new ThreadTest();   
        t1.start();   
        for (int i = 0; i < 100; i++) {      
            System.out.println(Thread.currentThread().getName() + ":" + i);   
        }       
        t2.start();    
        //必须用start()来执行，如果调用run()，只是单线程    }}
```

#### 创建方式二：实现Runnable接口

1. 创建一个实现了Runnable接口的类
2. 实现类实现Runnable中的抽象方法: run()
3. 创建实现类的对象
4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
5. 通过Thread类的对象调用start()

例：

```java
public class ThreadDemo {  
    public static void main(String[] args) {   
        Thread3 t3 = new Thread3();//创建实现类对象  
        Thread thread3 = new Thread(t3);//将实现类对象作为参数传入Thread类构造方法      
        thread3.start();     
        Thread.currentThread().setName("主线程");    
        for (int i = 0; i < 100; i++) {      
            System.out.println(Thread.currentThread().getName() + "__" + i); 
        }  
    }
}
class Thread3 implements Runnable{//创建实现类实现Runnable接口  
    @Override  
    public void run() {
        //重写Runnable接口中的run()抽象方法    
        for (int i = 0; i < 50; i++) {   
            System.out.println(Thread.currentThread().getName() + "__" + i);     
        }  
    }
}
```

**比较两种创建线程的方式**

开发中：优先选择<u>实现Runnable接口</u>的方式
原因：

1. 实现的方式没有类的单继承性的局限性

2. 实现的方式更适合来处理多个线程有共享数据的情况

联系：public class Thread implements Runnable
相同点：两种方式都需要重写run(),将线程要执行的逻辑声明在run()中。

#### 创建方式三：实现Callable接口（8.13）

与Runnable相比：

- 相比run()，可以有返回值
- 方法可以抛出异常
- 支持泛型的返回值
- 需要借助FutureTask类，比如获取返回结果

**具体步骤：**

1. 创建一个类实现Callable接口
2. 重写call()
3. 实例化Callable接口的实现类
4. 将实现类的对象传入FutureTask的构造器中，创建FutureTask的对象
5. 将FutureTask对象传入thread构造器中，创建Thread对象，并start()
6. 如果想要获取call()中的返回值，则调用FutureTask的get()

```java
package com.csii.jwh;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class ThreadTest2 {  
    public static void main(String[] args) { 
        //3. 实例化Callable接口的实现类   
        NumThread numThread = new NumThread();  
        //4. 将实现类的对象传入FutnreTask的构造器中，创建FutureTask的对象  
        FutureTask futureTask = new FutureTask(numThread);  
        //5. 将FutureTask对象传入thread构造器中，创建Thread对象，并start()   
        new Thread(futureTask).start();   
        //6. 如果想要获取call()中的返回值，则调用FutureTask的get()    
        try {        
            System.out.println(futureTask.get());   
        } catch (InterruptedException e) {     
            e.printStackTrace();   
        } catch (ExecutionException e) { 
            e.printStackTrace();    
        }  
    }
}
//1. 创建一个类实现Callable接口
class NumThread implements Callable {  
    //2. 重写call() 
    @Override  
    public Object call() throws Exception {     
        int sum = 0;     
        for (int i = 1; i <= 100; i++) {    
            System.out.println(i);   
            sum += i;   
        }      
        return sum;  
    }
}
```



#### 创建方式四：使用线程池（8.13）

**思路：**提前创建多个线程，放在线程池，使用时直接获取，用完了放回池中，可以避免频繁创建、销毁，实现重复利用

**好处：**

- 提高响应速度
- 降低资源消耗
- 便于线程管理

JDK 5.0起提供了线程池相关API: ExecutorService和Executors
**ExecutorService：**真正的线程池接口。常见子类ThreadPoolExecutor

- void execute(Runnable command):执行任务命令，没有返回值，一般用来执行
  Runnable
- <T> Future<T> submit(Callable<T> task):执行任务，有返回值，一般又来执行
  Callable
- void shutdown() :关闭连接池

**Executors：**工具类、线程池的工厂类，用于创建并返回不同类型的线程池

- Executors.newCachedThreadPool(): 创建一个可根据需要创建新线程的线程池
- Executors.newFixedThreadPool(n); 创建一个可重用固定线程数的线程池
- Executors.newSingleThreadExecutor(): 创建一 个只有一个线程的线程池
- Executors.newScheduledThreadPool(n):创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

**具体步骤：**

1. 创建指定线程数的线程池
2. 执行指定操作，需要提供Runnable接口或Callable接口的实现类对象
3. 关闭连接池

```java
package com.csii.jwh;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
/**
* 使用线程池创建多线程
*/
public class ThreadPoolTest { 
    public static void main(String[] args) { 
        //1. 创建指定线程数的线程池
        ExecutorService service = Executors.newFixedThreadPool(10); 
        //2. 执行指定操作，需要提供Runnable接口或Callable接口的实现类对象
        service.submit(new NumThread1());//适用于Callable，能接收返回值 
        service.execute(new NumThread1());//适用于Runnable
        service.submit(new NumThread2());//submit同样可以传Runnable进去，但一般不这样用
        //3. 关闭连接池
        service.shutdown();
    }
}
class NumThread1 implements Runnable {
    @Override
    public void run() { 
        for (int i = 0; i < 101; i++) {
            if (i % 2 == 0) { 
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}
class NumThread2 implements Callable { 
    @Override
    public Object call() throws Exception { 
        for (int i = 0; i < 101; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
        return 1;
    }
}
```



### 多线程中常用方法

1. start()：启动当前线程，调用线程的run()

2. run()：通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中

3. currentThread()：是一个静态方法，返回<u>当前代码的线程</u>

4. getName()：获取当前线程的名字

5. setName()：设置当前线程的名字（也可以在子线程类中写一个构造方法MyThread(String name)，内部执行父类构造方法super(name)来设置名字）

6. yield()：释放当前CPU的执行权（但有可能再分配执行权给当前这个线程）

   ​	*当i%3==0时，yield()*

![image-20210812104503133](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210812104503133.png)

7. join()：相当于插队，在A线程中调用B线程的join()，则A进程进入阻塞状态，B线程启动，等B执行完毕了，A线程结束阻塞状态

   ​	*当主线程的i==20时，分线程111执行join()，在此之前，分线程<u>必须先start()</u>*

![image-20210812144651799](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210812144651799.png)

​				*分线程111运行完毕，主线程继续*

![image-20210812144806240](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210812144806240.png)

8. sleep(long milltime)：让当前线程“睡眠”指定毫秒数，在这期间，线程是阻塞状态
9. isAlive()：返回值为boolean，判断当前线程是否存活

### 线程的优先级

三个常量：

- MAX_PRIORITY：10
- MIN_PRIORITY：1
- NORM_PRIORITY：5   *默认优先级*

常用方法：

- getPriority()：返回线程的优先值
- setPriority(int newPriority)：改变线程的优先级

说明：

- 子线程创建时继承父线程的优先级
- 低优先级只是获得调度的<u>概率低</u>，并不是一定在高优先级线程后才被调度

### 线程的生命周期

JDK中用Thread.State类定义了线程的几种状态

- **新建:**当一个Thread类或其子类的对象被声明并创建时，新生的线程对象处于新建状态

- **就绪:**处于新建状态的线程被start()后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件，只是没分配到CPU资源
- **运行:**当就绪的线程被调度并获得CPU资源时,便进入运行状态，run()方法定义了线程的操作和功能
- **阻塞:**在某种特殊情况下，被人为挂起或执行输入输出操作时，让出CPU并临时中止自己的执行，进入阻塞状态
- **死亡:**线程完成了它的全部工作或线程被提前强制性地中止或出现异常导致结束

![image-20210812173410423](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210812173410423.png)

### 线程安全

例子：创建三个窗口卖票，总票数为100张.使用实现Runnable接口的方式
1.问题:卖票过程中，出现了重票（买同一张票）、错票（买不存在的票）-->出现了线程的安全问题
2.问题出现的原因:当某个线程操作车票的过程中，尚未操作完成时，其他线程参与进来，也操作车票
3.如何解决:当一个线程a在操作ticket的时候，其他线程不能参与进来。直到线程a操作完ticket时，
线程才可以开始操作ticket。这种情况即使线程a出现了阻塞，也不能被改变。

<u>在Java中，我们通过同步机制，来解决线程的安全问题</u>

线程不安全代码：

```java
package com.csii.jwh;public class TicketTest {  
    public static void main(String[] args) { 
        Windows windows = new Windows();    
        Thread t1 = new Thread(windows);  
        Thread t2 = new Thread(windows);    
        Thread t3 = new Thread(windows);   
        t1.setName("分线程1_");      
        t2.setName("分线程2_");       
        t3.setName("分线程3_");       
        t1.start();       
        t2.start();      
        t3.start();  
    }
}
class Windows implements Runnable { 
    private int ticket = 100;   
    @Override   
    public void run() {   
        while (true) {      
            if (ticket > 0) {  
                try {      
                    Thread.sleep(100); 
                } catch (InterruptedException e) { 
                    e.printStackTrace();         
                }              
                System.out.println(Thread.currentThread().getName() + ticket); 
                ticket--;           
            } else {    
                break;   
            }      
        } 
    }
}
```

重票![image-20210813093921976](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210813093921976.png)													错票![image-20210813093950908](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210813093950908.png)

#### 方式一：同步代码块（8.13）

```java
synchronize(同步监视器){	//需要被同步的代码}
```

说明：

1. 操作<u>共享数据</u>的代码，就是需要被同步的代码

2. 共享数据：多个线程共同操作的数据（如例子中的ticket）

3. 同步监视器（又称 锁）：<u>*任何一个类的对象，都可以充当锁*</u>

   多个线程共用同一个锁

   一个线程拿到锁，其他线程就只能等待，相当于又变成单线程了

```java
class Windows implements Runnable {  
    private int ticket = 100; 
    Object obj = new Object();
    // 随便实例化一个对象，必须放在run()外边 
    @Override   
    public void run() { 
        while (true) {  
            //正确的方式     
            //synchronized(Windows.class) 类也是对象 
            synchronized(obj){
                // 将对象传入synchronized()充当锁 
                //错误的方式synchronized(this)，this代表t1,t2,t3三个对象 （慎用）
                if (ticket > 0) {
                    //操作共享数据的代码需要被进行同步
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) { 
                        e.printStackTrace();  
                    }           
                    System.out.println(Thread.currentThread().getName() + ticket); 
                    ticket--;  
                } else {    
                    break;   
                }         
            }     
        }  
    }
}
```

*<u>继承方式</u>的多线程用<u>同步代码块</u>来解决线程安全：*

类本身来充当同步监视器

```java
class Windows2 extends Thread { 
    private static int ticket = 100; 
    Object obj = new Object();
    @Override  
    public void run() {    
        while (true) {   
            synchronized (Windows2.class) {   
                show();         
            }     
        }  
    }  
    //  public static synchronized void show() { //同步方法    public void show() {        if (ticket > 0) {            try {                Thread.sleep(100);            } catch (InterruptedException e) {                e.printStackTrace();            }            System.out.println(Thread.currentThread().getName() + ticket);            ticket--;        }    }}
```



#### 方拾二：同步方法

说明：

1. 同步方法仍然涉及到同步监视器，只是不需要我们自己声明

2. 非静态的同步方法，同步监视器是this

   静态的同步方法，同步监视器是当前<u>类本身</u>

如果操作共享数据的代码，完整的在一个方法中，不妨将此方法声明为同步的(synchronized)，例：

```java
class Windows implements Runnable {    private int ticket = 100;    @Override    public void run() {        while (true) {            show();        }    }    public synchronized void show() { //同步方法,synchronized        if (ticket > 0) {            try {                Thread.sleep(100);            } catch (InterruptedException e) {                e.printStackTrace();            }            System.out.println(Thread.currentThread().getName() + ticket);            ticket--;        }    }}
```

*<u>继承方式</u>的多线程用<u>同步方法</u>来解决线程安全：*

除了声明部分加synchronized，再加上static

```java
class Windows2 extends Thread {    private static int ticket = 100;    @Override    public void run() {        while (true) {//            synchronized (Windows2.class) {            show();//            }        }    }    public static synchronized void show() { //同步方法        if (ticket > 0) {            try {                Thread.sleep(100);            } catch (InterruptedException e) {                e.printStackTrace();            }            System.out.println(Thread.currentThread().getName() + ticket);            ticket--;        }    }}
```

#### 死锁问题

不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了死锁，出现死锁不会出现异常，所有线程均阻塞

解决方法：

- 专门的算法和原则
- 尽量减少同步资源的定义
- 尽量避免嵌套同步

```java
package com.csii.jwh;public class DeadLockTest {    public static void main(String[] args) {        StringBuffer s1 = new StringBuffer();        StringBuffer s2 = new StringBuffer();        new Thread() {            @Override            public void run() {                synchronized (s1) {                    try {                        sleep(100); //sleep增加线程死锁的几率                    } catch (InterruptedException e) {                        e.printStackTrace();                    }                    s1.append('1');                    s2.append('a');                    synchronized (s2) {                        s1.append('2');                        s2.append('b');                    }                }                System.out.println("s1:" + s1);                System.out.println("s2:" + s2);            }        }.start();        new Thread(new Runnable(){            @Override            public void run() {                synchronized (s2) {                    s1.append('3');                    s2.append('c');                    synchronized (s1) {                        s1.append('4');                        s2.append('d');                    }                }                System.out.println("s1:" + s1);                System.out.println("s2:" + s2);            }        }).start();    }}
```



## 枚举类和注解

### 枚举类的使用

1. 枚举类的理解:类的对象只有<u>有限个</u>，<u>确定的</u>。我们称此类为枚举类
2. 当需要定义一组常量时，强烈建议使用枚举类
3. 如果枚举类中只有一个对象,则可以作为单例模式的实现方式。

**如何定义：**

- JDK5.0前，自定义枚举类
- JDK5.0后，enum关键字定义枚举类

#### 自定义枚举类

1. 声明类的属性为：private final，私有且不能再更改（有限确定）
2. <u>私有化</u>类的构造器，并给属性赋值
3. 提供当前枚举类的多个对象：public static final
4. 提供getter方法，toString方法（可选）

```java
package com.csii.jwh;
public class SeasonTest {
    public static void main(String[] args) {
        System.out.println(Season.PRING);
    }
}
class Season {
    //1. 声明类的属性为：private final，私有且不能再更改（有限确定）
    private final String seasonName;
    private final String seasonDesc;
    //2. 私有化类的构造器，并给属性赋值
    private Season(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;     
        this.seasonDesc = seasonDesc;   
    }
    //4. 提供setter方法，toString方法 
    public String getSeasonName() { 
        return seasonName;  
    }  
    public String getSeasonDesc() {  
        return seasonDesc;  
    }   
    @Override  
    public String toString() {  
        return "Season{" +    
            "seasonName='" + seasonName + '\'' +       
            ", seasonDesc='" + seasonDesc + '\'' +      
            '}';  
    }
    //3. 提供当前枚举类的多个对象：public static final
    public static final Season PRING = new Season("春天", "春暖花开"); 
    public static final Season SUMMER = new Season("夏天", "烈日炎炎");  
    public static final Season AUTUMN = new Season("秋田", "秋高气爽"); 
    public static final Season WINTER = new Season("冬天", "白雪皑皑");}
```

#### enum定义枚举类

1. 提供当前枚举类的对象，多个对象之间用“,”隔开，格式：对象名称(属性1，属性2，...，属性n)

2. 声明对象的属性，private final修饰

3. 私有化构造器，并给对象属性赋值

4. getter方法，toString方法（可选）

   （在enum中，toString已经被重写，只返回对象名称）

```java
/**
* enum
*/
enum Season1 {
    SPRING("春天", "春暖花开"),
    SUMMER("夏天", "烈日炎炎"),
    AUTUMN("秋田", "秋高气爽"),
    WINTER("冬天", "白雪皑皑");

    private final String seasonName;
    private final String seasonDesc;

    private Season1(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
}
```



### 注解Annotation（8.16）

JDK5.0新增内容

#### 自定义注解

参照@SuppressWarning

- 定义新的Annotation类型使用<u>@interface</u>关键字
- 自定义注解自动继承了<u>java.lang.annotation.Annotation</u>接口
- Annotation的成员变量在Annotation定义中以无参数方法的形式来声明。其方法名和返回值定义了该成员的名字和类型。我们称为配置参数。类型只能是<u>八种基本数据类型</u>、<u>String类型</u>、 <u>Class类型</u>、<u>enum</u>类型、<u>Annotation</u>类型以上所有类型的<u>数组</u>。
- 可以在定义Annotation的成员变量时为其指定初始值，指定成员变量的初始值可使用<u>default关键字</u>
- 如果只有一个参数成员，建议使用<u>参数名为value</u>
- 如果定义的注解含有配置参数，那么使用时必须指定参数值，除非它有默认值。格式是“参数名=参数值”，如果只有一个参数成员，且名称为value,可以省略”value=”
- 没有成员定义的Annotation称为<u>标记</u>；包含成员变量的Annotation 称为元数据Annotation

**步骤：**

1. 注解声明为：@interface
2. 内部定义成员，通常使用value表示（可以没有）
3. 可以指定默认值，使用default定义（可以没有）



## 集合

| 序号 | 接口描述                                                     |
| :--- | :----------------------------------------------------------- |
| 1    | Collection 接口 Collection 是最基本的集合接口，一个 Collection 代表一组 Object，即 Collection 的元素, Java不提供直接继承自Collection的类，只提供继承于的子接口(如List和set)。Collection 接口存储一组不唯一，无序的对象。 |
| 2    | List 接口 List接口是一个有序的 Collection，使用此接口能够精确的控制每个元素插入的位置，能够通过索引(元素在List中位置，类似于数组的下标)来访问List中的元素，第一个元素的索引为 0，而且允许有相同的元素。List 接口存储一组不唯一，有序（插入顺序）的对象。 |
| 3    | Set Set 具有与 Collection 完全一样的接口，只是行为上不同，Set 不保存重复的元素。Set 接口存储一组唯一，无序的对象。 |
| 4    | SortedSet 继承于Set保存有序的集合。                          |
| 5    | Map Map 接口存储一组键值对象，提供key（键）到value（值）的映射。 |
| 6    | Map.Entry 描述在一个Map中的一个元素（键/值对）。是一个 Map 的内部接口。 |
| 7    | SortedMap 继承于 Map，使 Key 保持在升序排列。                |
| 8    | Enumeration 这是一个传统的接口和定义的方法，通过它可以枚举（一次获得一个）对象集合中的元素。这个传统接口已被迭代器取代。 |

**java集合框架**

![image-20210816115321246](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210816115321246.png)

Collection中常用方法

1. contains(Object obj)：判断集合中是否存在obj

```java
Collection list1 = new ArrayList();list1.add("123");list1.add(new String("Hello"));list1.add(new Person("jwh",22));System.out.println(list1.contains("123"));      //trueSystem.out.println(list1.contains(new String("Hello")));     //true，比较的是内容System.out.println(list1.contains(new Person("jwh",22)));    //false，Person中没有重写equals()
```

Object中也没有重写equals方法，为重写的equals方法都是使用的==进行比较

**向Collection接口的实现类的对象中添加数据obj时，要求obj所在类必须要<u>重写equals()</u>**

2. containsAll(Collection coll1)：判断形参coll1中的所有元素是否都存在于当前集合中

3. remove(Object obj)：从当前集合中移除obj
4. removeAll(Collection coll1): 从当前集合中移除coll1中所有的元素
5. retainAll(Collection coll1)：获取当前集合和coll1集合的交集，并返回
6. equals(Object obj)：当前集合和形参集合的元素相同，则返回true
7. iterator()：返回Iterator接口的实例，用于遍历集合元素。Iterator接口中两个常用方法 hasNext() 和 next()

```java
Iterator iterator = list1.iterator();while (iterator.hasNext()) {	System.out.println(iterator.next());}
```

### Collection子接口：List接口

- 元素有序，可重复
- List容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素
- 常用实现类有：ArrayList（线程不安全）、LinkedList和Vector（线程安全）

**常用方法：**

void add(int index, object ele): 在index位置插入ele元素
boolean addAll(int index, Collection eles): 从index位置开始将eles中的所有元素添加进来
object get(int index): 获取指定index位置的元素
int indexOf(Object obj): 返回obj在集合中首次出现的位置
int lastIndexOf(object obj): 返回obj在当前集合中末次出现的位置
object remove(int lindex):移除指定index位置的元素，并返回此元素
Object set(int index, object ele): 设置指定index位置的元素为ele
List sublist(int fromIndex, int toIndex): 返回从fromIndex到toIndex位置的子集合

### Collection子接口：Set接口

- 元素无序，不可重复

- 判断两个对象是否相同使用的是equals()

- 常用实现类：

  Set集合（存储无序，不可重复的数据）

  ​	**HashSet**（线程不安全，可以存储null）

  ​		**LinkedHashSet**（HashSet的子类，遍历内部数据时可按照添加顺序进行遍历）

  ​	**TreeSet**（可以按照添加对象的指定属性，进行排序）

  无序性：不代表随机，是存储数据的底层数组中并不是按数组索引的顺序添加，而是根据数据的哈希值决定

  不可重复性：相同元素只能添加一个。对象必须重写equals()和hashCode()以保证不可重复性

```java
//去重，使用LinkedHashSet，保证其输出的顺序
LinkedHashSet set2 = new LinkedHashSet();
Scanner scanner = new Scanner(System.in);
System.out.println("请输入：");
String s = scanner.nextLine();
for (int i = 0; i < s.length(); i++) {
    System.out.println(s.charAt(i)); 
    set2.add(s.charAt(i));
}
System.out.println("ok~~");
iterator = set2.iterator();
while (iterator.hasNext()) {
    System.out.print(iterator.next());
}
```

**关于TreeSet（添加进去的数据会自动排序）：**

1. 向TreeSet中添加的数据，要求是同一个类的对象

2. 两种排序方式，<u>自然排序</u>（实现comparable接口，<u>重写compareTo方法</u>） 和 <u>定制排序</u>（实例化Comparator，<u>重写其compare方法</u>）

3. 自然排序中，比较两个对象是否相同的标准为: compareTo() 返回0则相同。不再是equals()

4. 定制排序中，比较两个对象是否相同的标准为: compare() 返回0则相同。不再是equals()

   <u>*...有区别？？*</u>

**添加的对象需要实现comparable接口，并重写其compareTo()**

TreeSetTest类

```java
package com.csii.jwh.collection;
import com.csii.jwh.Person;
import java.util.Iterator;
import java.util.TreeSet;
public class TreeSetTest{ 
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet();
        treeSet.add(new Person("jwh", 22));
        treeSet.add(new Person("lsx", 41));
        treeSet.add(new Person("wyf", 25));
        treeSet.add(new Person("wlh", 16));
        treeSet.add(new Person("wlh", 18));
        Iterator iterator = treeSet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next()); 
        }
    }
}
```

Person实体类

```java
package com.csii.jwh;
import java.util.Objects;
public class Person implements Comparable {
    private String name;
    private int age;
    /*
    一些get/set方法，以及重写的toString(),hashCode(),equals()
    */ 
    //重写compareTo方法
    //根据姓名排序，从小到大
    @Override
    public int compareTo(Object o) { 
        if (o instanceof Person) {
            Person o1 = (Person) o; 
            //从大到小排序
            //return -this.name.compareTo(o1.name);
            //从小到大排序
            int a = this.name.compareTo(o1.name);
            //进行姓名的比较，如果没有对年龄进行比较，则认为两个对象是相同的
            if (a != 0) { 
                return a;
            } else {
                //姓名相同则比较年龄，都相同则认为两个对象是相同的
                //从小到大排序
                return Integer.compare(this.age, o1.age);
            }
        } else { 
            throw new RuntimeException("类型错了！！！");
        }
    }
}
```

以上是自然排序，下面是定制排序

```java
@Test
public void test2() {
    //实例化Comparator，重写其compare方法
    Comparator com = new Comparator() {
        @Override
        public int compare(Object o1, Object o2) { 
            if (o1 instanceof Person && o2 instanceof Person) { 
                Person o11 = (Person) o1;
                Person o21 = (Person) o2;
                //return Integer.compare(o11.getAge(), o21.getAge());  
                return o11.getName().compareTo(o21.getName()); 
            } else { 
                throw new RuntimeException("类型又错啦！！！"); 
            }  
        }   
    };    
    TreeSet treeSet = new TreeSet(com);
    treeSet.add(new Person("jwh", 22));
    treeSet.add(new Person("lsx", 41)); 
    treeSet.add(new Person("wyf", 25));  
    treeSet.add(new Person("wlh", 16));    
    treeSet.add(new Person("wlh", 18));    
    Iterator iterator = treeSet.iterator();   
    while (iterator.hasNext()) {          
        System.out.println(iterator.next() + "*******"); 
    } 
}
```



### Map接口（8.17）

**Map:**双列数据，存储key-value对的数据
		**HashMap:** 作为Map的主要实现类；线程不安全的，效率高；可以存储null的key和value
				**LinkedHashMap:**保证在遍历map元素时，可以按照添加的顺序实现遍历。
					对于频繁的遍历操作，LinkedHashMap执行效率高于HashMap。
		**TreeMap:**保证按照添加的key-value对进行排序，实现排序遍历。此时考虑key的自然排序或定制排序
			底层使用红黑树
		**Hashtable:**作为古老的实现类；线程安全的，效率低；不能存储null的key和value
				**properties：**常用来处理配置文件。key和value都是String类型



**HashMap的底层：**  jdk1.7及以前：数组+链表

​								 jdk1.8：数组+链表+红黑树

**Map结构的理解：**

1. Map中的key:无序的、不可重复的，使用<u>Set 存储所有的key</u>

   ​	-->**key的所在类，需要重写equals()和hashCode()**

2. Map中的value:无序的、可重复的，使用<u>Collection存储所有的value</u>

   ​	-->**value的所在类，需要重写equals()**

3. 一个键值对: key-value构成了一个Entry对象

4. Map中的entry:无序的、不可重复的，使用Set存储所有的entry

**Map的添加操作不是add()，而是put()和putAll()**

**Map常用方法：**

添加、删除、修改操作:
object put(0bject key, object value): 将指定key-value添加到(或修改)当前map对象中
void putAll(Map m): 将m中的所有key-value对存放到当前map中
object remove(0bject key):移除指定key的key-value对，并返回value
void clear(): 清空当前map中的所有数据
元素查询的操作:
object get(0bject key):获取指定key对应的value
boolean containsKey(object key):是否包含指定的key
boolean containsValue(0bject value): 是否包含指定的value
int size(): 返回map中key-value对的个数
boolean isEmpty(): 判断当前map是否为空
boolean equals(Object obj):判断当前map和参数对象obj是否相等
元视图操作的方法:
Set keySet(): 返回所有key构成的Set集合
Collection values(): 返回所有value构成的Collection集合
Set entrySet(): 返回所有key-value对构成的Set集合

```java
public class TreeMapTest {
    //自然排序，按照key排序，key必须是同一对象。Person实现Comparable接口，重写compareTo方法
    @Test
    public void test1() {
        TreeMap treeMap = new TreeMap(); 
        treeMap.put(new Person("zs", 23), 11); 
        treeMap.put(new Person("ls", 22), 42); 
        treeMap.put(new Person("jwh", 16), 35); 
        treeMap.put(new Person("hdh", 33), 51); 
        System.out.println("自然排序");
        System.out.println(treeMap);
    }
    //定制排序，按照key排序，key必须是同一对象。实例化Comparetor并重写其compare方法
    @Test
    public void test2() {
        TreeMap treeMap = new TreeMap(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) { 
                if (o1 instanceof Person && o2 instanceof Person) {
                    Person person1 = (Person) o1;
                    Person person2 = (Person) o2;
                    return Integer.compare(person1.getAge(), person2.getAge()); 
                }
                throw new RuntimeException("类型又又错了！！！"); 
            }
        });
        treeMap.put(new Person("zs", 23), 11);
        treeMap.put(new Person("ls", 22), 42); 
        treeMap.put(new Person("jwh", 16), 35);
        treeMap.put(new Person("hdh", 33), 51);
        System.out.println("定制排序");
        System.out.println(treeMap);
    }
}
```



## 泛型（Generic）

集合中没有使用泛型可能出现的问题：

1. 类型不安全（可以存放多种类型而不报错）
2. 在强转时可能会出现ClassCastException

```java
for(Object score : list){    int studScore = (Integer)score;}
```

泛型的使用：

1. JDK1.5新特性，集合接口或集合类都修改为带泛型的结构
2. 在实例化集合类时，可以指明具体泛型类型
3. 泛型类型必须是类，基本数据类型都用包装类代替

*以下是有了泛型过后的自然排序和定制排序：*

```java
package com.csii.jwh.collection;import org.junit.Test;import java.util.Comparator;import java.util.TreeMap;public class Generic {    @Test    public void test1() {        TreeMap<Person, Integer> hashMap = new TreeMap<>();    	//见下方Person实体类        hashMap.put(new Person("zs", 23), 15);        hashMap.put(new Person("ls", 66), 18);        hashMap.put(new Person("djj", 15), 23);        System.out.println(hashMap);    }    @Test    public void test2() {        TreeMap<Person, Integer> hashMap = new TreeMap<>(new Comparator<Person>() {            @Override            public int compare(Person o1, Person o2) {                return Integer.compare(o1.getAge(), o2.getAge());            }        });        hashMap.put(new Person("zs", 23), 15);        hashMap.put(new Person("ls", 51), 18);        hashMap.put(new Person("djj", 15), 23);        System.out.println(hashMap);    }}
```

```java
//使用了泛型，Person声明需要实现接口也要设定泛型//public class Person implements Comparable<Person>    @Override    public int compareTo(Person o) {        return -this.name.compareTo(o.name);    }
```

### 自定义泛型类或接口

```java
package com.csii.jwh.collection;import java.util.Objects;public class Order<T> {    private String name;    private int age;    T orderT;    /*一些get/set方法*/    public T getOrderT() {        return orderT;    }    public void setOrderT(T orderT) {        this.orderT = orderT;    }    public Order(String name, int age, T orderT) {        this.name = name;        this.age = age;        this.orderT = orderT;    }}
```

```java
@Testpublic void test01() {    Order<Integer> order = new Order<>("jwh", 22, 2333);    System.out.println(order);    Person djj = new Person("djj", 26);    Order<Person> order1 = new Order<>("jwh", 22, djj);    System.out.println(order1);}
```

**注意事项：**

> 1. 泛型类可能有多个参数，此时应将多个参数一 起放在尖括号内。比如:<E1,E2,E3>
>
> 2. 泛型类的构造器如下: public GenericClass(){}。
>
> 3. 实例化后，操作原来泛型位置的结构必须与指定的泛型类型一致。
>
> 4. 泛型不同的引用不能相互赋值。
>
>    *尽管在编译时ArrayList<String>和ArrayList<Integer>是两种类型，但是，在运行时只有一个ArrayList被加载到JVM中。*
>
> 5. 泛型如果不指定，将被擦除，泛型对应的类型均按照Object处理，但不等价于Object。
>
> 6. 如果泛型类是一个接口或抽象类，则不可创建泛型类的对象。
>
> 7. jdk1.7,泛型的简化操作: ArrayList<Fruit> flist = new ArrayList<>();
>
> 8. 泛型的指定中不能使用基本数据类型，可以使用包装类替换。
>
> 9. 静态方法中不能使用泛型

```java
class Father<T1, T2> {//子类不保留父类的泛型// 1)没有类型擦除class Son<A, B> extends Father{//等价于class Son extends Father<0bject , 0bject>{}// 2)具体类型class Son2<A, B> extends Father<Integer, String> {}//子类保留父类的泛型// 1)全部保留class Son3<T1, T2, A, B> extends Father<T1, T2> {// 2)部分保留class Son4<T2, A, B> extends Father<Integer, T2> {}
```

### 泛型方法

```java
//泛型方法public <E> List<E> copyFromArrayToList(E[] arr) {    ArrayList<E> list = new ArrayList<>();    for (E e : arr) {        list.add(e);    }    return list;}
```

*泛型方法可以声明为静态的。原因:泛型参数是在调用方法时确定的。并非在实例化类时确定。*

### 通配符的使用

```java
//通配符： ?public void print(List<?> list) {    Iterator<?> iterator = list.iterator();    while (iterator.hasNext()) {        Object o = iterator.next();        System.out.println(o);    }}@Testpublic void test3() {    List<Object> list1 = new LinkedList<>();    ArrayList<String> list2 = new ArrayList<>();    list2.add("asd");    list2.add("bcxb");    list2.add("cdg");    list1.add("asd");    list1.add(123);    list1.add('s');        List<?> list = null;  //不能向内部添加数据，list.add()非法,list.add(null)除外        list = list2;    Object o = list.get(0);//不能往里写，但可以往外读    System.out.println(o + "---");        print(list);    print(list2);    print(list1);}
```



## IO流（8.18）

### File类的使用

| 构造方法                                                     |
| ------------------------------------------------------------ |
| `File(String pathname)`  通过将给定的路径名字符串转换为抽象路径名来创建新的 `File`实例。 |
| `File(String parent,  String child)`  从父路径名字符串和子路径名字符串创建新的 `File`实例。 |
| `File(File parent, String child)`  从父抽象路径名和子路径名字符串创建新的 `File`实例。 |
| `File(URI uri)`  通过将给定的 `file:` URI转换为抽象路径名来创建新的 `File`实例。 |

```java
@Testpublic void test() {    //构造器1    File file = new File("file1.txt");    System.out.println(file);    File file1 = new File("E:\\workspace\\idea\\ge.txt");    System.out.println(file1);    //构造器2    File file2 = new File("E:\\workspace\\idea\\", "he");    System.out.println(file2);    //构造器3    File file3 = new File(file2, "he.txt");    System.out.println(file3);}
```

**常用方法**

> public String getAbsolutePath(): 获取绝对路径
> public String getPath() :获取路径
> public String getName() :获取名称
> public String getParent(): 获取上层文件目录路径。若无，返回null
> public Long Length() :获取文件长度(即:字节数)。不能获取目录的长度。
> public Long LastModified() :获取最后一次的修改时间，毫秒值
>
> public String[] list() :获取指定目录下的所有文件或者文件目录的名称数组（String，输出名字）
> public File[] listFiles() :获取指定目录下的所有文件或者文件目录的File数组（File类，输出路径）

![image-20210817170031181](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210817170031181.png)![image-20210817170137912](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210817170137912.png)

> **重命名**
>
> public boolean renameTo(File dest): 把文件重命名为指定的文件路径（相当于Linux中mv命令）
>
> ​	**dest不能存在**

```java
System.out.println(file.renameTo(new File("E:\\io\\file.txt")));//结果：true//注：E:\\io\\file.txt 必须不存在
```

> public boolean isDirectory(): 判断是否是文件目录
> public boolean isFile() :判断是否是文件
> public boolean exists() :判断是否存在
> public boolean canRead() :判断是否可读
> public boolean canWrite() :判断是否可写
> public boolean isHidden() :判断是否隐藏

### 流

流的分类：

1. 操作数据单位：字节流(8bit)、字符流(16bit)
2. 数据的流向：输入流、输出流
3. 流的角色：节点流、处理流

流的体系结构：

| 抽象基类     | 节点流(或文件流) | 缓冲流(处理流的一种) |
| ------------ | ---------------- | -------------------- |
| InputStream  | FileInputStream  | BufferedInputStream  |
| OutputStream | FileOutputStream | BufferedOutputStream |
| Reader       | FileReader       | BufferedReader       |
| Writer       | FileWriter       | BufferedWriter       |

![image-20210818094441592](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20210818094441592.png)

#### 流的使用（FileReader）

1. 实例化File类对象，指明要操作的文件

2. 提供操作用的流

3. 读取数据

4. 关闭流close()

*read()读取：*

```java
@Test
public void test() {
    //1. 实例化File类对象，指明要操作的文件
    File file = new File("hello.txt");
    System.out.println(file.getAbsolutePath());
    //2. 提供操作用的流
    FileReader fileReader = null;
    try {
        fileReader = new FileReader(file);
        //3. 读取数据
        int read = fileReader.read();
        //read()读取下一个字符，当读到末尾了，会返回-1
        while (read != -1) {
            System.out.print((char) read);
            read = fileReader.read();//读取下一个字符
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        //4. 关闭流
        try {
            if (fileReader != null) //在第9行出异常时，就不需要close
                fileReader.close(); //放在finally中一定执行
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

*read(char[] cbuf)读取：*使用char[]暂时存储数据

```java
//read(char[] cbuf) 读取到的字符存入cbuf字符数组，并返回读取个数，当读到末尾返回-1
char[] chars = new char[5];
int len;
while ((len = fileReader.read(chars)) != -1) {
    for (int i = 0; i < len; i++) {
        System.out.print(chars[i]);
    }
//  String s = new String(chars, 0, len);
//  System.out.print(s);
}
```

#### 流的使用（FileWriter）

1. 实例化File类对象，指明操作文件对象

2. 提供一个流

3. 写出数据（如果文件不存在，会新建；如果已存在，会覆盖）

   如果流使用的构造器是: FileWriter(file,false) / FileWriter(file):对原有进行覆盖
   如果流使用的构造器是: FileWriter(file, true):不会对原有文件覆盖，而是在<u>原文件末尾</u>继续添加

4. 关闭流

```java
@Test
public void test1() {
    //1. 实例化File类对象，指明操作文件对象
    File file = new File("hello1.txt");
    FileWriter writer = null;
    try {
        //2. 提供一个流
        writer = new FileWriter(file);
        //3. 写出数据
        writer.write("Hello World!!\n");
        writer.write("Nothing is true.");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4. 关闭流
        try {
            if (writer != null)
                writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 流的使用（FileInputStream、FileOutPutStream）

整体流程和<u>字符流</u>相同，不过用byte[]来存储数据，可输出，也可用来写出到其他文件（见20~26行）

```java
package com.csii.jwh.file;

import org.junit.Test;

import java.io.*;

public class FileInputOutputStream {
    @Test
    public void test() {
        File file1 = new File("hello2.txt");
        File file2 = new File("hello3.txt");

        int i = 0;
        FileInputStream fileInputStream = null;
        FileOutputStream fileOutputStream = null;
        try {
            fileInputStream = new FileInputStream(file1);
            fileOutputStream = new FileOutputStream(file2);
            
            byte[] bytes = new byte[1024]; //一次读取1024字节
            int len;
            while ((len = fileInputStream.read(bytes)) != -1) { //读取数据并获取读取长度len
                String s = new String(bytes, 0, len);//将bytes中0到len位置的数据存入String
                fileOutputStream.write(bytes, 0, len);
                System.out.print(s);
            }

            System.out.println("复制完毕！！");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileInputStream != null)
                    fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (fileOutputStream != null)
                    fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

>  字符流只能用于<u>文本文件</u>
>  字节流可用于文本文件和<u>非文本文件</u>

#### 处理流之一：缓冲流的使用

缓冲流是为了提高流读取、写入的速度

**操作步骤仍然相同，不过在创建缓冲流的时候，需要<u>创建并传入对应的节点流</u>（见18、19行）**

**FileReader、FileWriter等的构造器可以直接放文件名，就不需要new File了（见50、51行）**

```java
package com.csii.jwh.file;

import org.junit.Test;

import java.io.*;

public class BufferTest {
    //缓冲字节流（BufferedInputStream、BufferedOutputStream）
    @Test
    public void test() {
        File file1 = new File("AoJiao.jpg");
        File file2 = new File("AoJiao3.jpg");

        BufferedInputStream bufferedInputStream = null;
        BufferedOutputStream bufferedOutputStream = null;

        try {
            bufferedInputStream = new BufferedInputStream(new FileInputStream(file1));
            bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(file2));

            byte[] buffer = new byte[1024];
            int len;
            while ((len = bufferedInputStream.read(buffer)) != -1) {
                bufferedOutputStream.write(buffer, 0, len);
            }
            System.out.println("复制完毕！！");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {

            try {
                if (bufferedInputStream != null)
                    bufferedInputStream.close();
                if (bufferedOutputStream != null)
                    bufferedOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }

    }

    //缓冲字符流（BufferedReader、BufferedWriter）
    @Test
    public void test2() {
        BufferedReader bufferedReader = null;
        BufferedWriter bufferedWriter = null;
        try {
            bufferedReader = new BufferedReader(new FileReader("hello3.txt"));
            bufferedWriter = new BufferedWriter(new FileWriter("hello4.txt"));

            int len;
            char[] chars = new char[1024];
            while ((len = bufferedReader.read(chars)) != -1) {
                bufferedWriter.write(chars, 0, len);
            }
            System.out.println("复制完毕！！");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {

            try {
                bufferedReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                bufferedWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 处理流之二：转换流

- 转换流提供了在字节流和字符流之间的转换
- Java API提供了两个转换流:
  ➢InputStreamReader: 将InputStream转换为Reader
  ➢OutputStreamWriter: 将Writer转换为OutputStream
- 字节流中的数据都是字符时，转成字符流操作<u>更高效</u>
- 很多时候我们使用转换流来处理文件乱码问题。实现编码和解码的功能。

- 转换流：属于字符流
  InputStreamReader:将一个<u>字节</u>的输入流转换为<u>字符</u>的输入流  **解码**
  OutputStreamWriter:将一个<u>字符</u>的输出流转换为<u>字节</u>的输出流  **编码**

```java
package com.csii.jwh.file;

import org.junit.Test;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
//以InputStreamReader为例编写代码
public class InputStreamReaderTest {
    @Test
    public void test() {
        InputStreamReader inputStreamReader = null;
        try {
            InputStream inputStream = new FileInputStream("hello4.txt");
     //两种创建方式，第二个参数为指定输出的字符编码格式，这个编码格式以文件存储的编码格式为准
//          inputStreamReader = new InputStreamReader(inputStream, "gbk");
            inputStreamReader = new InputStreamReader(inputStream);
            
            char[] chars = new char[10];
            int len;
            while ((len = inputStreamReader.read(chars)) != -1) {
                String s = new String(chars, 0, len);
                System.out.print(s);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (inputStreamReader != null) {
                    inputStreamReader.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

通过解码再编码，可以改变文件的字符编码（如：从UTF-8转换为GBK）

```java
@Test
public void test() {
    InputStreamReader reader = null;
    OutputStreamWriter writer = null;
    try {
        reader = new InputStreamReader(new FileInputStream("hello4.txt"));
        writer = new OutputStreamWriter(new FileOutputStream("hello4_gbk.txt"), "gbk");

        int len;
        char[] chars = new char[10];
        while ((len = reader.read(chars)) != -1) {
            writer.write(chars, 0, len);
        }
        System.out.println("转换完毕！！");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (reader != null)
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        try {
            if (writer != null)
                writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 对象流（序列化反序列化 ）（8.19）

序列化：使内存中的Java对象转换成与平台无关的二进制流	使用ObjectOutputStream：writeObject

反序列化：将磁盘中的对象还原为内存中的一个java对象	使用ObjectInputStream：readObject

**如果要将Person类序列化，必须让Person类<u>*可序列化*</u>**

1. 实现Serializable接口
2. 提供全局常量：SerialVersionUID

```java
public class Person implements Comparable<Person>, Serializable {
    private static final long serialVersionUID = 758527700021769456L;
    private String name;
    private int age;
    
    /*
    *一些方法
    */
}
```

```java
public class ObjectIOStreamTest {
    public static void main(String[] args) {
        ObjectOutputStream objectOutputStream = null;
        ObjectInputStream objectInputStream = null;
        try {
            objectOutputStream = new ObjectOutputStream(new FileOutputStream("person.dat"));
            objectInputStream = new ObjectInputStream(new FileInputStream("person.dat"));

            objectOutputStream.writeObject(new Person("jwh", 22));
            Object o = objectInputStream.readObject();
            String s = o.toString();
            System.out.println(s);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                if (objectOutputStream != null) {
                    objectOutputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (objectInputStream != null) {
                    objectInputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

注：

1. ObjectOutputStream和ObjectlnputStream不能序列化static和transient修饰的成员变量
2. 除了实现接口，类的其他属性也必须是可序列化的（基本数据类型默认可序列化）

## 网络编程

### TCP/IP协议簇

传输层协议有两个重要协议：传输控制协议TCP、用户数据报协议UDP

> TCP：
>
> - 需建立TCP连接，形成传输数据通道
> - 三次握手，四次挥手，点对点通信，是**可靠的**
>
> - 两个进程：客户端、服务端
> - 连接中可进行大数据量的传输
> - 传输完毕，**需释放已建立的连接，效率低**
>
> UDP：
>
> - 将数据、源、目的封装成数据包，**不需要建立连接**
> - 每个数据包64k以内
> - **不可靠**
>
> - 可以广播发送
> - 发送数据结束时，**不需要释放资源，开销小，速度快**

### InetAddress类

InetAddress类位于java.net包中，是Java对IP地址（包括IPv4和IPv6）的高层表示。

这个类的每个实例包含一个IP地址（IP address）以及这个IP地址对应的主机名（host name）。

这个类没有public构造器，只能通过静态方法：

```java
package com.csii.jwh.network;

import java.net.InetAddress;
import java.net.UnknownHostException;

public class InetAddressTest {
    public static void main(String[] args) {
        InetAddress byName = null;
        try {
            byName = InetAddress.getByName("www.baidu.com");
            InetAddress localHost = InetAddress.getLocalHost();
            System.out.println(localHost);
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
        System.out.println(byName);
    }
}
```

### Socket

端口号和IP地址组合得到一个网络套接字：Socket

#### TCP方式

```java
package com.csii.jwh.network;

import org.junit.Test;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;
import java.net.UnknownHostException;

public class TCPTest {
    //客户端
    @Test
    public void testClient() {
        InetAddress inet = null;
        try {
            //1. 获取InetAddress对象，得到IP地址
            inet = InetAddress.getByName("127.0.0.1");
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
        Socket socket = null;
        OutputStream socketOutputStream = null;
        try {
            //2. 实例化Socket，指明IP和端口
            socket = new Socket(inet, 8899);
            //3. 创建一个流，并写出数据
            socketOutputStream = socket.getOutputStream();
            for (int i = 0; i < 50; i++) {
                socketOutputStream.write(("*********" + (i + 1) + "这是一个关于TCP/IP的测试！！\n").getBytes());
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (socket != null) {
                try {
                    //4. 关闭资源
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socketOutputStream != null) {
                try {
                    socketOutputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //服务端
    @Test
    public void testServer() {
        //1. 实例化ServerSocket
        //2. 执行其accept方法得到一个Socket
        //3. 执行其getInputStream方法创建一个流，用来读入数据
        try (InputStream inputStream = new ServerSocket(8899).accept().getInputStream();
             //ByteArrayOutputStream类实现了将数据写入字节数组的输出流
             //当数据写入缓冲区时，缓冲区会自动增长
             //数据一次性输出，可有效避免中文字符乱码问题
             ByteArrayOutputStream stream = new ByteArrayOutputStream()) {
            int len;
            byte[] bytes = new byte[5];
            while ((len = inputStream.read(bytes)) != -1) {
                //使用了ByteArrayOutputStream，存好数据后一次输出，避免中文乱码
                stream.write(bytes, 0, len);
            }
            System.out.println(stream);
        } catch (IOException e) {
            e.printStackTrace();
        }
        //4. 关闭资源（将流等资源的声明写在try()中，会自动close）
    }
}
```

**总结：**

>客户端步骤：
>
>1. 获取InetAddress对象，得到IP地址
>2. 实例化Socket，指明IP和端口
>3. 创建一个流，并写出数据
>4. 关闭资源
>
>服务端步骤：
>
>1. 实例化ServerSocket，指明自己的端口
>2. 调用accept方法接收来自于客户端的Socket
>3. 调用getInputStream方法创建一个流
>4. 读取输入流中的数据
>5. 关闭资源（将流等资源的声明写在try()中，会自动close）

```java
package com.csii.jwh.network;

import org.junit.Test;

import java.io.*;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

public class TCPTest1 {
    //客户端
    @Test
    public void testClient() throws Exception {
        InetAddress address = InetAddress.getByName("127.0.0.1");
        Socket socket = new Socket(address, 8899);
        OutputStream outputStream = socket.getOutputStream();
        FileInputStream fileInputStream = new FileInputStream("AoJiao.jpg");
        int len;
        byte[] bytes = new byte[5];
        while ((len = fileInputStream.read(bytes)) != -1) {
            outputStream.write(bytes, 0, len);
        }
        System.out.println("传输完毕");
        //*******代表传输已完毕，服务器那一边才知道停止接收********//
        socket.shutdownOutput();
        //*******代表传输已完毕，服务器那一边才知道停止接收********//
        InputStream inputStream = socket.getInputStream();
        int len1;
        byte[] bytes1 = new byte[5];
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        while ((len1 = inputStream.read(bytes1)) != -1) {
            baos.write(bytes1, 0, len1);
        }
        System.out.println(baos);

        baos.close();   //不用关闭，close()不做任何操作
        inputStream.close();
        fileInputStream.close();
        outputStream.close();
        socket.close();
    }

    //服务端
    @Test
    public void testServer() throws Exception {
        ServerSocket serverSocket = new ServerSocket(8899);
        Socket socket = serverSocket.accept();
        InputStream inputStream = socket.getInputStream();
        int len;
        byte[] bytes = new byte[5];
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        FileOutputStream fileOutputStream = new FileOutputStream("AoJiao_Server.jpg");
        while ((len = inputStream.read(bytes)) != -1) {
//            baos.write(bytes, 0, len);
            fileOutputStream.write(bytes, 0, len);
        }
        System.out.println("接收完毕！");
//        System.out.println(baos);
        OutputStream outputStream = socket.getOutputStream();
        //服务器发送反馈
        outputStream.write("我是服务器，我已经收到图片啦！".getBytes());
        outputStream.close();
        baos.close();
        inputStream.close();
        socket.close();
        serverSocket.close();
    }
}
```

#### UDP方式

```java
package com.csii.jwh.network;

import org.junit.Test;

import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UDPTest {
    @Test
    public void testClient() throws Exception {
        String s = new String("我是UDP测试数据！！");
        byte[] data = s.getBytes();
        InetAddress address = InetAddress.getByName("127.0.0.1");

        DatagramSocket socket = new DatagramSocket();
        //DatagramPacket类中封装要发送的数据，长度，发送地址以及端口号
        DatagramPacket packet = new DatagramPacket(data, 0, data.length, address, 8899);
        //DataGramSocket的send方法发送
        socket.send(packet);

        socket.close();
    }

    @Test
    public void testServer() throws Exception {
        //指定端口号啊
        DatagramSocket socket = new DatagramSocket(8899);
        byte[] buffer = new byte[1024];
        DatagramPacket packet = new DatagramPacket(buffer, 0, buffer.length);
        //接收数据存入packet
        socket.receive(packet);
        String s = new String(packet.getData(), 0, packet.getLength());
        System.out.println(s);
        
        socket.close();
    }
}

```

> UDP步骤总结：
>
> 服务端：
>
> 1. 创建DatagramSocket对象socket，指明接收端口
> 2. 创建一个byte数组buffer充当缓冲区
> 3. 创建DatagramPacket对象packet，放入buffer
> 4. socket执行receive(packet)，将接收到的数据放入packet
> 5. 创建String，packet.getData()获取byte[]并转换为String
> 6. 关闭资源
>
> 客户端：
>
> 1. 将要发送的数据转换为byte[] data
> 2. 获取InetAddress对象，指明IP地址
> 3. 创建DatagramPacket对象，将要发送的数据、目标IP地址、目标端口号存入
> 4. 创建DatagramSocket对象，执行send(packet)将数据发送出去
> 5. 关闭资源

### URL编程

URL：统一资源定位符，表示Internet上某一资源的地址

```java
URL url = new URL("http://localhost:8080/examples/%E5%82%B2%E5%A8%87.jpg");
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
urlConnection.connect();
inputStream = urlConnection.getInputStream();
```

> 1. 创建URL对象，指定URL
> 2. 打开连接为HttpURLConnection
> 3. 执行connect()建立连接
> 4. 获取流

## 反射

通过反射，可以调用类的私有属性、方法

> **反射能提供的功能**
>
> 在运行时判断任意一个对象所属的类
> 在运行时构造任意一个类的对象
> 在运行时判断任意一个类所具有的成员变量和方法
> 在运行时获取泛型信息
> 在运行时调用任意一个对象的成员变量和方法
> 在运行时处理注解
> 生成动态代理

```java
package com.csii.jwh.reflection;

import org.junit.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReflectionTest {
    @Test
    public void test1() throws Exception {
        Class<Person> personClass = Person.class;

        //1. 利用构造器，创建people类的对象
        Constructor<Person> constructor = personClass.getConstructor(String.class, int.class);
        Person person = constructor.newInstance("金文浩", 22);
        System.out.println(person.toString());
        //2. 通过反射调用属性和方法
        //方法调用
        Method show = personClass.getDeclaredMethod("show");
        show.invoke(person);
        //属性调用
        Field name = personClass.getDeclaredField("name");
        String s = name.get(person).toString();
        System.out.println(s);

        //调用私有属性、构造方法和其他方法
        //访问并修改私有属性
        Field age = personClass.getDeclaredField("age");
        age.setAccessible(true); //所有的私有属性、私有方法，调用前setAccessible(true)
        age.set(person, 25);
        System.out.println(age.get(person));

        //调用私有构造方法
        Constructor<Person> constructor1 = personClass.getDeclaredConstructor(int.class);
        constructor1.setAccessible(true);
        Person person1 = constructor1.newInstance(25);
        System.out.println(person1);

        //调用私有方法
        Method show1 = personClass.getDeclaredMethod("show1");
        show1.setAccessible(true);
        show1.invoke(person1);
    }
}
```

### java.lang.Class（8.23）

所有的**类**本身是**Class的对象**：

> 程序经过javac.exe命令以后，会生成一个或多个字节码文件(.class结尾)
> 接着我们使用java.exe命令对某个字节码文件进行解释运行
> 相当于将某个字节码文件加载到内存中
> 此过程就称为类的加载。加载到内存中的类，称为**运行时类**
> 运行时类，就作为Class的一个实例
>
> 也就是说，Class的实例就对应着一个运行时类
>
> 加载到内存中的运行时类，会缓存一定时间，在此期间可通过**不同的方式获取**此运行时类（见下方代码）

```java
@Test
public void test2() throws Exception {
    //方式一：调用运行时类的属性：.class
    Class<Person> clazz1 = Person.class;
    System.out.println(clazz1);

    //方式二：通过运行时类的对象，调用getClass()
    Person person = new Person();
    Class<? extends Person> clazz2 = person.getClass();
    System.out.println(clazz2);

    //方式三：调用Class的静态方法：forName(String classPath)（常用）
    Class<?> clazz3 = Class.forName("com.csii.jwh.reflection.Person");
    System.out.println(clazz3);
    
    //方式四：使用类加载器：ClassLoader（了解）
    ClassLoader classLoader = ReflectionTest.class.getClassLoader();
    Class<?> clazz4 = classLoader.loadClass("com.csii.jwh.reflection.Person");
    System.out.println(clazz4);

    System.out.println(clazz1 == clazz2); //true
    System.out.println(clazz2 == clazz3); //true
    //运行时类只有一个，不过是通过不同的方法进行引用了而已
}
```

数组的**元素类型**和**维度**相同，则也是同一个Class

getFields():获取当前运行时类及其父类中声明为public访问权限的属性

getDeclaredFields():获取当前运行时类中声明的所有属性。 (不包含父类中声明的属性）

```java
Class clazz = Person.class;
Field[] declaredFields = clazz.getDeclaredFields();
for (Field f : declaredFields) {
    //1.权限修饰符
    int modifier = f.getModifiers();
    System.out.print(Modifier.toString(modifier) + "\t");
    //2.数据类型
    Class type = f.getType();
    System.out.print(type.getName() + "\t");
    //3.变量名
    String fName = f.getName();
    System.out.print(fName);
    System.out.println();
}
```

getSuperClass():获取父类

getGenericSuperClass():获取带泛型的父类

```java
/*获取带泛型父类的泛型，框架中用到*/
Type genericSuperclass = clazz . getGenericSuperclass();
ParameterizedType paramType = (ParameterizedType) genericSuperclass;
//获取泛型类型
Type[] actualTypeArguments = paramType . getActualTypeArguments();
System. out. println(actual TypeArguments[e]. getTypeName());
System. out . print1n(((Class )actualTypeArguments[0]). getName());
```

