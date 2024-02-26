***
## 1、Spring 是什么😀
>- Spring 是一个轻量级、非侵入式的控制反转和面向切面的框架
>- Spring 框架大大的提高了我们开发的效率和程序的可维护性
>- 说起 Spring 就离不开它的两大特性 IoC 和 AOP

## 2、Spring 有哪些特性😀
>- 方便解耦，简化开发，Spring 就是一个大工厂，可以将所有对象的创建与依赖关系交给 spring 管理
>- 面向切面编程 (AOP)，方便对交叉业务进行封装
>- 声明式事务，通过注解实现对事务的管理，无需手动编码
>- 方便程序测试，支持 junit 单元测试
>- 方便集成各种优秀的框架，比如 hibernate. Mybatis
>- 降低 java EE API 的使用难度
## 3、Spring 常用的注解😀
>- @RestController: 就是 Controller 注解和 ResponseBody 注解的组合，Controller 注解应用在控制层，纳入 spring 容器的管理，ResponseBody 注解是将返回的值以 json 的格式放入响应体中
>- @RequestMapping, 用于映射 web 请求，还可以根据请求类型选择对应的注解，比如 GetMapping, PostMapping
>- @RequestBoby, 就是将请求的参数放入请求体中，而不用放在地址栏后面
>- @PathVariable: 用于接收路径参数
>- @Autowired: 自动注入 spring 管理的 bean, 根据类型进行注入，可以和 Qualifier 一起使用，根据名称进行注入
>- @Configuration: 声明一个配置类，取代配置文件
>- @Bean: 用在方法上，返回一个 bean 纳入 spring 管理
>- @Aspect: 声明一个切面类

## 4、Spring 中用了哪些设计模式😅
>- 工厂模式：Spring 容器本身就是一个大工厂，通过工厂 模式实现 BeanFactory
>- 代理模式：Spring 的 AOP 就是通过代理模式来实现的，分为静态代理和动态代理
>- 单例模式：spring 中的 bean 默认都是单例的
>- 模版模式：spring 中的 jdbcTemplate 就用到了模板模式
>- 观察者模式：spring 事件驱动模型就是观察者模式的应用
>- 适配器模式：
>- 策略模式：

## 5、什么是控制反转，依赖注入😀
>控制反转就是由容器来负责对象的生命周期和对象之间的关系
>依赖注入就是在实例化对象时把它依赖的类注入给它
## 6、说说 BeanFactory 和 ApplicationContext😀
>BeanFactory 是类的通用工厂，也是 spring 顶级接口，可以创建并管理各种类的对象
>ApplicationContext 是 BeanFactory 的子类，提供了更多面向实际应用的功能

## 7、Spring 容器启动阶段会干什么😀
>IOC 容器工作可以分为两阶容器段：容器启动阶段，Bean 实例化阶段
>容器启动阶段主要负责加载和解析配置文件，将解析后配置信息保存到 BeanDefinntion 这个接口，再将 BeanDefinition 注册到相应的 BeanDefinitionRegistry

## 8、Spring 容器的生命周期😥
>Spring 的生命周期细分可以分为 10 步：
>- 实例化 Bean
>- 为对象赋值
>- 检查 Awaer 相关接口，并设置相关依赖，比如调用 BeanNameAwaer 设置 bean 的名称，调用 BeanFactoryAweaer 为 bean 的创建选择工厂
>- 初始化之前通过调用 BeanPostProcesser 接口进行前置处理
>- 再判断是否实现 InitailizingBean 接口，进行登记操作
>- 调用 init 方法进行初始化
>- 调用 BeanPostProcesser 进行后置处理
>- 注册回调接口
>- 使用 bean, 使用完就进行销毁操作
>- 检查是否实现 DisposableBean 接口
>- 调用 destory 销毁方法

## 9、Bean 的依赖注入的方式😥
>- 直接编码方式，我们一般接触不到直接编码方式，但最终都是通过编码的方式实现的
>- 配置文件方式，通过配置文件的编写完成对 bean 的属性注入
>- 注解方式，最常用的方式，spring 扫描注解完成对 bean 依赖关系的注入
## 10、依赖注入有哪几种方式😀
>- 宏观上可以分为 xml 配置文件注入和通过注解的方式进行注入
>- xml 配置文件注入，可以分为 set 方法注入，构造方法注入，静态工厂方法注入和实例工厂方法注入
>- 静态工厂方法注入，就是将对象创建的过程封装到静态方法中，当我们调用一个对象时，不用关心对象创建的细节，直接调用静态方法即可
>- 实例工厂方法注入就是获取对象的方法不是静态的，我们要先把工厂对象创建出来，在通过对象调用普通的实例方法，工厂对象要纳入 Spring 容器的管理，工厂所要创建的对象同样要纳入 Spring 容器的管理
>- 通过注解方法进行自动注入，主要有 `AutoWired` 注解，这个注解可以用在属性上也可以用在构造方法的形参中，默认先是通过类型进行自动注入，如果找到多个指定的类型，那就会通过 byName 的方法进行自动注入，当然你要注册的 bean 对象一定是纳入了 Spring 容器的管理中，可以联合 `Qualifier` 注解指定注入 bean 的名称
>- 构造方法的推断：类中同时提供了多个构造方法执行注入，如果开发者没有指定需要使用的构造方法，则 Spring 会自动根据一定的规则去选择构造方法的注入
>- `Resource` 注解进行注入，他是先根据名称进行注入，如果名称不匹配再根据类型进行注入，可以通过 name 属性指定 bean 的名称，如果没有 name 属性则根据成员变量的名称进行注入
## 11、Spring 中重要的组件
>- Core Container 是 Spring 的核心组件，其中的 Beans 和 Context 是实现 IoC 的基础
>- AOP 组件用来做面向切面编程
>- Web 组件实现表现层的实现，代表技术就是 SpringMVC 框架
>- ORM 用于支持其他第三方框架，像 MyBatis

## 12、Spring 框架的好处
>- 方便解耦，简化开发，将对象的创建与依赖关系交给 SpringIoC 容器进行管理，通过依赖注入可以大大降低程序之间的耦合度
>- AOP 面向切面编程，将程序中的一些交叉业务代码单独封装成一个模块进行集中的管理
>- 声明式事务，以前都是以硬编码的方式进行事务管理，如果使用声明式事务，我们就可以通过注解的方式玩成对事务的管理，这样我们可以更灵活的对事务进行管理，而且提高我们开发的效率，更专注对业务逻辑的开发
>- 方便程序的测试，Spring 框架支持 junit 单元测试，使我们测试的过程变得更简单
>- 方便集成各种优秀的框架，比如 hibernate, mybatis 等 orm 框架，而且还降低了框架的使用难度
>- 降低了 javaEE API 的使用难度，比如为 JDBC 的使用提供了简易的封装

## 13、聊聊 Spring 的 IoC
>如果在一个项目中不使用 IoC 容器的话，一旦类之间依赖关系增多，那类与类之间的关系管理起来就会非常的复杂，难以维护类的依赖关系，如果使用了 IoC 容器帮助我们进行创建对象和依赖关系管理，我们就可以更专注业务代码的开发
>IoC 是一种设计思想，将原本程序中手动创建对象的控制权交给 spring 框架管理，IoC 容器是 Spring 用来创建和管路对象的载体，实际上也是一个 Map 结构
>DI 是 IoC 的一种实现方式，在实例化一个对象时将他依赖的对象注入给他
>只有实现了控制反转，Spring 框架才会维护对象之间的依赖关系
## 14、Spring IoC 的实现原理
>Spring 容器启动过程中，他首先会去读取配置文件中关于 bean 的配置信息，并在 Spring 容器中生成一张 bean 定义的注册表，然后根据注册表利用反射机制实例化对应的 bean 对象，生成的 bean 对象会放进一个单例池中，当我们使用一个 bean 对象时，就从单例池中取出

## 15. AtuoWired 和 Resource 的区别
>@AutoWired 注解是 Spring 为我们提供，@Resource 是 JDK 自带的
>@AutoWired 注解是先通过 byType 的方式进行注入，再根据 byName 的方式进行注入
>@Resource 注解是先通过 byName 方式进行注入，如果没有找到指定名称的 bean 则通过 byType 的方式进行注入

## 16、@Primary 的使用
>在我们使用自动装配时，如果根据类型查询到多个匹配的 bean ,我们可以通过@Primary 注解做 bean 的优先级处理，被@Primary 注解标识的 bean 具有较高的优先级
>对于@AutoWired 注解自动装配流程，如果将@Qualifier 和@Primary 结合起来，装配的流程如下：
>- 先通过 AutoWired 根据 byType 的方式进行注入，如果根据类型查询到多个匹配的 bean, 再根据 Qualifier 的指定名称进行注入，如果没有 Qualifier, 再根据 Primary 扫描每个 bean, 通过 bean 的优先级进行注入，如果都没有，再根据 byName 的方式进行注入

## 17、@Component 和@Bean 的区别
>Component 注解是表示在类上的，通过该类创建的 对象都将纳入 Spring 容器的管理
>Bean 注解是用在配置类当中的方法上的，方法返回的一个对象将会被纳入 Spring 容器的管理
>Component 注解使用得较多，因为使用起来比较简单，Bean 注解注册的 bean 对象形式更加集中，可以将配置类当做早期的 spring 配置文件，Bean 注解就相当于 bean 标签，如果生产一个对象比较麻烦，需要做一些预处理工作，那么使用@bean 较为合适

## 18、@Value 中 '#'和‘$’的区别
>${}中的值都是来源于 Spring 的环境信息，例如 JVM 环境，yaml 配置文件中的信息，这些环境变量都是以键值对的方式进行存储的，可以通过对应的 key 值去得环境变量的值
>#{}是用来对属性赋值的，不但可以为基本类型赋值，还可以为引用类型注入对应名称的 bean

## 19、谈谈你对 SpringBean 的理解
>在 Spring 中，由 Spring 容器管理的对象称为 bean
>Bean 对象不单单只是实体类的对象，在 MVC 分层开发中，dao, service, controller 层都可以注册为 spring 的 bean
>Spring 的 bean 是通过控制反转帮我们创建出来的，他并不是通过 new 的方式创建出来的，而是通过反射的机制帮助我们创建对象，除此之外，在对象创建完毕之后还会进行一系列的操作，例如依赖注入，预处理，aop 等相关操作，最终得到的对象才是 spring 的 bean
>如果 bean 的创建通过了 aop 相关的操作，那么最终得到的对象就不是原始对象了，而是通过原始对象创建出来的代理对象



