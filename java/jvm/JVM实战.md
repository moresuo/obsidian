***
## 内存调优
### 内存溢出和内存泄露
- <mark style="background: #ABF7F7A6;">在 java 程序中如果不在使用一个对象，但是该对象依然在 GC root 的引用链上，那么这个对象就不会被回收</mark>，这种现象就称为内存泄漏
- 在实际工作中，内存泄露多是由堆内存泄露引起的
- 少量的内存泄露是可以允许的，在许多公司多多少少都存在内存泄漏，但是如果持续的发生内存泄露，那么就会像滚雪球一样，造成大量的内存泄漏，最终内存被消耗完，造成内存溢出。<mark style="background: #ABF7F7A6;">但是产生内存溢出的原因可不止内存泄露这一种</mark>
![[Pasted image 20231129211527.png]]

内存泄漏常见场景：
- 内存泄漏导致溢出的常见场景是大型的 Java 后端应用中，在处理用户的请求之后，没<mark style="background: #ABF7F7A6;">有及时将用户的数据删除</mark>。随着用户请求数量越来越多，内存泄漏的对象占满了堆内存最终导致内存溢出
- 这种产生的内存溢出会直接导致用户请求无法处理，影响用户的正常使用。<mark style="background: #ABF7F7A6;">重启可以恢复应用使用，但是在运行一段时间之后依然会出现内存溢出</mark>
- 第二种常见场景是分布式任务调度系统如 Elastic-job、Quartz 等进行任务调度时，被调度的 Java 应用在调度任务结束中出现了内存泄漏，最终导致多次调度之后内存溢出
- 这种产生的内存溢出会导致应用执行下次的调度任务执行。同样重启可以恢复应用使用，但是在调度执行一段时间之后依然会出现内存溢出
### 解决内存溢出的方法
解决内存溢出就像医生给你看病一样，先要问你的症状，然后判断是何种原因引起的，在进行医治，最后吃药等待病好，所以解决内存溢出分为以下几个步骤：
- <mark style="background: #FF5582A6;">发现问题</mark>：通过一系列诊断工具
- <mark style="background: #FF5582A6;">诊断原因</mark>：通过分析工具诊断出问题的根源
- <mark style="background: #FF5582A6;">修复问题</mark>：修复源代码
- <mark style="background: #FF5582A6;">测试验证</mark>：看问题是否解决

#### 发现问题
<mark style="background: #FF5582A6;">Top 命令</mark>：
- Top 命令是 linux 下用来查看系统信息的一个命令，它提供给我们去实时地去查看系统的资源，比如执行时的进程、线程和系统参数等信息
- 进程使用的内存为 RES（常驻内存）- SHR（共享内存）
![[Pasted image 20231129213659.png]]
- 优点：操作简单，无需安装其他软件
- 缺点：只能看见最基础的进程信息，不能看到每个内存区域所占的大小

<mark style="background: #FF5582A6;">VisualVM 工具：</mark>
- VisualVM 是多功能合一的 Java 故障排除工具并且他是一款可视化工具，整合了命令行 JDK 工具和轻量级分析功能，功能非常强大
- 这款软件在 Oracle JDK 6~8 中发布，但是在 Oracle JDK 9 之后不在 JDK 安装目录下需要单独下载
- 下载地址：[VisualVM: Home](https://visualvm.github.io/)
- <mark style="background: #FFB86CA6;">根据 IDEA 插件绑定 VisualVM 工具：</mark>
![[Pasted image 20231129215215.png]]
点击安装确定后再次点击设置，打开其他设置
![[Pasted image 20231129215736.png]]
绑定本地 VisualVM
![[Pasted image 20231129220019.png]]
这样本地的 java 项目启动时就可查看内存的变化了，启动并加载 VisualVM 工具
![[Pasted image 20231129220150.png]]
- <mark style="background: #FFB86CA6;">如何连接远程服务：</mark>
```shell 
"VisualVM远程连接命令"
java -jar -Djava.rmi.server.hostname=服务器ip地址 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9122 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false jvm-service.jar
```
点检 remote 远程服务，并填写主机地址和端口号，不要开启 SSH 加密功能
![[Pasted image 20231129220701.png]]
![[Pasted image 20231129220719.png]]
- VisualVM 不能用于生产环境上，因为生产环境的运行会影响用户的使用，VisualVM 有对内存 GC 的功能，应用于测试环境上

<mark style="background: #FF5582A6;">Arthas 工具：</mark>
- Arthas 是一款线上监控诊断产品，通过全局视角实时查看应用 load、内存、gc、线程的状态信息，并能在不修改应用代码的情况下，对业务问题进行诊断，包括查看方法调用的出入参、异常，监测方法执行耗时，类加载信息等，大大提升线上问题排查效率
- 优点：功能强大，不止于监控基础的信息，还能监控单个方法的执行耗时等细节内容，支持应用的集群管理
- 缺点：部分高级功能使用门槛较高
- 
