### 1. Java和C++的区别？   
* 都是面向对象的语言，都支持封装、继承、多态；

* Java**不提供指针**直接访问内存，程序内存更加安全；

* Java类是**单继承**的，C++支持多继承；（Java接口支持多继承）  

* Java有自动内存管理**垃圾回收机制**（GC），不需要程序员手动释放内存；  

* **在 C 语言中，字符串或字符数组最后都会有一个额外的字符'\0'来表示结束。但是，Java 语言中没有结束符这一概念**。   

### 2. Java应用程序和小程序主类的差别？  
在 Java 应用程序中，这个主类是指包含 main() 方法的类。而在 Java 小程序中，这个主类是一个继承自系统类 JApplet 或 Applet 的子类。应用程序的主类不一定要求是 public 类，但小程序的主类要求必须是 public 类。  

### 3. Java 应用程序与小程序之间有哪些差别?  
> 应用程序是从**主线程**启动(也就是 main() 方法)。  
> applet 小程序没有 main() 方法，主要是嵌在**浏览器页面**上运行(调用init()或者run()来启动)，嵌入浏览器这点跟 flash 的小游戏类似。  

### 4.  import java 和 javax 有什么区别？  
> 没啥区别 都是标准 API 的一部分。  

### 5. 字符型常量和字符串常量的区别?  
- 形式上：字符型为单引号`''`字符串为双引号`""`  

- 含义上：字符常量相当于一个**整型值**( ASCII 值),可以参加表达式运算; 字符串常量代表一个**地址值**(该字符串在内存中存放位置)  

- 占内存大小：字符常量只占**2个字节**; 字符串常量占**若干个字节**。   

### 6. Java泛型了解么？什么是类型擦除？介绍一下常用的通配符？  
* Java泛型：泛型的本质是**参数化类型**。（像参数一样在调用时再赋值）   

* 类型擦除：Java的泛型是伪泛型，这是因为**Java在编译期间，所有的泛型信息都会被擦掉**，这也就是通常所说类型擦除 。  

* 常用的通配符： **T，E，K，V，？**  
  * ？ 表示不确定的 java 类型  

  * T (type) 表示具体的一个java类型（String  Integer ...）  

  * K V (key value) 分别代表java键值中的Key Value  

  * E (element) 代表Element  

### 7. ==和equals的区别?  
- 基本数据类型==比较的是**值**，引用数据类型==比较的是**内存地址**。  

- equals() : 它的作用也是**判断两个对象是否相等**，它不能用于比较基本数据类型的变量。

- Object 的`equals`方法是比较的对象的**内存地址**，而 String 的`equals`方法比较的是**对象的值**。 (String 中的 equals 方法是被重写过的)   

- 建议安装阿里巴巴的p3c插件，可以检测到错误使用==会报错。

- 重写 equals 时必须重写 hashCode 方法。  

- **hashCode()在散列表中才有用，在其它情况下没用**。在散列表中 hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。  

- 如果两个对象相等，则 hashcode 一定也是相同的；两个对象有相同的 hashcode 值，它们也不一定是相等的（存在碰撞冲突的情况）；因此，**equals 方法被覆盖过，则 hashCode 方法也必须被覆盖**；  

- hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

### 8. Java 里使用 long 类型的数据一定要在数值后面加上 L，否则将作为整型解析。

### 9. Java的装箱和拆箱？  
- 装箱：`Integer i = 10;`  拆箱:`int n = i;`  

- 实现过程：装箱过程是通过调用包装器的 `valueOf` 方法实现的，而拆箱过程是通过调用包装器的 `xxxValue` 方法实现的。（整型的就是intValue()）  

- 在通过 `valueOf` 方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；**否则创建一个新的Integer对象**。（这里很坑）  

- Integer、Short、Byte、Long这几个类的valueOf方法的实现是类似的。（**[-128，127]**）  

- Character创建了数值在 **[0,127]** 范围的缓存数据，Boolean 直接返回**True Or False**。

- Double、Float的valueOf方法的实现是类似的。（**直接创建新对象** 所以不可能完全相等）

- 当 `==`运算符的**两个操作数**都是 **包装器**类型的引用，则是比较指向的是否是**同一个对象**，而如果其中有**一个操作数是表达式**（即包含算术运算）则比较的是**数值**（即会触发自动拆箱的过程）。(很重要)  

- `Integer i1=40;` Java 在编译的时候会直接将代码封装成 `Integer i1=Integer.valueOf(40);`，从而使用**常量池**中的对象。(所有基本类型数据都存在常量池中)  

- `Integer i1 = new Integer(40);` 这种情况下会创建新的对象。


### 10. 为什么 Java 中只有值传递？  
Java 程序设计语言总是采用**按值调用**。也就是说，**方法得到的是所有参数值的一个拷贝**，也就是说，**方法不能修改传递给它的任何参数变量的内容**。但如果方法传的是**单个**引用类型，则可以修改引用类型的值。（因为传进来的是地址  只能在该地址所在内存区进行修改？  Key）

- 一个方法不能修改一个**基本数据类型**的参数。

- 一个方法可以改变一个对象参数的状态。（只有一个地址  `change(Student a)` a的内容可以改变）  

- **一个方法不能让对象参数引用一个新的对象**。 （`swap(Student a, Student b)`最后a、b不会改变） 

### 11. 重写  
- **方法名、参数列表必须相同**，返回值类型应比父类方法返回值类型更小或相等，抛出的异常范围小于等于父类，访问修饰符范围**大于**等于父类。 （**“两同两小一大”**）

- 如果父类方法访问修饰符为 private/final/static 则子类就不能重写该方法，但是被 static 修饰的方法能够被再次声明。  

- **构造方法无法被重写**   

- 重载发生在编译期，重写发生在运行期。

### 12. 深拷贝 vs 浅拷贝  
- **浅拷贝**：对基本数据类型进行**值传递**(不会改变变量)，对引用数据类型进行**引用传递**般的拷贝，此为浅拷贝。（同一片内存区）

- **深拷贝**：对基本数据类型进行**值传递**，对引用数据类型，**创建一个新的对象**，并复制其内容，此为深拷贝。 （开辟一块新的内存区）  

### 13. 面向对象和面向过程的区别  
- **面向过程** ：**面向过程性能比面向对象高**。 因为类调用时需要实例化，开销比较大，比较消耗资源。（并不是绝对的  有些面向过程的性能可能还差点）  

- **面向对象** ：**面向对象易维护、易复用、易扩展**。 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。

### 14. Constructor 不能被 override（重写）,但是可以 overload（重载）,所以你可以看到一个类中有多个构造函数的情况。  

### 15. 在 Java 中定义一个不做事且没有参数的构造方法的作用（父子类中很实用）  
- 实例化一个不需要马上给属性赋值的对象  

- **子类如果没有用 `super()` 来调用父类特定的构造方法，则会调用父类中“没有参数的构造方法”**。如果子类没有用 `super()`而父类又只定义了有参数的构造方法则编译时会报错。  

### 16. 成员变量与局部变量的区别有哪些？  
- 语法形式：成员变量是属于类的；局部变量是在代码块或方法中定义的变量或是方法的参数。  

- 访问控制符：成员变量可以被 public,private,static 等修饰符所修饰；局部变量不能被访问控制修饰符及 static 所修饰；两者都可以被final修饰。  

- 内存中的存储方式：如果成员变量是使用static修饰的，那么这个成员变量是属于类的，如果没有使用static修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。

- 生存时间：成员变量是对象的一部分，它随着对象的创建而存在；而局部变量随着方法的调用而自动消失。  

- 初始值：成员变量如果没有被赋初值:则会自动以类型的默认值而赋值（一种情况例外:**被 final 修饰的成员变量也必须显式地赋值**），而局部变量则不会自动赋值。

### 17. 对象实体与对象引用有何不同?  
new 创建对象实例（对象实例在**堆内存**中），对象引用指向对象实例（对象引用存放在**栈内存**中）。一个实体可以有n个引用值向它。

### 18. 构造方法有哪些特性？  
- 名字和类名相同  

- 没有返回值当不能用void来声明

- 生成类对象时自动执行 无需调用  

### 19.  在调用子类构造方法之前会先调用父类没有参数的构造方法,其目的是?  
帮助子类做初始化工作。

### 20. 对象的相等与指向他们的引用相等,两者有什么不同?  
- 对象的相等：内存中**存放的内容**是否相等。  

- 引用相等：指向的**内存地址**是否相等。

### 21. 多态存在的三个必要条件(只能发生在父子类之间？)  
- 继承  

- 重写  

- 父类引用指向子类对象 （`Parent p = new Child();`）

使用前提：a.有类的继承 b.有子类对父类方法的重写  

当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误；如果有，再去调用子类的同名方法。（父类中必须包含该方法）  

### 22. 静态方法和实例方法有何不同？  
- 调用方式：在外部调用静态方法时，可以使用"类名.方法名"的方式，也可以使用"对象名.方法名"的方式；而实例方法只有后面这种方式。**调用静态方法可以无需创建对象**。  

- 可访问的成员：静态方法只允许访问静态成员（即静态成员变量和静态方法），而不允许访问实例成员变量和实例方法；实例方法则无此限制。

### 23.接口和抽象类的区别是什么？
- 访问修饰符：接口默认的是public，抽象方法可以有 public、protected 和 default 这些修饰符。（不能有private）  

- 继承性：一个类可以实现多个接口但只能实现一个抽象类。  

- 方法要求：接口所有方法都必须没有方法体，而抽象类可以包含有方法体的方法。  

- 变量：接口中除了 static、final 变量，不能有其他变量，而抽象类中则不一定。  

- 设计层面：抽象是对**类**的抽象，是一种**模板设计**，而接口是对**行为**的抽象，是一种**行为的规范**。

### 24.  String、String StringBuffer 和 StringBuilder 的区别是什么?  
- 可变性：`String` 类中使用 `final` 关键字修饰字符数组来保存字符串，`private final char value[]`，所以 **`String` 对象是不可变的**。后两者继承自 `AbstractStringBuilder` 类没有用 final 关键字修饰，是可变的。  

- 线程安全性：`String` 中的对象是不可变的，也就可以理解为常量，线程安全。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是**线程安全**的。`StringBuilder` 并没有对方法进行加同步锁，所以是**非线程安全**的。  

- 性能：每次改动`String`类型的值，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。  

- 总结：  
1.少量数据：String  
2.大量单线程数据：StringBuilder  
3.大量多线程数据：StringBuffer  

### 25. Java 序列化中如果有些字段不想进行序列化，怎么办？  
对于不想进行序列化的变量，使用 `transient` 关键字修饰。

### 26. 获取用键盘输入常用的两种方法  
- `Scanner`    
```java
Scanner sc = new Scanner(System.in);
String str = sc.nextLine();
```

- `BufferedReader`(会抛异常)
```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
String s = input.readLine();
```

### 27. try-catch-finally  
- try块：**捕获异常**；后面接0个或多个catch；如果是0个catch必须要跟一个finally块。  

- catch块：**处理异常**；  

- finally块：**无论是否捕获或处理异常，finally 块里的语句都会被执行**。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在方法返回之前被执行。  

  以下几种情况finally块不会执行：  

  1.finally块的第一行发生了异常。  

  2.在前面的代码中用了 `System.exit(int)`已退出程序。 exit 是带参函数 ；若该语句在异常语句之后，finally 会执行。  

  3.程序所在的线程死亡。  

  4.关闭 CPU.

### 28. 简述线程、程序、进程的基本概念。以及他们之间关系是什么?  


### 29.线程有哪些基本状态（生命周期）?
- NEW ：初始状态；线程被构建，但还没调`start()`方法。

- RUNNABLE ：运行状态；Java线程将操作系统中的就绪（`READY`）和运行（`RUNNING`）两种状态笼统的称为“运行中”。

- BLOCKED ：阻塞状态；表示线程阻塞于锁。 

- WAITING ：等待状态；表示线程进入等待状态，需要等待其它线程做出一些特定动作。(`wait()` ...)

- TIME_WAITING ：超时等待状态；可以在指定时间内自行返回。(`sleep(long)`、`wait(long)` ...)

- TERMINATED ：终止状态；表示当前线程已执行完毕。（执行`RUNNING`的`run()`方法后进入的状态）

### 30. Java 中 IO 流分为几种?   
- 流向：输入流  输出流  

- 操作单元：字节流  字符流  

- 角色：节点流  处理流  

- 基类：  
  InputStream/Reader ：所有的**输入流**的基类，前者是字节输入流，后者是字符输入流。

  OutputStream/Writer ：所有**输出流**的基类，前者是字节输出流，后者是字符输出流。

### 31.既然有了字节流,为什么还要有字符流? （信息的最小存储单元都是字节） 
 Java虚拟机会将字节转换成字符流，这个过程比较耗时，而且如果不知道编码类型很容易出现乱码现象，所以 I/O 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好。

### 32.BIO,NIO,AIO 有什么区别?  
- BIO (Blocking I/O)：同步阻塞 I/O 模型，**数据的读取写入必须阻塞在一个线程内等待其完成**。  

- NIO (Non-blocking/New I/O)：同步非阻塞的 I/O 模型，支持面向缓冲的，基于通道的 I/O 操作方法。  

- AIO (Asynchronous I/O)：异步非阻塞的 IO 模型，相当于NIO 的改良版 NIO 2。异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

### 33. 不能使用一个值为null的引用类型变量来调用非静态方法，否则会抛出异常。 

### 34.BigDecimal 的用处？  
- **浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用 equals 来判断**。因此需要使用 BigDecimal 来定义浮点数的值，再进行浮点数的运算操作。（和JS一样存在精度丢失问题）  
```java
BigDecimal a = new BigDecimal(1.0);
BigDecimal b = new BigDecimal(0.9);
BigDecimal c = new BigDecimal(0.8);
    	
System.out.println(a.subtract(b)==(b.subtract(c)));  //false  地址
System.out.println(a.subtract(b).equals(b.subtract(c)));  //true 值 
```
- BigDecimal 的大小比较  

`a.compareTo(b)` : 返回 -1 表示 a 小于 b，0 表示 a 等于 b ， 1表示 a 大于 b。 

- BigDecimal 保留几位小数  

通过`setScale` 方法设置保留几位小数以及保留规则。  

- **为了防止精度丢失，推荐使用它的 BigDecimal(String) 构造方法来创建对象**(日常开发中强制使用字符串传参  非常重要)。    
```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = BigDecimal.valueOf(1.0);//使用valueOf会先将double类型转为String
```

- BigDecimal 主要用来操作（大）浮点数，BigInteger 主要用来操作大整数（超过 long 类型）。  

- BigDecimal 的实现利用到了 BigInteger, 所不同的是 BigDecimal 加入了小数位的概念.

### 35. 基本数据类型一般用于局部变量，其它地方变量的声明建议都用包装数据类型。

### 36. Arrays.asList()  
- **使用工具类Arrays.asList()把数组转为集合时，不能使用其修改集合相关的方法**；他的add/remove/clear方法会抛异常；且如果此时修改数组的内容，集合中的内容也会发生改变。 

> `asList()`返回对象是Arrays的一个内部类，并没有实现集合的修改方法。  

- 传递的数组必须是**对象数组**，而不是基本类型。如果是基本类型的，传进去的只有一个值，就是数组对象本身。

### 37. 如何正确的将数组转换为ArrayList?  
最简单的方法：  
`List list = new ArrayList<>(Arrays.asList("a", "b", "c"))`  

其它方式：
[JavaGuide](https://github.com/fwfm/JavaGuide/blob/master/docs/java/Java%E7%96%91%E9%9A%BE%E7%82%B9.md)  

### 38. Collection.toArray()方法需要指定返回类型 否则会报错。  
如果是返回String数组建议写成`String[] s = list.toArray(new String[0]);`

### 39. 不要在foreach循环里进行元素的remove/add操作，remove要使用迭代器Iterator方式，如果并发操作，需要对Iterator对象加锁。  

