# JUC并发编程

**管程**（monitor监视器），也就是锁，是一种**同步机制**，保证同一时间内，只有一个线程访问被保护的数据或代码

**守护线程**：默默运行在后台，如垃圾回收

当有**用户线程**存在时，JVM就不会关闭；当用户线程和主线程都结束了，尽管有守护线程，JVM仍然关闭。

## Lock接口

为锁和等待条件提供一个框架的接口和类，不同于内置同步和监视器， LOCK是类，可通过类实现同步访问，多个接口实现类：可重入锁等

### 多线程编程步骤（上）

第一步：创建资源类，在资源类创建属性和操作方法

第二步：创建多个线程，调用资源类的多个操作方法

### synchronized与lock的异同：

* synchronized是java内置的关键字，而Lock是一个接口，可以实现同步访问且比synchronized中的方法更加丰富
* synchronized不会手动释放锁，而lock需手动释放锁（不解锁会出现死锁，需要在 finally 块中释放锁）
* lock等待锁的线程会响应中断，而synchronized不会响应，只会一直等待
* 通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到
* Lock 可以提高多个线程进行读操作的效率（当竞争激烈时，Lock性能远优于Synchronized）

### Synchronized案例

```java
class Ticket {
    private int number = 30;

    //添加关键字，同步方法，会自动开关锁
    public synchronized boolean sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "：" + number--);
            return true;
        } else
            return false;
    }
}

public class SaleTicket {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (ticket.sale()){}
            }
        },"线程1").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (ticket.sale()){}
            }
        },"线程2").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (ticket.sale()){}
            }
        },"线程3").start();
    }
}
```

### Lock案例

```java
class LTicket {
    private int number = 30;

    //直接定义可重入锁，然后手动开关锁
    private final ReentrantLock lock = new ReentrantLock(true);

    public void sale() {
        lock.lock();
        try {
            if (number > 0){
                    System.out.println(Thread.currentThread().getName() + number--);
                    sleep(100);
                }
        }catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            //解锁
            lock.unlock();
        }
    }
}

public class LSaleTicket {
    public static void main(String[] args) {
        LTicket ticket = new LTicket();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale();
            }
        }, "线程1:").start();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale();
            }
        }, "线程2:").start();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale();
            }
        }, "线程3:").start();
    }
}
```

## 线程间通信

线程间通信的模型有两种：==共享内存==和==消息传递==

### 线程间的通信具体步骤（中）

1. 创建资源类，在资源类中创建属性和操作方法
2. 在资源类操作方法：判断、操作、通知
3. 创建多个线程，调用资源类的操作方法
4. 防止虚拟唤醒问题

### Synchronized案例

```java
package com.jwh.sync;

//第一步 创建资源类，创建属性和方法
class Num {
    private int num = 0;
    //第二步 判断、干活、通知
    // +1 方法
    public synchronized void incr() throws InterruptedException {
        //判断条件,达不到执行条件则等待
        //if (num != 0) {  //可能会造成虚假唤醒
        //while防止虚假唤醒
        while (num != 0) {
            //释放锁，让出CPU，进入等待状态
            this.wait();
        }
        //干活
        num++;
        System.out.println(Thread.currentThread().getName() + "::" + num);
        //操作完成，通知，唤醒所有正在沉睡的线程
        this.notifyAll();
    }

    public synchronized void decr() throws InterruptedException {
        if (num != 1) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "::" + num);
        this.notifyAll();
    }
}

public class ThreadDemo01 {
    public static void main(String[] args) {
        Num num = new Num();
        //第三部，创建多个线程，调用资源类的操作方法
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    num.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"线程1").start();

        //共创建4个线程，2个加，2个减（略）
    }
}
```

> ### wait(),notify(),notifyAll()方法 详解
>
> 1. wait()、notify/notifyAll() 方法是Object的本地final方法，无法被重写。
>
> 2. wait()使当前线程阻塞，前提是 必须先获得锁，一般配合synchronized 关键字使用，即，一般在synchronized 同步代码块里使用 wait()、notify/notifyAll() 方法。
>
> 3. 由于 wait()、notify/notifyAll() 在synchronized 代码块执行，说明当前线程一定是获取了锁的。
>
>    ​	==当线程执行wait()方法时候，会释放当前的锁，然后让出CPU，进入等待状态。==
>
>    ​	==只有当 notify/notifyAll() 被执行时候，才会唤醒一个或多个正处于等待状态的线程，然后继续往下执行，直到执行完synchronized 代码块的代码或是中途遇到wait() ，再次释放锁。==
>
>    ​	也就是说，notify/notifyAll() 的执行只是唤醒沉睡的线程，而不会立即释放锁，锁的释放要看代码块的具体执行情况。所以在编程中，尽量在使用了notify/notifyAll() 后立即退出临界区，以唤醒其他线程让其获得锁
>
> 4、wait() 需要被try/catch包围，以便发生异常中断也可以使wait等待的线程唤醒。
>
> 5、notify 和wait 的顺序不能错，如果A线程先执行notify方法，B线程在执行wait方法，那么B线程是无法被唤醒的。
>
> 6、notify 和 notifyAll的区别
>
> ​	notify方法只唤醒一个等待（对象的）线程并使该线程开始执行。所以如果有多个线程等待一个对象，这个方法只会唤醒其中一个线程，选择哪个线程取决于操作系统对多线程管理的实现。notifyAll 会唤醒所有等待(对象的)线程，尽管哪一个线程将会第一个处理取决于操作系统的实现。如果当前情况下有多个线程需要被唤醒，推荐使用notifyAll 方法。比如在生产者-消费者里面的使用，每次都需要唤醒所有的消费者或是生产者，以判断程序是否可以继续往下执行。
>
> 7、在多线程中要测试某个条件的变化，使用if 还是while？
>
> ​	要注意，notify唤醒沉睡的线程后，线程会接着上次的执行继续往下执行(==假如上次在wait()沉睡了，那唤醒后则从wait()开始执行，可能会造成虚假唤醒==)。所以在进行条件判断时候，可以先把 wait 语句忽略不计来进行考虑；显然，要确保程序一定要执行，并且要保证程序直到满足一定的条件再执行，要使用while进行等待，直到满足条件才继续往下执行。
>
> **虚假唤醒：**
>
> ![image-20211019150129233](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20211019150129233.png)

### Lock案例

```java
class Num {
    private int num = 0;

    //可重入锁
    private final Lock lock = new ReentrantLock(true);
    //用于实现线程的实现和等待【重点】
    private final Condition condition = lock.newCondition();

    public void incr() throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断
            while (num != 0) {
                condition.await();
            }
            //干活
            System.out.println(Thread.currentThread().getName() + "::" + ++num);
            //通知
            condition.signalAll();
        }finally {
            //解锁
            lock.unlock();
        }
    }

    public void decr() throws InterruptedException {
        lock.lock();
        try {
            while (num != 1) {
                condition.await();
            }
            System.out.println(Thread.currentThread().getName() + "::" + --num);
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
}

public class ThreadDemo02 {
    public static void main(String[] args) {
        Num num = new Num();

        new Thread(() -> {
            try {
                num.decr();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程1").start();
		 
        //共创建4个线程，2个加，2个减（略）
    }
}
```

## 线程间定制化通信

**主要使用flag进行标识控制，需要三个Condition**

```java
package com.jwh.lock;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Print {
    private int flag = 1;  //1 AA,2 BB,3 CC

    private Lock lock = new ReentrantLock(true);

    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();

    public void print3(int loop) throws InterruptedException {
        lock.lock();
        try {
            while (flag != 1) {
                c1.await();
            }
            for (int i = 0; i < 3; i++) {
                System.out.println("第" + loop + "轮::" + Thread.currentThread().getName() + "::" + (i + 1));
            }
            flag = 2;
            c2.signal();
        } finally {
            lock.unlock();
        }
    }

    public void print4(int loop) throws InterruptedException {
        lock.lock();
        try {
            while (flag != 2) {
                c2.await();
            }
            for (int i = 0; i < 4; i++) {
                System.out.println("第" + loop + "轮::" + Thread.currentThread().getName() + "::" + (i + 1));
            }
            flag = 3;
            c3.signal();
        } finally {
            lock.unlock();
        }
    }

    public void print5(int loop) throws InterruptedException {
        lock.lock();
        try {
            while (flag != 3) {
                c3.await();
            }
            for (int i = 0; i < 5; i++) {
                System.out.println("第" + loop + "轮::" + Thread.currentThread().getName() + "::" + (i + 1));
            }
            flag = 1;
            c1.signal();
        } finally {
            lock.unlock();
        }
    }
}

public class ThreadDemo03 {
    public static void main(String[] args) {
        Print print = new Print();

        new Thread(() -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    print.print3(i);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程1").start();

        new Thread(() -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    print.print4(i);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程2").start();

        new Thread(() -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    print.print5(i);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程3").start();

    }
}
```

## 集合的线程安全

**ArrayList线程不安全**，在下图代码中，运行时可能会在syso(list)处报`并发修改异常`（ConcurrentModificationException）

```java
public class ThreadDemo04 {
    public static void main(String[] args) {
//        线程不安全
//        List<String> list = new ArrayList<>();

//        Vector解决
//        List<String> list = new Vector<>();

//        Collections解决
//        List<String> list = Collections.synchronizedList(new ArrayList<>());

//        CopyOnWriteArrayList类解决（荐）
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();

        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0, 3));
                //此处并发修改异常
                System.out.println(list);
            }).start();
        }
    }
}
```

**HashSet线程不安全**，解决方案`CopyOnWriteArraySet`

**HashMap线程不安全**，解决方案`ConcurrentHashMap`

## 八种锁

### 公平锁和非公平锁

1. 公平锁是指多个线程按照申请所得顺序来获取锁。
2. 非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请锁的线程，先获取锁。有可能可能会造成**优先级反转**或者**饥饿现象**
3. 对于java中ReentrantLock而言，通过构造函数指定该锁是否是公平锁， 默认是非公平锁。非公平锁的优点在于吞吐量比公平锁要大**（非公平锁效率高）**。
4. 对于Synchronized而言，也是一种非公平锁。由于不像ReentrantLock是通过AQS来实现线程调度，所以并没有任何办法使其变成公平锁。

### 可重入锁

1. 可重入锁又称为递归锁，是指在同一线程的外层获取锁的时候，在进入内层方法会自动获取锁。
2. Synchronized（隐式）和ReentrantLock（显式）都是可重入锁。可重入锁的好处在于可以一定程度的**避免死锁**。

==可重入就是说某个线程已经获得某个锁，可以再次获取锁而不会出现死锁==

## 多线程运行原理

### 栈与栈帧

JVM中由堆、栈、方法区所组成，其中栈内存是给谁用的呢？其实就是线程，每个线程启动后，虚拟机就会为其分配一块栈内存。

* 每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存（调用一个方法，对应出现一个栈帧）
* 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

![image-20211021164311961](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20211021164311961.png)

### 线程上下文切换（Thread Context Switch）

因为以下一些原因导致cpu不再执行当前的线程，转而执行另一个线程的代码

* 线程的 cpu 时间片用完
* 垃圾回收
* 有更高优先级的线程需要运行
* 线程自己调用了 sleep、yield、wait、join、park、synchronized、lock 等方法

当 Context Switch 发生时，需要由操作系统保存当前线程的状态，并恢复另一个线程的状态，Java 中对应的概念 就是程序计数器（Program Counter Register），它的作用是记住下一条 jvm 指令的执行地址，是线程私有的

* 状态包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等 Context Switch
* 频繁发生会影响性能（频繁保存/恢复线程状态）

## 常用方法对比

### start() 与 run()

* 直接调用 run 是在主线程中执行了 run，没有启动新的线程
* 使用 start 是启动新的线程，通过新的线程间接执行 run 中的代码

### sleep() 与 yield()

#### sleep

1. 调用 sleep 会让当前线程从 Running 进入 Timed Waiting 状态（阻塞）
2. 其它线程可以使用 interrupt 方法打断正在睡眠的线程，这时 sleep 方法会抛出 InterruptedException
3. 睡眠结束后的线程未必会立刻得到执行
4. 建议用 TimeUnit 的 sleep 代替 Thread 的 sleep 来获得更好的可读性

#### yield

1. 调用 yield 会让当前线程从 Running 进入 Runnable 就绪状态，然后调度执行其它线程
2. 具体的实现依赖于操作系统的任务调度器

==sleep()会有真正意义上的等待时间，yield()则没有，有可能被再次分配到==

### 案例-防止CPU占用100%

#### sleep实现方式

在没有利用 cpu 来计算时，不要让 while(true) 空转浪费 cpu，这时可以使用 yield 或 sleep 来让出 cpu 的使用权给其他程序

```java
while(true) {
    try {
        Thread.sleep(50);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

* 可以用 wait 或 条件变量达到类似的效果
* 不同的是，后两种都需要加锁，并且需要相应的唤醒操作，一般适用于要进行同步的场景
* sleep 适用于无需锁同步的场景

### join()

等待线程运行结束

join(long n) 等待时长最多n毫秒，如果线程运行结束，则不再等待

### interrup()

打断线程

如果被打断线程正在 sleep，wait，join 会导致被打断的线程抛出 InterruptedException，并清除打断标记；如果打断的正在运行的线程，则会设置打断标记；park 的线程被打断，也会设置打断标记

## 多线程设计模式-两阶段终止

![image-20211022162357407](https://note-image-1303976927.cos.ap-shanghai.myqcloud.com/image-20211022162357407.png)

### 利用isInterrupted
