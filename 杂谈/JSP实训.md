***
## 2023-12-11
### 技术栈
#### 后端
- Servlet
- Jdbc
- Thymeleaf+ognl (动态页面渲染)
- Maven
#### 前端
- HTML
- CSS
- Js
- BootStrap
### 开发流程
- 项目立项
- 需求分析
	- 功能性需求分析
	- 非功能性需求分析
- 数据库设计
	- 数据字典设计
	- 逻辑设计
	- 物理设计
- 项目开发
	- UI
	- 前端
	- 后端
- 功能测试
- 项目提交、部署
- 市场推广
- 更新迭代
### 环境搭建
- JDK 搭建，推荐 java 8
- 安装 IDEA
- MySQL 8, navicat
Maven 仓库：[Maven Repository: Search/Browse/Explore (mvnrepository.com)](https://mvnrepository.com/)
引入 maven 依赖：
```xml
<dependencies>  
    <!--MySQL驱动-->  
    <dependency>  
        <groupId>mysql</groupId>  
        <artifactId>mysql-connector-java</artifactId>  
        <version>8.0.12</version>  
    </dependency>  
    <dependency>  
        <groupId>com.alibaba</groupId>  
        <artifactId>druid</artifactId>  
        <version>1.2.3</version>  
    </dependency>  
    <dependency>  
        <groupId>commons-dbutils</groupId>  
        <artifactId>commons-dbutils</artifactId>  
        <version>1.6</version>  
    </dependency>  
    <dependency>  
        <groupId>org.projectlombok</groupId>  
        <artifactId>lombok</artifactId>  
        <version>1.18.26</version>  
    </dependency>  
    <!--加入servlet依赖（servlet的jar）-->  
    <dependency>  
        <groupId>javax.servlet</groupId>  
        <artifactId>javax.servlet-api</artifactId>  
        <version>4.0.1</version>  
        <scope>provided</scope>  
    </dependency>  
    <!--hutool-->  
    <dependency>  
        <groupId>cn.hutool</groupId>  
        <artifactId>hutool-all</artifactId>  
        <version>5.7.17</version>  
    </dependency>  
</dependencies>
```
启动 tomcat 服务器：
![[Pasted image 20231211093316.png]]

- 服务启动默认加载 index. Html 页面，如果要指定启动页面，可以在 web 配置文件中配置
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"  
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"  
         version="4.0">  
    <welcome-file-list>  
        <welcome-file>login.html</welcome-file>  
    </welcome-file-list>  
</web-app>
```

### 项目执行流程
前端发送请求给服务器，获取数据，服务器从数据库中获取数据返回给前端
- Controller: 接收 web 请求
- Service: 处理业务逻辑
- Dao: 数据访问，操作数据
- Utils: 工具类
- Model：存放实体类
![[Pasted image 20231211150018.png]]
### JDBC 连接数据库
1. 导入 MySQL 驱动依赖和德鲁伊连接池，DBUtils
2. 加载驱动创建对象
3. 注册 Driver
4. 配置连接数据库的参数，username, password, driver, url
5. 建立连接关系
6. 操作数据库
7. 结果集处理
8. 回滚，释放连接

#### 创建 user 表

| 字段     | 类型       |
| -------- | ---------- |
| id       | int (主键) |
| name     | varchar    |
| password | varchar    |
| gender   | varchar    |

```sql
create table t_user  
(  
    id       int auto_increment comment '主键'  
        primary key,  
    name     varchar(255) null comment '姓名',  
    password varchar(255) not null comment '密码',  
    gender   varchar(1)   null comment '性别'  
);
```


> [!NOTE] 根据 user 表创建实体类
```java
package com.yhk.blog.model;  
  
import lombok.Data;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/11 16:31  
 * @注释 用户实体类  
 */  
@Data  
public class User {  
    private Integer id;  
    private String name;  
    private String password;  
    private String gender;  
}
```

#### 创建配置文件
```properties
 # druid配置文件
driverClassName=com.mysql.cj.jdbc.Driver  
url=jdbc:mysql://127.0.0.1:3306/blog?serverTimezone=UTC  
username=root password=17723670835yang  
initialSize=10  
minIdle=1  
maxActive=10  
maxWait=10000  
timeBetweenEvictionRunsMillis=6000  
minEvictableIdleTimeMillis=300000  
testWhileIdle=true  
testOnBorrow=true  
testOnReturn=true  
poolPreparedStatements=true  
maxPoolPreparedStatementPerConnectionSize=20  
validationQuery=select 1  
filters=stat
```
```properties
# 数据库连接配置文件
username=root  
password=17723670835yang  
url=jdbc:mysql://localhost:3306/book?serverTimezone=UTC&rewriteBatchedStatements=true  
driverClassName=com.mysql.cj.jdbc.Driver  
initialSize=5  
maxActive=10
```
#### 创建 JdbcUtil
- ThreadLocal 可以保存一个线程中的副本，保存的数据是线程隔离的，保证每个线程中的数据不受其他线程影响，可以用来保存数据库连接
- 在类加载阶段读取配置文件，注册 Driver

> [!NOTE] JdbcUtil
```java
package com.yhk.blog.utils;  
  
import com.alibaba.druid.pool.DruidDataSource;  
import com.alibaba.druid.pool.DruidDataSourceFactory;  
  
import javax.sql.DataSource;  
import java.io.IOException;  
import java.io.InputStream;  
import java.sql.Connection;  
import java.sql.SQLException;  
import java.util.Properties;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/11 15:44  
 * @注释 数据库连接池  
 */  
public class JdbcUtil {  
    private static ThreadLocal<Connection> conn = new ThreadLocal<>();//当前线程所有业务共享一个数据库连接  
    private static DruidDataSource druidDataSource;  
    static {  
        try {  
            Properties properties = new Properties();  
            //读取jdbc配置文件  
            InputStream in = JdbcUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");  
            //从流中加载数据  
            properties.load(in);  
            druidDataSource  = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);  
        } catch (Exception e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    /**  
     * 获取数据库连接  
     * @return connection  
     */    public static Connection getConnection(){  
        Connection con=conn.get();  
        if(con==null){  
            try {  
                con= druidDataSource.getConnection();  
                //将连接放入ThreadLocal  
                conn.set(con);  
            } catch (Exception e) {  
                throw new RuntimeException(e);  
            }  
        }  
        return con;  
    }  
  
    /**  
     * 提交并关闭数据库  
     */  
    public static void commitAndClose(){  
        Connection con=conn.get();  
        if(con!=null){  
            try {  
                con.commit();//提交  
            } catch (Exception e) {  
                throw new RuntimeException(e);  
            }finally {  
                try {  
                    con.close();//关闭数据库  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
        }  
        //最后一定要将ThreadLocal中的对象释放，否则可能会内存泄露  
        conn.remove();  
    }  
  
    /**  
     * 回滚并关闭数据库  
     */  
    public static void rollbackAndClose(){  
        Connection con=conn.get();  
        if(con!=null){  
            try {  
                con.rollback();//回滚  
            } catch (SQLException e) {  
                throw new RuntimeException(e);  
            }finally {  
                try {  
                    con.close();//关闭连接  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
        }  
        conn.remove();  
    }  
}
```
- 使用 DBUtils 封装增删改查的操作，提供一套模版方法供 dao 操作数据库

> [!NOTE] BaseDao
```java
package com.yhk.blog.dao;  
  
import com.yhk.blog.utils.JdbcUtil;  
import org.apache.commons.dbutils.QueryRunner;  
import org.apache.commons.dbutils.handlers.BeanHandler;  
import org.apache.commons.dbutils.handlers.BeanListHandler;  
import org.apache.commons.dbutils.handlers.ScalarHandler;  
  
import java.sql.Connection;  
import java.sql.SQLException;  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/11 16:01  
 * @注释 抽象父类，提供操作数据库的模版方法  
 */  
public abstract class BaseDao {  
    private QueryRunner queryRunner = new QueryRunner();  
  
    /**  
     * 执行insert/update/delete操作  
     * @param sql sql语句  
     * @param args 语句参数  
     * @return 返回-1.执行失败  
     */  
    public int update(String sql,Object...args){  
        Connection connection= JdbcUtil.getConnection();  
        try {  
            return queryRunner.update(connection, sql, args);  
        } catch (SQLException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    /**  
     * 查询一个javaBean  
     * @param sql sql语句  
     * @param type 返回类型  
     * @param args 参数  
     * @return 返回null,查询失败  
     * @param <T> 泛型  
     */  
    public <T> T queryForOne(String sql,Class<T> type,Object...args){  
        Connection connection=JdbcUtil.getConnection();  
        try {  
            return queryRunner.query(connection, sql, new BeanHandler<>(type), args);  
        } catch (SQLException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    /**  
     * 查询多个javaBean  
     * @param sql sql  
     * @param type 类型  
     * @param args 参数  
     * @return  
     * @param <T>  
     */  
    public <T> List<T> queryForList(String sql,Class<T> type,Object...args){  
        Connection connection=JdbcUtil.getConnection();  
        try {  
            return queryRunner.query(connection, sql, new BeanListHandler<>(type), args);  
        } catch (SQLException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    /**  
     * 返回单个字段  
     * @param sql  
     * @param args  
     * @return  
     */  
    public Object queryForSingleValue(String sql,Object...args){  
        Connection connection=JdbcUtil.getConnection();  
        try {  
            return queryRunner.query(connection,sql, new ScalarHandler(), args);  
        } catch (SQLException e) {  
            throw new RuntimeException(e);  
        }  
    }  
}
```
- 通过继承 BaseDao 操作数据库
#### 测试数据库连接
```java
package com.yhk.blog.test;  
  
import com.yhk.blog.dao.BaseDao;  
import com.yhk.blog.dao.impl.UserDaoImpl;  
import com.yhk.blog.model.User;  
import org.junit.Test;  
  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/11 16:41  
 * @注释  
 */  
public class JdbcTest {  
    private BaseDao baseDao=new UserDaoImpl();  
    @Test  
    public void test01(){  
        String sql="select * from t_user";  
        List<User> users = baseDao.queryForList(sql, User.class);  
        System.out.println(users);  
    }  
    @Test  
    public void test02(){  
        String sql="select * from t_user where id=?";  
        User user = baseDao.queryForOne(sql, User.class, 1);  
        System.out.println(user);  
    }  
    @Test  
    public void test03(){  
        User user=new User();  
        user.setName("jack");  
        user.setPassword("123456");  
        user.setGender("女");  
        String sql = "insert into t_user values(null,?,?,?)";  
        int count = baseDao.update(sql, user.getName(), user.getPassword(), user.getGender());  
        System.out.println(count);  
    }  
    @Test  
    public void test04(){  
        String sql = "select name from t_user where id=?";  
        Object name = baseDao.queryForSingleValue(sql, 1);  
        System.out.println(name);  
    }  
}
```


### Servlet
- 使用注解的方式完成对 web 请求的映射，`@WebServlet`，里面填写请求路径
- 创建一个 Servlet 类
![[Pasted image 20231211173257.png]]
- 通过浏览器地址栏访问对应的 web 请求路径
```java
package com.yhk.blog.controller; /**  
 * @Author moresuo  
 * @Date 2023/12/11 17:32  
 * @version 1.0  
 * @注释  
 */  
  
import javax.servlet.*;  
import javax.servlet.http.*;  
import javax.servlet.annotation.*;  
import java.io.IOException;  
  
@WebServlet("/user")  
public class UserServlet extends HttpServlet {  
    @Override  
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  
        System.out.println("servlet");  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  
        this.doGet(request, response);  
    }  
}
```
![[Pasted image 20231211173647.png]]

<mark style="background: #ABF7F7A6;">Servlet 的作用：</mark>
- 拦截请求，请求方式：GET, POST, PUT, DELETE
- 设置编码格式：UTF-8, GB 2312，`request.setCharacterEncoding(编码格式)`
- 获取请求参数，地址栏参数是 key-value 的格式，通过 key 获取 value, `request.getParameter(key)`
- 身份校验
- 调用 service 层处理业务
- 封装结果，数据脱敏
- 响应
	- 响应格式：`response.setContentType("text/html;charset=utf-8");`
	- 请求转发，`request.getRequestDispatcher(/路径).forward(request,response);`
	- 重定向，`response.sendRedirect(/资源路径);`

>WEB-INF 下的资源只能服务器访问，可以通过服务器端请求转发，不能重定向，因为重定向是浏览器重新发了一次请求

## 2023-12-12
### Thymeleaf 模版引擎
Thymeleaf 是用来开发 Web 和独立环境项目的现代服务器端 Java 模板引擎，既适用于 web 环境，也适用于独立环境，比较适合当前的人员分工问题。其能够处理 HTML、XML、JavaScript、CSS 甚至纯文本。提供了一种优雅且高度可维护的模板创建方法，可以直接在浏览器中正确显示，也可以作为静态原型方便开发团队协作。

1. 导入 Thymeleaf 依赖
2. 创建模版对象
3. 获取请求上下文，创建 thymeleaf 需要解析的模版
	1. 配置编码格式
	2. 配置页面路径，<mark style="background: #ABF7F7A6;">前缀和后缀</mark>
4. 创建 context 对象
5. Context 对象存放数据
6. 响应浏览器渲染
7. 在页面中动态渲染


> [!NOTE] 引入 thymeleaf 依赖
```xml
<!-- https://mvnrepository.com/artifact/org.thymeleaf/thymeleaf -->  
<dependency>  
    <groupId>org.thymeleaf</groupId>  
    <artifactId>thymeleaf</artifactId>  
    <version>3.0.11.RELEASE</version>  
</dependency>
```


> [!NOTE] 第一个 Thymeleaf 程序
```java
package com.yhk.blog.controller;  
  
import org.thymeleaf.TemplateEngine;  
import org.thymeleaf.context.Context;  
import org.thymeleaf.templateresolver.ServletContextTemplateResolver;  
  
import javax.naming.InitialContext;  
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.HttpServlet;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.IOException;  
import java.io.PrintWriter;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/12 9:29  
 * @注释  
 */  
@WebServlet("/thymeleaf")  
public class ThymeleafServlet extends HttpServlet {  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        //创建模版对象  
        TemplateEngine templateEngine=new TemplateEngine();  
        ServletContextTemplateResolver scstr = new ServletContextTemplateResolver(req.getServletContext());  
        scstr.setCharacterEncoding("utf-8");  
        //请求上下文，如果访问这个servlet,会自动将前缀和后缀带入请求路径中  
        scstr.setPrefix("/WEB-INF/templates");  
        scstr.setSuffix(".html");  
        templateEngine.setTemplateResolver(scstr);  
  
        Context context = new Context();  
        context.setVariable("name", "admin");//渲染数据  
        String index = templateEngine.process("index", context);  
        resp.setContentType("text/html;charset=utf-8");  
        PrintWriter writer = resp.getWriter();  
        writer.print(index);  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        this.doGet(req,resp);  
    }  
}
```

> [!NOTE] index. Html
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8" xmlns:th="http://www.thymeleaf.org">  
    <title>Title</title>  
</head>  
<body>  
  <h1>hello Thymeleaf</h1>  
  <div th:text="${name}">我是离线数据</div>  
</body>  
</html>
```
![[Pasted image 20231212100744.png]]
- 如果通过服务器 (有网)访问 thymeleaf 页面，就会显示 `${name}` 里的数据，而不会显示标签中的死数据
- 如果无网情况下访问 thymeleaf 页面则会显示标签中的死数据

#### Thymeleaf 语法
- **标签作用示例**：
	- th:id替换id : `<input th:id="${user.id}"/>` 
	- th: text 文本替换: `<p text:="${user.name}">bigsai</p>`
	- th: utext 支持 html 的文本替换: `<p utext:="${htmlcontent}">content</p>`
	- th: object 替换对象: `<div th:object="${user}"></div>`
	- th: value 替换值: `<input th:value="${user.name}" >`
	- th: each 迭代: `<tr th:each="student:${user}" >`
	- th: href 替换超链接: `<a th:href="@{index.html}">超链接</a>`
	- th: src 替换资源 `<script type="text/javascript" th:src="@{index.js}"></script>`
	- th: if 条件判断 `<p th:if="${result}">result为真显示</p>`
- 获取一个 javaBean 对象属性
```html
<h2>JavaBean对象</h2>
<table bgcolor="#ffe4c4" border="1">
    <tr>
        <td>介绍</td>
        <td th:text="${user.name}"></td>
    </tr>
    <tr>
        <td>年龄</td>
        <td th:text="${user['age']}"></td>
    </tr>
    <tr>
        <td>介绍</td>
        <td th:text="${user.getDetail()}"></td>
    </tr>
</table>
```


> [!NOTE] Demo
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8" xmlns:th="http://www.thymeleaf.org">  
    <title>Title</title>  
</head>  
<body>  
    <h1>hello Thymeleaf</h1>  
    <div th:text="${name}">我是离线数据</div>  
    <div th:object="${user}">  
        <p th:text="*{getName()}"></p>  
        <p th:text="*{getPassword()}"></p>  
        <p th:text="*{getGender()}"></p>  
    </div>  
    <p th:if="${result}">张三</p>  
    <div th:each="user:${users}">  
        <p th:text="${user.getName()}"></p>  
        <p th:text="${user.getPassword()}"></p>  
        <p th:text="${user.getGender()}"></p>  
    </div>  
</body>  
</html>
```

### Md 5 加密工具类
敏感信息不能明文存储在数据库
1. 引入 shiro-core 依赖，其中有 md 5 加密工具类
2. 将加密类进行封装

> [!NOTE] Md 5 Util
```java
package com.yhk.blog.utils;  
  
import org.apache.shiro.crypto.hash.Md5Hash;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/12 11:49  
 * @注释 md5加密工具类  
 */  
public class MD5Util {  
    public static String encry(String name,String password){  
        Md5Hash md5Hash = new Md5Hash(name, password);  
        String result = md5Hash.toString();  
        return result;  
    }  
}
```
- Name 是加密的<mark style="background: #FF5582A6;">盐</mark>，让密码更加不容易破解
- 也可以在后面指定散列的次数，`new Md5Hash(name, password,3);`
### Thymeleaf 模版引擎上下文加载封装为工具类

```java
package com.yhk.blog.utils;  
  
import com.yhk.blog.model.User;  
import org.thymeleaf.TemplateEngine;  
import org.thymeleaf.context.Context;  
import org.thymeleaf.templateresolver.ServletContextTemplateResolver;  
  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.PrintWriter;  
import java.util.ArrayList;  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/12 15:50  
 * @注释  
 */  
public class THUtil {  
    public static void thymeleaf(HttpServletRequest req, HttpServletResponse resp,Context context,String path) throws Exception{  
        //创建模版对象  
        TemplateEngine templateEngine=new TemplateEngine();  
        ServletContextTemplateResolver scstr = new ServletContextTemplateResolver(req.getServletContext());  
        scstr.setCharacterEncoding("utf-8");  
        //请求上下文，如果访问这个servlet,会自动将前缀和后缀带入请求路径中  
        scstr.setPrefix("/WEB-INF/templates/");  
        scstr.setSuffix(".html");  
        templateEngine.setTemplateResolver(scstr);  
  
        String index = templateEngine.process(path, context);//渲染页面及数据  
        resp.setContentType("text/html;charset=utf-8");  
        PrintWriter writer = resp.getWriter();  
        writer.print(index);  
    }  
}
```
- 在 controller 层访问 templates 资源时，只需要将 <mark style="background: #ABF7F7A6;">http 请求参数，资源容器, 资源路径</mark>传入
### 安装 easyCode 插件
EasyCode 插件可以将数据库表生成对应实体类
1. ![[Pasted image 20231212162839.png]]
2. 连接数据库，右键点击 easyCode, ![[Pasted image 20231212162924.png]]
3. 选择生成实体类的包
4. 可以选择网上的模版，一键生成 controller 的方法👍
5. 如果出现配置信息错误，将插件卸载重装即可
6. ![[Pasted image 20231212164657.png]]
7. <mark style="background: #FF5582A6;">TUser 实体类中的主键字段类型与数据库中不一致，要修改为 Integer 类型</mark>
### 登录模块

> [!NOTE] Controller
- 获取用户输入的用户名和密码
- 调用业务层查询用户结果集
- 将结果集保存至 context 容器
- 调用 thymeleaf 工具类，渲染页面
```java
package com.yhk.blog.controller; /**  
 * @Author moresuo  
 * @Date 2023/12/11 17:32  
 * @version 1.0  
 * @注释  
 */  
  
import com.yhk.blog.service.UserService;  
import com.yhk.blog.service.impl.UserServiceImpl;  
import com.yhk.blog.utils.THUtil;  
import org.thymeleaf.context.Context;  
  
import javax.servlet.*;  
import javax.servlet.http.*;  
import javax.servlet.annotation.*;  
import java.io.IOException;  
import java.util.Map;  
  
@WebServlet("/login")  
public class UserServlet extends HttpServlet {  
    private UserService userService=new UserServiceImpl();  
    @Override  
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  
       //设置接收编码格式  
        request.setCharacterEncoding("UTF-8");  
        String username = request.getParameter("username");  
        String password = request.getParameter("password");  
        Map<String, Object> maps = userService.login(username, password);  
        Context context = new Context();  
        context.setVariable("maps", maps);  
        THUtil.thymeleaf(request,response,context,"login");  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  
        this.doGet(request, response);  
    }  
}
```

> [!NOTE] Service 层
- 将用户名字符串去空格
- 根据用户名查询用户信息
- 将传递过来的密码加密与数据库中密码作对比
- 将返回数据作为一个 map 集合
	- Code: 信息编码
	- Message: 信息
	- Data: 返回的数据
```java
package com.yhk.blog.service.impl;  
  
import com.yhk.blog.dao.UserDao;  
import com.yhk.blog.dao.impl.UserDaoImpl;  
import com.yhk.blog.model.TUser;  
import com.yhk.blog.service.UserService;  
import com.yhk.blog.utils.MD5Util;  
  
import java.util.HashMap;  
import java.util.Map;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/12 16:50  
 * @注释  
 */  
public class UserServiceImpl implements UserService {  
    private UserDao userDao=new UserDaoImpl();  
    @Override  
    public Map<String,Object> login(String username, String password) {  
        Map<String, Object> map = new HashMap<>();  
        //取出username空格  
        if(username!=null){  
            username=username.trim();  
        }else{  
            return null;  
        }        //查找用户信息  
        TUser tUser = userDao.queryByName(username);  
        if(tUser!=null){  
            //判断密码是否一致  
            //将密码进行加密  
            String encry = MD5Util.encry(username, password);//将加密之后的密码与数据库中的密码进行匹配  
            if (encry.equals(tUser.getUserPassword())) {  
                map.put("code", 1001);  
                map.put("message", "登陆成功");  
                map.put("data", tUser);  
                return map;  
            }else{  
                map.put("code", 1002);  
                map.put("message", "密码错误");  
                map.put("data", "");  
                return map;  
            }  
        }  
        map.put("code", 1003);  
        map.put("message", "用户不存在");  
        map.put("data", "");  
        return map;  
    }  
}
```


> [!NOTE] Dao 层
- 创建 sql 语句
- 调用模版方法查询一个用户数据
```java
package com.yhk.blog.dao.impl;  
  
import com.yhk.blog.dao.BaseDao;  
import com.yhk.blog.dao.UserDao;  
import com.yhk.blog.model.TUser;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/11 16:43  
 * @注释  
 */  
public class UserDaoImpl extends BaseDao implements UserDao {  
  
    @Override  
    public TUser queryByName(String name) {  
        String sql="select * from t_user where user_name=?";  
        return this.queryForOne(sql, TUser.class, name);  
    }  
}
```


> [!warning] 注意
> 数据库中的字段名一定要和实体类中的属性名一致，我的数据库表中字段是下划线连接，实体类是驼峰命令，不能像 MyBatis 一样像不会自动转换😭
## 2023-12-13
### 前端登录界面
前端工具：vscode
前端框架：bootstrap3, jquery
项目结构：
![[Pasted image 20231213095526.png]]
帮助文档：[全局 CSS 样式 · Bootstrap v3 中文文档 | Bootstrap 中文网 (bootcss.com)](https://v3.bootcss.com/css/#forms-control-validation)
前端界面：![[Pasted image 20231213095626.png]]

> [!NOTE] 登录界面
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>登录界面</title>
    <script src="../js/jquery-3.7.1.js"></script>
    <script src="../dist/js/bootstrap.js"></script>
    <link rel="stylesheet" href="../dist/css/bootstrap.css">
    <link rel="stylesheet" href="../css/login.css">
</head>

<body>
    <div class="container-fluid" id="con">
        <form>
            <div><h3>MyBlog</h3></div>
            <div class="form-group has-feedback">
              <input type="text" class="form-control" id="username" placeholder="账号">
              <span class="glyphicon glyphicon-user form-control-feedback" aria-hidden="true"></span>
            </div>
            <div class="form-group has-feedback">
              <input type="password" class="form-control" id="password" placeholder="密码">
              <span class="glyphicon glyphicon-lock form-control-feedback" aria-hidden="true"></span>
            </div>
            
            <button type="button" class="btn btn-primary btn-block">登录</button>
            <a>进入注册页面</a>
          </form>
          <div id="bottom">
            <img src="../img/github.jpg" alt="">
            <span><a href="https://github.com/">Github</a></span>
            <span>&nbsp;|&nbsp;</span>
            <span><a href="https://gitee.com/">GitEE</a></span>
          </div>
    </div>
    <div></div>
</body>
</html>
```
- 前端要的是 JSON 格式的数据
- 引入 axios, `<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>`,前端发送异步请求

> [!NOTE] axios
```javascript
$("#btn").click(function () {  
    var username = $("#username").val();  
    var password = $("#password").val();  
    axios({//发送异步请求  
        method:"get",  
        url:"http://localhost:8080/day01/login?username="+username+"&password="+password,  
    }).then(function (resp) {  
        if(resp.status==200){//登陆成功跳转页面  
            window.location.href="/thymeleaf"  
        }else{  
            alert(resp.data.message) 
        }  
    })  
})
```

Axios 相关参数：
- `method`: 请求方式，`Get` 或 `post`
- `url`: 请求路径，如果是 get 请求，则要在路径后面拼接上请求参数，`url?parame1=value1&parame2=value2`
- `data`: 请求体中的数据，传递 json 格式的字符串，也可以是 js 对象，axios 会自动转换为 json 格式
- `then()` 需要传递一个匿名函数。我们将 `then()` 中传递的匿名函数称为回调函数，意思是该匿名函数在发送请求时不会被调用，而是在成功响应后调用的函数。而该回调函数中的 `resp` 参数是对响应的数据进行封装的对象，通过 `resp.data` 可以获取到响应的数据

<mark style="background: #FFB86CA6;">既然前端要接受一个 json 格式的字符串，那后端如何将传递的数据转为 json 格式进行传递？</mark>
- 引入 fastjson 依赖，提供转 json 格式的操作
```xml
<dependency>  
    <groupId>com.alibaba</groupId>  
    <artifactId>fastjson</artifactId>  
    <version>1.2.62</version>  
</dependency>
```
`JSON.toJSONString(maps)`,将 map 集合转为 json 字符串
`PrintWriter writer = response.getWriter();writer.write(s)`,将 json 字符串发送给响应体中
`User user = JSON.ParseObject (jsonStr, User. Class);` 将请求体中的 json 字符串转为对象
### 前端首页
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>首页</title>
    <script src="../js/jquery-3.7.1.js"></script>
    <script src="../dist/js/bootstrap.js"></script>
    <link rel="stylesheet" href="../dist/css/bootstrap.css">
    <link rel="stylesheet" href="../css/index.css">
</head>

<body>
    <div class="container" id="con">
        <div class="row" id="row">
            <div class="col-md-4" id="left">
                <div id="one">
                    <div><h2>MyBlog</h2></div>
                    <div>Winner Winner Chicken Dinner!</div>
                    <div><img src="../img/avarta.jpg" alt=""></div>
                    <div>admin</div>
                    <div>高兴</div>
                </div>
                <div id="two"></div>
                <div id="three"></div>
                <div id="four"></div>
            </div>
            <div class="col-md-6 col-md-offset-2" id="right">
                <div class="list-group">
                    <div href="#" class="list-group-item active">
                      文章
                    </div>
                    <div href="#" class="list-group-item">
                        <div class="media">
                            <div class="media-left media-middle" id="artImg">
                              <a href="#">
                                <img class="media-object" src="../img/dog.jpg" alt="...">
                              </a>
                            </div>
                            <div class="media-body" id="artConf">
                              <h4 class="media-heading">MyBatis高级映射多对多查询</h4>
                              <div>
                                <span>2021-09-18|</span>
                                <a href=""><span>MyBatis|</span></a>
                                <span>75</span>
                              </div>
                              <div>
                                <span>详细内容请点击阅读全文查看</span>
                              </div>
                              <div>
                                <a href="">阅读全文</a>
                              </div>
                            </div>
                          </div>
                    </div>
                </div>
                <div class="list-group">
                    <div href="#" class="list-group-item active">
                      文章
                    </div>
                    <div href="#" class="list-group-item">
                        <div class="media">
                            <div class="media-left media-middle" id="artImg">
                              <a href="#">
                                <img class="media-object" src="../img/dog.jpg" alt="...">
                              </a>
                            </div>
                            <div class="media-body" id="artConf">
                              <h4 class="media-heading">MyBatis高级映射多对多查询</h4>
                              <div>
                                <span>2021-09-18|</span>
                                <a href=""><span>MyBatis|</span></a>
                                <span>75</span>
                              </div>
                              <div>
                                <span>详细内容请点击阅读全文查看</span>
                              </div>
                              <div>
                                <a href="">阅读全文</a>
                              </div>
                            </div>
                          </div>
                    </div>
                </div>
                <div class="list-group">
                    <div href="#" class="list-group-item active">
                      文章
                    </div>
                    <div href="#" class="list-group-item">
                        <div class="media">
                            <div class="media-left media-middle" id="artImg">
                              <a href="#">
                                <img class="media-object" src="../img/dog.jpg" alt="...">
                              </a>
                            </div>
                            <div class="media-body" id="artConf">
                              <h4 class="media-heading">MyBatis高级映射多对多查询</h4>
                              <div>
                                <span>2021-09-18|</span>
                                <a href=""><span>MyBatis|</span></a>
                                <span>75</span>
                              </div>
                              <div>
                                <span>详细内容请点击阅读全文查看</span>
                              </div>
                              <div>
                                <a href="">阅读全文</a>
                              </div>
                            </div>
                          </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```
![[Pasted image 20231213145954.png]]

### 管理系统界面
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>管理系统</title>
    <script src="../js/jquery-3.7.1.js"></script>
    <script src="../dist/js/bootstrap.js"></script>
    <link rel="stylesheet" href="../dist/css/bootstrap.css">
    <link rel="stylesheet" href="../css/admin.css">
</head>
<body>
    <div class="container-fluid" id="con">
        <div class="row" id="head">

        </div>
        <div class="row" id="bottom">
            <div class="col-md-3" id="left">
                <div class="panel-group" id="accordion" role="tablist" aria-multiselectable="true">
                    <div class="panel panel-default">
                      <div class="panel-heading" role="tab" id="headingOne">
                        <h4 class="panel-title">
                          <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
                            用户管理
                          </a>
                        </h4>
                      </div>
                      <div id="collapseOne" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="headingOne">
                        <div class="panel-body">
                         <div onclick="goto('./login.html')">用户查询</div>
                          <div onclick="goto('./index.html')">新增用户</div>
                        </div>
                      </div>
                    </div>
                    <div class="panel panel-default">
                      <div class="panel-heading" role="tab" id="headingTwo">
                        <h4 class="panel-title">
                          <a class="collapsed" role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">
                            Collapsible Group Item #2
                          </a>
                        </h4>
                      </div>
                      <div id="collapseTwo" class="panel-collapse collapse" role="tabpanel" aria-labelledby="headingTwo">
                        <div class="panel-body">
                          
                        </div>
                      </div>
                    </div>
                    <div class="panel panel-default">
                      <div class="panel-heading" role="tab" id="headingThree">
                        <h4 class="panel-title">
                          <a class="collapsed" role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseThree" aria-expanded="false" aria-controls="collapseThree">
                            Collapsible Group Item #3
                          </a>
                        </h4>
                      </div>
                      <div id="collapseThree" class="panel-collapse collapse" role="tabpanel" aria-labelledby="headingThree">
                        <div class="panel-body">
                          
                        </div>
                      </div>
                    </div>
                  </div>
            </div>
            <div class="col-md-9" id="right">
                <iframe id="iframe" src="./index.html" frameborder="0"></iframe>
            </div>
        </div>
    </div>
    <script>
       function goto(param){
            var iframe=document.getElementById("iframe")
            iframe.setAttribute('src',param)
        }
    </script>
</body>
</html>
```
![[Pasted image 20231213150136.png]]

### 后端请求到首页
- 前端发送账号及密码给后端
- 后端进行验证
	- 用户存在，将用户信息保存至 session 中，返回前端，跳转至首页
	- 用户不存在，返回前端，提示错误
- 用户存在，发送 ajax 请求到首页，跳转至首页时，从 session 中获取用户信息动态渲染页面

**后端发送请求到 index 页面**, 将后端数据渲染给前端

> [!NOTE] IndexServlet
```java
package com.yhk.blog.controller;  
  
import com.yhk.blog.dao.IndexDao;  
import com.yhk.blog.dao.impl.IndexDaoImpl;  
import com.yhk.blog.model.TArticle;  
import com.yhk.blog.model.User;  
import com.yhk.blog.utils.THUtil;  
import org.thymeleaf.context.Context;  
  
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.HttpServlet;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.IOException;  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/13 14:57  
 * @注释  
 */  
@WebServlet("/index")  
public class IndexServlet extends HttpServlet {  
    private IndexDao indexDao=new IndexDaoImpl();  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        Context context = new Context();  
        //获取用户信息  
        User user = (User) req.getSession().getAttribute("user");  
        List<TArticle> allArt = indexDao.getAllArt();  
        context.setVariable("user", user);  
        context.setVariable("arts", allArt);  
        THUtil.thymeleaf(req, resp, context, "index");  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        this.doGet(req,resp);  
    }  
}
```

> [!NOTE] IndexDaoImpl
```java
package com.yhk.blog.dao.impl;  
  
import com.yhk.blog.dao.BaseDao;  
import com.yhk.blog.dao.IndexDao;  
import com.yhk.blog.model.TArticle;  
  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/13 16:19  
 * @注释  
 */  
public class IndexDaoImpl extends BaseDao implements IndexDao {  
  
    @Override  
    public List<TArticle> getAllArt() {  
        String sql = "select * from t_article";  
        return this.queryForList(sql, TArticle.class);  
    }  
}
```

> [!warning] 问题: 服务器加载不出静态资源
> 解决方法：将 static 目录复制一份到 templates 目录下👍

## 2023-12-14
### 获取文章、分类、标签总数
- 获取文章总数：`select count(*) from t_article`
- 获取文章分类总数：`select count(distinct(sort)) from t_article`
- 获取标签总数：`select count(distinct(id)) from t_tag`,因为存在重复标签，要去重
- 将数据保存至 context, 传至前端

> [!NOTE] ArticleDaoImpl
```java
package com.yhk.blog.dao.impl;  
  
import com.yhk.blog.dao.ArticleDao;  
import com.yhk.blog.dao.BaseDao;  
import com.yhk.blog.model.TArticle;  
  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/14 9:04  
 * @注释  
 */  
public class ArticleDaoImpl extends BaseDao implements ArticleDao {  
    @Override  
    public List<TArticle> getAllArt() {  
        String sql = "select * from t_article";  
        return this.queryForList(sql, TArticle.class);  
    }  
  
    @Override  
    public Integer count() {  
        String sql="select count(*) from t_article";  
        Number count = (Number) this.queryForSingleValue(sql);  
        return count.intValue();  
    }  
  
    @Override  
    public Integer getSortCount() {  
        String sql = "select count(distinct(sort)) from t_article";  
        Number count = (Number) this.queryForSingleValue(sql);  
        return count.intValue();  
    }  
  
    @Override  
    public Integer getTagCount() {  
        String sql = "select count(distinct(id)) from t_tag";  
        Number count = (Number) this.queryForSingleValue(sql);  
        return count.intValue();  
    }  
  
  
}
```


> [!NOTE] IndexServlet
```java
package com.yhk.blog.controller;  
  
import com.yhk.blog.dao.ArticleDao;  
import com.yhk.blog.dao.IndexDao;  
import com.yhk.blog.dao.impl.ArticleDaoImpl;  
import com.yhk.blog.dao.impl.IndexDaoImpl;  
import com.yhk.blog.model.TArticle;  
import com.yhk.blog.model.User;  
import com.yhk.blog.service.ArticleService;  
import com.yhk.blog.service.impl.ArticleServiceImpl;  
import com.yhk.blog.utils.THUtil;  
import org.thymeleaf.context.Context;  
  
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.HttpServlet;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.IOException;  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/13 14:57  
 * @注释  
 */  
@WebServlet("/index")  
public class IndexServlet extends HttpServlet {  
    private ArticleDao articleDao=new ArticleDaoImpl();  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        Context context = new Context();  
        //获取用户信息  
        User user = (User) req.getSession().getAttribute("user");  
        //文章信息  
        List<TArticle> allArt = articleDao.getAllArt();  
        //文章、分类、标签总数  
        Integer artCount=articleDao.count();  
        Integer sortCount=articleDao.getSortCount();  
        Integer tagCount=articleDao.getTagCount();  
        context.setVariable("user", user);  
        context.setVariable("arts", allArt);  
        context.setVariable("artCount", artCount);  
        context.setVariable("sortCount", sortCount);  
        context.setVariable("tagCount", tagCount);  
        THUtil.thymeleaf(req, resp, context, "index");  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        this.doGet(req,resp);  
    }  
}
```

### 分页
- 前端传来 `pageNum` 页码
- 分页语句：`select * from t_article limit begin size*`
- `begin`: 开始记录的下标，`begin=(pageNum-1)*size`
- `size`: 每页的记录数，可以前端传来，也可以后端自定义
- `totalPage`: 页面总数，`totalCount=(artCount%size)==0 ? artCount/size:(artCount/size)+1 `
- 传递给前端分页对象及总页数

> [!NOTE] IndexServlet
```java
package com.yhk.blog.controller;  
  
import com.yhk.blog.dao.ArticleDao;  
import com.yhk.blog.dao.IndexDao;  
import com.yhk.blog.dao.impl.ArticleDaoImpl;  
import com.yhk.blog.dao.impl.IndexDaoImpl;  
import com.yhk.blog.model.TArticle;  
import com.yhk.blog.model.User;  
import com.yhk.blog.service.ArticleService;  
import com.yhk.blog.service.impl.ArticleServiceImpl;  
import com.yhk.blog.utils.THUtil;  
import org.thymeleaf.context.Context;  
  
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.HttpServlet;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.IOException;  
import java.util.List;  
  
/**  
 * @version 1.0  
 * @Author moresuo  
 * @Date 2023/12/13 14:57  
 * @注释  
 */  
@WebServlet("/index")  
public class IndexServlet extends HttpServlet {  
    private ArticleDao articleDao=new ArticleDaoImpl();  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        Context context = new Context();  
        //获取用户信息  
        User user = (User) req.getSession().getAttribute("user");  
        //获取页码  
        Integer page = Integer.valueOf(req.getParameter("page"));  
        //获取分页对象  
        //每页记录数，默认为4  
        int pageSize=4;  
        int begin = (page - 1) * pageSize;  
  
        //文章信息  
        List<TArticle> allArt = articleDao.getAllArt(begin,pageSize);  
        //文章、分类、标签总数  
        Integer artCount=articleDao.count();  
        Integer sortCount=articleDao.getSortCount();  
        Integer tagCount=articleDao.getTagCount();  
        //页面总数  
        int totalPage=artCount%pageSize==0?(artCount/pageSize):(artCount/pageSize)+1;  
        context.setVariable("user", user);  
        context.setVariable("arts", allArt);  
        context.setVariable("totalPage",totalPage);  
        context.setVariable("artCount", artCount);  
        context.setVariable("sortCount", sortCount);  
        context.setVariable("tagCount", tagCount);  
        THUtil.thymeleaf(req, resp, context, "index");  
    }  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        this.doGet(req,resp);  
    }  
}
```
### 团队项目
#### 项目名：图书管理系统
**技术栈**：
- 后端：javaweb, thymeleaf, mysql，采用 mvc 三层架构
- 前端：html. Js, css, axaj, bootstrap
**功能模块：**
- 登录
- 图书分页展示 (首页)
- 管理员后台管理（增删改查）


