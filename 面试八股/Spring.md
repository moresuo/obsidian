***
## 1、Spring 是什么😀
>Spring 是一个轻量级、非侵入式的控制反转和面向切面的框架

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
>- 构造方法注入，通过构造方法变量注入
>- set 方法注入，也就是属性注入
>- 工厂方法注入，工厂方法又分为静态工厂和实例工厂

