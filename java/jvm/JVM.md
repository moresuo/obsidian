***
## 前言

> [!question] 什么是 JVM?
> JVM 就是 java 虚拟机，java 程序的运行环境，<mark style="background: #ABF7F7A6;">编译器将 java 文件编译为与平台无关的字节码文件，对应平台的 JVM 对字节码文件翻译成操作系统能够运行的机器指令</mark>，所以 JVM 帮助 java 语言实现了跨平台性，也就是所谓的<mark style="background: #ABF7F7A6;">一次编译，到处运行</mark>

- 优点：
	1. 一次编译，到处运行
	2. 自动内存管理，垃圾回收功能
	3. 数组下标越界检查
	4. 多态
- JVM, JRE, JDK 之间的关系
![[JVM-JRE-JDK关系.excalidraw]]


> [!question] JVM 有什么用？
> - 理解底层实现原理
> - 中高级程序员必备技能

![[JVM学习路线.excalidraw]]

## 内存结构
### 程序计数器
- 基本作用：
	- **记录下一条 JVM 指令的地址**
	- **确保被唤醒的线程从正确的位置开始执行**
	- **记录方法调用和返回的地址**
- 特点：
	- **线程私有**
	- **不会存在内存溢出**
### 虚拟机栈
- 定义：
	- 每个线程运行时所需要的内存，称为虚拟机栈
	- 每个栈由多个栈帧组成
	- 每个线程只能有一个<mark style="background: #FF5582A6;">活动栈帧</mark>，对应当前正在执行的那个方法、

> [!question] 什么是栈帧？
>栈帧是一种数据结构，用于<mark style="background: #ABF7F7A6;">存储函数调用的相关信息</mark>，如参数、局部变量、返回地址和上下文每一次函数的调用，都会在调用栈上创建一个新的栈帧，用来保存函数的执行环境。栈帧的大小和内容由编译器和函数的实现决定

<mark style="background: #ABF7F7A6;">栈帧的结构一般包括以下几个部分</mark>：
- 函数的返回地址：指向函数调用者的下一条指令的地址，用于函数返回时继续执行。
- 函数的参数：按照调用约定，将函数的实参压入栈中，供被调用函数使用。
- 旧的帧指针：保存调用者的帧指针（EBP）的值，用于恢复上一个栈帧的位置。
- 函数的局部变量：分配在栈上的非静态局部变量，用于函数内部的计算和存储。
- 栈顶指针：指向当前栈帧的顶部（ESP），用于压入或弹出数据。

> [!question] 问题辨析
> 1. 垃圾回收是否涉及栈内存？
> 	- 不会，栈内存是方法调用后产生的，方法调用结束后会自动释放内存
> 2. 栈内存分配越大越好吗？
> 	- 不是，因为物理内存是固定的，栈内存越大，可以支持更多的递归调用，但是可执行的线程变少了，比如默认栈内存为 1 M, 物理固定内存为 500 M, 那么同时可以运行 500 个线程，如果扩大栈内存，一个线程的栈内存为 2 M, 那么最多同时可以运行 250 个线程，降低了并发性能
> 3. 方法内的局部变量是否线程安全？
> 	- 如果方法内的局部变量作用范围没有逃离方法的作用访问，那它是线程安全的
> 	- 如果是局部变量引用了对象，并逃离了方法的访问，那就要考虑线程安全问题

$$栈内存溢出
$$
- 栈帧过多导致栈内存溢出
![[栈内存溢出15.01.34.excalidraw]]
- 栈帧过大导致栈内存溢出
![[JVM 2024-01-30 15.08.15.excalidraw]]
- 占内存溢出会报 `stackOverflowError` 异常
- 使用虚拟机参数 `-Xss256k` 指定栈内存大小
你可能会说你绝对不会再工作中出现递归的代码，不会导致栈内存溢出，但是我们在调用一些第三方库的时候可能会发生 `stackOverflowError`,例如我们使用 `jkson` 库下的 json 序列化方法
```java
public class Demo1_19 {  
  
    public static void main(String[] args) throws JsonProcessingException {  
        Dept d = new Dept();  
        d.setName("Market");  
  
        Emp e1 = new Emp();  
        e1.setName("zhang");  
        e1.setDept(d);  
  
        Emp e2 = new Emp();  
        e2.setName("li");  
        e2.setDept(d);  
  
        d.setEmps(Arrays.asList(e1, e2));  
  
        // { name: 'Market', emps: [{ name:'zhang', dept:{ name:'', emps: [ {}]} },] }  
        ObjectMapper mapper = new ObjectMapper();  
        System.out.println(mapper.writeValueAsString(d));  
    }  
}  
  
class Emp {  
    private String name;  
    @JsonIgnore  
    private Dept dept;  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public Dept getDept() {  
        return dept;  
    }  
  
    public void setDept(Dept dept) {  
        this.dept = dept;  
    }  
}  
class Dept {  
    private String name;  
    private List<Emp> emps;  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public List<Emp> getEmps() {  
        return emps;  
    }  
  
    public void setEmps(List<Emp> emps) {  
        this.emps = emps;  
    }  
}
```
其中 Dept 和 Emp 产生了循环依赖，使用 `ObjectMapper` 序列化就会发生占内存溢出
$$线程运行诊断$$

案例一：CPU 占用过多
- `top` 命令，查看哪个进程占用 CPU 过高
- `ps H -eo pid,tid,%cpu | grep pid`,通过 ps 命令进一步查看哪个线程占用的 CPU 过高
- `jstack java进程id` 查看 java 进程中的线程信息，通过线程 id 来定位对应的线程信息，jstack 中查找出的线程 id 是 16 进制的，需要进行转换
案例二：程序运行很长时间没有结果
- 可能发生了死锁，可以使用 `jstack 进程id` 查看 java 线程信息，如果线程中存在死锁，那就会提示 `deadlock` 相关信息
### 本地方法栈
本地方法栈就是专门为一些 `native` 方法提供的栈内存，一些带有 native 关键字的方法就是需要 JAVA 去调用本地的 C 或者 C++方法，因为 JAVA 有时候没法直接和操作系统底层交互，所以需要用到本地方法栈，服务于带 native 关键字的方法。
### 堆
$$
定义
$$
- 通过 new 关键字创建的对象都会被存放在堆内存中
- 它是线程共享的，堆内存中的对象都要考虑线程安全问题
- 堆内存有垃圾回收机制
$$
堆内存溢出
$$
- `java.lang.OutofMemoryError ：java heap space`. 堆内存溢出
- 可以使用虚拟机参数 `-Xmx8m` 来指定堆内存大小
$$
堆内存诊断
$$
1. Jps 工具
	- 查看当前系统中有哪些 java 进程
2. Jmap 工具
	- 查看堆内存占用情况，`jmap -heap 进程id`
3. Jconsole 工具
	- 图形界面，多功能检测工具，可以连续监测
4. Jvisualvm 工具
	- 图形界面工具，比 jconsole 功能更强大
<mark style="background: #FFB86CA6;">案例：以下代码分为 3 个阶段，array 数组创建之前，array 数组创建之后，array 数组销毁，垃圾回收之后，我们分别观察这三个阶段堆内存所占的大小</mark>
```java
public class Demo1_4 {  
  
    public static void main(String[] args) throws InterruptedException {  
        System.out.println("1...");  
        Thread.sleep(30000);  
        byte[] array = new byte[1024 * 1024 * 10]; // 10 Mb  
        System.out.println("2...");  
        Thread.sleep(20000);  
        array = null;  
        System.gc();  
        System.out.println("3...");  
        Thread.sleep(1000000L);  
    }  
}
```
运行以上代码在第 2 阶段之前，在终端输入 `jps` 命令查看以上 java 程序运行的进程的 id
![[Pasted image 20240130162134.png]]
紧接着在第 2 阶段之前，在终端输入 `jmap -heap 进程id`,查看第一阶段所占的堆内存大小
![[Pasted image 20240130162325.png]]
等待第 2 阶段发生，在第 3 阶段之前，再次在终端输入 `jmap -heap 进程id`，查看第二阶段所占堆内存大小
![[Pasted image 20240130162542.png]]
最后等待第 3 阶段发生，再次在终端输入 `jmap -heap 进程id`，查看第三阶段所占堆内存大小
![[Pasted image 20240130162714.png]]
重新运行程序，使用 jconsole 图形化工具查看堆内存的使用情况，在终端输入 `jconsole` 会弹出图形化界面，选择需要观察的 java 进程
![[Pasted image 20240130173009.png]]
![[Pasted image 20240130173243.png]]

```java
public class Demo1_13 {  
  
    public static void main(String[] args) throws InterruptedException {  
        List<Student> students = new ArrayList<>();  
        for (int i = 0; i < 200; i++) {  
            students.add(new Student());  
//            Student student = new Student();  
        }  
        Thread.sleep(1000000000L);  
    }  
}  
class Student {  
    private byte[] big = new byte[1024*1024];  
}
```
运行以上程序，在终端中输入 `jvisualvm` 查看堆内存的使用情况
![[Pasted image 20240130174730.png]]
![[Pasted image 20240130174855.png]]
### 方法区
$$
定义
$$
Java 虚拟机有一个在所有 Java 虚拟机<mark style="background: #FF5582A6;">线程之间共享</mark>的方法区域。方法区域类似于用于传统语言的编译代码的存储区域，或者类似于操作系统进程中的“文本”段。它存储每个类的结构，例如<mark style="background: #FF5582A6;">运行时常量池、字段和方法数据，以及方法和构造函数的代码，包括特殊方法</mark>，用于类和实例初始化以及接口初始化方法区域是在虚拟机启动时创建的。尽管方法区域在逻辑上是堆的一部分，但简单的实现可能不会选择垃圾收集或压缩它。此规范不强制指定方法区的位置或用于管理已编译代码的策略。方法区域可以具有固定的大小，或者可以根据计算的需要进行扩展，并且如果不需要更大的方法区域，则可以收缩。方法区域的内存不需要是连续的！
$$
组成
$$
![[Pasted image 20240201140603.png]]
- 方法区只是一个<mark style="background: #FF5582A6;">概念</mark>，在 <mark style="background: #ABF7F7A6;">JDK 1.8 之前方法区由永久代</mark>实现，用于保存<mark style="background: #ABF7F7A6;">运行时常量池，类信息，类加载器</mark>；在 <mark style="background: #FF5582A6;">JDK 1.8 及之后方法区由元空间</mark>实现，元空间的实际内存依赖于<mark style="background: #FF5582A6;">操作系统的本地内存</mark>，而不是 JVM 内存，元空间中的常量池不在存储字符串常量了，<mark style="background: #FF5582A6;">字符串常量池转到了堆内存中</mark>
$$
方法区的内存溢出
$$
- JDK 1.8 之前导致永久代内存溢出
	- 异常信息：`java.lang.OutOfMemoryError:PerGen space`
	- 使用 `-XX:MaxPermSize=8m` 指定永久代内存大小
- JDK 1.8 之后导致元空间内存溢出
	- 异常信息：`java.lang.OutOfMemoryError:Metaspace`
	- 使用 `-XX:MaxMetaspaceSize=8m` 指定元空间大小（默认与操作系统本地内存一致）
方法区内存溢出的根本原因就是在运行时加载的类信息过多，导致的内存不足，在实际场景中也是有可能发生的
- Spring
- MyBatis
在 Spring 和 MyBatis 框架中都使用到了 <mark style="background: #ABF7F7A6;">CGLIB 的动态代理机制生成代理对象</mark>，CGLIB 的实现就是依赖于字节码处理框架 ASM, 在运行时<mark style="background: #ABF7F7A6;">不断地生成类的字节码信息</mark>，而类的信息往往都存放在方法区中，这就存在方法区内存溢出的隐患了
$$
常量池
$$
- 二进制字节码信息包含：类的基本信息，常量池，类方法定义，包含了虚拟机的指令
编译以下代码
```java
public class Test {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }

}
```
然后使用 `javap -v Test.class` 命令反编译查看结果
![[Pasted image 20240201144802.png]]
每条指令都会对应常量池表中一个地址，常量池表中的地址可能对应着一个类名、方法名、参数类型等信息
![[Pasted image 20240201144823.png]]

**常量池**：就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量信息
**运行时常量池**：常量池是*.class 文件中的，当该类被加载以后，它的常量池信息就会放入运行时常量池，并把里面的符号地址变为真实地址
### StringTable
```java
// StringTable [ "a", "b" ,"ab" ]  hashtable 结构，不能扩容  
public class Demo1_22 {  
    // 常量池中的信息，都会被加载到运行时常量池中， 这时 a b ab 都是常量池中的符号，还没有变为 java 字符串对象  
    // ldc #2 会把 a 符号变为 "a" 字符串对象  
    // ldc #3 会把 b 符号变为 "b" 字符串对象  
    // ldc #4 会把 ab 符号变为 "ab" 字符串对象  
  
    public static void main(String[] args) {  
        String s1 = "a"; // 懒惰的  
        String s2 = "b";  
        String s3 = "ab";  
        String s4 = s1 + s2; // new StringBuilder().append("a").append("b").toString()  new String("ab")  
        String s5 = "a" + "b";  // javac 在编译期间的优化，结果已经在编译期确定为ab  
  
        System.out.println(s3 == s5);  
    }  
}
```
- 常量池中的字符串仅仅是符号，<mark style="background: #ABF7F7A6;">只有在被用到的时候才会转换为对象</mark>，并不是一下子就将类中的所有字符串就加载到常量池中
- 字符串常量池就是一个 HashTable 结构，不能扩容，字符串不能重复
- 加载字符串到局部变量表时，会先去字符串常量池中寻找，如果没有就会将对应的字符串加入字符串常量池中
- 如果<mark style="background: #FFF3A3A6;">两个字符串变量</mark>用+号进行拼接，那么默认使用的是<mark style="background: #FF5582A6;"> StringBuilder 中的 append 方法进行拼接</mark>（JDK 1.8），最后在通过<mark style="background: #FF5582A6;"> toString 方法转换为字符串对象</mark>，注意：<mark style="background: #ABF7F7A6;">通过 toString 创建字符串对象实际是通过 new 的方式创建的字符串对象，也就是说 toString 之后的字符串对象存储在堆区，而不是字符串常量池中</mark>
- 如果<mark style="background: #FFF3A3A6;">两个字符串常量</mark>用+号进行拼接，那么会在<mark style="background: #FF5582A6;">编译期间进行优化</mark>，可以直接从字符串常量池中获取拼接的结果，因为都是常量在运行过程中的结果也是不会变的，则可以在编译器就确定结果
- 通过字符串的 `intern` 方法可以尝试将堆区中的<mark style="background: #FF5582A6;">字符串对象</mark>放入字符串常量池中，如果有则不用放入，没有就放入，并且返回字符串常量池中的对象（JDK 1.8 之后）
- 如果是 JDK 1.6 中的 `intern` 方法，会将堆区中的字符串对象拷贝一份，然后存入字符串常量池中，也就是说并没有把堆区中的字符串对象存入常量池中，只是将拷贝的一份对象存入常量池中，并返回常量池中的字符串对象
$$
StringTable的位置
$$
<mark style="background: #FF5582A6;">jdk1.6 StringTable 位置是在永久代中，1.8 StringTable 位置是在堆中。</mark>
$$
StringTable垃圾回收
$$
有的人可能认为字符串存储在常量池中，不会发生垃圾回收，但是并不是这样的，当字符串常量池不足以存储新增的字符串对象时，就会发生字符串的垃圾回收
>运行以下代码，并设置对应的虚拟机参数，观察字符串常量池中字符串数量的变化
- `-Xmx10m`: 指定堆内存大小
- `-XX:PrintStringTableStatistics`: 打印字符串常量池信息
- `-XX:+PrintGCDetails`
- `-verbose:gc`：打印 gc 次数，耗费时间等信息
```java
/**
 * 演示 StringTable 垃圾回收
 * -Xmx10m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails -verbose:gc
 */
public class Code_05_StringTableTest {

    public static void main(String[] args) {
        int i = 0;
        try {
            for(int j = 0; j < 100000; j++) { // j = 100, j = 10000
                String.valueOf(j).intern();
                i++;
            }
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            System.out.println(i);
        }
    }
}
```
![[Pasted image 20240201165447.png]]
可以看到字符串常量池最多容纳 6 万多个字符串对象，但是我们在程序中实际创建了 100000 个字符串对象，然而字符串常量池中显示的字符串对象个数是两万多个，这就说明在字符串常量池容量不够时发生了垃圾回收
![[Pasted image 20240201165916.png]]
$$
StringTable性能调优
$$
- 因为 StringTable 是由 HashTable 实现的，所以我们可以增加 HashTable 桶的个数，以此来减少字符串垃圾回收的次数，提高入池效率
- `-XX:StringTableSize=桶个数（最少设置1009个以上）`
- 如果我们单纯的使用堆内存来存储字符串对象，那么相同的字符串也会占用同样的内存空间，如果我们使用字符串常量池存储字符串对象，那么相同的字符串只需要保存一份内存空间，大大减少内存的消耗
>运行以下程序，读取 linux. Words 文件下的字符串，并且将每个字符串对象存入 List 集合中，在终端打开 jvisualvm 图形化工具，查看字符串内存的使用情况
```java
public class Demo1_25 {  
  
    public static void main(String[] args) throws IOException {  
  
        List<String> address = new ArrayList<>();  
        System.in.read();  
        for (int i = 0; i < 10; i++) {  
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("linux.words"), "utf-8"))) {  
                String line = null;  
                long start = System.nanoTime();  
                while (true) {  
                    line = reader.readLine();  
                    if(line == null) {  
                        break;  
                    }  
                    address.add(line);  
                }  
                System.out.println("cost:" +(System.nanoTime()-start)/1000000);  
            }  
        }  
        System.in.read();  
  
  
    }  
}
```
![[Pasted image 20240201172519.png]]
>如果将字符串对象都存入常量池中，再看看字符串内存的占用比例（`line.intern`）

![[Pasted image 20240201172923.png]]
### 直接内存
$$
定义
$$
- 常见于 NIO 操作时，用于数据缓冲区
- 分配回收成本较高，但读写性能高
- 不受 JVM 内存回收管理
$$
使用直接内存的好处
$$
普通文件读写流程：
![[Pasted image 20240202144402.png]]
因为 java 不能直接操作文件管理系统，需要切换到内核态，调用本地方法进行操作，然后读取磁盘文件，会在系统内存中创建一个缓冲区，将数据读取到系统缓冲区，然后再将系统缓冲区数据复制到 java 堆内存中。缺点是数据存储了两份，在系统内存中有一份，在 java 堆内存中有一份，造成了不必要的复制
**使用了 DirectBuffer 文件读取流程**
![[Pasted image 20240202144808.png]]
>直接内存是操作系统和 Java 代码都可以访问的一块区域，无需将代码从系统内存复制到 Java 堆内存，从而提高了效率。

$$
直接内存释放原理
$$
>运行以下代码，会为直接内存分配 1 G 的内存空间，但是因为直接内存不受 JVM 管理，所以并不能直接使用观测 java 内存的监测工具，可以直接使用任务管理器查看系统内存的变化

```java
public class Demo1_26 {  
    static int _1Gb = 1024 * 1024 * 1024;  
  
    /*  
     * -XX:+DisableExplicitGC 显式的  
     */    public static void main(String[] args) throws IOException {  
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1Gb);  
        System.out.println("分配完毕...");  
        System.in.read();  
        System.out.println("开始释放...");  
        byteBuffer = null;  
        System.gc(); // 显式的垃圾回收，Full GC  
        System.in.read();  
    }  
}
```
**直接内存分配完毕**
![[Pasted image 20240202145743.png]]
**直接内存释放**
![[Pasted image 20240202145916.png]]
可以发现 JVM 内存回收之后直接内存也被释放了，但是前面不是说直接内存不受 JVM 内存回收管理吗？🤔
直接内存的释放不是通过 JVM 垃圾回收来释放的，而是调用了 `unsafe.freeMemory（）` 这个方法手动释放
1. 第一步：allocateDirect 的实现
```java
public static ByteBuffer allocateDirect(int capacity) { 
	return new DirectByteBuffer(capacity); 
}
```
底层创建了一个 `DirectByteBuffer` 对象
2. 第二步：DirectByteBuffer 类
```java
DirectByteBuffer(int cap) {   // package-private
   
    super(-1, 0, cap, cap);
    boolean pa = VM.isDirectMemoryPageAligned();
    int ps = Bits.pageSize();
    long size = Math.max(1L, (long)cap + (pa ? ps : 0));
    Bits.reserveMemory(size, cap);

    long base = 0;
    try {
        base = unsafe.allocateMemory(size); // 申请内存
    } catch (OutOfMemoryError x) {
        Bits.unreserveMemory(size, cap);
        throw x;
    }
    unsafe.setMemory(base, size, (byte) 0);
    if (pa && (base % ps != 0)) {
        // Round up to page boundary
        address = base + ps - (base & (ps - 1));
    } else {
        address = base;
    }
    cleaner = Cleaner.create(this, new Deallocator(base, size, cap)); // 通过虚引用，来实现直接内存的释放，this为虚引用的实际对象, 第二个参数是一个回调，实现了 runnable 接口，run 方法中通过 unsafe 释放内存。
    att = null;
}
```
这里调用了一个 <mark style="background: #ABF7F7A6;">Cleaner 的 create 方法</mark>，后台线程会对虚引用的对象进行检测，如果虚引用的实际对象（this 指的是 DirectByteBuffer）被回收以后，就会调用 <mark style="background: #ABF7F7A6;">Cleaner 的 clear 方法</mark>来释放直接内存
3. 调用回调函数执行直接内存的释放
```java
 public void clean() {
        if (remove(this)) {
            try {
            // 都用函数的 run 方法, 释放内存
                this.thunk.run();
            } catch (final Throwable var2) {
                AccessController.doPrivileged(new PrivilegedAction<Void>() {
                    public Void run() {
                        if (System.err != null) {
                            (new Error("Cleaner terminated abnormally", var2)).printStackTrace();
                        }

                        System.exit(1);
                        return null;
                    }
                });
            }

        }
    }
```
可以看到关键的一行代码，` this.thunk.run()`，thunk 是 Runnable 对象。run 方法就是回调 Deallocator 中的 run 方法，run 方法中根据内存地址调用 `unsafe.freeMemory(address)` 进行释放直接内存
```java
public void run() {
	if (address == 0) {
		// Paranoia
		return;
	}
	// 释放内存
	unsafe.freeMemory(address);
	address = 0;
	Bits.unreserveMemory(size, capacity);
}
```
**总结**
- 使用了 <mark style="background: #FF5582A6;">Unsafe 类</mark>来完成直接内存的分配回收，回收需要主动调用 `freeMemory` 方法
- ByteBuffer 的实现内部使用了 Cleaner（虚引用）来检测 ByteBuffer 。一旦 ByteBuffer 被垃圾回收，那么会由 ReferenceHandler（守护线程） 来调用 Cleaner 的 clean 方法调用 freeMemory 来释放内存
- JVM 调优时一般会加上 `-XX:+DisableExplicitGC` 参数，目的是让显示的 GC 调用失效，也就是让代码 `System.gc()` 失效，因为这种 GC 是一种 full GC, 会回收新生代和老年代，造成程序执行时间较长，但是如果显示的 GC 调用失效了话，那么 `ByteBuffer` 对象也可能不会被释放，就会导致直接内存不能及时释放，所以我们就通过 unsafe 对象手动调用 freeMemory 的方式释放内存。
## 垃圾回收
### 判断对象是否可以进行回收
$$
引用计数法
$$
当一个对象被引用时，该对象的引用计数器就会加一，当对象的引用计数器为 0 时，表示当前没有其他对象引用当前对象，则代表当前对象可以被垃圾回收
>引用计数法听着不错，但是有一个弊端，就比如循环依赖，两个对象的计数都为 1，想要回收 a 对象就必须先释放 b 对象，但是释放 b 对象又要先释放 a 对象，导致两个对象永远无法被回收，而且每次引用和取消引用都会消耗一定的性能，在 java 中肯定不会采用引用计数法
![[Pasted image 20240202153924.png]]

$$
可达性分析算法
$$
- JVM 中的垃圾回收器通过可达性分析算法来检测所有存活的对象
- 扫描堆中的对象，能被 GC Root 直接或间接引用的对象标识为存活对象，如果以 GC Root 为起点沿着引用链找不到的对象则可以被垃圾回收
![[Pasted image 20240202155910.png]]
**什么是 GC Root, 哪些对象可以作为 GC Root**:
- GC Root 就是垃圾回收的根对象，在垃圾回收时根据判断其对象是否被根对象直接或间接引用从而决定对象是否可以被垃圾回收
- 虚拟机栈（栈帧中的本地变量表）中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中 JNI（即一般说的 Native 方法）引用的对象
>运行以下代码，在终端输入命令，将程序的堆内存信息转换为一个二进制文件

```java
public static void main(String[] args) throws IOException {

        ArrayList<Object> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add(1);
        System.out.println(1);
        System.in.read();

        list = null;
        System.out.println(2);
        System.in.read();
        System.out.println("end");
    }
```
- 第一步：打开终端，使用 `jps` 命令，查看 java 进程 id
![[Pasted image 20240202160951.png]]
- 第二部：输入 `jmap -dump:format=b,live,file=1.bin 16104` 将堆内存信息保存为二进制文件
	- `dump`: 转储文件
	- `format=b`: 格式为二进制文件
	- `file`: 文件名
	- `16104`: 进程 id
![[Pasted image 20240202161243.png]]
- 第三步：打开 Eclipse Memory Analyzer 对 1. Bin 文件进行分析
![[Pasted image 20240202161304.png]]
分析的 gc root，找到了 ArrayList 对象，然后将 list 置为 null，再次转储，那么 list 对象就会被回收
### 5 种引用
- 强引用：<mark style="background: #FF5582A6;">强引用对象一般只在 GC Root 不在引用时才会被垃圾回收</mark>，通常就是通过 new 关键字创建的对象赋给一个引用变量，这个引用变量就是强引用
- 软引用（SoftReference）：<mark style="background: #FF5582A6;">仅有</mark>软引用引用该对象时，在垃圾回收后如果发生<mark style="background: #FF5582A6;">内存不足现象就会回收软引用引用对象</mark>，软引用本身也是一个对象，占一定的内存空间，<mark style="background: #FF5582A6;">在软引用引用的对象都被回收后</mark>，会将软引用放入一个引用队列来释放软引用自身
- 弱引用（WeakReference）：仅有弱引用引用该对象时，在 full GC时，不论内存空间是否充足都会回收该对象，软引用本身也是一个对象，同样在弱引用引用的对象都被回收后，会将弱引用本身放入一个引用队列来释放弱引用自身
- 虚引用（PhantomReference）: 必须配合引用队列使用，主要配合 <mark style="background: #ABF7F7A6;">ByteBuffer</mark> 使用，当引用对象被回收时，虚引用会进入引用队列中，由 <mark style="background: #ABF7F7A6;">Reference Handler 线程</mark>调用虚引用相关方法释放直接内存
- 终结器引用（FinalReference）: 无需手动编码，但其内部配合引用队列使用，在垃圾回收时，终结器引用入队（被引用对象暂时没有被回收），再由 <mark style="background: #ABF7F7A6;">Finalizer 线程</mark>通过终结器引用找到被引用对象并调用它的 <mark style="background: #ABF7F7A6;">finalize</mark> 方法，第二次 GC 时才能回收被引用对象。
$$
软引用演示
$$
>使用 `-Xmx20m` 虚拟机参数将堆内存设置为 20 M, 运行以下代码看会发生什么

```java
public class Demo2_3 {  
  
    private static final int _4MB = 4 * 1024 * 1024;  
  
  
    public static void main(String[] args) throws IOException {  
        List<byte[]> list = new ArrayList<>();  
        for (int i = 0; i < 5; i++) {  
            list.add(new byte[_4MB]);  
        }  
        System.in.read();  
    }  
}
```
![[Pasted image 20240202171221.png]]
很明显发生了堆内存的溢出，因为每次循环都会向 List 中增加 4 MB 的对象，List 与 byte 数组之间是强引用关系，byte 数组不能进行垃圾回收从而导致内存溢出
>如果我们将 List 集合中存放一个软引用对象（SoftReference）用来指向 byte 数组，是否结果会不一致呢
>添加虚拟机参数 `-XX:+PrintGCDetails -verbose:gc` 用于查看垃圾回收详细信息

```java
public class Demo2_3 {  
  
    private static final int _4MB = 4 * 1024 * 1024;  
  
  
  
    public static void main(String[] args) throws IOException {  
        /*List<byte[]> list = new ArrayList<>();  
        for (int i = 0; i < 5; i++) {            list.add(new byte[_4MB]);        }*/  
        //System.in.read();       
         soft();  
  
  
    }  
  
    public static void soft() {  
        // list --> SoftReference --> byte[]  
  
        List<SoftReference<byte[]>> list = new ArrayList<>();  
        for (int i = 0; i < 5; i++) {  
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB]);  
            System.out.println(ref.get());  
            list.add(ref);  
            System.out.println(list.size());  
  
        }        System.out.println("循环结束：" + list.size());  
        for (SoftReference<byte[]> ref : list) {  
            System.out.println(ref.get());  
        }   
        
	}  
}
```
![[Pasted image 20240202172342.png]]
很明显没有发生堆内存溢出现象，原因就是在内存不足时触发了 full GC，释放了前面 4 个软连接对象，导致最后遍历 byte 数组只存活了第五个对象
>既然软引用引用的对象都被回收了，那自然软引用自身也可以被释放掉，可以配合引用队列对软引用进行回收

```java
public class Demo2_4 {  
    private static final int _4MB = 4 * 1024 * 1024;  
  
    public static void main(String[] args) {  
        List<SoftReference<byte[]>> list = new ArrayList<>();  
  
        // 引用队列  
        ReferenceQueue<byte[]> queue = new ReferenceQueue<>();  
  
        for (int i = 0; i < 5; i++) {  
            // 关联了引用队列， 当软引用所关联的 byte[]被回收时，软引用自己会加入到 queue 中去  
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB], queue);  
            System.out.println(ref.get());  
            list.add(ref);  
            System.out.println(list.size());  
        }  
        // 从队列中获取无用的 软引用对象，并移除  
        Reference<? extends byte[]> poll = queue.poll();  
        while( poll != null) {  
            list.remove(poll);  
            poll = queue.poll();  
        }  
        System.out.println("===========================");  
        for (SoftReference<byte[]> reference : list) {  
            System.out.println(reference.get());  
        }  
    }  
}
```
![[Pasted image 20240202173330.png]]
$$
弱引用演示
$$
>弱引用演示就是将前面的 `SoftReference` 变为 `WeakReference`

```java
public class Demo2_5 {  
    private static final int _4MB = 4 * 1024 * 1024;  
  
    public static void main(String[] args) {  
        //  list --> WeakReference --> byte[]  
        List<WeakReference<byte[]>> list = new ArrayList<>();  
        for (int i = 0; i < 10; i++) {  
            WeakReference<byte[]> ref = new WeakReference<>(new byte[_4MB]);  
            list.add(ref);  
            for (WeakReference<byte[]> w : list) {  
                System.out.print(w.get()+" ");  
            }  
            System.out.println();  
  
        }        System.out.println("循环结束：" + list.size());  
    }  
}
```
![[Pasted image 20240202174817.png]]

### 垃圾回收算法
$$
标记清除算法
$$
- 标记清除算法分为两个阶段：<mark style="background: #ABF7F7A6;">标记阶段</mark>和<mark style="background: #ABF7F7A6;">清除阶段</mark>
- 在标记阶段首先根据根节点遍历整个引用链，标记所有与根节点之间存在引用关系的对象，未被标记的对象就是要被垃圾回收的对象
- 在清除阶段就是将未被标记的对象进行垃圾回收
![[Pasted image 20240202180025.png]]
- 优点：速度较快
- 缺点：<mark style="background: #FF5582A6;">容易产生内存碎片</mark>，一个对象的大小大于空闲表中每一块大小，但是小于其中两块空闲分区大小之和，扫描了整个空间两次
**适用场景**：
- 存活对象较多的情况下比较高效
- 适用于年老代（即旧生代）
$$
标记整理算法
$$
- 标记整理算法和标记清除算法一样分为两个阶段，<mark style="background: #ABF7F7A6;">标记和整理阶段</mark>
- 首先也需要从根节点开始对所有可达对象做一次标记，但之后，它并不简单地清理未标记的对象，<mark style="background: #ABF7F7A6;">而是将所有的存活对象压缩到内存的一端</mark>。之后，<mark style="background: #ABF7F7A6;">清理边界外所有的空间</mark>。这种方法既<mark style="background: #FF5582A6;">避免了碎片</mark>的产生，又不需要两块相同的内存空间，因此，其性价比比较高。
- 缺点：对象内存的压缩过程比较耗时
![[Pasted image 20240202181456.png]]

$$
复制算法
$$
- 复制算法将内存空间分为两个区域：<mark style="background: #ABF7F7A6;">from 区和 to 区</mark>
- From 区用于<mark style="background: #FF5582A6;">存放所有对象</mark>，to 区域<mark style="background: #FF5582A6;">不存放任何对象</mark>，从根节点开始扫描，将所有与根节点之间存在引用关系的对象<mark style="background: #FF5582A6;">按顺序</mark>移动到 to 区域中，那么 from 区域就只剩下了要被回收的对象，就可以直接清理 from 区域中的对象，最后将 from 区域和 to 区域<mark style="background: #FF5582A6;">名称互换</mark>，下一次垃圾回收时又从 from 区域开始
- 优点：<mark style="background: #ABF7F7A6;">不会有内存碎片</mark>，<mark style="background: #ABF7F7A6;">吞吐量高</mark>，复制算法只需要遍历一次存活对象复制到 To 空间即可，比标记-整理算法少了一次遍历的过程，因而性能较好
- 缺点：内存使用效率低，每次只能使用一半的内存来给对象使用
![[Pasted image 20240202182828.png]]
**使用场景**：
- 存活对象较少的情况下比较高效
- 扫描了整个空间一次（标记存活对象并复制移动）
- 适**用于年轻代（即新生代）**：基本上 98%的对象是"朝生夕死"的，存活下来的会很少
$$
分代垃圾回收
$$
- 分代垃圾回收算法将对象分别存放在<mark style="background: #FF5582A6;">新生代</mark>和<mark style="background: #FF5582A6;">老年代</mark>中
- 新生代分为 <mark style="background: #FF5582A6;">eden 区，幸存者 from 区和幸存者 to 区</mark>，主要存放一些经常回收的对象
- 老年代主要存放一些不容易回收，生命周期较长的对象
![[Pasted image 20240203203805.png]]
- 新创建的对象存放在 eden 区中
- 如果 eden 区的内存不足时，会发生一次 `minor GC`,使用复制算法将 eden 区和幸存者 from 区中存活的对象放入幸存者 to 区，并且对象的寿命加一，清除未被标记的对象，然后交换幸存者 from 和幸存者 to
- `minor GC` 会触发 <mark style="background: #FF5582A6;">stop the world (STW)</mark>,暂停其他线程，等垃圾回收结束后，恢复用户线程
- 当幸存者区的对象寿命达到了一定阈值，对象会晋升到老年代，默认的阈值是 15
- 当老年代空间不足时，会先触发 `minor GC`,如果空间仍然不足，则会触发 `full GC`
**JVM 相关参数**

| 含义 | 参数 |
| --- | --- |
| 堆的初始大小 | -Xms |
| 堆的最大大小 | -Xmx 或-XX:MaxHeapSize=size或 |
| 新生代大小 | -Xmn或 (-XX:NewSize=size + -XX:MaxNewSize=size ) |
| 幸存者区在新生代中的比例（动态） | -XX:InitialSurvivorRatio=ratio 和-XX:+UseAdaptiveSizePolicy和 |
| 幸存者区比例 | -XX:SurvivorRatio=ratio |
| 晋升阈值 | -XX:MaxTenuringThreshold=threshold |
| 晋升详情 | -XX:+PrintTenuringDistribution |
| GC 详情情 | -XX:+PrintGCDetails -verbose:gc |
| Full GC 前 Minor GC前 | -XX:+ScavengeBeforeFullGC |

**当新生代发生 minor GC 时，将幸存的对象存入幸存者区中，如果幸存的对象要大于幸存者区的空间，那会发生什么？**
- 会将幸存的大对象直接晋升到老年代中，如果老年代剩余空间也无法容下这个对象，这时就会触发 full GC
**当新建一个对象，该对象的容量大于新生代的 eden 区，那又会怎样？**
- 如果老年代空间充裕，直接将这个对象存入老年代中，如果老年代空间不足就会触发 full GC, 如果垃圾回收之后还是容不下这个对象，那就会报出 `outOfMemoryError`
>一个子线程的内存溢出，不会导致主线程直接结束

### 垃圾回收器
$$
相关概念
$$
- **并行收集**：指多条垃圾收集线程并行工作，但此时用户线程仍处于等待状态
- **并发收集**：指用户线程与垃圾收集线程同时工作（不一定是并行的可能会交替执行）。用户程序在继续运行，而垃圾收集程序运行在另一个 CPU 上
- **吞吐量**：即 CPU 用于运行用户代码的时间与 CPU 总消耗时间的比值（吞吐量 = 运行用户代码时间 / ( 运行用户代码时间 + 垃圾收集时间 )），也就是。例如：虚拟机共运行 100 分钟，垃圾收集器花掉 1 分钟，那么吞吐量就是 99% 。

$$
串行垃圾回收器
$$
**串行**
- 单线程
- 堆内存较少，适合个人电脑
![[Pasted image 20240203215239.png]]
使用串行垃圾回收器的虚拟机参数：`-XX:+UseSerialGC=Serial + SerialOld`
当使用串行垃圾回收器时，如果发生垃圾回收，所有用户线程会在一个安全点阻塞下来，只运行一个垃圾回收线程，等垃圾回收线程执行垃圾回收完毕，所有用户线程在从安全点开始运行，<mark style="background: #FF5582A6;">可以看出，用户线程与垃圾回收线程之间是串行执行的</mark>
**安全点**：让其他线程都在这个点停下来，以免垃圾回收时移动对象地址，使得其他线程找不到被移动的对象  
因为是串行的，所以只有一个垃圾回收线程。且在该线程执行回收工作时，其他线程进入阻塞状态
**Serial 收集器**
- Serial 收集器是最基本的、发展历史最悠久的收集器
- 特点：单线程、简单高效（与其他收集器的单线程相比），<mark style="background: #FF5582A6;">采用复制算法</mark>。对于限定单个 CPU 的环境来说，Serial 收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率。收集器进行垃圾回收时，必须暂停其他所有的工作线程，直到它结束（Stop The World）！
**ParNew 收集器**
- ParNew 收集器其实就是 Serial 收集器的多线程版本
- 特点：多线程、ParNew 收集器默认开启的<mark style="background: #ABF7F7A6;">垃圾回收线程数与 CPU 的数量相同</mark>，在 CPU 非常多的环境中，可以使用 `-XX: ParallelGCThreads ` 参数来限制垃圾收集的线程数。和 Serial 收集器一样存在 Stop The World 问题
**Serial Old 收集器**
- Serial Old 是 Serial 收集器的老年代版本
- 特点：同样是单线程收集器，采用<mark style="background: #FF5582A6;">标记-整理算法</mark>
$$
吞吐量优先垃圾回收器
$$
**特点**：
- 多线程
- 堆内存较大，多核 CPU
- 让单位时间内，STW 时间最短
![[Pasted image 20240203221233.png]]

**Parallel Scavenge 收集器**
- 与吞吐量关系密切，故也称为吞吐量优先收集器
- 特点：属于<mark style="background: #FF5582A6;">新生代收集器</mark>也是采用<mark style="background: #FF5582A6;">复制算法</mark>的收集器（用到了新生代的幸存区），又是并行的多线程收集器（与 <mark style="background: #FF5582A6;">ParNew</mark> 收集器类似）
- 该收集器的目标是达到一个可控制的吞吐量。还有一个值得关注的点是：<mark style="background: #FF5582A6;">GC 自适应调节策略</mark>（与 ParNew 收集器最重要的一个区别）
- 默认的垃圾回收线程数与 CPU 数一致，所以在垃圾回收时 CPU 的利用率会达到 100%，可以通过 `-XX:ParallelGCThreads=n` 设置垃圾回收线程的数量
**GC 自适应调节策略：**
Parallel Scavenge 收集器可设置 `-XX:+UseAdptiveSizePolicy` 参数。
当开关打开时不需要手动指定新生代的大小（-Xmn）、Eden 与 Survivor 区的比例（-XX:SurvivorRation）、晋升老年代的对象年龄（-XX:PretenureSizeThreshold）等，虚拟机会根据系统的运行状况收集性能监控信息，动态设置这些参数以提供最优的停顿时间和最高的吞吐量，这种调节方式称为 GC 的自适应调节策略。
**Parallel Scavenge 收集器使用两个参数控制吞吐量：**
- `XX:MaxGCPauseMillis=ms` 控制最大的垃圾收集停顿时间（默认 200 ms）
- `XX:GCTimeRatio=rario` 直接设置吞吐量的大小, 控制单位时间内垃圾回收所占时间，比如 rario 设置为 1%，则表示 100 分钟内垃圾回收的时间要控制在 1 分钟内
**Parallel Old 收集器**
- 是 Parallel Scavenge 收集器的老年代版本
- 特点：多线程，<mark style="background: #FF5582A6;">采用标记-整理算法</mark>（老年代没有幸存区）

$$
响应时间垃圾回收器
$$
**特点**：
- 多线程
- 堆内存较大，多核 CPU
- 尽可能让单次 STW 时间最短
![[Pasted image 20240203224017.png]]
**CMS 收集器**
- Concurrent Mark Sweep，一种以获取最短回收停顿时间为目标的**老年代收集器**
- 特点：基于标记-清除算法实现。并发收集、低停顿，但是会产生内存碎片
- 应用场景：适用于注重服务的响应速度，希望系统停顿时间最短，给用户带来更好的体验等场景下。如 web 程序、b/s 服务
- **CMS 收集器的运行过程分为下列 4 步：**
	- <mark style="background: #ABF7F7A6;">初始标记</mark>：标记 GC Roots 能直接到的对象。速度很快但是仍存在 Stop The World 问题。
	- <mark style="background: #ABF7F7A6;">并发标记</mark>：使用多条并发标记线程并行执行，并与用户线程并发执行。此过程进行可达性分析，标记出所有废弃的对象，速度很慢。
	- <mark style="background: #ABF7F7A6;">重新标记</mark>：为了修正并发标记期间因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，使用多条线程并行执行，将刚才并发过程中新出现的废弃对象标出来，仍然存在 Stop The World 问题
	- <mark style="background: #ABF7F7A6;">并发清除</mark>：对标记的对象进行清除回收，清除的过程中，可能任然会有新的垃圾产生，这些垃圾就叫<mark style="background: #FF5582A6;">浮动垃圾</mark>，如果当用户需要存入一个很大的对象时，新生代放不下去，老年代由于内存碎片过多，就会退化为 serial Old 收集器，将老年代垃圾进行标记-整理，当然这也是很耗费时间的！
- CMS 收集器的内存回收过程是与用户线程一起并发执行的，可以搭配 ParNew 收集器（多线程，新生代，复制算法）与 Serial Old 收集器（单线程，老年代，标记-整理算法）使用
**参数**
- `-XX:+UseConcMarkSweepGC ~ -XX:+UseParNewGC ~ SerialOld`：使用 CMS, ParNew, Serial Old 垃圾回收器配合一起使用
- `-XX:ParallelGCThreads=n ~ -XX:ConcGCThreads=threads`：设置并行垃圾回收线程数和并发垃圾回收线程数
- `-XX:CMSInitiatingOccupancyFraction=percent`：在老年代中进行垃圾回收的时机，采用 CMS 垃圾回收器会产生一些浮动垃圾，所以要预留一些内存来存放这些浮动垃圾，可以设置当内存占用达到一定百分比时就触发垃圾回收
- `XX:+CMSScavengeBeforeRemark`：在重新标记阶段，一些新生代的对象可能会引用老年代的对象，通过新生代对象扫描一遍老年代对象做可达性分析，这样对性能的影响比较大，因为新生代的对象较多而且容易被回收掉，相当于做了一些无用功，这个参数就是在重新标记之前，对新生代做一次垃圾回收工作，垃圾回收后存活的对象少了，那扫描的对象也就变少了，减轻重新标记时的压力

$$
G1垃圾回收器
$$
**定义**：`Garbage First`
**适用场景**
- 同时注重吞吐量和低延迟
- 超大堆内存，会将堆内存划分为多个大小相等的区域
- 整体上是标记整理算法，两个区域之间是复制算法
**相关参数**
- `-XX:+UseG1GC`: 开启 G 1 垃圾回收器，JDK 9 之前不是默认开启的，JDK 9 之后才作为默认的垃圾回收器
- `-XX:G1HeapRegionSize=size`: G 1 垃圾回收器会将堆内存划分为多个大小相等的 region 区，这个是设置 region 区域的大小
- `-XX:MaxGCPauseMillis=time`: 控制最大的垃圾收集停顿时间（默认 200 ms）
**G 1 垃圾回收阶段**
![[Pasted image 20240205132654.png]]
`Young Collection`: 对新生代垃圾收集
`Young Collection+Concurrent Mark`:如果老年代内存到达一定的阈值了，新生代垃圾收集同时会执行一些并发的标记
`Mixed Collection`:会对新生代 + 老年代 + 幸存区等进行混合收集，然后收集结束，会重新进入新生代收集。

**Young Collection**
- 分代是按对象的生命周期划分，分区则是将堆空间划分连续几个不同小区间，每一个小区间独立回收，可以控制一次回收多少个小区间，方便控制 GC 产生的停顿时间！
- 新生代收集会产生 STW ！
- 新生代中的 eden 区如果进行垃圾回收，会采用复制算法，将存活的对象存入幸存者区中
- 堆中的每个 region 区可以单独作为 eden 区，幸存者区，老年代
![[20210210122339138.gif]]
**Young Collection+CM*
- 在新生代垃圾回收时就会进行初始标记，标记出所有 GC Root
- 当老年代的空间达到堆空间比例的阈值时，就会进行并发标记，不会发生 STW, 阈值由 `-XXInitiatingHeapOccupancyPercent=percent` (默认 45%)设置
![[Pasted image 20240205134230.png]]

**Mix Collection**
- 对 eden 区，幸存者区，老年代进行全面的回收
- 最终标记会 STW, 在并发标记时用户线程未被阻塞，可能产生新的垃圾回收对象
- 拷贝存活对象会 STW
- `-XX:MaxGCPauseMills=xxms`: 用于指定最长的暂停时间
**为什么有的老年代存活对象被拷贝了，有的没有？**
- 因为指定了最大停顿时间，如果对所有老年代都进行回收，耗时可能过高。为了保证时间不超过设定的停顿时间，会回收最有价值的老年代（回收后，能够得到更多内存）
![[Pasted image 20240205142152.png]]

$$
full GC
$$
- 所有垃圾回收器在新生代发生的垃圾回收机制都是 `minor GC`，采用复制算法
- 在串行和并行垃圾回收器中，老年代的垃圾回收都是 `full GC`
- 在 CMS 和 G 1 垃圾回收器中如果老年代的内存不足时（老年代内存达到堆内存占比的阈值）
	- 如果垃圾产生的速度小于垃圾回收的速度，不会触发 `full GC`
	- 如果垃圾产生的速度大于垃圾回收的素的，则触发 `full GC`,然后退化为 serial Old 收集器阻塞用户线程进行垃圾回收，采用标记整理算法
$$
Young Collection跨代引用
$$
**如果新生代想找根对象，但是很多根对象都在老年代中，这时就需要遍历老年代找新生代中的跟对象吗**
>采用卡表的技术，将老年代区域在进行一次划分，换分为一个个 card, 这样就不用遍历整个老年代了，只需要关注脏卡的区域
- 卡表与 Remembered Set
- Remembered Set 存在于 E 中，用于保存新生代对象对应的脏卡
- 脏卡：O 被划分为多个区域（一个区域 512 K），如果该区域引用了新生代对象，则该区域被称为脏卡
在引用变更时通过 `post-write barried + dirty card queue`
`concurrent refinement threads` 更新 Remembered Set
![[Pasted image 20240205144129.png]]

$$
Remark
$$
**重新标记阶段**
- 黑色：已被处理，需要保留的
- 灰色：正在处理中的
- 白色：还未处理的
![[Pasted image 20240205145644.png]]

**为什么需要重新标记**
在并发标记过程中，假设处理到 A 对象时，还未引用 C 对象，在并发标记处理还没结束之前，用户线程将 A 对象中的属性引用 C 对象，这时 C 对象就不应该被清理，因为与 A 对象产生了依赖关系
- 之前 C 对象未被引用，这时 A 引用了 C, 就会给 C 加一个写屏障，写屏障的指令会执行，将 C 放入一个队列当中，并将 C 变为正在处理的状态
- 在并发标记阶段结束以后，重新标记阶段会 STW ，然后将放在该队列中的对象重新处理，发现有强引用引用它，就会处理它，由灰色变成黑色。
![[Pasted image 20240205150359.png]]
![[Pasted image 20240205150406.png]]

$$
JDK8u20字符串去重
$$
**过程**
- 将所有新分配的字符串（底层是 char[] ）放入一个队列
- 当新生代回收时，G 1 并发检查是否有重复的字符串
- 如果字符串的值一样，就让他们引用同一个字符数组对象
- 注意，其与 String.Intern () 的区别
	- String.Intern () 关注的是字符串对象
	- 字符串去重关注的是 char[]
	- 在 JVM 内部，使用了不同的字符串标
```java
String a=new String("hello");
String b=new String("hello");
```
以上 a, b 对象底层都引用了一个 final 的字符数组
>那你可能会问 a 和 b 对象相等吗？
>肯定不相等 a 和 b 是在堆区中不同的字符串对象，只不过字符串的实现都引用了同一个字符数组

![[JVM 2024-02-05 15.17.46.excalidraw]]
**优点和缺点**
- 节省了大量内存
- 新生代回收时间略微增加，导致略微多占用 CPU（在垃圾回收时，会去看有没有相同的字符串，如果有则生成一个字符数组，让这些相同字符串对象指向同一个字符数组，这时就可以清理掉那些重复的字符串了）
- 使用 `-XX:+UseStringDeduplication` 设置

$$
JDK 8u40 并发标记类卸载
$$
在并发标记阶段结束以后，就能知道哪些类不再被使用。如果一个类加载器的所有类都不在使用，则卸载它所加载的所有类
$$
JDK 8u60 回收巨型对象
$$
- 一个对象大于 region 的一半时，就称为巨型对象
- G1不会对巨型对象进行拷贝
- 回收时被优先考虑
- G 1 会跟踪老年代所有 incoming 引用，如果老年代 incoming 引用为 0 的巨型对象就可以在新生代垃圾回收时处理掉
![[Pasted image 20240205153228.png]]
$$
JDK 9 并发标记起始时间的调整
$$
- 并发标记必须在堆空间占满前完成，否则退化为 Full GC
- JDK 9 之前需要使用 -XX:InitiatingHeapOccupancyPercent
- JDK 9 可以动态调整
	- `-XX: InitiatingHeapOccupancyPercent` 用来设置初始值
	- 进行数据采样并动态调整
	- 总会添加一个安全的空挡空间
### 垃圾回收调优
>查看虚拟机参数命令：`D:\JavaJDK1.8\bin\java  -XX:+PrintFlagsFinal -version | findstr "GC"`

$$
调优领域
$$
- 内存
- 锁竞争
- CPU 占用
- Io
- GC
$$
确定目标
$$
低延迟或高吞吐量，选择合适的垃圾回收器和 JVM
- CMS, G 1, ZGC: 低延迟
- Parallel GC: 高吞吐量
- Zing
$$
最快的GC
$$
首先排除减少因为自身编写的代码而引发的内存问题
**查看 Full GC 前后的内存占用，考虑以下几个问题**
- 加载到 JVM 中的数据是否太多
	- `resultSet = statement.executeQuery(“select * from 大表 limit n”)`，在实际项目中，千万不能直接全表查询，如果全表查询的话，会将表中所有数据加载到 JVM 中，最好加一个范围
- 对象表示是否太过臃肿
	- 对象图
	- 能用基本类型尽量用基本类型，包装类型内存占用较大
- 是否存在内存泄露
	- Static Map map……
	- 软引用
	- 弱引用
	- 第三方缓存实现
$$
新生代内存调优
$$
**新生代的特点**
- 所有 new 对象的操作分配内存都是非常廉价的
	- TLAB thread-lcoal allocation buffer
- 死亡对象回收零代价
- 大部分对象用过即死
- Minor GC 时间远小于 Full GC
**新生代内存越大越好吗**
- 不是
	- 新生代内存太小：频繁触发 Minor GC ，会 STW ，会使得吞吐量下降
	- 新生代内存太大：老年代内存占比有所降低，会更频繁地触发 Full GC。而且触发 Minor GC 时，清理新生代所花费的时间会更长
- 新生代内存设置为内容纳[并发量*(请求-响应)]的数据为宜

幸存区需要能够保存 <mark style="background: #ABF7F7A6;">当前活跃对象+需要晋升的对象</mark>
晋升阈值配置得当，让长时间存活的对象尽快晋升
>设置晋升老年代的阈值参数：`-XX:MaxTenuringThreshold=threshold`

$$
老年代调优
$$
**以 CSM 为例**
- CMS 的老年代内存越大越好
- 先尝试不做调优，如果没有 Full GC 那么已经，否者先尝试调优新生代。
- 观察发现 Full GC 时老年代内存占用，将老年代内存预设调大 1/4 ~ 1/3
- 设置老年代发生垃圾回收时所占老年代空间的占比：`-XX:CMSInitiatingOccupancyFraction=percent`
$$
案例
$$
案例1：Full GC 和 Minor GC 频繁  
案例2：请求高峰期发生 Full GC，单次暂停时间特别长（CMS）  
案例3：老年代充裕情况下，发生 Full GC（jdk1.7）
## 类加载与字节码技术
![[Pasted image 20240207105518.png]]
### 类文件结构
通过 `javac` 编译后的 java 文件，会生成一个 class 文件
以下是十六进制字节码文件：
```
0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09 
0000020 00 16 00 17 08 00 18 0a 00 19 00 1a 07 00 1b 07 
0000040 00 1c 01 00 06 3c 69 6e 69 74 3e 01 00 03 28 29 
0000060 56 01 00 04 43 6f 64 65 01 00 0f 4c 69 6e 65 4e 
0000100 75 6d 62 65 72 54 61 62 6c 65 01 00 12 4c 6f 63 
0000120 61 6c 56 61 72 69 61 62 6c 65 54 61 62 6c 65 01 
0000140 00 04 74 68 69 73 01 00 1d 4c 63 6e 2f 69 74 63 
0000160 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 6f 
0000200 57 6f 72 6c 64 3b 01 00 04 6d 61 69 6e 01 00 16 
0000220 28 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72 
0000240 69 6e 67 3b 29 56 01 00 04 61 72 67 73 01 00 13 
0000260 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 
0000300 6e 67 3b 01 00 10 4d 65 74 68 6f 64 50 61 72 61 
0000320 6d 65 74 65 72 73 01 00 0a 53 6f 75 72 63 65 46 
0000340 69 6c 65 01 00 0f 48 65 6c 6c 6f 57 6f 72 6c 64
0000360 2e 6a 61 76 61 0c 00 07 00 08 07 00 1d 0c 00 1e 
0000400 00 1f 01 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64 
0000420 07 00 20 0c 00 21 00 22 01 00 1b 63 6e 2f 69 74 
0000440 63 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 
0000460 6f 57 6f 72 6c 64 01 00 10 6a 61 76 61 2f 6c 61 
0000500 6e 67 2f 4f 62 6a 65 63 74 01 00 10 6a 61 76 61 
0000520 2f 6c 61 6e 67 2f 53 79 73 74 65 6d 01 00 03 6f 
0000540 75 74 01 00 15 4c 6a 61 76 61 2f 69 6f 2f 50 72 
0000560 69 6e 74 53 74 72 65 61 6d 3b 01 00 13 6a 61 76 
0000600 61 2f 69 6f 2f 50 72 69 6e 74 53 74 72 65 61 6d 
0000620 01 00 07 70 72 69 6e 74 6c 6e 01 00 15 28 4c 6a 
0000640 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 6e 67 3b 
0000660 29 56 00 21 00 05 00 06 00 00 00 00 00 02 00 01 
0000700 00 07 00 08 00 01 00 09 00 00 00 2f 00 01 00 01 
0000720 00 00 00 05 2a b7 00 01 b1 00 00 00 02 00 0a 00 
0000740 00 00 06 00 01 00 00 00 04 00 0b 00 00 00 0c 00 
0000760 01 00 00 00 05 00 0c 00 0d 00 00 00 09 00 0e 00 
0001000 0f 00 02 00 09 00 00 00 37 00 02 00 01 00 00 00 
0001020 09 b2 00 02 12 03 b6 00 04 b1 00 00 00 02 00 0a 
0001040 00 00 00 0a 00 02 00 00 00 06 00 08 00 07 00 0b 
0001060 00 00 00 0c 00 01 00 00 00 09 00 10 00 11 00 00 
0001100 00 12 00 00 00 05 01 00 10 00 00 00 01 00 13 00 
0001120 00 00 02 00 14
```
根据 JVM 规范，类文件结构如下：
```
u4 			   magic
u2             minor_version;    
u2             major_version;    
u2             constant_pool_count;    
cp_info        constant_pool[constant_pool_count-1];    
u2             access_flags;    
u2             this_class;    
u2             super_class;   
u2             interfaces_count;    
u2             interfaces[interfaces_count];   
u2             fields_count;    
field_info     fields[fields_count];   
u2             methods_count;    
method_info    methods[methods_count];    
u2             attributes_count;    
attribute_info attributes[attributes_count];
```
$$
魔数
$$
- 魔数就是文件头，代表其<mark style="background: #FF5582A6;">文件的类型</mark>，通常一个文件是否是 `txt,jpg` 等格式的文件是由魔数来决定的，而不是文件的后缀名
- 魔数就是字节码文件的前 4 个字节
- 例如：`0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09`，`ca fe ba be` 就是魔数，代表一个 `.class` 字节码文件，不同的文件类型有不同的魔数
$$
版本
$$
u2 minor_version;  
u2 major_version;  
0000000 ca fe ba be <mark style="background: #FF5582A6;">00 00 00</mark> <mark style="background: #FF5582A6;">34</mark> 00 23 0a 00 06 00 15 09  
00 00 00 34：34H（16进制） = 52（10进制），代表 JDK8
$$
常量池
$$

| 常量类型 | 所代表的十进制值 |
| --- | --- |
| CONSTANT_Class | 7 |
| CONSTANT_Fieldref | 9 |
| CONSTANT_Methodref | 10 |
| CONSTANT_InterfaceMethodref | 11 |
| CONSTANT_String | 8 |
| CONSTANT_Integer | 3 |
| CONSTANT_Float | 4 |
| CONSTANT_Long | 5 |
| CONSTANT_Double | 6 |
| CONSTANT_NameAndType | 12 |
| CONSTANT_Utf8 | 1 |
| CONSTANT_MethodHandle | 15 |
| CONSTANT_MethodType | 16 |
| CONSTANT_InvokeDynamic | 18 |
8~9 字节，表示常量池长度，00 23 （35） 表示常量池有 #1 ~ #34 ，注意 #0 项不计入，也没有值
### 字节码指令
$$
入门
$$
接着上一节，研究一下两组字节码指令，一个是
`public cn.itcast.jvm.t5.HelloWorld();` 构造方法的字节码指令
```
2a b7 00 01 b1
```
1. 2a => aload_0 加载 slot 0 的局部变量，即 this，做为下面的 `invokespecial` 构造方法调用的参数
2. b7 => invokespecial 预备调用构造方法，哪个方法呢？
3. 00 01 引用常量池中 #1 项，即 `【 Method java/lang/Object."<init>": () V 】`
4. b1 表示返回
另一个是 ` public static void main(java.lang.String[]);` 主方法的字节码指令
```
b2 00 02 12 03 b6 00 04 b1
```
1. b2 => getstatic 用来加载静态变量，哪个静态变量呢？
2. 00 02 引用常量池中 #2 项，即【Field java/lang/System.out:Ljava/io/PrintStream;】
3. 12 => ldc 加载参数，哪个参数呢？
4. 03 引用常量池中 #3 项，即 【String hello world】
5. b6 => invokevirtual 预备调用成员方法，哪个方法呢？
6. 00 04 引用常量池中 #4 项，即【Method java/io/PrintStream.println:(Ljava/lang/String;)V】
7. B 1 表示返回
$$
javap
$$
Java 中提供了 javap 工具来反编译 class 文件
`javap -v HelloWorld.class`
```
[root@localhost ~]# javap -v HelloWorld.class
Classfile /root/HelloWorld.class
Last modified Jul 7, 2019; size 597 bytes
MD5 checksum 361dca1c3f4ae38644a9cd5060ac6dbc
Compiled from "HelloWorld.java"
public class cn.itcast.jvm.t5.HelloWorld
minor version: 0
major version: 52
flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
#1 = Methodref #6.#21 // java/lang/Object."<init>":()V
#2 = Fieldref #22.#23 //
java/lang/System.out:Ljava/io/PrintStream;
#3 = String #24 // hello world
#4 = Methodref #25.#26 // java/io/PrintStream.println:
(Ljava/lang/String;)V
#5 = Class #27 // cn/itcast/jvm/t5/HelloWorld
#6 = Class #28 // java/lang/Object
#7 = Utf8 <init>
#8 = Utf8 ()V
#9 = Utf8 Code
#10 = Utf8 LineNumberTable
#11 = Utf8 LocalVariableTable
#12 = Utf8 this
#13 = Utf8 Lcn/itcast/jvm/t5/HelloWorld;
#14 = Utf8 main
#15 = Utf8 ([Ljava/lang/String;)V
#16 = Utf8 args
#17 = Utf8 [Ljava/lang/String;
#18 = Utf8 MethodParameters
#19 = Utf8 SourceFile
#20 = Utf8 HelloWorld.java
#21 = NameAndType #7:#8 // "<init>":()V
#22 = Class #29 // java/lang/System
#23 = NameAndType #30:#31 // out:Ljava/io/PrintStream;
#24 = Utf8 hello world
#25 = Class #32 // java/io/PrintStream
#26 = NameAndType #33:#34 // println:(Ljava/lang/String;)V
#27 = Utf8 cn/itcast/jvm/t5/HelloWorld
#28 = Utf8 java/lang/Object
#29 = Utf8 java/lang/System
#30 = Utf8 out
#31 = Utf8 Ljava/io/PrintStream;
#32 = Utf8 java/io/PrintStream
#33 = Utf8 println
#34 = Utf8 (Ljava/lang/String;)V
{
	public cn.itcast.jvm.t5.HelloWorld();
	descriptor: ()V
	flags: ACC_PUBLIC
	Code:
	stack=1, locals=1, args_size=1
	0: aload_0
	1: invokespecial #1 // Method java/lang/Object."
	<init>":()V
	4: return
	LineNumberTable:
	line 4: 0
	LocalVariableTable:
	Start Length Slot Name Signature
	0 5 0 this Lcn/itcast/jvm/t5/HelloWorld;
	public static void main(java.lang.String[]);
	descriptor: ([Ljava/lang/String;)V
	flags: ACC_PUBLIC, ACC_STATIC
	Code:
	stack=2, locals=1, args_size=1
	0: getstatic #2 // Field
	java/lang/System.out:Ljava/io/PrintStream;
	3: ldc #3 // String hello world
	5: invokevirtual #4 // Method
	java/io/PrintStream.println:(Ljava/lang/String;)V
	8: return
	LineNumberTable:
	line 6: 0
	line 7: 8
	LocalVariableTable:
	Start Length Slot Name Signature
	0 9 0 args [Ljava/lang/String;
	MethodParameters:
	Name Flags
	args
}
```
$$
图解方法执行流程
$$
**原始代码**
```java
public class Demo3_1 {    
	public static void main(String[] args) {        
		int a = 10;        
		int b = Short.MAX_VALUE + 1;        
		int c = a + b;        
		System.out.println(c);   
    } 
}
```
**编译后的字节码文件**
```
[root@localhost ~]# javap -v Demo3_1.class
Classfile /root/Demo3_1.class
Last modified Jul 7, 2019; size 665 bytes
MD5 checksum a2c29a22421e218d4924d31e6990cfc5
Compiled from "Demo3_1.java"
public class cn.itcast.jvm.t3.bytecode.Demo3_1
minor version: 0
major version: 52
flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
#1 = Methodref #7.#26 // java/lang/Object."<init>":()V
#2 = Class #27 // java/lang/Short
#3 = Integer 32768
#4 = Fieldref #28.#29 //
java/lang/System.out:Ljava/io/PrintStream;
#5 = Methodref #30.#31 // java/io/PrintStream.println:(I)V
#6 = Class #32 // cn/itcast/jvm/t3/bytecode/Demo3_1
#7 = Class #33 // java/lang/Object
#8 = Utf8 <init>
#9 = Utf8 ()V
#10 = Utf8 Code
#11 = Utf8 LineNumberTable
#12 = Utf8 LocalVariableTable
#13 = Utf8 this
#14 = Utf8 Lcn/itcast/jvm/t3/bytecode/Demo3_1;
#15 = Utf8 main
#16 = Utf8 ([Ljava/lang/String;)V
#17 = Utf8 args
#18 = Utf8 [Ljava/lang/String;
#19 = Utf8 a
#20 = Utf8 I
#21 = Utf8 b
#22 = Utf8 c
#23 = Utf8 MethodParameters
#24 = Utf8 SourceFile
#25 = Utf8 Demo3_1.java
#26 = NameAndType #8:#9 // "<init>":()V
#27 = Utf8 java/lang/Short
#28 = Class #34 // java/lang/System
#29 = NameAndType #35:#36 // out:Ljava/io/PrintStream;
#30 = Class #37 // java/io/PrintStream
#31 = NameAndType #38:#39 // println:(I)V
#32 = Utf8 cn/itcast/jvm/t3/bytecode/Demo3_1
#33 = Utf8 java/lang/Object
#34 = Utf8 java/lang/System
#35 = Utf8 out
#36 = Utf8 Ljava/io/PrintStream;
#37 = Utf8 java/io/PrintStream
#38 = Utf8 println
#39 = Utf8 (I)V
{
	public cn.itcast.jvm.t3.bytecode.Demo3_1();
	descriptor: ()V
	flags: ACC_PUBLIC
	Code:
	stack=1, locals=1, args_size=1
	0: aload_0
	1: invokespecial #1 // Method java/lang/Object."
	<init>":()V
	4: return
	LineNumberTable:
	line 6: 0
	LocalVariableTable:
	Start Length Slot Name Signature
	0 5 0 this Lcn/itcast/jvm/t3/bytecode/Demo3_1;
	public static void main(java.lang.String[]);
	descriptor: ([Ljava/lang/String;)V
	flags: ACC_PUBLIC, ACC_STATIC
	Code:
	stack=2, locals=4, args_size=1
	0: bipush 10
	2: istore_1
	3: ldc #3 // int 32768
	5: istore_2
	6: iload_1
	7: iload_2
	8: iadd
	9: istore_3
	10: getstatic #4 // Field
	java/lang/System.out:Ljava/io/PrintStream;
	13: iload_3
	14: invokevirtual #5 // Method
	java/io/PrintStream.println:(I)V
	17: return
	LineNumberTable:
	line 8: 0
	line 9: 3
	line 10: 6
	line 11: 10
	line 12: 17
	LocalVariableTable:
	Start Length Slot Name Signature
	0 18 0 args [Ljava/lang/String;
	3 15 1 a I
	6 12 2 b I
	10 8 3 c I
	MethodParameters:
	Name Flags
	args
}
```
**在类加载时将常量池中的信息载入方法区的运行时常量池**
![[Pasted image 20240207123113.png]]
**方法的字节码指令载入方法区**
![[Pasted image 20240207123232.png]]
**main 线程开始运行，分配栈帧内存**
>从上面的字节码指令可以看出，main 方法的操作数栈的容量为 2（`stack=2`）, 局部变量的大小为 4（`local=4`）

![[Pasted image 20240207123454.png]]
**执行引擎开始执行字节码**
`bipush 10`: 将一个 byte 压入操作数栈（长度会补齐 4 个字节），类似的指令还有：
- `sipush`: 将一个 short 压入操作数栈
- `ldc`: 将一个 int 压入操作数栈
- `ldc2_w`: 将一个 long 压入操作数栈（分两次压入，因为 long 是 8 个字节）
- 这里小的数值都是和字节码指令存在一起，**超过 short 范围的数字存入了常量池**
![[Pasted image 20240207130640.png]]
`istore 1`: 将操作数栈的栈顶元素弹出，放入局部变量表的 1 号槽位
![[Pasted image 20240207130752.png]]
`ldc #3`: 将常量池中地址编号为 3 的一个 int 类型的数值放入操作数栈中
读取运行时常量池中 #3 ，即 32768 (超过 short 最大值范围的数会被放到运行时常量池中)，将其加载到操作数栈中  
注意 Short.MAX_VALUE 是 32767，所以 32768 = Short.MAX_VALUE + 1 实际是在编译期间计算好的。
![[Pasted image 20240207131714.png]]
`istore 2`: 将操作数栈顶元素（32768）弹出，放入局部变量 2 号槽
![[Pasted image 20240207131818.png]]
`iload 1,iload 2`: 将局部变量 1 号槽和 2 号槽的元素分别放入操作数栈，因为只能在操作数栈中执行运算操作
![[Pasted image 20240207132036.png]]
`iadd`: 将操作数栈的前两个元素弹出栈并相加，将结果压入操作数栈中
![[Pasted image 20240207132335.png]]
`istore 3`: 将上面相加的结果（32778）弹出放入局部变量表的 3 号槽位
![[Pasted image 20240207132516.png]]
`getstatic #4`:在运行时常量池中找到 #4 ，发现是一个对象，在堆内存中找到该对象，并将其引用放入操作数栈中
![[Pasted image 20240207132740.png]]
![[Pasted image 20240207132844.png]]
`iload 3`: 将局部变量表中 3 号槽位压入操作数栈中
![[Pasted image 20240207133006.png]]
`invokevirtual #5`:找到常量池 #5 项，定位到方法区 java/io/PrintStream.println:(I)V 方法
生成新的栈帧（分配 locals、stack 等）
传递参数，执行新栈帧中的字节码
![[Pasted image 20240207133207.png]]
执行完毕，弹出栈帧  
清除 main 操作数栈内容
![[Pasted image 20240207133230.png]]
`return`: 完成 main 方法的调用，弹出 main 栈帧，程序结束
$$
练习1：分析i++
$$
**源码**
```java
package cn.itcast.jvm.t3.bytecode;
/**
* 从字节码角度分析 a++ 相关题目
*/
public class Demo3_2 {
	public static void main(String[] args) {
		int a = 10;
		int b = a++ + ++a + a--;
		System.out.println(a);
		System.out.println(b);
	}
}
```
**字节码**
```
public static void main(java.lang.String[]);
descriptor: ([Ljava/lang/String;)V
flags: (0x0009) ACC_PUBLIC, ACC_STATIC
Code:
stack=2, locals=3, args_size=1
0: bipush 10
2: istore_1
3: iload_1
4: iinc 1, 1
7: iinc 1, 1
10: iload_1
11: iadd
12: iload_1
13: iinc 1, -1
16: iadd
17: istore_2
18: getstatic #2 // Field
java/lang/System.out:Ljava/io/PrintStream;
21: iload_1
22: invokevirtual #3 // Method
java/io/PrintStream.println:(I)V
25: getstatic #2 // Field
java/lang/System.out:Ljava/io/PrintStream;
28: iload_2
29: invokevirtual #3 // Method
java/io/PrintStream.println:(I)V
32: return
LineNumberTable:
line 8: 0
line 9: 3
line 10: 18
line 11: 25
line 12: 32
LocalVariableTable:
Start Length Slot Name Signature
0 33 0 args [Ljava/lang/String;
3 30 1 a I
18 15 2 b I
```
**注意**：
- 指令 `iinc` 是直接在局部变量表上进行运算
- `a++` 和 `++a` 的区别是先执行 `iload` 还是 `iinc`
	- `a++` 是先执行 `iload`,再执行 `iinc`,也就是先加载到操作数栈中，然后在对局部变量中的数据做自增
	- `++a` 是先执行 `iinc`,再执行 `iload`,也就是先对局部变量表中的数据做自增，然后再加载到操作数栈中
**执行流程**
`bipush 10`: 将 10 放入操作数栈中
![[Pasted image 20240207134630.png]]
`istore_1`: 将操作数栈栈顶元素弹出，放入局部变量表 1 号槽位
![[Pasted image 20240207134727.png]]
`iload_1`: 将局部变量表 1 号槽位放入操作数栈中（a++操作，先入操作数栈中）
![[Pasted image 20240207134817.png]]
`iinc 1,1`: 将局部变量表中的 1 号槽位加 1（a++已经执行完毕）
![[Pasted image 20240207135034.png]]
`iinc 1,1`: 将局部变量中 1 号槽位加 1（++a 是先自增，然后再放入操作数栈中）
![[Pasted image 20240207135254.png]]
`iload_1`: 将局部变量表中 1 号槽位放入操作数栈中，后序执行加法操作（++a 操作已经执行完毕）
![[Pasted image 20240207135404.png]]
`iadd`: 将操作数栈中顶部两个元素弹出相加，再放入栈中
![[Pasted image 20240207135507.png]]
`iload_1`: 将局部变量表中 1 号槽位放入操作数栈中（开始执行 a--操作）
![[Pasted image 20240207135714.png]]
`iinc 1,-1`: 将局部变量中 1 号槽位的数值加上 -1（a--操作已经执行完毕）
![[Pasted image 20240207135803.png]]
`iadd`: 再次将操作数栈中顶部两个元素相加
![[Pasted image 20240207135845.png]]
`istore_2`: 最后将结果保存至局部变量表的 2 号槽位
![[Pasted image 20240207135938.png]]

$$
条件判断指令
$$
- Byte，short，char 都会按 int 比较，因为操作数栈都是 4 字节
- `goto` 指令 用来进行跳转到指定行号的字节码
**源码**
```java
public class Demo3_3 {
	public static void main(String[] args) {
		int a = 0;
		if(a == 0) {
			a = 10;
		} else {
			a = 20;
		}
	}
}
```
**字节码**
```
0: iconst_0
1: istore_1
2: iload_1
3: ifne 12
6: bipush 10
8: istore_1
9: goto 15
12: bipush 20
14: istore_1
15: return
```
- 数值范围在 0~5 之间用 `iconst` 指令加载到操作数栈中
- `ifne 12`: 如果栈顶元素不等于 0，则跳转到 12 位置编号的指令，`bipush 20`,将 20 压入操作数栈，再弹出覆盖掉原来 1 号槽位的局部变量表，相当于重新给 a 赋值
- 如果相等，就执行 `bipush 10`,再将 10 覆盖掉原来局部变量表中的值
$$
循环控制指令
$$
**源码**
```java
public class Demo3_4 {
	public static void main(String[] args) {
		int a = 0;
		while (a < 10) {
			a++;
		}
	}
}
```
**字节码**
```
0: iconst_0
1: istore_1
2: iload_1
3: bipush 10
5: if_icmpge 14 //将操作数栈栈顶两个元素进行比较，如果相等则跳转到位置编号为14的字节码指令
8: iinc 1, 1
11: goto 2
14: return
```
$$
练习：判断结果
$$
**源码**
```java
public class Demo3_6_1 {
	public static void main(String[] args) {
		int i = 0;
		int x = 0;
		while (i < 10) {
			x = x++;
			i++;
		}
		System.out.println(x); // 结果是 0
	}
}
```
为什么最终的 x 结果为 0 呢？ 通过分析字节码指令即可知晓
```
Code:
     stack=2, locals=3, args_size=1	// 操作数栈分配2个空间，局部变量表分配 3 个空间
        0: iconst_0	// 准备一个常数 0
        1: istore_1	// 将常数 0 放入局部变量表的 1 号槽位 i = 0
        2: iconst_0	// 准备一个常数 0
        3: istore_2	// 将常数 0 放入局部变量的 2 号槽位 x = 0	
        4: iload_1		// 将局部变量表 1 号槽位的数放入操作数栈中
        5: bipush        10	// 将数字 10 放入操作数栈中，此时操作数栈中有 2 个数
        7: if_icmpge     21	// 比较操作数栈中的两个数，如果下面的数大于上面的数，就跳转到 21 。这里的比较是将两个数做减法。因为涉及运算操作，所以会将两个数弹出操作数栈来进行运算。运算结束后操作数栈为空
       10: iload_2		// 将局部变量 2 号槽位的数放入操作数栈中，放入的值是 0 
       11: iinc          2, 1	// 将局部变量 2 号槽位的数加 1 ，自增后，槽位中的值为 1 
       14: istore_2	//将操作数栈中的数放入到局部变量表的 2 号槽位，2 号槽位的值又变为了0
       15: iinc          1, 1 // 1 号槽位的值自增 1 
       18: goto          4 // 跳转到第4条指令
       21: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       24: iload_2
       25: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
       28: return
```
$$
构造方法
$$
**cinit()V**
```java
public class Code_12_CinitTest {
	static int i = 10;

	static {
		i = 20;
	}

	static {
		i = 30;
	}

	public static void main(String[] args) {
		System.out.println(i); // 30
	}
}
```
编译器会按从上至下的顺序，收集所有 static 静态代码块和静态成员赋值的代码，合并为一个特殊的方法 cinit()V ：
```
stack=1, locals=0, args_size=0
         0: bipush        10
         2: putstatic     #3                  // Field i:I
         5: bipush        20
         7: putstatic     #3                  // Field i:I
        10: bipush        30
        12: putstatic     #3                  // Field i:I
        15: return
```
**init()V**
```java
public class Code_13_InitTest {

    private String a = "s1";

    {
        b = 20;
    }

    private int b = 10;

    {
        a = "s2";
    }

    public Code_13_InitTest(String a, int b) {
        this.a = a;
        this.b = b;
    }

    public static void main(String[] args) {
        Code_13_InitTest d = new Code_13_InitTest("s3", 30);
        System.out.println(d.a);
        System.out.println(d.b);
    }

}
```
编译器会按从上至下的顺序，收集所有实例代码块和成员变量赋值的代码，形成新的构造方法，但原始构造方法总是在最后执行
```
Code:
     stack=2, locals=3, args_size=3
        0: aload_0
        1: invokespecial #1                  // Method java/lang/Object."<init>":()V
        4: aload_0
        5: ldc           #2                  // String s1
        7: putfield      #3                  // Field a:Ljava/lang/String;
       10: aload_0
       11: bipush        20
       13: putfield      #4                  // Field b:I
       16: aload_0
       17: bipush        10
       19: putfield      #4                  // Field b:I
       22: aload_0
       23: ldc           #5                  // String s2
       25: putfield      #3                  // Field a:Ljava/lang/String;
       // 原始构造方法在最后执行
       28: aload_0
       29: aload_1
       30: putfield      #3                  // Field a:Ljava/lang/String;
       33: aload_0
       34: iload_2
       35: putfield      #4                  // Field b:I
       38: return
```







