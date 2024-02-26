***
## Redis 入门
### Redis 简单介绍
**redis 是一种键值型的 NoSQL 数据库**
- 键值型：redis 中的数据都是以 key-value 的形式进行存储，value 的形式多种多样，可以是字符串，数值，甚至 json 格式
- NoSQL: 非关系型数据库，对于存储的数据没有 MySQL 那么严格的约束
### 认识 NoSQL 数据库
**NoSQL 可以翻译为 Not only SQL (不仅仅是 SQL)**
关系型数据库与非关系型数据库的区别：

| 场景     | 关系型数据库                               | 非关系数据库                           |
| -------- | ------------------------------------------ | -------------------------------------- |
| 数据结构 | 结构化（数据用表格来组织）                 | 非结构化（键值型，文档图等多种结构）   |
| 数据关联 | 关联的（外键建立表之间联系）               | 非关联                                 |
| 事务特征 | 支持 ACID                                  | CAP                                    |
| 存储方式 | 磁盘                                       | 内存                                   |
| 扩展性   | 垂直扩展（升级硬件）                       | 水平扩展（添加更多节点）               |
| 使用场景 | 数据结构固定，对数据的安全性，一致性要求高 | 数据结构不固定，对安全性，一致性要求低 |
#### 结构化与非结构化
**传统关系型数据库是结构化数据，每⼀张表都有严格的约束信息：字段名.字段数据**
**类型.字段约束等等信息，插⼊的数据必须遵守这些约束**
#### 关联和非关联
**传统数据库的表与表之间往往存在关联，例如外键，⽽⾮关系型数据库不存在关联关系，要维护关系要么靠代码中的业务逻辑，要么靠数据之间的耦合**
#### 查询方式
**传统关系型数据库会基于 Sql 语句做查询，语法有统⼀标准；⽽不同的⾮关系数据库查询语法差异极⼤，五花⼋⻔各种各样**

**总结：**
- 存储方式
	- 关系型数据库基于磁盘进行存储，会有大量的磁盘 IO, 对性能有一定影响
	- 非关系型数据库，操作依赖于内存来操作，内存的读写速度远远快于磁盘的读写速度
- 扩展性
	- 关系型数据库一般是主从，主从数据库一致起到备份的作用，称为垂直扩展
	- 非关系型数据库可以将数据拆分，存储在不同的机器上，可以保存海量数据，解决内存大小有限的问题，称为水平扩展
	- 关系型数据库因为表与表之间存在关联关系，如果做水平扩展会给数据查询带来很多麻烦
### 认识 Redis
#### Redis 的特征
- 键值型，value 支持多种不同的数据结构
- 单线程，每个命令具有原子性
- 低延迟，速度快
- 支持数据持久化
- 支持主从集群，分片集群
- 支持多语言客户端
### 安装 Redis
⼤多数企业都是基于 Linux 服务器来部署项⽬，⽽且 Redis 官⽅也没有提供 Windows 版本的安装包。因此课程中我们会基于 Linux 系统来安装 Redis
#### 依赖库
Redis 是基于 C 语⾔编写的，因此⾸先需要安装 Redis 所需要的 gcc 依赖：
```shell
yum install -y gcc tcl
```
执行安装命令
```shell
wget http https://download.redis.io/releases/redis-6.2.10.tar.gz
```
![[Pasted image 20240110142246.png]]
#### 上传安装包并解压
将刚刚下载的压缩包移动到/usr/local/bin 目录下
```shell
mv redis-6.2.10.tar.gz /usr/local/bin
```
解压
```shell
tar -zxvf redis-6.2.10.tar.gz
```
进入 redis 目录，运行编译命令
```shell
cd redis-6.2.10
make
make install
```
该⽬录已经默认配置到环境变量，因此可以在任意⽬录下运⾏这些命令。其中：
- `redis-cli`: redis 提供的命令行客户端
- `redis-server`: redis 服务端启动脚本
- `redis-sentinel`: redis 哨兵启动脚本
### 启动 Redis
#### 默认启动
安装完成后，在任意目录下输入 `redis-server` 命令即可启动 redis
![[Pasted image 20240110143758.png]]

> [!NOTE] Title
> 这种属于前台启动，会阻塞整个命令窗口，按 `Ctrl C` 结束 redis 进程, 一般不会使用
#### 后台运行
如果想要 redis 以后台的方式进行启动，必须修改 redis 的配置文件，就在我们解压之后的安装包目录下（`/usr/local/bin/redis-6.2.6`）, 名字叫做 `redis.conf`
![[Pasted image 20240110144215.png]]

先将 `redis.conf` 备份一份
```shell
cp redis.conf redis.conf.bak
```
然后修改 `redis.conf` 中的配置信息
```shell
vim redis.conf
```
```shell
#允许访问的地址，默认是127.0.0.1，只能本地访问，修改为0.0.0.0表示任意主机可以访问
bind 0.0.0.0
# 设置是否后台运行，默认是no,修改为yes表示后台运行
daemonize yes
# 设置登录redis的密码
requirepass 17723670835yang
# 监听端口
port 6379
# 工作目录，默认是当前目录
dir .
# 数据库数量，设置为1，代表只使用一个库，默认有16个库，编号0~15
databases 1
# 设置redis能够使用的最大内存
maxmemory 512mb
# 日志文件 默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```
修改完之后退出保存
```
:wq
```
在 redis 安装目录下启动 redis
```
redis-server redis.conf
```
停止服务
```shell
# 利⽤redis-cli来执⾏ shutdown 命令，即可停⽌ Redis 服务，
# 因为之前配置了密码，因此需要通过 -u 来指定密码
redis-cli -u 17723670835yang shutdown
```
#### 开机自启动
创建一个系统服务文件
```shell
vim /etc/systemd/system/redis.service
```
```shell
[Unit]
Description=redis-server
After=network.target
[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /usr/local/bin/redis-6.2.10/redis.conf
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```
重载系统服务
```shell
systemctl daemon-reload
```
使用系统命令操作 redis 服务
```shell
# 启动
systemctl start redis
# 停⽌
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
```
设置开机自启动
```shell
systemctl enable redis
```
打开 redis 的防火墙端口号
![[Pasted image 20240110153133.png]]
### Redis 客户端
安装完成 redis, 我们就可以操作 redis 了，实现数据的 CRUD, 这需要利用到 redis 的客户端
- 命令行客户端
- 图形化客户端
- 编程客户端
#### 命令行客户端
Redis 安装完之后默认自带命令行客户端，使用方式如下：
```shell
redis-cli [options] [commonds]
```
常见的 options:
- `-h`: 连接 redis 节点的 ip 地址，默认是 127.0.0.1
- `-p`: 连接 redis 节点的端口号，默认是 6379
- `-a`: 指定 redis 访问密码
#### 图形化桌面客户端
下载地址 : https://github.com/lework/RedisDesktopManager-Windows/releases
![[Pasted image 20240110153744.png]]
连接 redis
![[Pasted image 20240110153828.png]]
![[Pasted image 20240110153909.png]]

## Redis 常见命令
### Redis 数据结构
**redis 是一个 key-value 的数据库，key 一般是 String 类型，Value 可以是多种类型**

| value     | 类型      |
| --------- | --------- |
| String    | 字符串    |
| Hash      | 字典      |
| List      | 列表      |
| set       | 集合      |
| SortedSet | 有序集合  |
| GEO       | 坐标      |
| BitMap    | 01 字符串 |
| HyperLog  | 01 字符串 |
### Redis 通用命令
- `KEYS`: 查看符合模板的所有 key
- `DEL`: 删除一个指定的 key
- `EXISTS`: 判断 key 是否存在
- `EXPIRE`: 设置一个 key 的有效期，默认单位为秒
- `TTL`: 查看 key 的剩余有效期
![[Pasted image 20240114151234.png]]
<mark style="background: #ABF7F7A6;">在⽣产环境下，不推荐使⽤ keys 命令，因为这个命令在 key 过多的情况下，效率不⾼</mark>
![[Pasted image 20240114151517.png]]
![[Pasted image 20240114151817.png]]
![[Pasted image 20240114152123.png]]

<mark style="background: #ABF7F7A6;">内存十分的宝贵，一些数据应该给他指定过期时间，时间到期之后被自动删除，例如短信验证码</mark>
![[Pasted image 20240114152504.png]]
![[Pasted image 20240114152631.png]]

>如果没有设置有效期，ttl 返回值为-1

### String 类型
String 类型也就是字符串类型，是 Redis 中最简单的类型。Value 是字符串，不过根据字符串的格式又可以分为 3 类：
- String: 普通字符串
- Int: 整数类型，可以做自增自减操作
- Float: 浮点类型，可以做自增自减操作
>不管哪种类型，底层都是字节数组形式存储，只不过编码方式不同，字符串类型不能超过 512 M


> [!NOTE] String 常见的命令
> - <mark style="background: #ABF7F7A6;">SET</mark>: 添加或者修改一个已存在的 String 类型键值对
> - <mark style="background: #ABF7F7A6;">GET</mark>: 根据 key 获取 String 类型的 value 值
> - <mark style="background: #ABF7F7A6;">MSET</mark>: 批量添加 String 类型的键值对
> - <mark style="background: #ABF7F7A6;">MGET</mark>: 根据多个 key 获取多个 String 类型的 value
> - <mark style="background: #ABF7F7A6;">INCR</mark>: 让一个 int 类型的 value 自增 1
> - <mark style="background: #ABF7F7A6;">INCRBY</mark>: 让一个 int 类型的 value 自增指定步长
> - <mark style="background: #ABF7F7A6;">INCRBYFLOAT</mark>: 让一个浮点类型的 value 自增指定步长
> - <mark style="background: #ABF7F7A6;">SETNX</mark>: 添加一个键值对，如果 key 存在, 则不执行
> - <mark style="background: #ABF7F7A6;">SETEX</mark>: 添加一个键值对，并设置有效期

![[Pasted image 20240114155236.png]]

![[Pasted image 20240114155521.png]]

![[Pasted image 20240114160215.png]]
![[Pasted image 20240114160628.png]]
![[Pasted image 20240114160854.png]]
### Redis 的层级结构
<mark style="background: #FFB86CA6;">Redis 没有类似 MySQL 中的 Table 的概念，我们该如何区分不同类型的 key 呢？</mark>
例如，需要存储⽤户.商品信息到 redis，有⼀个⽤户 id 是 1，有⼀个商品 id 恰好也是 1，此时如果使⽤ id 作为 key，那就会冲突了，该怎么办？
我们可以通过给 key 添加前缀加以区分，不过这个前缀不是随便加的，有⼀定的规范：
>Redis 中的 key 允许有多个单词形成的层级结构，多个单词之间用 ：隔开，格式如下
> `项目名:业务名:类型:id`

例如我们的项目名叫做 heima, 有 user 和 product 两种不同类型的数据，我们可以这样定义 key:
- User 相关的 key：`heima:user:1`
- Product 相关的 key: `heoma:product:1`
**如果 value 是一个对象，比如 User 对象, 我们可以将对象序列化为 JSON 字符串进行存储**
![[Pasted image 20240114163524.png]]
### Hash 类型
Hash 类型也叫散列，其 value 是一个无序字典，类似于 java 中的 hashmap 结构
<mark style="background: #ABF7F7A6;">String 类型是将对象序列化为 JSON 字符串，当需要修改对象的某个字段时，会变得不方便，hash 结构可以将对象中的每一个字段独立存储，可以对单个字段做 CRUD</mark>
![[Pasted image 20240114184903.png]]

> [!NOTE] Hash 常见命令
> - HSET key field value: 添加或者修改 Hash 类型的 key 的 value 值
> - HGET key field: 获取一个 hash 类型 key 的 field 的值
> - HMSET: 批量添加多个 hash 类型
> - HMGET: 批量获取多个 hash 类型的 field 值
> - HGETALL: 获取一个 hash 类型中所有的 field 值和 value 值
> - HKEYS: 获取一个 hash 类型中所有的 field 值
> - HINCRBY: 让 hash 类型中所有 value 值指定步长增加
> - HSETNX: 添加一个 hash 类型的 field 值和 value 值，如果 field 存在，则不做任何操作

![[Pasted image 20240114190406.png]]
![[Pasted image 20240114190657.png]]
![[Pasted image 20240114190902.png]]
![[Pasted image 20240114191056.png]]
![[Pasted image 20240114191310.png]]
![[Pasted image 20240114191718.png]]
### List 类型
>Redis 中的 List 类型与 java 中的 LinkedList 相似，可以看做一个双向链表结构，既可以支持正向检索功能也可以支持反向检索功能

**特征也与 LinkedList 类似**：
- 有序
- 元素可以重复
- 插入和删除速度快
- 查询速度一般
常用来存储一个<mark style="background: #ABF7F7A6;">有序数据</mark>，例如朋友圈点赞列表，评论列表

> [!NOTE] List 常见命令
> - LPUSH key element: 向列表左侧插入一个或多个元素
> - LPOP key: 移除并返回列表左侧第一个元素，没有则返回 nil
> - RPUSH key element: 向列表右侧插入一个或多个元素
> - RPOP key: 移除并返回列表右侧第一个元素，没有则返回 nil
> - LRANGE key start end: 返回一段角标范围内的所有元素
> - BLPOP 和 BRPOP: 与 LPOP 和 RPOP 类似，只不过在没有元素时会等待指定时间，而不是直接返回 nil

![[Pasted image 20240114193717.png]]
![[Pasted image 20240114193844.png]]
### Set 类型

>Redis 中 set 结构与 java 中的 hashSet 类似，可以看做是一个 value 为空的 hashMap, 因此 set 具有以下特征：
> - 无序
> - 元素不可以重复
> - 查找快
> - 支持交集，并集，差集等功能

> [!NOTE] Set 类型常见命令
> - SADD key mumber: 向 set 中添加一个或多个元素
> - SREM key menber: 向 Set 中删除指定元素
> - SCARD key: 返回 set 集合中的元素个数
> - SISMEMBER key member: 判断一个元素是否存在于 set 集合中
> - SMEMBERS: 获取 set 中所有元素
> - SINTER key 1 key 2: 求 key 1 与 key 2 的交集
> - SDIFF key 1 key 2: 求 key 1 与 key 2 的差集
> - SUNION key 1 key 2: 求 key 1 与 key 1 的并集

![[Pasted image 20240114195605.png]]

<mark style="background: #FFB86CA6;">案例</mark>
将下列数据⽤ Redis 的 Set 集合来存储：
张三的好友有：李四.王五.赵六
`SADD zs ls ww zl`
李四的好友有：王五.麻⼦.⼆狗
`SADD ls ww mz eg`
<mark style="background: #FFB86CA6;">利⽤ Set 的命令实现下列功能：</mark>（集合操作）
计算张三的好友有⼏⼈
`SCARD zs`
计算张三和李四有哪些共同好友
`SINTER zs ls`
查询哪些⼈是张三的好友却不是李四的好友
`SDIFF zs ls`
查询张三和李四的好友总共有哪些⼈
`SUNION zs ls`
判断李四是否是张三的好友
`SISMEMBER zs ls`
判断张三是否是李四的好友
`SISMEMBER ls zs`
将李四从张三的好友列表中移除
`SREM zs ls`
### SortedSet 类型
>Redis 的 SortedSet 是⼀个可排序的 set 集合，与 Java 中的 TreeSet 有些类似，但底层数据结构却差别很⼤。SortedSet 中的每⼀个元素都带有⼀个 score 属性，可以基于 score 属性对元素排序，底层的实现是⼀个跳表（SkipList）加 hash 表

SortedSet 的特性如下：
- 可排序
- 元素不可重复
- 查询速度快
**因为 SortedSet 具有排序功能，所以经常用来做排行榜**

> [!NOTE] SortedSet 常用命令
> - ZADD key score member: 添加一个或多个元素到 sortedset, 如果已经存在则更新 score
> - ZREM key member: 删除 sortedSet 中的指定元素
> - ZSCORE key member: 获取 sortedset 中指定元素的 score
> - ZRANK key member: 获取 sortedset 中指定元素的排名
> - ZCARD key: 获取 sortedset 中元素个数
> - ZCOUNT key min max: 统计指定 score 范围内的所有元素
> - ZINCRBY key increment member: 让 sortedset 中指定元素增长指定步长，步长为 increment
> - ZRANGE key min max: 按照 score 排序后，指定排名范围内的元素

## Redis 客户端
### Jedis 快速入门

> [!NOTE] 引入依赖
```xml
<!--jedis-->
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>3.7.0</version>
</dependency>
<!--单元测试-->
<dependency>
	<groupId>org.junit.jupiter</groupId>
	<artifactId>junit-jupiter</artifactId>
	<version>5.7.0</version>
	<scope>test</scope>
</dependency>
```

>Jedis 本身是线程不安全的，并且频繁的创建和销毁连接会有性能损耗，因此推荐使用 Jedis 连接池替代 Jedis 直连方式

> [!NOTE] 创建 Jedis 连接池
```java
public class JedisConnectionFactory {  
    private static final JedisPool jedisPool;  
    static {  
        //配置连接池  
        JedisPoolConfig poolConfig=new JedisPoolConfig();  
        poolConfig.setMaxTotal(8);  
        poolConfig.setMaxIdle(8);  
        poolConfig.setMinIdle(0);  
        poolConfig.setMaxWaitMillis(1000);  
        //创建连接池对象  
        jedisPool = new JedisPool(poolConfig, "111,229,199,44", 6379,1000,"17723670835yang");  
    }  
    public static Jedis getJedis(){  
        return jedisPool.getResource();  
    }  
}
```
- `JedisConnectionFactory`: 利用了设计模式中的工厂模式，对象的创建任务交给一个工厂类去完成，降低程序的耦合度
- `静态代码块`：在类加载时执行静态代码块中内容，完成连接 redis 数据库的操作
- `getJedis()`: 静态方法，返回连接池中的一个连接

> [!NOTE] 创建测试类
```java
private Jedis jedis;  
@BeforeEach  
void setUp(){  
    jedis=JedisConnectionFactory.getJedis();  
    jedis.select(0);//选择库  
}  
@AfterEach  
void tearDown(){  
    if(jedis!=null){  
        jedis.close();  
	}
}
```
- `BeforeEach`: JUnit 框架中的一个注解，用于标识在一个方法上，每个测试方法执行前都会先执行该方法，用来准备测试环境
- `AfterEach`: 和 BeforeEach 注解成对的，用于在所有测试方法执行完毕后做的后续操作，关闭资源

> [!NOTE] 测试案例
```java
@Test  
public void testString(){  
    //存入数据  
    String res = jedis.set("name", "虎哥");  
    System.out.println("result=" + res);  
    //获取数据  
    String name = jedis.get("name");  
    System.out.println(name);  
}
```
- `set`: 用于添加 String 类型的键值对
- `get`：根据 key 值获取 value 值
![[Pasted image 20240115162405.png]]
```java
@Test  
public void testHash(){  
    //存入数据  
    jedis.hset("user:1", "name", "Jack");  
    jedis.hset("user:1", "age", "21");  
    //获取  
    Map<String, String> user = jedis.hgetAll("user:1");  
    System.out.println(user);  
}
```
![[Pasted image 20240115162712.png]]
### SpringDataRedis
SpringData 是 Spring 中数据操作模块，包含对各种数据库的集成，其中对 Redis 集成的模块就叫做 SpringDataRedis

> [!NOTE] SpringDataRedis 的特点
> - 提供对不同 Redis 客户端的整合（Lettuce 和 Jedis）
> - 提供 RedisTemplate 同一 API 来操作 Redis
> - 支持 Redis 的发布订阅模型
> - 支持 Redis 哨兵和 Redis 集群
> - 支持基于 Lettuce 的响应式编程
> - 支持基于 JDK. JSON. 字符串. Spring 对象的数据序列化和反序列化

<mark style="background: #ABF7F7A6;">SpringDataRedis 中提供了 RedisTemplate ⼯具类，其中封装了各种对 Redis 的操作。并且将不同数据类型的操作 API 封装到了不同的类型中：</mark>
![[Pasted image 20240115163624.png]]
#### 快速入门
> [!NOTE] 引入依赖
```xml
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>2.5.7</version>  
    <relativePath/> <!-- lookup parent from repository -->  
</parent>
<dependencies>
	<!--redis依赖-->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-redis</artifactId>
	</dependency>
	<!--common-pool-->
	<dependency>
		<groupId>org.apache.commons</groupId>
		<artifactId>commons-pool2</artifactId>
	</dependency>
	<!--Jackson依赖-->
	<dependency>
		<groupId>com.fasterxml.jackson.core</groupId>
		<artifactId>jackson-databind</artifactId>
	</dependency>
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<optional>true</optional>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
			<excludes>
				<exclude>
					<groupId>org.projectlombok</groupId>
					<artifactId>lombok</artifactId>
				</exclude>
			</excludes>
			</configuration>
		</plugin>
	</plugins>
</build>
```
>SpringBoot 已经提供了对 SpringDataRedis 的支持，只需要引入依赖即可


> [!NOTE] 配置文件
```yml
spring:  
  redis:  
    host: 111.229.199.44  
    port: 6379  
    password: 17723670835yang  
    lettuce:  
      pool:  
        max-active: 8 #最大连接数  
        max-idle: 8 #最大空闲连接  
        min-idle: 0 #最小空闲连接  
        max-wait: 1000 #连接等待时间（毫秒）
```

> [!NOTE] 编写测试类
```java
@SpringBootTest(classes = RedisDemoApplicationTest.class)  
public class RedisDemoApplicationTest {  
    @Resource  
    private RedisTemplate<String,Object> redisTemplate;  
    @Test  
    void testString(){  
        //添加String类型的键值对  
        redisTemplate.opsForValue().set("name", "yhk");  
        //获取String数据  
        Object name = redisTemplate.opsForValue().get("name");  
        System.out.println("name=" + name);  
    }  
}
```
#### 数据序列化器
RedisTemplate 可以接受任意 Object 作为值写入 Redis,只不过写⼊前会把 Object 序列化为字节形式，默认是采⽤ JDK 序列化, 这样的后果就是：可读性差，内存占用大

> [!NOTE] 自定义 RedisTemplate 序列化方式
```java
@Configuration
public class RedisConfig {
	@Bean
	public RedisTemplate<String, Object> redisTemplate(RedisConnection
		// 创建RedisTemplate对象
		RedisTemplate<String, Object> template = new RedisTemplate<>()
		// 设置连接⼯⼚
		template.setConnectionFactory(connectionFactory);
		// 创建JSON序列化⼯具
		GenericJackson2JsonRedisSerializer jsonRedisSerializer =
		new GenericJackson2JsonRedisSerializer();
		// 设置Key的序列化
		template.setKeySerializer(RedisSerializer.string());
		template.setHashKeySerializer(RedisSerializer.string());
		// 设置Value的序列化
		template.setValueSerializer(jsonRedisSerializer);
		template.setHashValueSerializer(jsonRedisSerializer);
		// 返回
		return template;
	}
}
```
>用 JSON 序列化的方式代替 JDK 序列化方式，老实说看着就麻烦😱

整体可读性有了很⼤提升，并且能将 Java 对象⾃动的序列化为 JSON 字符串，并且查询时能⾃动把 JSON 反序列化为 Java 对象。不过，<mark style="background: #ABF7F7A6;">其中记录了序列化时对应的 class 名称</mark>，⽬的是为了查询时实现⾃动反序列化。这会带来额外的内存开销
#### StringRedisTemplate
>很显然自动序列化会在 redis 中保存对应的 class 名称，这会占用大量内存，不如自己手动序列化和反序列化

> [!NOTE] 手动序列化
```java
@SpringBootTest
class RedisStringTests {
	@Autowired
	private StringRedisTemplate stringRedisTemplate;
	@Test
	void testString() {
		// 写入⼀条String数据
		stringRedisTemplate.opsForValue().set("verify:phone:1360052763
		// 获取string数据
		Object name = stringRedisTemplate.opsForValue().get("name");
		System.out.println("name = " + name);
	}
	private static final ObjectMapper mapper = new ObjectMapper();
	@Test
	void testSaveUser() throws JsonProcessingException {
		// 创建对象
		User user = new User("虎哥", 21);
		// ⼿动序列化
		String json = mapper.writeValueAsString(user);
		// 写入数据
		stringRedisTemplate.opsForValue().set("user:200", json);
		// 获取数据
		String jsonUser = stringRedisTemplate.opsForValue().get("user:
		// ⼿动反序列化
		User user1 = mapper.readValue(jsonUser, User.class);
		System.out.println("user1 = " + user1);
	}
}
```


> [!NOTE] java 中序列化和反序列化
1. 使用 Jackson 库
```java
ObjectMapper objectMapper = new ObjectMapper();
String jsonString = objectMapper.writeValueAsString(myObject);//序列化为JSON
```
```java
ObjectMapper objectMapper = new ObjectMapper();
MyClass myObject = objectMapper.readValue(jsonString, MyClass.class);//反序列化为java对象
```
2. 使用 Gson 库
```java
Gson gson = new Gson();
String jsonString = gson.toJson(myObject);//序列化为JSON
```
```java
Gson gson = new Gson();
MyClass myObject = gson.fromJson(jsonString, MyClass.class);//反序列化为java对象
```
3. 使用 Hutool 库（推荐）
```java
MyClass myObject=JSONUtil.toBean(jsonString,MyClass.class);
String jsonString=JSONUtil.toJsonStr(myObject);
```



