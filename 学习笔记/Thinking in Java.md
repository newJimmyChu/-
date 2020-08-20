# Thinking in Java

### 4.2 方法过载

函数名称相同，签名不同  *单纯的返还类型不同不能让方法过载

this关键字：可以用this()方法来调用构建器，但是只能调用一次，并且必须是第一行要执行的代码

编译器禁止在任何constructor之外的地方调用this()构建器

```java
Flower(String s, int petals) {
    this(petals);
    //!    this(s); // Can't call two!
    this.s = s; // Another use of "this"
    System.out.println("String & int args");
  }
```

static方法没有this，因为static相当于一个全局静态变量，本质上不属于面向对象的范畴

### 4.3 GC

Java不允许我们创建局部对象（本地对象），所有对象的创建必须通过new关键字 也就是说，所有的对象都会被分配到堆上

finalize()函数能够让我们得知GC的信息，以及对象何时将被回收

### 4.4 成员初始化

1. 初始化顺序：在一个类里，初始化顺序是由变量在类内的定义顺序决定的。类变量的初始化会在调用任何方法之前得到初始化

   ```java
   // 在Card类中，t1,t2,t3会按照代码顺序得到初始化，
   // 之后才会调用构造函数
   class Card {
     Tag t1 = new Tag(1); // Before constructor
     Card() {
       // Indicate we're in the constructor:
       System.out.println("Card()");
       t3 = new Tag(33); // Re-initialize t3
     }
     Tag t2 = new Tag(2); // After constructor
     void f() {
       System.out.println("f()");
     }
     Tag t3 = new Tag(3); // At end
   }
   
   ```

2. 静态数据的初始化：

   1. 如果该数据属于基本类型，且未得到初始化，就会得到自己标准类型的初始值
   2. 如果是一个对象的引用，除非有一个新的对象，否则就会得到一个(NULL)

   静态对象初始化顺序：首先static，然后是非static

   静态对象初始化只有在必要的时候进行。如果不创建static对象，那么就不会对其初始化

   

   static代码块中只会在首次生成一个类的一个对象时或者首次访问属于那个类的static成员时

   ```java
   class Spoon {
     static int i;
     static {
       i = 47;
     }
   ```



### 4.5 数组初始化

数组初始化基本方式：

```java
// 一维数组
int[] a = { 1, 2, 3, 4, 5 };

int[] a = new int[pRand(20)];

// 二维数组
int[][] a = {
{ 1, 2, 3, },
{ 4, 5, 6, },
};

Integer[][] a4 = {
  { new Integer(1), new Integer(2)},
  { new Integer(3), new Integer(4)},
  { new Integer(5), new Integer(6)},
};

// 三维数组
int[][][] a3 = new int[3][][];
    for(int i = 0; i < a3.length; i++) {
      a3[i] = new int[5][];
      for(int j = 0; j < a3[i].length; j++)
        a3[i][j] = new int[7];
    }
```



### 5.1 包：库单元

命名空间（Name Space）：解决潜在命名冲突

编译一个.java文件时，我们会获得一个名字完全相同的输出文件；但对于.java文件中的每个类，它们都有一个.class扩展名。 少量.java文件 -> 大量.class文件

每个.java文件内有且只能有一个public类，并且类名需与文件名称相同

package语句：必须作为文件第一个非注释语句出现，名字必须为小写

Java解释器工作程序：

1. 首先找到环境变量CLASSPATH，然后对.class文件展开搜索
2. 将包名称的dot置换为/或者\：package foo.bar.baz -> foo/bar/baz
3. 在目标路径搜索.class文件

编译器遇到import语句后，会搜索CLASSPATH指定的路径，然后查找相应名称（相应的.class文件）

自动编译：为导入的类首次创建一个对象时，编译器会在目录中寻找同名的.class文件。若只发现一个同名.class文件，则使用它。反之如果还有其他同名.java文件，如果.java文件比较新，那么就会自动编译.java文件，生成一个新的.class

### 5.2 Java访问指示符

#### 5.2.1 Friendly 有好的

舍弃访问修饰符，包内其他成员均可访问，包外不可访问

新建一个包，并从另一个包的类里面继承，则只能访问public不能访问friendly

#### 5.2.4 Protected 

Protected与友好的区别：

1. Protected可以被同包中的任意类以及不同包中的子类调用

2. 友好的类不能被不同包的类继承，并且不能被不同包的类或对象调用

继承相关

### 5.3 接口与实现

接口与实现分离

### 5.4 类访问

只能将类设置为友好的或者public



### 6.2 继承的语法

```java
public class student extends people {
    private final int id;
}
```

在构造一个子类时，对父类constructor的调用是必须的

### 6.8 final关键字

无论是static还是final，都只能存储一个数据且不可变

final可以修饰句柄（引用）

当final修饰句柄时，指向的对象不可变，但是对象的属性可变

final字段可以没有初始值，但是必须在被使用前初始化

final修饰函数的参数：表明该参数不能被指向其他对象（只读）

final修饰方法：

1. “上锁”，防止任何继承类修改
2. 效率，编译器不会执行入栈出栈的操作，而是会直接执行代码副本。同时，final会禁止动态绑定，以加快运行速度

类内的所有方法都会自动成为final的，可以为private方法添加final修饰符，但是没有额外意义

final类：不能从final类继承，不能衍生子类，final类内的所有方法默认为final



### 7.1 上溯造型

多形性：可以只写一个方法，将父类作为参数传入方法，然后运行相应子类方法

### 7.2 深入理解

#### 7.2.1 方法调用的绑定

绑定（binding）：将一个方法调用与一个方法主体连接到一起

动态绑定（运行期绑定）：在运行时编译器才判断具体类型

#### 7.2.3 扩展性

只与基础类接口通信是一种良好的设计模式，因为这样我们对类的扩展可以满足**开放-封闭原则**，即对扩展开放，对修改封闭

![1597066059390](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1597066059390.png)

### 7.3 覆盖与过载

编译器允许子类对父类方法进行过载，但是这种行为并不推荐

对父类的过载很可能会引起混淆，因为如果在多数情况下，我们在外部调用父类方法时并不清楚子类的具体类型

假如传入了父类的参数类型就不能正确运行地运行子类的方法

### 7.4 抽象类和方法

抽象类：如果一个类里包含了一个或者多个抽象方法，那么该类就必须被指定为抽象类，并且继承自该类的子类需要对所有的抽象方法进行实现

抽象类的方法可以不是抽象的，换而言之，抽象类中的方法可以有自己的实现

### 7.5 接口

接口：一个更为纯粹的抽象类，接口数据成员默认为static和final的，接口方法默认为public

Java的多重继承：Java本身不支持多重继承，但是一个类可以实现多个接口，从而变相地实现多重继承

 字段并不是接口的一部分，而是保存于那个接口的static存储区域中。 

### 7.6 内部类

一个外部类有一个特殊方法，它会返还指向一个内部类的句柄

内部类在向上回溯的时候进入不可用/不可见状态，这可以很好地**隐藏实现细节**

当我们需要在一个类的其中一个方法中返还一个外部接口时，我们可以通过创建一个匿名内部类，实现并返还该接口。这样做可以很好地**隐藏内部实现**。

非内部类不能被设置为private或者protected，只能被设置为public或有好的

下面一个例子展示了如何在一个方法中返还一个继承自外部接口的内部类

```java
public class Parcel4 {
  	public Destination dest(String s) {
  		class PDestination implements Destination {
      		private String label;
      		private PDestination(String whereTo) {
        		label = whereTo;
      		}
      		public String readLabel() { return label; }
    	}
    	return new PDestination(s);
  	}
  	public static void main(String[] args) {
    	Parcel4 p = new Parcel4();
    	Destination d = p.dest("Tanzania");
  	}
} ///~
```

PDestination作为一个内部类继承自外部的Destination接口。

内部类可以在任意作用域内被嵌套，比如内部类可以包含在if代码块中，但是该内部类还是会随其他代码一同编译（非条件编译）

匿名类不能拥有构建器，并且在结束匿名类时需要使用分号标识

如果想在匿名类中调用外部对象，则需要将外部变量设为final

一个内部类可以访问其所在类中的成员（对象）

#### Static内部类

static内部类：

1. 创建static内部类对象不需要外部类对象
2. 不能从static内部类中访问一个外部类对象

static内部类可以成为接口的一部分，因为接口允许静态成员：

```java
interface IInterface {
  static class Inner {
    int i, j, k;
    public Inner() {}
    void f() {}
  }
}
```

#### 引用外部类对象

我们还可以通过一个外部类来创建一个内部类对象：

```java
// 注：Exam为内部类，s为包含内部类的类的对象
Student.Exam a = s.new Exam();
```

除非已经拥有包含内部类的类的对象，否则不能创建一个内部类对象

#### 内部类标识

内部类会产生一个.class文件：先是封装类的名字，在跟随一个$，再跟随内部类名字

```java
OuterClass$InnerClass.class
```

如果一个内部类是匿名的，编译器会生成数字作为标识

### 7.7 构建器和多形性

构建一个对象的初始化过程：

1. 首先为对象初始化存储空间，并将值初始化为零
2. 调用基类构造器方法
3. 如果在基类构造器方法中含有abstract方法，向下寻找已经实现的方法
4. 调用子类相应方法，但是方法中使用的成员值为零
5. 调用子类构造器

构造器设计原则：尽可能简单地去初始化对象，并尽可能地减少方法的调用（可以安全地使用基类中的private方法）

### 7.8 通过继承进行设计

很多情况下合成比继承要更加灵活

上溯与下溯（up-casting and down-casting）：

1. 上溯一定安全，因为基类的范围更小，子类是基类的超集
2. 下溯不一定安全，因为子类的范围更大，基类是子类的子集

RTTI（运行期标识）：在运行期间对类型进行检查（每一次使用downcast都会进行RTTI）

```java
Useful[] x = {
      new Useful(),
      new MoreUseful()
    };
x[0].f();
x[1].g();
// Compile-time: method not found in Useful:
//! x[1].u();
((MoreUseful)x[1]).u(); // Downcast/RTTI
((MoreUseful)x[0]).u(); // Exception thrown
```



### 8.1 数组

优点：访问速度快

缺点：大小固定（数组扩容需要复制原数组）

动态创建数组作为函数参数：

```java
func(new Student[] {new Student(), new Student()});
```

### 9.1 & 9.2 违例与捕获

基本违例控制器：

```java
try {
  // Code that might generate exceptions
} catch(Type1 id1) {
  // Handle exceptions of Type1
} catch(Type2 id2) {
  // Handle exceptions of Type2
} catch(Type3 id3) {
  // Handle exceptions of Type3
}
```

### 9.3 标准Java违例

Throwable类中的两种常规类型：

1. 编译期错误（Error）（不必捕获）
2. 运行期错误（Exception）

在实现自己的违例时，子类方法不能覆盖父类方法掷出的违例类型

但是衍生类（子类）的方法可以不掷出违例

```java
abstract class Inning {
  void event () throws BaseballException {}
}
public class StormyInning extends Inning {
    // !public void event() throws RainedOut {}
    // If the method doesn't already exist in the
    // base class, the exception is OK:
}
```

用于一个方法的违例规范接口可能在继承和覆盖时变窄，但是不能变宽

### 9.6 Finally关键字

无论是否掷出一个违例，finally从句都会执行



### 10.1 Java IO

IO流分类：

1. 输入流：只能从中读取数据
2. 输出流：只能向其写入数据

在Java中主要使用InputStream和Reader作为基础输入类，OutputStream和Writer作为基础输出类。他们都是抽象类

字节流和字符流：

1. 字节流：单元为8位的字节（1 Byte） InputStream / OutputStream 
2. 字符流：单元为16位的字符（2 Byte） Reader / Writer

Java输入/输出流体系中常用的流的分类表

| 分类       | 字节输入流               | 字节输出流                | 字符输入流          | 字符输出流          |
| :--------- | :----------------------- | :------------------------ | :------------------ | :------------------ |
| 抽象基类   | *InputStream*            | *OutputStream*            | *Reader*            | *Writer*            |
| 访问文件   | **FileInputStream**      | **FileOutputStream**      | **FileReader**      | **FileWriter**      |
| 访问数组   | **ByteArrayInputStream** | **ByteArrayOutputStream** | **CharArrayReader** | **CharArrayWriter** |
| 访问管道   | **PipedInputStream**     | **PipedOutputStream**     | **PipedReader**     | **PipedWriter**     |
| 访问字符串 |                          |                           | **StringReader**    | **StringWriter**    |
| 缓冲流     | BufferedInputStream      | BufferedOutputStream      | BufferedReader      | BufferedWriter      |
| 转换流     |                          |                           | InputStreamReader   | OutputStreamWriter  |
| 对象流     | ObjectInputStream        | ObjectOutputStream        |                     |                     |
| 抽象基类   | *FilterInputStream*      | *FilterOutputStream*      | *FilterReader*      | *FilterWriter*      |
| 打印流     |                          | PrintStream               |                     | PrintWriter         |
| 推回输入流 | PushbackInputStream      |                           | PushbackReader      |                     |
| 特殊流     | DataInputStream          | DataOutputStream          |                     |                     |

#### 输入流的类型

ByteArrayInputStream：允许内存的一个缓冲区作为InputStream使用从中提取字节的缓冲区作为数据源使用

StringBufferStream：将一个String转换为一个InputStream，将StringBuffer作为一个数据源使用

FileInputStream：用于从文件读取信息代表文件名的一个String或者一个FIle / FileDescriptor对象作为数据源

PipedInputString：产生为相关的PipedOutputStream写的数据。实现了“管道化”的概念

SequenceInputStream：将多个InputStream对象转换为一个InputStream使用

#### 输出流的类型

输入到不同的地方：Byte数组 / 文件 / 管道

ByteArrayOutputStream：发送给流的时雨讴有数据都会进入缓冲区，可选缓冲区的初始大小和目的地

FileOutputStream：将信息发给一个文件，选用File或FileDescriptor作为目的地

PipedOutputStream： 管道化：为多线程处理指出数据目的地



#### IO的基类分析

**InputStream & Reader的抽象方法**：

1. int read(); 读取单个字节/ 字符
2. int read(byte[] b) 从输入流中最多读取 b.length 个字节，并将其存储到b中，返回读取字节 / 字符数量
3. int read(byte[] b, int off, int len); 与上一个相同，不同点在于从offset开始，至多读取len个字节 / 字符

**移动指针方法**：

1. void mark(int readAheadLimit); 在记录指针当前位置记录一个标记（mark）。
2. boolean markSupported(); 判断此输入流是否支持mark()操作，即是否支持记录标记。
3. void reset(); 将此流的记录指针重新定位到上一次记录标记（mark）的位置。
4. long skip(long n); 记录指针向前移动n个字节 / 字符。

**OutputStream & Writer**：

1. void write(int c); 将指定的字节/字符输出到输出流中，其中c即可以代表字节，也可以代表字符。
2. void write(byte[]/char[] buf); 将字节数组/字符数组中的数据输出到指定输出流中。
3. void write(byte[]/char[] buf, int off,int len ); 将字节数组/字符数组中从off位置开始，长度为len的字节/字符输出到输出流中。
4. void write(String str); 将str字符串里包含的字符输出到指定输出流中。
5. void write (String str, int off, int len); 将str字符串里面从off位置开始，长度为len的字符输出到指定输出流中。



对象流：ObjectInputStream：

```java
BufferedOutputStream buf = new BufferedOutputStream(outputStream); 
obj = new ObjectOutputStream(buf);
obj.writeObject(new Person("A", 21));
// Person p = (Person) obj.readObject();
```



### 10.5 IO 流的典型应用

1. 缓存输入文件 （将文件读入内存）
2. 从内存获取输入内容  （将内存中的数据读入）
3. 格式化内存输入内容  
4. 计算文件行数 & 文件输出
5. 存储 / 回复数据 （输出备份文件 -> 回滚文件）
6. 读写Random Access File（类似于存放在filesystem中的长array）

### 10.8 压缩

典型常用压缩流：

1. ZipOutputStream： 将数据压缩成Zip文件格式
2. ZipInputStream：解压Zip格式文件
3. GZipOutputStream：将数据压缩成GZip格式
4. GZipInputStream：解压GZip格式文件

GZip和Zip可以可以压缩任何东西，包括网络数据

文本校验：Checksum： Adler32（更快） & CRC32（更慢但是准确）

```java
FileOutputStream f = new FileOutputStream("test.zip");
CheckedOutputStream csum = new CheckedOutputStream(f, new Alder32());
ZipOutputStream out = new ZipOutputStream(
						new BufferedOutputStream(csum));
```

#### Jar应用程序

一个JAR文件由一系列Zip压缩格式的文件构成，同时有一个描述文件描述压缩文件的信息

### 10.9 对象序列化

序列化对象需要实现Serializable接口（没有对应方法）

Java Beans应用了对象序列化技术。在使用一个Bean时，它的状态信息需要在程序启动后被保存下来以便恢复

序列化对象步骤：

1. 创建某些OutputStream的对象，然后将其封装到ObjectOutputStream对象内
2. 调用writeObject()方法写入对象

反序列化对象步骤：

1. 将一个InputStream对象封装到ObjectInputStream对象内
2. 调用readObject()方法读出对象
3. 得到的对象是一个Object对象，所以需要下溯造型

程序可以反序列化一个自己代码之外的类的对象，但是只能得到Object类型的对象

假如我们想反序列化一个对象并且得到它的具体类型，并且JVM不能在本地路径里找到该对象的.class文件，那么在下溯造型时就会出现错误

#### 序列化的控制

可以通过实现Externalizable（较快，无需 serialVersionUID ）代替实现Serializable接口

该接口增加了两个方法：readExternal() & writeExternal() （没有任何东西可以被自动序列化，需要在这两个方法中明确需要被序列化的对象）

这两个方法会在读写对象时被调用

在反序列化的过程中，Serializable只单纯地恢复数据，不调用构建器；但是Externalizable调用对象的构建器 （这样可以通过控制类构建器的权限，限制序列化 / 反序列化）

#### transient（临时）关键字

部分序列化的方法：

1. 实现Externalizable，在readExternal() & writeExternal() 中序列化和反序列化部分属性
2. 使用transient字段，关闭序列化（只能伴随Serializable使用）

#### 持久性

只要将对象都序列化到一个单独的数据流中，就能恢复获得与以前完全一样的对象数据网，不会造成对象的重复。

对象的成员对象（非基本类型 / String）必须可以被序列化

序列化总结：

1.  所有需要网络传输的对象都需要实现序列化接口，通过建议所有的javaBean都实现Serializable接口。 
2. 同一对象序列化多次，只有第一次序列化为二进制流，以后都只是保存序列化编号，不会重复序列化。
3. 建议所有可序列化的类加上serialVersionUID 版本号，方便项目升级。
4. 不能序列化static变量



### 11.1 RTTI（运行期类型鉴定）

#### 11.1.1 Class对象

类加载器：在运行时，如果我们想要创建某个对象，JVM会先检查那个类型的Class对象是否已经载入；若尚未载入，则JVM会查找同名的.class文件并载入 （JAVA在启动时不完全载入）

类加载：

```java
Class.forName("CLASS_NAME")
```

两种类加载方法：

1. class.forName()：将类的.class文件加载到jvm中，执行static块并初始化static成员
2. classLoader：将.class加载到jvm中，不执行static中的内容，只有在newInstance才会去执行static代码块

#### 11.3 反射：运行期类检查

反射：编译器不需要在编译器知道类的信息，只需要在运行期检查对象的所属类

### 12.2 克隆对象

本地副本，简单的生成一个本地一模一样的副本。需要覆盖clone()类

浅层复制：只复制对象中的部分值

深层复制：复制对象中所有属性和引用

Java库中的大多数类都不能克隆：

比如说克隆一个Int的封装类Integer就会在编译时产生错误

```java
Integer x = new Integer(y);
x = x.clone();
```

但是如果我们自己的一个类是继承自Object的，那么就可以调用Object.clone()方法克隆。注明：override的clone()是需要设置为public的

### 12.2.4 克隆的本质

克隆的本质：Object.clone()会先检查原先对象的大小，再按位复制。如果一个类不是继承自Cloneable接口的类，那么Object.clone()就会抛出CloneNotSupportException异常 （推荐使用try-catch代码块来检测异常）

### 12.2.5 Java检查等价

Java检查等价（==）是通过对比两个对象之间的句柄得到的，而并不比较对象中的值。

### 12.3 克隆的控制

1. 不为克隆做任何事情，即让子类决定是否可以克隆
2. Implement Cloneable 接口， 并且覆盖 clone() 函数
3. 不实现 Cloneable 接口但是将 clone() override 成为 protected : 方便子类调用super.clone()
4. 将类设置为final可以有效的放置克隆



### 14 多线程

利用对象，可以将一个程序分割成多个独立的区域，执行多个独立运行的子任务

每个独立的子任务被称为线程（Thread）

应用情景：如果程序的一部分同特定的资源联系在一起，同时又不想中断程序的其他部分，那么我们就可以创建一个线程，使其独立于主程序运行

虽然单个cpu在切换线程时会带来时间 / 性能上的消耗，但是我们可以通过多线程保证多任务资源的平衡 -> 防止一个进程占据cpu过长时间

同时，系统可以自动分配线程给其他的cpu，这样一来多线程会带来性能上的提升 

#### 14.1.1 从线程继承

创建线程的方法：

1. 通过创建一个继承自Thread类的类，我们可以创建一个线程
2. 通过实现Runnable接口，创建一个线程，将Runnable对象作为参数传到新建的Thread中

继承Thread时需要覆盖public void run()方法

线程不是按照创建时间顺序运行的，而是由调度器调度的（可以使用setPriority()方法调整线程的优先级）

线程对象会注册自己，并在某些位置被引用，所以GC不能回收线程对象

#### 线程相关API

1. start():1.启动当前线程2.调用线程中的run方法
2. run():通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中
3. currentThread():静态方法，返回执行当前代码的线程
4. getName():获取当前线程的名字
5. setName():设置当前线程的名字
6. yield():主动释放当前线程的执行权
7. join():在线程中插入执行另一个线程，该线程被阻塞，直到插入执行的线程完全执行完毕以后，该线程才继续执行下去
8. stop():过时方法。当执行此方法时，强制结束当前线程。
9. sleep（long millitime）：线程休眠一段时间
10. isAlive（）：判断当前线程是否存活 

#### 线程调度

 Java的调度方法：

1. 对于同优先级的线程组成先进先出队列（先到先服务），使用时间片策略
2. 对高优先级，使用优先调度的抢占式策略（优先级从1到10，默认为5）
3. 高优先级线程并不一定比低优先级线程先执行完，只意味着可能性

###  **14.2 共享有限资源**

**同步：**

1. 将特定方法设置为synchronized：调用synchronized方法会自动将调用该方法的对象锁定。一个特定对象的所有synchronized方法都共享着一把锁
2. synchronized static方法：可以在一个类的范围内同步（锁定）
3. synchronized代码块：可以使用对象作为锁，同步关键区域

#### 14.2.3 多线程与Java Beans

无论何时创建了一个Bean，必须嘉定它要在一个多线程环境中运行

### 14.3 阻塞

线程的四种状态：

1. 已经创建但尚未启动（New）
2. 可运行（Runnable）
3. 线程死亡（Dead）
4. 线程阻塞（Blocked）

