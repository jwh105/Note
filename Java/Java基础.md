# Java基础

## 数据类型

### 基本数据类型

|   类型名称   | 关键字  | 占用内存 |                  取值范围                  |
| :---------: | :-----: | :------: | :----------------------------------------: |
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

2. 创建数组会开辟一整块连续的空间
3. 数组长度一旦确定就不能被修改

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
