# JavaSE

```shell
# 编译运行java程序
$ javac helloworld.java
$ java helloworld
```

## 基础语法

### **数据类型**

> 整数、浮点数、字符串、布尔值、枚举、数组、包装类型

* 整数：int、short、long
* 浮点数：float、double
* 字符串：char、String
* 布尔值：boolean
* 枚举：enum
* 数组：int[]、float[]、char[]、boolean[]
* 包装类型：Integer、Float、Character、Boolean、Enum

### **流程控制**

> for、while、if...else、break、continue、switch...case1...default

* for：`for(int i=0;i < n;i++)；for(String str: strs)；`
* while：`while(非空非0则一直执行)`
* switch：

```java
int a = 0;
switch(a){
    case 0:
        System.out.println("0");
    case 1:
        System.out.println("1");
    default:
        System.out.println("default");
}
```

### 集合框架

> Collection：List、Set、Queue；Map：HashMap、TreeMap

* **Collection**：`add(E e)`；`remove(Object o)`；`contains(Object o)`；`size()\isEmpty()\iterator()\clear()\toArray()`
  * List：
    * * `get(int index)` - 获取指定位置元素
      * `set(int index, E element)` - 设置指定位置元素
      * `add(int index, E element)` - 在指定位置插入元素
      * `remove(int index)` - 删除指定位置元素
      * `indexOf(Object o)` - 返回元素首次出现的位置
      * `lastIndexOf(Object o)` - 返回元素最后一次出现的位置
      * `subList(int fromIndex, int toIndex)` - 返回子列表
    * ArrayList
    * LinkedList
    * Vector
  * Set：继承Collection接口
    * HashSet
    * LinkedHashSet
    * TreeSet
  * Queue：
    * * `offer(E e)` - 添加元素(优于add)
      * `poll()` - 获取并移除队首元素
      * `peek()` - 获取但不移除队首元素
      * `element()` - 获取但不移除队首元素(空队列抛异常)
    * LinkedList
    * PriorityQueue
  
* **Map**：`put(K key, V value)`；`get(Object key)`；`remove(Object key)`；`containsKey(Object key)`；`containsValue(Object value)`；`size()\isEmpty()\clear()\keySet()\values()\entrySet()`；
  * HashMap
  * LinkedHashMap
  * TreeMap
    * `firstKey()` - 返回第一个键
    * `lastKey()` - 返回最后一个键
    * `higherKey(K key)` - 返回大于给定键的最小键
    * `lowerKey(K key)` - 返回小于给定键的最大键
  * HashTable

## 面向对象

### **访问范围**

`顶级类只能是public或default，内部类可用public、private、protected、缺省`

* public：不同包，不同类，同类中都可访问
* private：只在同一类中访问，不同包不可访问
* protected：只在同一包和不同包子类中可访问
* default（缺省）：同一包中可访问

```java
package com.example.package1;
public class A {
    public void publicMethod() {
        System.out.println("Public method in A");
    }

    protected void protectedMethod() {
        System.out.println("Protected method in A");
    }

    void defaultMethod() {
        System.out.println("Default method in A");
    }

    private void privateMethod() {
        System.out.println("Private method in A");
    }
}
//------------------------------------------
package com.example.package1;
public class B {
    public void testAccess() {
        A a = new A();
        a.publicMethod();     // 可以访问
        a.protectedMethod();  // 可以访问
        a.defaultMethod();    // 可以访问
        // a.privateMethod(); // 编译错误，不能访问
    }
}
//------------------------------------------
package com.example.package2;
import com.example.package1.A;
public class C extends A {
    public void testAccess() {
        A a = new A();
        a.publicMethod();     // 可以访问
        a.protectedMethod();  // 可以访问，因为 C 是 A 的子类
        // a.defaultMethod(); // 编译错误，不能访问，因为不在同一包内
        // a.privateMethod(); // 编译错误，不能访问
    }
}
```

****

### **方法**

> 在方法上可以添加的关键字：访问修饰符、非访问修饰符、返回类型、方法参数、异常处理、注解

* 访问修饰符：public、private、protected、default
* 非访问修饰符：
  * static：静态方法，可通过类名直接调用
  * final：方法不能被子类重写
  * synchronized：同步锁，确保一时间只有一个线程执行该方法
* 返回类型：基本数据类型、对象类型、void
* 方法参数：任何Java有效的数据类型
* 异常处理：throws声明方法可能抛出的异常类型
* 注解：
  * `@Override`：标志该方法重写了父类中的方法
  * `@SuppressWarnings`：抑制方法中编译器的警告
  * `@FunctionalInterface`：标记接口类为函数式接口，保证接口中只有一个抽象方法

### **类**

> 普通类、接口类、枚举类、注解类

* `class`：顶级类（只能public或default）、普通内部类（不能定义static对象，可用所有访问关键字）、外部类（只能用default关键字）、静态内部类（类中可定义static对象）、**匿名内部类**
  * **匿名内部类**：`new 类名\接口名(){}`当格式为`new 类名(){}`时表示new一个继承这个类的匿名内部类；当格式为`new 接口名(){}`时表示new一个实现这个接口的类的匿名内部类
* `interface`：常量、抽象方法、默认方法（default void method(){}）、静态方法（static void method(){}）
* `enum`：常量、构造方法、其他自定义方法
* `@interface`：常与@Target、@Retention注解配合

```java
//Target常用参数：`ElementType：type、field、method、parameter`
//Retention常用参数：`source(源代码级别，编译时被丢弃)、class(保存在编译后字节码文件中，编译后不能通过反射访问)、runtime(保存在编译后的字节码文件中，并且在运行时可以通过反射访问)`
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface AnnotationClass{
    //参数
    int age() default 18;
    String name() default "zhangsan";
}
```

### **反射**

> Class.forName("全类名")；类.class；实例对象.getClass()；三种方法获取类反射对象

* 获取类信息：`Class<?> clazz = Demo.class`
  * `clazz.getName()`：获取类名
  * `clazz.getConstructors()`：获取构造器
  * `clazz.getMethods()`：获取类方法，不包括public和protected修饰的方法`getDeclaredMethods()`会返回包括私有和受保护的所有方法
  * `clazz.getFields()`：获取类字段
* 创建对象实例：`Constructor<?> constructor = clazz.getConstructor()`
  * `Object instance = constructor.newInstance();`
* 调用方法：`Method method = clazz.getMethod("test"); method.invoke(instance)`
* 访问字段：`Field field = clazz.getField("name"); (String)field.get(instance);`
* 操作注解：`clazz.getAnnotation(AnnotationClass.class);`反射获取类、方法、字段上的注解信息
* 动态代理：创建动态代理，实现接口的动态实现

### 多态

> 面向对象中主要通过类的继承和接口来实现多态。

* 方法重写：子类@Override父类方法。方法名、返回值类型、参数列表必须和父类相同
* **向上转型**：`FatherClass fc = new SonClass();`此时`fc`实例对象可以调用`SonClass`中重写了父类方法的方法。此为隐式转换，无需强制转换
* **向下转型**：`FatherClass fc = new SonClass();  SonClass sc = (SonClass) fc`此时`sc`实例对象可用`SonClass`类中的所有方法。向下转型是显示转型，需要强制类型转换，向下转型存在风险，如果父类引用不是子类对象会导致`ClassCastException`，可用instanceOf提前判断是否为子类对象。
* 接口多态：一个接口可被多个类实现

## 高级特性

### IO

> 节点流：直接对数据进行对接，更加接近数据。例：下图访问文件、数组、管道、字符串
>
> 处理流（包装流）：将节点流进行统一处理。例：缓冲流构造函数中可以引入节点流中的类

| 分类         | 字节输入流              | 字节输出流               | 字符输入流         | 字符输出流         |
| ------------ | ----------------------- | ------------------------ | ------------------ | ------------------ |
| **抽象基类** | InputStream             | OutputStream             | Reader             | Writer             |
| **访问文件** | **FileInputStream**     | **FileOutputStream**     | **FileReader**     | **FileWriter**     |
| 访问数组     | ByteArrayInputStream    | ByteArrayOutputStream    | CharArrayReader    | CharArrayWriter    |
| 访问管道     | PipedInputStream        | PipedOutputStream        | PipedReader        | PipedWriter        |
| 访字符串     |                         |                          | StringReader       | StringWriter       |
| **缓冲流**   | **BufferedInputStream** | **BufferedOutputStream** | **BufferedReader** | **BufferedWriter** |
| 对象流       | ObjectInputStream       | ObjectOutputStream       |                    |                    |
| 抽象基类     | FilterInputStream       | FilterOutputStream       | FilterReader       | FilterWriter       |
| 打印流       |                         | PrintStream              |                    | PrintWriter        |
| 推回输入流   | PushbackInputStream     |                          | PushbackReader     |                    |
| 特殊流       | DataInputStream         | DataOutputStream         |                    |                    |

* 文件流
  * `File`：操作文件的对象实现类，实现`Serializable, Comparable<File>`接口。

* 字节流（二进制文件）
  * `InputStream`：字节输入流总接口
    * `FileInputStream`：文件输入流
    * `FilterInputStream`：主要作用是作为其他输入流类的基类
      * `BufferedInputStream`：缓冲字节输入流
      * `DataInputStream`：读取基本数据类型的输入流
    * `ObjectInputStream`：读取对象的输入流
  * `OutputStream`：字节输出流总接口
    * `FileOutputStream`：文件字节输出流
    * `FilterOutputStream`：主要作用是作为其他输入流类的基类
      * `BufferedOutputStream`：文件字节输出流
      * `DataOutputStream`：写入基本数据类型的输出流
      * `PrintStream`：打印流，支持格式化输出
    * `ObjectOutputStream`：写入对象的输出流
* 字符流（字符文件）
  * `Reader`：字符输入流总接口
    * `InputStreamReader`：将字节流转换为字符流
      * `FileReader`：文件字符输入流
    * `BufferedReader`：带缓冲的字符输入流
  * `Writer`：字节输出流总接口
    * `OutputStreamWriter`：将字符流转换为字节流
      * `FileWriter`：文件字符输出流
    * `BufferedWriter`：带缓冲的字符输出流
    * `PrintWriter`：打印流，支持格式化输出

### 多线程

- `Thread`：线程类
- `Runnable`：线程任务接口
- `ExecutorService`：线程池服务
- `Future`：表示异步计算的结果
- `Callable`：返回结果的任务接口
- `Semaphore`：信号量
- `CountDownLatch`：倒计数锁存器
- `CyclicBarrier`：循环屏障
- `ConcurrentHashMap`：线程安全的哈希映射
- `CopyOnWriteArrayList`：线程安全的列表
- `AtomicInteger`：原子整数
- `AtomicLong`：原子长整数
- `AtomicBoolean`：原子布尔值

### 网络编程

- `Socket`：客户端套接字
- `ServerSocket`：服务器端套接字
- `InetAddress`：表示 IP 地址
- `URL`：表示统一资源定位符
- `URLConnection`：连接到 URL 的工具类
- `HttpURLConnection`：HTTP 协议的连接工具类

### JUC

## 常用对象

### 日期和时间
- `Date`：表示日期和时间
- `Calendar`：日历类，用于日期和时间的计算
- `SimpleDateFormat`：日期格式化工具
- `LocalDate`：表示日期（Java 8+）
- `LocalTime`：表示时间（Java 8+）
- `LocalDateTime`：表示日期和时间（Java 8+）
- `ZonedDateTime`：表示带时区的日期和时间（Java 8+）
- `Duration`：表示时间间隔（Java 8+）
- `Period`：表示日期间隔（Java 8+）

### 数学和随机数
- `Math`：数学运算工具类
- `Random`：生成随机数

### 文件和路径
- `File`：表示文件和目录路径
- `Path`：表示文件路径（Java 7+）
- `Paths`：路径工具类（Java 7+）
- `Files`：文件操作工具类（Java 7+）

### 其他常用类
- `System`：系统相关的操作
- `Runtime`：运行时环境
- `ProcessBuilder`：进程构建器
- `Properties`：属性文件操作
- `TimeZone`：时区操作
- `Locale`：地区和语言环境
- `Formatter`：格式化输出
- `Scanner`：输入扫描器
- `Pattern`：正则表达式模式
- `Matcher`：正则表达式匹配器

## 关键字

* static：被static修饰的类、方法、字段特点；
  * 共享性：所有实例共享一个静态成员
  * 生命周期：静态成员与类生命周期相同
  * 访问方式：通过类名直接调用，无需实例化
  * 初始化顺序：静态成员和静态代码块按照在类中出现的顺序初始化
  * 线程安全性：静态成员只会在内存中出现一次，线程共享
* final：被final修饰的类、方法、字段特点；
  * 类：不可被继承，final的类没有任何子类
  * 方法：不可被子类重写
  * 字段：基本数据类型初始化后不可被修改，引用数据类型被初始化后不能被改变但可以修改引用指向对象
* abstract：抽象方法必须在抽象类中，其子类必须重写抽象类中的所有抽象方法。
  * 抽象类不可实例化
  * 抽象类存在构造方法，用于子类通过super()访问
  * 抽象类存在普通方法，用于子类实例化后调用
  * 抽象类的子类要么重写所有抽象方法，要么自己定义为抽象类
* Interface：接口中存在常量（默认加public static final）、方法（默认加public abstract）、默认方法、静态方法。在Java9之后接口中可以定义private修饰的方法，但方法必须被接口中其他的默认方法或者静态方法引用。

# 基础总结

## 基础入门

```shell
# 编译
$ javac -encoding UTF-8 hello.java
$ java hello
helloworld
```

* 对象：类的实例，有状态和行为。
* 类：一个模板，描述对象的行为和状态
* 方法：行为，类中可以有很多方法（行为）
* 实例变量：每个对象都有独特的实例变量

### 语法基础

* 数据类型：整数（int、short、long、byte）、浮点（float、double）、字符串（char、String）、布尔（boolean）、枚举（enum）
* 运算符：算术运算符、关系运算符、逻辑运算符
* 控制流程：for、while、if-else if-else、switch

### 面向对象编程（OOP）

* 类和对象：class、interface、abstract、annotation、enum、record（java14堆出）
* 封装继承多态：private（声明的类中）、protected（同一包中类和子类）、public（不同包不同类都可）、default（同一包中）；extends；方法重载and方法重写
* 成员内部类（类中定义的类）、匿名内部类（定义接口并在创建方法时立即重写）、局部内部类（在方法中定义的类）、静态内部类（static修饰的类，不需要创建对象就可用类名调用的类）

### 异常处理

* 异常分类：运行时异常（RuntimeException）、编译时异常（CheckedException）；Error不可预见错误
* try-catch-finally语句
* throw和throws关键字
* 自定义异常（继承Exception的类，并在构造函数中super(message)）

### 数组和字符串

* 数组声明、初始化、遍历、多维数组、数组排序
* 字符串（String、StringBuilder、StringBuffer）、字符串常用方法

### 集合框架

* Collection
  * List：ArrayList、LinkedList、Vector、Stack
  * Set：HashSet、LinkedHashSet、TreeSet、EnumSet
  * Queue：LinkedList、PriorityQueue、ArrayDeque
* Map
  * HashMap
  * LinkedHashMap
  * TreeMap
  * EnumMap
  * Hashable
  * Properties
* for-each和迭代器
* 集合工具类

### 输入输出流

* 字节流：
  * InputStream
    * FileInputStream
    * BufferedInputStream
  * OutputStream
    * FileOutputStream
    * BufferedOutputStream
* 字符流：
  * Reader
    * InputStreamReader：FileReader
    * BufferedReader
  * Writer
    * OutputStreamWriter：FileWriter
    * BufferedWriter
* Serializable：序列化关键字，允许对象被序列化。用于保存对象的即时状态（对象转换为字节流），可以被反序列化恢复对象当时状态（字节流转化回对象）。
  * 可以被存在：内存、硬盘、网络中
  * 持久化存储、对象传输、对象复制

### 多线程

> New \ Runnable \ Running \ Blocked \ Waiting \ Timed Waiting \ Terminated

* 实现多线程

  * Thread：自定义继承Thread重写run方法

  * Runnable：实现接口并实现run方法

  * ExecutorService
    * newFixedThreadPool：固定大小线程池
    * newSingleThreadExecutor：单个线程池，按照被添加到线程池的任务顺序依次执行任务
    * newCachedThreadPool：可缓存线程池，线程闲置超过60秒则回收线程。适合执行大量短时间任务
    * newScheduledThreadPool：定时执行任务的线程池
    * newSingleThreadScheduledExecutor：单个执行定时任务的线程池
    * ThreadPoolExecutor：自定义线程池；核心数、最大线程数、空闲线程存活时间、工作队列方式、线程工厂、拒绝策略

  * CompletableFuture

  * Fork/Join

* 实现线程同步
  * synchronized
  * wait()/notify()/notifyAll()
  * volatile
  * ReentrantLock：提供比synchronized更丰富的锁功能，与Condition接口使用

### 网络编程

* InetAddress：用于表示IP地址
* URL：统一资源定位器
* URLConnection：URL的连接
* Socket：客户端套接字
* ServerSocket：服务端套接字
