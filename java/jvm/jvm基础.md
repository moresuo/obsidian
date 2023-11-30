***
## 初识 JVM
<mark style="background: #FFB86CA6;">什么是 JVM?</mark>
- JVM 全称是 Java Virtual Machine，中文译名 Java 虚拟机
- JVM 本质上是一个运行在计算机上的程序，他的职责是运行 Java 字节码文件
![[Pasted image 20231120165720.png]]
<mark style="background: #FFB86CA6;">JVM 有什么功能？</mark>
- 解释和运行：<mark style="background: #ABF7F7A6;">对字节码文件中的指令，实时解释成机器码，让计算机执行</mark>
- 内存管理：<mark style="background: #ABF7F7A6;">自动为对象、方法分配内存，自动的垃圾回收机制，回收不在使用的对象</mark>
- 即时编译：<mark style="background: #ABF7F7A6;">对热点代码进行优化，提升执行效率，实现跨平台性</mark>
<mark style="background: #FFB86CA6;">常见的 JVM 有哪些？</mark>
![[Pasted image 20231120170327.png]]
## 字节码文件详解
<mark style="background: #FFB86CA6;">JVM 由哪些部分组成？</mark>
![[Pasted image 20231120171950.png]]
### 如何打开一个字节码文件
字节码文件是一个二进制文件，不能用一般的记事本打开，推荐使用 <mark style="background: #FF5582A6;">jclasslib 工具</mark>查看字节码文件
下载地址：[https://github.com/ingokegel/jclasslib](https://github.com/ingokegel/jclasslib)
打开工具的地址：默认在 C:\\Program Files\\jclasslib\\bin
![[Pasted image 20231120172446.png]]
### 字节码文件的组成
![[Pasted image 20231120173712.png]]
#### 基本信息
<mark style="background: #FFB86CA6;">什么是魔数 (Magic)?</mark>
- 文件是无法通过修改文件扩展名的方式来修改文件的类型，扩展名修改了，但是文件的内容并不会修改
- <mark style="background: #D2B3FFA6;">软件一般通过文件头部几个字节去检验文件的类型，如果软件不支持该类型就会报错，例如将 png 图片扩展名改为 avi, 在通过视频播放器去打开这个文件，很明显打开失败，因为视频播放器这个软件检验文件类型是通过查看文件头部的几个字节，而不是看文件的扩展名</mark>
- Java 字节码文件中，文件头称为魔数 (Magic)
![[Pasted image 20231120174439.png]]
<mark style="background: #FFB86CA6;">什么是版本号？</mark>
- 就是编译字节码文件的 JDK 版本
- <mark style="background: #D2B3FFA6;">版本号的作用主要是判断当前字节码的版本和运行时的 JDK 是否兼容</mark>
<mark style="background: #FFB86CA6;">什么是访问标志？</mark>
- 标识是类还是接口、注解、枚举、模块
- 标识 public final abstract
<mark style="background: #FFB86CA6;">什么是本类索引，父类索引？</mark>
通过这些索引可以找到类、父类、接口的信息
#### 常量池
<mark style="background: #FFB86CA6;">字节码文件常量池的作用？</mark>
避免相同的内容重复定义，节省空间
常量池中的数据都有一个编号，编号从 1 开始，字段和字节码指令可以通过该编号快速定位到常量数据
字节码指令通过常量编号引用到常量池的过程称为<mark style="background: #FF5582A6;">符号引用</mark>
#### 方法
字节码中的方法区域是存放字节码指令的核心位置，字节码指令的内容存放在方法的 Code 属性中
<mark style="background: #FF5582A6;">操作数栈</mark>是临时存放数据的地方，<mark style="background: #FF5582A6;">局部变量表</mark>是存放方法中的局部变量的位置
常用的字节码指令：
- Iconst_常量：将一个常量存入操作数栈
- Istore_位置：将操作数栈中的数据存入局部变量表指定位置 
- Iload_位置：将局部变量表指定位置的数据复制一份放入操作数栈
- Iadd: 将操作数栈中顶部两个数据相加，放入操作数栈
- Iinc 位置 by 常量：在局部变量表的指定位置加上一个常量
- Return: 方法结束，返回
例子：
<mark style="background: #FFB86CA6;">int i = 0; i = i++; 最终 i 的值是多少？</mark>
- 首先执行 iconst 指令，将常量 0 放入操作数栈临时保存
- 在执行 istore 指令将常量 0 放入局部变量表中
- 然后再执行 iload 指令将局部变量表中的数据 (0)复制一份到操作数栈
- 然后再执行 iinc by 指令将局部变量表中的数据加一 (1)
- 接下来的话又会执行 istore 指令将操作数栈中的值，也就是 0 存入局部变量表中，将 1 覆盖为 0
- 最后返回
回答：<mark style="background: #D2B3FFA6;">答案是 0，通过分析字节码指令，发现 i++之前先把 0 取出放入操作数栈中，然后将局部变量表中的 i 进行加 1 操作，最后在将操作数栈中保存的临时值赋给 i, i 就变为了 0</mark>
### 字节码常用工具
- `javap -v` JDK 自带的反编译工具，通过控制台查看字节码文件，通常在 linux 上用，如果 jar 包需要先使用 jar –xvf 命令解压
- Jclasslib 插件，在利用 idea 开发编译后可以使用该插件查看对应的字节码文件
![[Pasted image 20231121170102.png]]
![[Pasted image 20231121170120.png]]
先要选中要查看的 java 文件，然后在利用视图查看
![[Pasted image 20231121170355.png]]
- <mark style="background: #FF5582A6;">阿里 arthas 工具</mark>
	- Arthas 可以对运行中的代码进行实时监控，用于线上服务
	- 下载： https://github.com/alibaba/arthas/releases
	- 官网：[简介 | arthas (aliyun.com)](https://arthas.aliyun.com/doc/)
	- 启动 arthas: `java -jar arthas-boot.jar`
	- 启动之后可以选择你要查看的 java 进程，输入前面的编号
	- 展示当前进程信息：`dashboard` 回车，Ctrl+C 中断
	- 将加载的字节码文件保存到指定目录：`dump -d`
	- 反编译字节码文件：`jad 类的全限定名`
<mark style="background: #FFB86CA6;">当我们修复了一个 bug，要将修复后的字节码文件部署到服务器，如何查看是否部署成功？</mark>
启动 arthas, 使用 jad 命令加上想查看的类名，查看源代码是否更新
## 类的生命周期
类的生命周期粗略的分为 5 步：
- 加载
- 连接
- 初始化
- 使用
- 卸载
### 加载
- 加载 (Loading)阶段第一步是<mark style="background: #FF5582A6;">类加载器</mark>根据类的全限定名通过不同的渠道以<mark style="background: #FF5582A6;">二进制流</mark>的方式获取字节码信息，程序员可以使用 Java 代码拓展的不同的渠道。
- 类加载器在加载完类之后，Java 虚拟机会将字节码中的信息保存到方法区中
- 在方法区生成一个 InstanceKlass 对象，保存类的所有信息，里边还包含实现特定功能比如多态的信息
![[Pasted image 20231121180504.png]]
- 同时，Java 虚拟机还会在堆中生成一份与方法区中数据类似的 java. Lang. Class 对象，<mark style="background: #ABF7F7A6;">作用是在 Java 代码中去获取类的信息以及存储静态字段的数据（JDK 8 及之后）</mark>
![[Pasted image 20231121180736.png]]
- 对于开发者来说，只需要访问堆区中的 class 对象，而不需要去访问方法区中的所有信息，<mark style="background: #ABF7F7A6;">这样 Java 虚拟机就能很好地控制开发者访问数据的范围</mark>
<mark style="background: #FFB86CA6;">如何查看内存中的对象？</mark>
- 推荐使用 JDK 自带的 hsdb 工具查看 Java 虚拟机内存信息。工具位于 JDK 安装目录下 lib 文件夹中的 sa-jdi.Jar中
- 启动命令：`java -cp sa-jdi.jar sun.jvm.hotspot.HSDB`
### 连接
连接阶段分为 3 步：
- 验证
- 准备
- 解析
#### 验证阶段
- 连接阶段的第一步就是验证字节码文件是否遵守了《java 虚拟机规范》，这个阶段程序员不用参与
- 验证阶段主要包括 4 部分：
	- 文件格式验证，比如字节码文件头是否是 0 XCAFEBABE
	- 元信息验证
	- 验证程序执行的指令是否合理
	- 符号引用验证，比如访问了其他类中的 private 方法
<mark style="background: #FFB86CA6;">案例：Hotspot JDK8中虚拟机源码对版本号检测</mark>
![[Pasted image 20231121183413.png]]
主版本号不能高于环境主版本号，主版本相等，副版本号也不能超过环境副版本号
#### 准备阶段
- 准备阶段会为静态字段创建内存并赋予初始值
![[Pasted image 20231121184255.png]]
- Final 修饰的静态字段，准备阶段会直接将代码中的值赋给静态变量，如果没有 final 的话，就算显示给静态字段定义一个值，也不会在准备阶段就将代码中的值直接赋给静态变量，而是先赋予一个初始值，相当于两阶段赋值
![[Pasted image 20231121184636.png]]
#### 解析阶段
- 解析阶段主要是将常量池中的符号引用替换为直接引用
- 符号引用就是在字节码文件中使用编号来访问常量池中的内容
![[Pasted image 20231121184736.png]]
- 直接引用不在使用编号，而是使用<mark style="background: #FF5582A6;">内存中地址</mark>进行访问具体的数据
![[Pasted image 20231121184900.png]]
### 初始化
- 初始化阶段会执行静态代码块中的代码，并为静态变量赋值
- 初始化阶段会执行字节码文件中 clinit 部分的字节码指令
![[Pasted image 20231121200756.png]]
- 字节码指令：`putstatic #n` 将操作数栈中的值设置给静态变量
- <mark style="background: #ABF7F7A6;">初始化阶段的静态变量的赋值操作按照 java 代码编写的顺序执行</mark> 
<mark style="background: #FFB86CA6;">哪几种方式会导致类的初始化？</mark>
- 访问一个类的静态变量或静态方法，注意 <mark style="background: #ABF7F7A6;">final 修饰的静态变量等号右边是常量</mark>这种情况不会初始化类
- 调用 Class. ForName (String className)
- 通过 new 的方式创建一个类的对象
- 调用本类的 main 方法
<mark style="background: #FFB86CA6;">案例：</mark>
![[Pasted image 20231121201622.png]]
- 首先调用 main 方法会初始化这个类，所以先执行本类的静态代码块----->"D"
- 进入 main 方法打印----->"A"
- 通过 new 创建这个类的对象，但是这个类已经初始化过，所以不用再初始化了
- 执行这个类的构造方法，实例代码块会放入构造方法中并且先执行----->"C"----->"B"----->"C"----->"B"

Clinit 指令不会出现的情况：
- 无静态代码块且没有静态变量赋值语句
- 有静态变量的声明，而没有赋值
- 静态变量使用 final 修饰，静态变量会在准备阶段进行初始化

<mark style="background: #ABF7F7A6;">直接访问父类的静态变量，不会触发子类的初始化</mark>
<mark style="background: #ABF7F7A6;">子类初始化之前，会先初始化父类</mark>

<mark style="background: #FFB86CA6;">案例：</mark>
![[Pasted image 20231121203331.png]]
- 首先调用 main 方法，初始化 Demo 02, 但没有静态代码块
- 创建子类对象，首先初始化父类，在初始化子类，最后 a=2, 由子类赋值操作覆盖父类所赋的值
## 类加载器
<mark style="background: #FFB86CA6;">什么是类加载器？</mark>
- 类加载器就是 java 虚拟机提供给应用程序去获取字节码信息的一种技术
- 类加载器只参与加载过程中将字节码信息保存到内存的流程
<mark style="background: #FFB86CA6;">什么是 JNI？</mark>
- 本地接口 JNI 是 java native interface 的缩写，允许 java 调用其他语言编写的方法，在类加载器中，主要调用虚拟机中的方法，这些方法使用 C++编写
### 类加载器的分类
<mark style="background: #ABF7F7A6;">类加载器分为两类，一类是 java 代码实现，另一类是虚拟机源码实现</mark>
类加载器的设计在 JDK 8 之前和 JDK 8 之后有较大差别，JDK 8 之前默认的类加载器有：
- 启动类加载器，基于虚拟机源码实现
- 扩展类加载器，基于 java 代码实现
- 应用程序类加载器，基于 java 代码实现
类加载器的详细信息可以通过 arthas 的 `classloader` 命令查看
![[Pasted image 20231123182213.png]]
#### 启动类加载器
启动类加载器是由 HotSpot 虚拟机提供的，使用 C++编写的类加载器
默认加载 Java 安装目录/jre/lib 下的类文件，比如 rt.jar，tools.jar，resources.jar 等。
<mark style="background: #FFB86CA6;">如何在一个工程中加载自定义的 jar 包？</mark>
- 在虚拟机选项中输入-Xbootclasspath/a: jar 包目录/jar 包名进行扩展
![[Pasted image 20231123183839.png]]
![[Pasted image 20231123183823.png]]
当前类在类加载时，就会将自定义的 jar 包进行加载
#### 扩展类加载器和应用程序类加载器
扩展类加载器和应用程序类加载器都是 JDK 提供的，使用 java 代码编写的
![[Pasted image 20231123185235.png]]
<mark style="background: #ABF7F7A6;">扩展类加载器默认加载位置：默认加载 Java 安装目录/jre/lib/ext 下的类文件</mark>

<mark style="background: #FFB86CA6;">如何在工程中通过扩展类加载器加载自定义的 jar 包?</mark>
- 在虚拟机选项中输入参数：-Djava. Ext. Dirs=自定义 jar 包目录, 这种方式会覆盖原来的 jar 包目录，可以通过追加的方式将原来目录加入，window 下用';'隔开，macOS 和 linux 用': '隔开

> [!warning]- 注意
> Jar 包目录不能含有空格等特殊字符，否则会报错，可以将目录路径用引号包起来

应用程序类加载器 classpath 下的类文件
类加载器的路径可以通过 arthas 的 `classloader -c hash` 查看
![[Pasted image 20231123190124.png]]
### 双亲委派机制
每个基于 java 实现的类加载器有一个成员变量：父类加载器，可以理解为上级关系，不是继承关系
![[Pasted image 20231123191803.png]]
应用程序类加载器的父类加载器时扩展类加载器，<mark style="background: #ABF7F7A6;">扩展类加载器没有父类加载器，但是可以委派给启动类加载器</mark>
类加载器之间的关系可以通过 arthas 的 `classloader -t` 命令查看
![[Pasted image 20231123192136.png]]

<mark style="background: #FFB86CA6;">双亲委派机制的流程？</mark>
- 加载一个类时，首先会自底向上查看类加载器是否已经加载了该类，如果加载了就直接返回，没有加载就将类加载请求委派给父类加载器
- 如果所有类加载器都没有加载过该类，就自顶向下尝试加载该类，如果该类在当前类加载器的加载目录下则加载成功，不在当前加载目录下，就将请求委派给子类加载器，由子类加载器进行判断
简单地说就是，<mark style="background: #FF5582A6;">自底向上查找是否加载过，自顶向下尝试加载</mark>
<mark style="background: #FFB86CA6;">如果一个类重复出现在 3 个类加载器的加载路径下，那么由谁来进行加载？</mark>
启动类加载器，因为根据双亲委派机制，它的优先级是最高的
<mark style="background: #FFB86CA6;">String 类能覆盖吗？</mark>
不能，因为在自定义 String 类加载之前，启动类加载器已经将 JDK 下的 String 类加载过了，获取自定义 String 类的类加载器会返回 null
<mark style="background: #FFB86CA6;">这几个类加载彼此之间存在关系吗？</mark>
应用程序类加载器的父类加载器是扩展类加载器，扩展类加载器没有父类加载器，但是会委派给启动类加载器
<mark style="background: #FFB86CA6;">双亲委派机制有什么作用？</mark>
- 通过双亲委派机制可以让顶层类加载器去加载核心类，防止恶意替换 JDK 中的核心类，比如 String, 确保核心类的安全性与完整性
- 避免同一个类被多次加载
### 打破双亲委派机制
#### 自定义类加载器
自定义类加载器并重写 loadClass 方法，就可以将双亲委派机制的代码去除
![[Pasted image 20231123200920.png]]
![[Pasted image 20231123201006.png]]
<mark style="background: #FFB86CA6;">两个自定义类加载器加载相同限定名的类，不会冲突吗？</mark>
不会冲突，在同一个 java 虚拟机中，<mark style="background: #FF5582A6;">只有相同类加载器+相同类限定名才会被认为是同一个类</mark>
#### 线程上下文类加载器
<mark style="background: #FFB86CA6;">JDBC 案例</mark>
JDBC 中用 DriverManager 来管理项目中引入不同数据库的驱动，比如 mysql 驱动，oracle 驱动
DriverManager 位于 rt.jar 包下，由启动类加载器加载
依赖中的 mysql 驱动类位于类路径下，由应用程序类加载器加载
<mark style="background: #FFB86CA6;">在加载完 DriverManager 的同时还要去加载 mysql 驱动，那么这是如何加载的呢？</mark>
- DriverManager 利用 SPI 机制 (SPI 是 JDK 内置的一种服务发现机制)，获取线程上下文中保存的类加载器，这个类加载器就是应用程序类加载器，然后通过应用程序类加载器去加载 jar 包
![[Pasted image 20231124141103.png]]
<mark style="background: #FFB86CA6;">JDBC 加载驱动类打破了双亲委派机制吗？</mark>
- 周志明老师的《深入理解 java 虚拟机》中表示这种由启动类加载器加载类的后在委派应用程序类加载器去加载类的方式，打破了双亲委派机制
- 有的人认为没有，因为 JDBC 只是在 DriverManager 加载完之后，通过初始化阶段触发了驱动类加载器，类的加载依然遵循双亲委派机制
#### OSGI 模块化
- 历史上，OSGi 模块化框架。它存在同级之间的类加载器的委托加载。OSGi 还使用类加载器实现了热部署的功能
- 热部署是在服务不停止的情况下，动态的更新字节码文件到内存中
<mark style="background: #FFB86CA6;">案例：使用 arthas 不停机解决线上问题</mark>
背景：小李的团队将代码上线之后，发现存在一个小 bug，但是用户急着使用，如果重新打包再发布需要一个多小时的时间，所以希望能使用 arthas 尽快的将这个问题修复
思路：
- <mark style="background: #ABF7F7A6;">在出问题的服务器上部署一个 arthas，并启动</mark>
- <mark style="background: #ABF7F7A6;">Jad --source-only 类全限定名 > 目录/文件名. Java</mark> (反编译)，(jad 命令反编译，然后可以用其它编译器，比如 vim 来修改源码)
- <mark style="background: #ABF7F7A6;">Mc –c 类加载器的 hashcode 目录/文件名. Java -d 输出目录</mark> (mc 命令用来编译修改过的代码)
- <mark style="background: #ABF7F7A6;">retransform class 文件所在目录/xxx. Class</mark>，(用 retransform 命令加载新的字节码)

> [!warning]- 注意
> - 程序重启之后，字节码文件会恢复，除非将 class 文件放入 jar 包中进行更新
> - 使用 retransform 不能添加字段和方法，也不能更新正在执行的方法

### JDK 9 之后的类加载器
- JDK 9 之后的启动类加载器是由 java 编写的，不再是 c++编写的了，但是启动类加载器依然无法通过 java 代码获取，依然为 null, 保持统一
- JDK 9 之后引入了 module 的概念，类加载器不再是加载 jar 包而是加载 module 文件
- 扩展类加载器替换为平台类加载器
## Java 虚拟机内存结构
- Java 虚拟机在程序运行过程中管理的内存区域就是运行时数据区
- 运行时数据区分为线程共享和线程不共享
	- 线程不共享：<mark style="background: #ABF7F7A6;">程序计数器，java 虚拟机栈，本地方法栈</mark>
	- 线程共享：<mark style="background: #ABF7F7A6;">方法区，堆区</mark>
### 程序计数器
- 程序计数器也叫做 PC 寄存器，每个线程会将当前要执行的字节码指令地址保存在程序计数器中，保存的内存地址大小固定
![[Pasted image 20231124150524.png]]

<mark style="background: #FFB86CA6;">案例：</mark>
![[Pasted image 20231124150655.png]]
在加载阶段，虚拟机将字节码文件中读取到的字节码指令保存到内存中，将源文件中的偏移量转换为内存地址，每一个字节码指令都有一个内存地址
在代码执行过程中，程序计数器会保存下一个将要执行的字节码指令的地址，虚拟器的执行引擎根据程序计数器中的字节码指令地址找到对应的字节码指令执行

<mark style="background: #ABF7F7A6;">在多线程情况下，程序计数器会保存 CPU 切换之前执行的字节码指令地址，重新获取 CPU 执行权后，根据字节码指令地址继续执行代码</mark>

<mark style="background: #FFB86CA6;">程序计数器在运行过程中会出现内存溢出吗？</mark>
- 内存溢出指的是程序在运行过程中使用某一块内存区域，所需的数据容量超过了虚拟机提供的内存上限
- 因为每个程序计数器只只存储一个固定大小的内存地址，所以是不会发生内存溢出的
### 栈
Java 虚拟机栈采用栈的数据结构来管理方法中调用的基本数据，先进先出，每一个方法调用一个栈桢来保存
![[Pasted image 20231125113146.png]]
Java 虚拟机栈会随着线程的创建而创建，在线程销毁时回收，每个方法都可能会在不同的线程中执行，每个线程都有自己的虚拟机栈，方法中的局部变量是线程隔离的
#### 局部变量表
- 局部变量表的作用就是在方法运行过程中保存局部变量的内容，在编译成字节码文件时可以确定局部变量的值
![[Pasted image 20231125114005.png]]
![[Pasted image 20231125114146.png]]
- 局部变量表是一个数组，数组中的每一个位置称做<mark style="background: #FF5582A6;">槽</mark>，long 和 double 类型占两个槽，其他类型占一个槽
<mark style="background: #FFB86CA6;">局部变量表会存放哪些内容呢？</mark>
- 在实例方法中，局部变量表的第一个槽存放当前调用对象 (this), 后续存放方法的参数，接着存放方法中的局部变量
- 方法参数保存在局部变量中的顺序和参数定义的顺序一致
<mark style="background: #FFB86CA6;">以下代码会占用几个槽？</mark>
![[Pasted image 20231125114722.png]]
- 6 个槽
- This 占一个槽，参数占两个槽
- <mark style="background: #ABF7F7A6;">在代码块中的局部变量 a, b, c 在代码块执行结束后失效，a, b, c 所占用的槽就可以进行复用</mark>
- 局部变量 i 占一个槽，局部变量 j 占两个槽，加起来总和 6 个槽
#### 操作数栈
- 操作数栈是栈帧中虚拟机在执行指令过程中用来存放中间数据的一块区域。他是一种栈式的数据结构，如果一条指令将一个值压入操作数栈，则后面的指令可以弹出并使用该值
- 在编译期就可以确定操作数栈的最大深度，从而在执行时正确的分配内存大小
![[Pasted image 20231125120551.png]]
#### 帧数据
- 当前类的字节码指令引用了其他类的属性或者方法时，需要将符号引用（编号）转换成对应的运行时常量池中的内存地址。动态链接就保存了编号到运行时常量池的内存地址的映射关系
![[Pasted image 20231125121204.png]]
- 方法出口指的是方法在正确或者异常结束时，当前栈帧会被弹出，同时程序计数器应该指向上一个栈帧中的下一条指令的地址。所以在当前栈帧中，需要存储上一个方法执行的下一条指令的地址
- 异常表存放的是代码中异常的处理信息，包含了异常捕获的生效范围以及异常发生后跳转到的字节码指令位置
![[Pasted image 20231125121605.png]]

#### 栈内存溢出
- 如果栈的栈帧过多，可能会导致内存超过栈的最大容量，导致栈内存溢出
- 栈内存溢出时会报 StackOverflowErroe 错误
- 如果我们不指定栈内存的大小，将会使用 JVM 的默认栈内存大小
![[Pasted image 20231125123149.png]]

<mark style="background: #FFB86CA6;">怎么指定 JVM 虚拟机栈大小？</mark>
- 使用虚拟机参数 -Xss
- 使用方法：-Xss 栈的大小
![[Pasted image 20231125123413.png]]

HotSpot JVM 对栈的大小有范围要求：<mark style="background: #ABF7F7A6;">最小值为 180 k, 最大值为 1024 m</mark>
影响栈内存的因素：局部变量过多 (增大栈帧的容量)，操作数栈深度过大
<mark style="background: #FF5582A6;">一般情况下，工作中即便使用了递归进行操作，栈的深度最多也只能到几百,不会出现栈的溢出。所以此参数可以手动指定为-Xss256k 节省内存</mark>
#### 本地方法栈
- Java 虚拟机栈保存了调用 java 方法的栈帧，本地方法栈保存了调用 native 方法的栈帧
- 在 HotSpot 虚拟机中，java 虚拟机栈和本地方法栈使用了同一个栈空间
![[Pasted image 20231125124156.png]]
### 堆
- 在 java 程序中，堆内存是最大的内存区域，创建出来的对象都存放于堆内存上
- 栈上的局部变量表中可以存放堆上对象的引用
![[Pasted image 20231125131015.png]]

<mark style="background: #FFB86CA6;">堆内存是否有上限？</mark>
- 堆内存有上限，我们可以通过死循环创建对象，最后发现会报一个 <mark style="background: #FF5582A6;">OutOfMemoryError</mark> 错误

Java 堆内存需要关注 3 个值: <mark style="background: #FF5582A6;">userd, total, max</mark>
- Userd: 表示当前已使用的堆内存
- Total: 表示当前虚拟机已分配的内存
- Max: 表示虚拟机可以分配的最大内存
![[Pasted image 20231125131513.png]]

<mark style="background: #FFB86CA6;">如何查看堆内存的 userd, total, max 3 个值？</mark>
- 可以通过 arthas 工具的 `dashboard` 命令查看
- `dashboard -i 刷新频率(毫秒)` 表示手动指定刷新频率
- 也可以直接使用 `memory` 命令 查看堆内存的运行情况
![[Pasted image 20231125131821.png]]

随着对象的增多，当 total 使用的内存不足时，java 虚拟机会继续分配内存给 total, 直到 total 等于 max
如果不设置虚拟机参数，虚拟机堆内存的默认 max 为系统内存的 1/4, 默认 total 为系统内存的 1/64, 在实际应用中都需要设置 total 值和 max 值

<mark style="background: #FFB86CA6;">如何设置堆内存的默认大小？</mark>
- 可以使用虚拟机参数<mark style="background: #FF5582A6;">-Xmx</mark> 设置 max 的值，<mark style="background: #FF5582A6;">-Xms </mark>设置 total 的值
- 语法：-Xmx 大小，-Xms 大小
- 限制：max 的值必须大于 2 MB, total 的值必须大于 1 MB
- Java 服务端程序开发时，建议将-Xmx 和-Xms 设置为相同的值，这样在程序启动之后可使用的总内存就是最大存，而无需向 java 虚拟机再次申请，<mark style="background: #ABF7F7A6;">减少了申请并分配内存时间上的开销</mark>，同时也不会出现内存过剩之后堆收缩的情况
<mark style="background: #FFB86CA6;">为什么 arthas 中显示的堆内存大小与设置的值不一致？</mark>
![[Pasted image 20231125132741.png]]
arthas 中的 heap 堆内存使用了 JMX 技术中内存获取方式，这种方式与垃圾回收器有关，<mark style="background: #ABF7F7A6;">计算的是可以分配对象的内存，而不是整个内存</mark>
### 方法区
方法区是存放基础信息的位置，线程共享，主要存放 3 部分内容：<mark style="background: #ABF7F7A6;">类的元信息，运行时常量池，字符串常量池</mark>
- 方法区存放类的元信息，一般称之为<mark style="background: #FF5582A6;"> instanceKlass 对象</mark>，在类加载阶段完成
![[Pasted image 20231125134904.png]]
- 方法区除了存储类的元信息之外，还存放了运行时常量池。常量池中存放的是字节码中的常量池内容
- 字节码文件中通过编号查表的方式找到常量，这种常量池称为静态常量池。当常量池加载到内存中之后，可以通过内存地址快速的定位到常量池中的内容，这种常量池称为运行时常量池
![[Pasted image 20231125135112.png]]

JDK 7 及之前的方法区存放在<mark style="background: #ABF7F7A6;">堆区的永久代空间</mark>，堆的大小由虚拟机参数控制
JDK 8 及之后的方法区存放在元空间中，<mark style="background: #ABF7F7A6;">元空间的内存用的是操作系统的直接内存，跳出了 java 虚拟机的内存</mark>

<mark style="background: #FFB86CA6;">案例：通过 ByteBuddy 框架，动态生成字节码数据，加载到内存中。通过死循环不停地加载到方法区，观察方法区是否会出现内存溢出的情况。分别在JDK7和JDK8上运行上述代码</mark>
- 引入 ByteBuddy 的依赖
> [!NOTE]- ByteBuddy 依赖
```xml
 <dependency>
    <groupId>net.bytebuddy</groupId>
    <artifactId>byte-buddy</artifactId>
    <version>1.8.0</version>
</dependency>
```
- 创建 ClassWriter 对象
`ClassWriter classWriter=new ClassWriter(0);`
- 调用 visit 方法，创建字节码数据
`classWriter.visit(Opcodes.V1_7,Opcodes.ACC_PUBLIC,name,null ,"java/lang/Object",null);
`byte[] bytes = classWriter.toByteArray();
实验发现，JDK7上运行大概十几万次，就出现了错误。在 JDK8上运行百万次，程序都没有出现任何错误，但是内存会直线升高。这说明JDK7和JDK8在方法区的存放上，采用了不同的设计
- JDK 7 将方法区存放在堆区域的永久代空间，永久代空间由虚拟机参数 `-XX:MaxPermSize=值` 来控制
- JDK 8 将方法区存放在元空间中，默认情况下直接依赖于操作系统的内存大小，也可以使用 `-XX:MaxMetaspaceSize=值` 参数对元空间大小进行限制

#### 字符串常量池
顾名思义，字符串常量池就是存储字符串的
<mark style="background: #FFB86CA6;">字符串常量池和运行时常量池有什么区别呢？</mark>
- 在 JDK 7 之前字符串常量池属于运行时常量池，存储在永久代
- JDK 7 中的字符串常量池被存入堆中
- 在 JDK 8 之移除了永久代空间，字符串常量池还在堆中

<mark style="background: #FFB86CA6;">案例：</mark>
![[Pasted image 20231126142816.png]]
- 先将常量池中的字符串获取放入操作数栈中，再将操作数栈中的字符串移到局部变量表
- 两个字符串对象 a+b 操作使用的是 StringBuilder 的 append 方法进行拼接，而不是直接从字符串常量池中获取
- 因此 c 指向的是字符串常量池中的地址，d 指向的是堆中的地址
![[Pasted image 20231126143411.png]]
- 这种情况就会在编译阶段直接拼接从常量池中获取字符串

<mark style="background: #FFB86CA6;">String.intern ()有什么作用？</mark>
- 可以手动将字符串加入到字符串常量池中，也可以直接获取字符串常量池中字符串的地址
- JDK 6 版本的 intern 方法在第一次创建字符串时，会先去字符串常量池中寻找是否存在，如果存在则直接返回常量池中的引用，不存在的话会将字符串加入到字符串常量池中，返回字符串常量池中的地址
- JDK 7 版本之后的 intern 方法，也会先去字符串常量池中寻找是否存在字符串常量，不存在的话就会将堆中字符串的引用放入字符串常量池，调用 intern 方法还是会返回堆中字符串地址

<mark style="background: #FFB86CA6;">静态变量存放在哪里？</mark>
- JDK 6 及之前存放在永久代
![[Pasted image 20231126145044.png]]
- JDK 7 及之后存放在堆中 Class 对象上
![[Pasted image 20231126145106.png]]
### 直接内存
- 直接内存（Direct Memory）并不在《Java 虚拟机规范》中存在，所以并不属于 Java 运行时的内存区域
<mark style="background: #FFB86CA6;">直接内存的作用？</mark>
- Java 堆中的对象如果不再使用要回收，回收时会影响对象的创建和使用
- IO 操作比如读文件，需要先把文件读入直接内存（缓冲区）再把数据复制到 Java 堆中。现在直接放入直接内存即可，同时 Java 堆上维护直接内存的引用，减少了数据复制的开销。写文件也是类似的思路
![[Pasted image 20231126150230.png]]
要创建直接内存上的数据，可以使用<mark style="background: #FF5582A6;">ByteBuffer</mark>
语法： <mark style="background: #ABF7F7A6;">ByteBuffer directBuffer = ByteBuffer.allocateDirect(size);</mark>
注意事项： arthas 的 memory 命令可以查看直接内存大小，属性名 direct
![[Pasted image 20231126150415.png]]
调节直接内存大小：`-XX:MaxDirectMemorySize=大小` 虚拟机参数
<mark style="background: #FFB86CA6;">不同 JDK 版本 JVM 运行内存的区别？</mark>
- JDK 6 中的字符串常量池在永久代中，永久代使用的是堆区的内存
- JDK 7 及之后字符串常量池在堆中
- JDK 8 及之后取消了永久代空间，方法区存放在元空间中，使用操作系统直接内存
![[Pasted image 20231126151256.png]]
## 自动垃圾回收
Java 中为了简化对象的释放，引入了自动的垃圾回收（Garbage Collection 简称 GC）机制。通过垃圾回收器来对不再使用的对象完成自动的回收，垃圾回收器主要负责对堆上的内存进行回收。其他很多现代语言比如 C#，Python、Go 都拥有自己的垃圾回收器。
![[Pasted image 20231126153238.png]]
自动垃圾回收的应用场景：
- 解决系统僵死问题，大厂的系统出现许多系统僵死问题都与频繁地垃圾回收有关
- 性能优化，对垃圾回收器进行合理的设置可以优化性能
- <mark style="background: #ABF7F7A6;">高频面试 </mark>
### 方法区的回收
<mark style="background: #FFB86CA6;">为什么垃圾回收器不用回收栈中的内容？</mark>
因为线程不共享的内存区域，都是<mark style="background: #ABF7F7A6;">伴随线程的创建而创建，线程的销毁而销毁</mark>，方法在执行完之后栈帧会自动弹出栈内存并释放

- <mark style="background: #ABF7F7A6;">方法区回收的主要内容就是不再使用的类</mark>
判断一个类是否可以回收需要同时满足 3 个条件：
- 该类的所有实例对象均被回收，以及实例对象的子类对象
![[Pasted image 20231126154229.png]]
- 加载该类的类加载器被回收
![[Pasted image 20231126154243.png]]
- Class 对象没有任何引用
![[Pasted image 20231126154254.png]]

<mark style="background: #ABF7F7A6;">手动回收：System.gc (), 调用该方法不一定会立即执行垃圾回收，只是向 JVM 发送一个请求，是否进行垃圾回收由 JVM 自行判断</mark>
### 堆回收
<mark style="background: #FFB86CA6;">如何判断对上的对象可以进行回收？</mark>
根据堆中的对象是否被引用，如果已经没有引用指向对象则可以进行回收
![[Pasted image 20231126155941.png]]

<mark style="background: #FFB86CA6;">如何判断堆上的对象没有被引用？</mark>
常见的两种方法：<mark style="background: #FF5582A6;">引用计数法</mark>和<mark style="background: #FF5582A6;">可达性分析法</mark>
引用计数法：<mark style="background: #ABF7F7A6;">为每个对象维护一个引用计数器，当对象被一个引用指向时，引用计数器加一，当取消引用时，引用计数器减一</mark>
![[Pasted image 20231126160412.png]]
- 引用计数器的缺点：
	- 每次引用和取消引用都要去维护引用计数器，对性能有一定影响
	- 存在<mark style="background: #FF5582A6;">循环引用</mark>问题，两个对象相互引用，无法进行回收
![[Pasted image 20231126160639.png]]
- <mark style="background: #ABF7F7A6;">在 java 虚拟机 中使用的是可达性分析法作为对象回收的判断方法</mark>
可达性分析算法：<mark style="background: #ABF7F7A6;">可达性分析将对象分为两类 ：垃圾回收的根对象和普通对象，对象与对象之间存才引用关系的就会形成一个引用链，根对象就可以理解为这条引用链的头结点，如果从根对象开始可以到达某个普通对象，那么这个普通对象就是不可被回收的</mark>
![[Pasted image 20231127164453.png]]
- 哪些对象可以称之为根对象？
	- 线程 Thread 对象
	- 系统类加载器加载的 Class 对象 [[#加载|Class对象何时加载？]]
	- 监视器对象，用来保存同步锁持有的对象[[并发编程#25.synchornized的实现原理🤔|同步锁的实现原理?]]
	- 本地方法调用时的全局对象
#### 五种对象引用
- 强引用
可达性算法中描述的对象引用一般指的是<mark style="background: #FF5582A6;">强引用</mark>，只要根对象与普通对象存在引用关系，那么这个对象就不可以被回收

- 软引用
软引用相对于强引用来说是一种比较弱的引用关系，<mark style="background: #ABF7F7A6;">如果一个对象只有软引用关联到它，那么当堆内存不足时，会将软引用关联的对象进行垃圾回收</mark>
在 JDK 1.2 之后用 SoftReference 类来实现软引用，软引用通常用于缓存中
![[Pasted image 20231127172209.png]]
软引用的执行过程：
1. 将对象用 SoftReference 包装起来，`new SoftReference<对象类型>(包装对象)`
2. 堆内存不足时，尝试进行垃圾回收
3. 如果垃圾回收不能解决内存不足问题，回收软引用中的对象
4. 如果依然内存不足，抛出 OutOfMemoryError 异常
<mark style="background: #FFB86CA6;">当软引用包装的对象被回收了，那么 SoftReference 对象也应该被回收，如何知道哪些 SoftReference 对象该被回收？</mark>
- SoftReference 提供了一个引用队列，如果 SoftReference 中包装的对象被回收了，会自动将当前 SoftReference 对象加入引用队列，该队列专门用来存放 SoftReference 中为空的对象
- 通过代码遍历引用队列，将 SoftReference 的强引用删除
![[Pasted image 20231127173455.png]]

- 弱引用
弱引用的整体机制其实和软引用差不多，他们之间的区别就是在垃圾回收时，<mark style="background: #ABF7F7A6;">不管堆内存是否充足，都会对弱引用对象进行回收</mark>
JDK 1.2 之后利用 WeakReference 类来实现弱引用，弱引用主要实现在 ThreadLocal [[并发编程#11.ThreadLocal的原理🙂|关于ThreadLocal]]
弱引用对象本身也可以使用引用队列进行回收
![[Pasted image 20231127175306.png]]

- 虚引用
虚引用也叫幽灵引用/幻影引用，不能通过虚引用对象获取到包含的对象。虚引用唯一的用途是当对象被垃圾回收器回收时可以接收到对应的通知。Java 中使用 PhantomReference 实现了虚引用，直接内存中为了及时知道直接内存对象不再使用，从而回收内存，使用了虚引用来实现

- 终接器引用
终结器引用指的是在对象需要被回收时，终结器引用会关联对象并放置在 Finalizer 类中的引用队列中，在稍后由一条由 FinalizerThread 线程从队列中获取对象，然后执行对象的 finalize 方法，在对象第二次被回收时，该对象才真正的被回收。在这个过程中可以在 finalize 方法中再将自身对象使用强引用关联上，但是不建议这样做

#### 垃圾回收算法
垃圾回收算法的<mark style="background: #FF5582A6;">核心思想</mark>：
1. 找到内存中存活的对象
2. 释放不在存活对象的内存，使得程序能再次利用这部分空间
![[Pasted image 20231127181526.png]]
常见的垃圾回收算法
![[Pasted image 20231127181559.png]]

Java 的垃圾回收有专门的 GC 线程来实现，<mark style="background: #ABF7F7A6;">不管使用哪一种 GC 算法，在进行垃圾回收时都会停止所有用户线程</mark>，停止的这段过程被称为 Stop The World 简称 STW, 如果 STW 时间过长会影响用户的体验
![[Pasted image 20231127182003.png]]

<mark style="background: #FFB86CA6;">判断垃圾回收是否优秀从哪几个方面考虑？</mark>
1. 吞吐量
吞吐量指的是 CPU 用于执行用户代码的时间与 CPU 的总执行时间的比值，即<mark style="background: #ABF7F7A6;">吞吐量=执行用户代码时间/(执行用户代码时间+垃圾回收的时间)总执行时间</mark>，吞吐量越高，垃圾回收的效率就越高
2. 最大暂停时间
最大暂停时间就是在<mark style="background: #ABF7F7A6;">所有垃圾回收过程中 STW 花费时间的最大值</mark>，最大暂停时间越小，用户受垃圾回收的影响就越小
3. 堆使用的效率
不同垃圾回收算法，对堆内存的使用方式是不同的。比如标记清除算法，可以使用完整的堆内存。而复制算法会将堆内存一分为二，每次只能使用一半内存。从堆使用效率上来说，标记清除算法要优于复制算法

吞吐量，最大暂停时间，堆使用效率不可兼得
<mark style="background: #ABF7F7A6;">不同的垃圾回收算法，适用于不同的场景</mark>

- <mark style="background: #FF5582A6;">标记清除算法</mark>
标记清除算法的过程分为两部分：
1. 标记阶段，将所有存活的对象进行标记，使用可达性分析算法从根对象开始遍历引用链，将所有与根对象关联的对象标记为存活对象
2. 清除阶段，将内存中没有被标记的对象进行垃圾回收
![[Pasted image 20231127184218.png]]
优点：实现简单，只需要为每个与根对象关联的对象打上一个标记，就可以对未标记的对象进行清除
缺点：
- 碎片化问题，由于内存的分配是连续的，在一些对象被清除后就会出现许多不连续的内存碎片，有些内存碎片较小的话就难以进行复用，造成空间浪费
![[Pasted image 20231127184625.png]]
- 分配速度慢，因为内存碎片的存在，就需要维护一个空闲内存链表，每次想利用空闲内存时都需要去遍历这个链表
![[Pasted image 20231127184800.png]]

- <mark style="background: #FF5582A6;">复制算法</mark>
复制算法的核心思想：准备两块内存空间 From 空间和 To 空间，每次分配对象内存时，只能使用其中一块空间，也就是 From 空间，在 GC 的时候将 From 空间中根对象移到 To 空间，再将与根对象关联的对象也移到另一个空间，剩下的未被移动的对象都是将被进行回收的对象，直接清理 From 空间的对象，最后在将 From 空间和 To 空间名称互换，下一次进行垃圾回收时，又从 From 空间开始
![[Pasted image 20231127185416.png]]
优点：
- 吞吐量高，复制算法只需要遍历一次存活对象复制到 To 空间即可，比标记-整理算法少了一次遍历的过程，因而性能较好
- 不会发生碎片化，复制算法在复制对象后，会将对象按顺序放入 To 空间
缺点：内存使用效率低，每次只能使用一半的内存来给对象使用

- <mark style="background: #FF5582A6;">标记整理算法</mark>
标记整理算法也叫做标记压缩算法，是对标记清理算法中产生碎片化问题的解决方案
标记整理算法的核心思想也分为两部分：
1. 标记阶段，使用可达性分析算法，从根对象开始遍历引用链，标记出所有与根对象关联的对象
2. 清理阶段，将所有标记的对象移动到堆的另一端，清理掉未标记的对象
优点：
- 内存使用效率高，整个堆空间都可以使用，不会像复制算法一样只能使用半个堆内存
- 不会发生碎片化，在整理阶段可以将对象往内存的一侧进行移动，剩下的空间都是可以分配对象的有效空间
缺点：将对象移动到堆的另一侧耗时较长

- <mark style="background: #FF5582A6;">分代 GC</mark>
分代垃圾回收将整个内存区域分为<mark style="background: #FF5582A6;">年轻代</mark>和<mark style="background: #FF5582A6;">老年代</mark>
年轻代用于存放存活时间较短的对象，老年代用于存放存活时间较长的对象
![[Pasted image 20231128165403.png]]
在 JDK8中，添加`-XX:+UseSerialGC` 参数使用分代回收的垃圾回收器，运行程序
在 arthas 中使用 memory 命令查看内存，显示出三个区域的内存情况
![[Pasted image 20231128165524.png]]
调整堆内存区域大小的参数：
- `-Xms`,设置堆内存初始分配的内存大小，用 total 表示。[[#堆|关于堆内存的表示]]
- `-Xmx`,设置堆的最大内存，用 max 表示
- `-Xmn`,设置新生代大小，<mark style="background: #ABF7F7A6;">堆内存的总和减去新生代大小就是老年代的大小</mark>
- `-XX:SurvivorRatio`,设置伊甸园区和幸存区的比例，默认为 8
- `-XX:+PrintGCDetails`,打印 GC 日志
当使用分代垃圾回收时，新创建出来的对象首先会被放入伊甸园区，当伊甸园区对象满时，会触发年轻代的 GC，也称为 Minor GC, Minor GC 也会依据可达性分析法分析出哪些对象需要进行回收，依然是利用复制算法将不需要回收的对象复制到 To 区，剩下的对象都进行回收，然后 To 区和 From 区进行名称交换，<mark style="background: #ABF7F7A6;">注意：Minor GC 会记录对象的年龄，每发生一次 GC, 未被回收的对象年龄加一</mark>，当加到一个阈值时，会把该对象放入老年代，当老年代空间不足时，首先尝试 Minor GC, 如果还是不行的话，就进行 Full GC, Full GC 会将整个堆进行垃圾回收，如果还是不行，那就只有抛出 OutOfMemory 异常
#### 垃圾回收器
<mark style="background: #FFB86CA6;">为什么分代 GC 算法要把堆分为年轻代与老年代？</mark>
- 可以通过调整年轻代与老年代的比例来适应不同的应用程序，提高内存利用率
- 年轻代与老年代可以使用不同的垃圾回收算法，灵活度高
- 分代的设计中允许只回收新生代（minor gc），如果能满足对象分配的要求就不需要对整个堆进行回收 (fullgc), STW 时间就会减少
![[Pasted image 20231128174034.png]]

<mark style="background: #FFB86CA6;">垃圾回收器的组合关系</mark>：
- 垃圾回收器是垃圾回收算法的具体实现
- 由于垃圾回收器分为年轻代和老年代，所以除了 G 1 垃圾回收器以外的垃圾回收器都必须成对组合使用
![[Pasted image 20231128174305.png]]
<mark style="background: #FF5582A6;">年轻代-Serial 垃圾回收器</mark>：
- Serial 是是一种单线程串行回收年轻代的垃圾回收器
- 采用复制算法
- 优点：单 CPU 下吞吐量非常出色
- 缺点：多 CPU 下吞吐量不如其他垃圾回收器，堆如果偏大会让用户线程处于长时间的等待
- 使用场景：Java 编写的客户端程序或者硬件配置有限的场景
<mark style="background: #FF5582A6;">老年代-SerialOld 垃圾回收器</mark>：
- SerialOld 是 Serial 垃圾回收器的老年代版本，采用单线程串行回收`-XX:+UseSerialGC` 新生代、老年代都使用串行回收器
- 采用标记整理算法
- 优点：单 CPU 吞吐量出色
- 缺点：多 CPU 下吞吐量不如其他垃圾回收器，堆如果偏大会让用户线程处于长时间的等待
- 使用场景：与 Serial 垃圾回收器搭配使用，或者在 CMS 特殊情况下使用
<mark style="background: #FF5582A6;">年轻代-ParNew 垃圾回收器</mark>：
- ParNew 垃圾回收器本质上是对 Serial 在多 CPU 下的优化，使用多线程进行垃圾回收`-XX:+UseParNewGC` 新生代使用 ParNew 回收器，老年代使用串行回收器
![[Pasted image 20231128182132.png]]
- 采用复制算法
- 优点：多 CPU 处理下停顿时间短
- 缺点：不如 G 1
- 使用场景：在 JDK 8 之前与老年代 CMS 垃圾回收器搭配使用
<mark style="background: #FF5582A6;">老年代-CMS 垃圾回收器：</mark>
- CMS 垃圾回收器关注的是系统的暂停时间，允许用户线程和垃圾回收线程在某些步骤中同时执行，减少了用户线程的等待时间，`-XX:+UseConcMarkSweepGC`
- 采用标记清除算法
- 优点：STW 时间较短，用户体验好
- 缺点：内存碎片化，退化问题，浮动垃圾问题
- 使用场景：大型的互联网系统中用户请求数据量大、频率高的场景比如订单接口、商品接口等
![[Pasted image 20231128182641.png]]
- CMS 执行步骤：
1. 初始标记，用极短的时间标记出根对象关联的对象
2. 并发标记，标记所有对象，用户线程不需要暂停
3. 重新标记，由于并发标记阶段有些对象发生了变化，存在错标、漏标等情况，需要重新标记
4. 并发清理，清理死亡对象，用户线程不需要暂停
<mark style="background: #FF5582A6;">年轻代-Parallel Scavenge 垃圾回收器：</mark>
- Parallel Scavenge 是 JDK 8 默认的年轻代垃圾回收器，多线程并行回收，关注的是系统的吞吐量。具备自动调整堆内存大小的特点
- 采用复制算法
- 优点：吞吐量高，而且手动可控。为了提高吞吐量，虚拟机会动态调整堆的参数
- 缺点：不能保证单次的停顿时间
- 使用场景：后台任务，不需要与用户交互，并且容易产生大量的对象比如：大数据的处理，大文件导出
<mark style="background: #FF5582A6;">老年代-Parallel Old 垃圾回收</mark>：
- Parallel Old 是为 Parallel Scavenge 收集器设计的老年代版本，利用多线程并发收集
- 采用标记清理算法
- 优点：在多核 CPU 下效率高
- 缺点：暂停时间较长
- 使用场景：与 Parallel Scavenge 配套使用
<mark style="background: #FF5582A6;">G 1 垃圾回收器：</mark>
- JDK 9 之后的垃圾回收器默认是 G 1 垃圾回收器
- Parallel Scavenge 垃圾回收器关注的是垃圾回收的吞吐量，允许用户设置最大暂停时间，但是会减少年轻代可用空间的大小。CMS 关注暂停时间，但是会影响吞吐量。G 1 就是将两者的优点进行融合
1. <mark style="background: #ABF7F7A6;">支持巨大的堆空间回收，拥有较大的吞吐量</mark>
2. <mark style="background: #ABF7F7A6;">支持多 CPU 并行垃圾回收</mark>
3. <mark style="background: #ABF7F7A6;">允许用户设置最大暂停时间</mark>
- 使用 G 1 垃圾回收器与其他垃圾回收器的堆内存结构不一样，G 1 的堆空间结构会被划分为多个大小相等的区域，这个区域称为 Region, 分别将伊甸园区、幸存者区和老年代区划分在 Region 区域中，不会要求伊甸园区、幸存者区和老年代内存结构上的连续性，Region 的大小默认是堆空间的大小除以 2048
![[Pasted image 20231129201707.png]]
G 1 垃圾回收有两种方式：
- 年轻代回收
1. 新创建的对象会放在 Eden 区，当 G 1 判断年轻代空间不足时，需要进行 <mark style="background: #FF5582A6;">Young GC</mark>
2. 标记出 Eden 区和 Survivor 区中存活的对象
3. 将这些存活的对象放入一个新的 survivor 区中，年龄加一，然后清空剩余 Eden 区和 Survivor 区中的对象
4. 当某个对象的年龄达到 15 时，将会被放入老年代区
5. <mark style="background: #ABF7F7A6;">如果一个对象的内存占用空间超过了 Region 的一半也将会被放入老年代区</mark>
- 混合回收
1. 如果老年代中的对象不断地增多，当到达堆总内存的一定比例时，将会触发混合回收也就是 Mixed GC, 回收所有年轻代和部分老年代的对象，也采用复制算法完成，不会产生内存碎片
2. G 1 对老年代的清理会选择<mark style="background: #FF5582A6;">存活度最低</mark>的区域来进行回收，这样可以保证回收效率最高，这也是 G 1（Garbagefirst）名称的由来

> [!warning]- 注意
>注意：如果清理过程中发现没有足够的空 Region 存放转移的对象，会出现 <mark style="background: #FF5582A6;">Full GC</mark>。单线程执行<mark style="background: #FF5582A6;">标记-整理算法</mark>，此时会导致用户线程的暂停。所以尽量保证应该用的堆内存有一定多余的空间

![[Pasted image 20231129204536.png]]