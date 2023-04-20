# SSM框架整合

## 环境要求

环境：

- IDEA
- MySQL 5.7.19
- Tomcat 9
- Maven 3.6

要求：

- 需要熟练掌握MySQL数据库，Spring，JavaWeb及MyBatis知识，简单的前端知识；



## 数据库环境

```sql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

DROP TABLE IF EXISTS `books`;

CREATE TABLE `books` (
  `bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
  `bookName` VARCHAR(100) NOT NULL COMMENT '书名',
  `bookCounts` INT(11) NOT NULL COMMENT '数量',
  `detail` VARCHAR(200) NOT NULL COMMENT '描述',
  KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES 
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

## 环境搭建

1. 新建一个Maven项目，添加web支持

2. 导入相关的pom依赖

   ```xml
   <dependencies>
       <!--Junit-->
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.12</version>
       </dependency>
       <!--数据库驱动-->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>5.1.47</version>
       </dependency>
       <!-- 数据库连接池 -->
       <dependency>
           <groupId>com.mchange</groupId>
           <artifactId>c3p0</artifactId>
           <version>0.9.5.2</version>
       </dependency>
   
       <!--Servlet - JSP -->
       <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>servlet-api</artifactId>
           <version>2.5</version>
       </dependency>
       <dependency>
           <groupId>javax.servlet.jsp</groupId>
           <artifactId>jsp-api</artifactId>
           <version>2.2</version>
       </dependency>
       <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>jstl</artifactId>
           <version>1.2</version>
       </dependency>
   
       <!--Mybatis-->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.5.2</version>
       </dependency>
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis-spring</artifactId>
           <version>2.0.2</version>
       </dependency>
   
       <!--Spring-->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>5.1.9.RELEASE</version>
       </dependency>
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-jdbc</artifactId>
           <version>5.1.9.RELEASE</version>
       </dependency>
       
       <!--aop织入-->
       <dependency>
               <groupId>org.aspectj</groupId>
               <artifactId>aspectjweaver</artifactId>
               <version>1.8.13</version>
           </dependency>
       
       <!--lombok-->
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <version>1.18.10</version>
           </dependency>
   </dependencies>
   ```

3. Maven资源过滤设置

   ```xml
   <build>
       <resources>
           <resource>
               <directory>src/main/java</directory>
               <includes>
                   <include>**/*.properties</include>
                   <include>**/*.xml</include>
               </includes>
               <filtering>false</filtering>
           </resource>
           <resource>
               <directory>src/main/resources</directory>
               <includes>
                   <include>**/*.properties</include>
                   <include>**/*.xml</include>
               </includes>
               <filtering>false</filtering>
           </resource>
       </resources>
   </build>
   ```

4. 建立基本结构和配置框架

   - com.hitenine.pojo
   - com.hitenine.dao
   - com.hitenine.service
   - com.hitenine.controller
   
   
   
   - mybatis.xml
   
     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE configuration
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-config.dtd">
     <configuration>
     
     </configuration>
     ```
   
   - applicationContext.xml
   
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
             http://www.springframework.org/schema/beans/spring-beans.xsd">
     
     </beans>
     ```
   
   
   
   ## Mybatis底层编写
   
   1. 数据库配置文件 database.properties
   
      ```properties
      jdbc.driver=com.mysql.jdbc.Driver
      jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
      jdbc.username=root
      jdbc.password=123456
      ```
   
   2. IDEA关联数据库
   
   3. 编写MyBatis核心配置文件
   
      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-config.dtd">
      <configuration>
          
          <typeAliases>
              <package name="com.hitenine.pojo"/>
          </typeAliases>
          <mappers>
              <mapper resource="com/hitenine/dao/BookMapper.xml"/>
          </mappers>
      
      </configuration>
      ```
   
   4. 编写数据库对应的实体类 com.kuang.pojo.Books
      使用lombok插件！
      
      ```xml
      package com.hitenine.pojo;
      
      import lombok.AllArgsConstructor;
      import lombok.Data;
      import lombok.NoArgsConstructor;
      
      @Data
      @AllArgsConstructor
      @NoArgsConstructor
      public class Books {
      
          private int bookID;
          private String bookName;
          private int bookCounts;
          private String detail;
      
      }
      ```
      
   5. 编写Dao层d的Mapper接口
   
      ```java
      package com.hitenine.dao;
      
      import com.hitenine.pojo.Books;
      import org.apache.ibatis.annotations.Param;
      
      import java.util.List;
      
      public interface BookMapper {
      
          //增加一本书
          int addBook(Books books);
      
          //删除一本书
          int deleteBookById(@Param("bookID") int id);
      
          //更新一本书
          int updateBook(Books books);
      
          //查询一本书
          Books queryBookById(@Param("bookID") int id);
      
          //查询全部的书
          List<Books> queryAllBook();
      
      }
      ```
   
   6. 编写接口对应的 Mapper.xml 文件。需要导入MyBatis的包；
   
      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      
      <mapper namespace="com.kuang.dao.BookMapper">
      
          <!--增加一个Book-->
          <insert id="addBook" parameterType="Books">
              insert into ssmbuild.books(bookName,bookCounts,detail)
              values (#{bookName}, #{bookCounts}, #{detail})
          </insert>	
      
          <!--根据id删除一个Book-->
          <delete id="deleteBookById" parameterType="int">
              delete from ssmbuild.books where bookID=#{bookID}
          </delete>
      
          <!--更新Book-->
          <update id="updateBook" parameterType="Books">
              update ssmbuild.books
              set bookName = #{bookName},bookCounts = #{bookCounts},detail = #{detail}
              where bookID = #{bookID}
          </update>
      
          <!--根据id查询,返回一个Book-->
          <select id="queryBookById" resultType="Books">
              select * from ssmbuild.books
              where bookID = #{bookID}
          </select>
      
          <!--查询全部Book-->
          <select id="queryAllBook" resultType="Books">
              SELECT * from ssmbuild.books
          </select>
      
      </mapper>
      ```
   
   7. 编写Service层的接口和实现类
      接口：
   
      ```java
      package com.hitenine.service;
      
      import com.hitenine.pojo.Books;
      
      import java.util.List;
      
      public interface BookService {
      
          //增加一本书
          int addBook(Books books);
      
          //删除一本书
          int deleteBookById(int id);
      
          //更新一本书
          int updateBook(Books books);
      
          //查询一本书
          Books queryBookById(int id);
      
          //查询全部的书
          List<Books> queryAllBook();
      
      }
      ```
   
      实现类（注解或者手动配置bean）：
   
      ```java
      package com.hitenine.service;
      
      import com.hitenine.dao.BookMapper;
      import com.hitenine.pojo.Books;
      
      import java.util.List;
      
      public class BookServiceImpl implements BookService {
      
          //service层调dao层
          private BookMapper bookMapper;
      
          public void setBookMapper(BookMapper bookMapper) {
              this.bookMapper = bookMapper;
          }
      
          public int addBook(Books books) {
              return bookMapper.addBook(books);
          }
      
          public int deleteBookById(int id) {
              return bookMapper.deleteBookById(id);
          }
      
          public int updateBook(Books books) {
              return bookMapper.updateBook(books);
          }
      
          public Books queryBookById(int id) {
              return bookMapper.queryBookById(id);
          }
      
          public List<Books> queryAllBook() {
              return bookMapper.queryAllBook();
          }
      }
      ```
   
      **底层需求操作编写完毕**

## Spring层

1. 配置Spring整合MyBatis，我们这里数据源使用c3p0连接池；

2. 我们去编写Spring整合Mybatis的相关的配置文件； spring-dao.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--1.关联数据库配置文件-->
       <context:property-placeholder location="classpath:database.properties"/>
   
       <!--2.连接池
           dbcp：半自动化操作，不能自动连接
           cp30：自动化操作（自动化加载配置文件，并且可以自动设置到对象中！）
           druid：
           hikari：
       -->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="${jdbc.driver}"/>
           <property name="jdbcUrl" value="${jdbc.url}"/>
           <property name="user" value="${jdbc.username}"/>
           <property name="password" value="${jdbc.password}"/>
   
           <!-- c3p0连接池的私有属性 -->
           <property name="maxPoolSize" value="30"/>
           <property name="minPoolSize" value="10"/>
           <!-- 关闭连接后不自动commit -->
           <property name="autoCommitOnClose" value="false"/>
           <!-- 获取连接超时时间 -->
           <property name="checkoutTimeout" value="10000"/>
           <!-- 当获取连接失败重试次数 -->
           <property name="acquireRetryAttempts" value="2"/>
       </bean>
   
       <!--3.sqlSessionFactory-->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <!--绑定mybatis的配置文件-->
           <property name="configLocation" value="classpath:mybatis-config.xml"/>
       </bean>
   
       <!--配置dao接口扫描包，动态的实现了dao接口可以注入到Spring容器中-->
       <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
           <!--注入sqlSessionFactory-->
           <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
           <!--要扫描的dao包-->
           <property name="basePackage" value="com.hitenine.dao"/>
       </bean>
   
   </beans>
   ```

3. Spring整合service层

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--1.扫描service下的包-->
       <context:component-scan base-package="com.hitenine.service"/>
   
       <!--2.将我们的所有业务类注入到Spring中，可以通过配置，或者注解-->
       <bean id="BookServiceImpl" class="com.hitenine.service.BookServiceImpl">
           <property name="bookMapper" ref="bookMapper"/>
       </bean>
   
       <!--3.声明式事务配置-->
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <!--注入数据源-->
           <property name="dataSource" ref="dataSource"/>
       </bean>
   
       <!--4.aop事务支持-->
   
   </beans>
   ```

   **Spring层搞定！再次理解一下，Spring就是一个大杂烩，一个容器！对吧！**



## SpringMVC层

1. web.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!--DispatcherServlet-->
       <servlet>
           <servlet-name>DispatcherServlet</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:applicationContext.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>DispatcherServlet</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
       <!--encodingFilter-->
       <filter>
           <filter-name>encodingFilter</filter-name>
           <filter-class>
               org.springframework.web.filter.CharacterEncodingFilter
           </filter-class>
           <init-param>
               <param-name>encoding</param-name>
               <param-value>utf-8</param-value>
           </init-param>
       </filter>
       <filter-mapping>
           <filter-name>encodingFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
       
       <!--Session过期时间 15分钟-->
       <session-config>
           <session-timeout>15</session-timeout>
       </session-config>
       
   </web-app>
   ```

2. spring-mvc.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--1.注解驱动-->
       <mvc:annotation-driven/>
       <!--2.静态资源过滤-->
       <mvc:default-servlet-handler/>
       <!--扫描包-->
       <context:component-scan base-package="com.hitenine.controller"/>
   
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   </beans>
   ```

3. Spring配置整合文件，applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <import resource="spring-dao.xml"/>
       <import resource="spring-service.xml"/>
       <import resource="spring-mvc.xml"/>
       
   </beans>
   ```

   **配置文件暂时结束！**

   

   ## **Controller 和视图层编写**

   1. BookController 类编写 ， 方法一：查询全部书籍

      ```java
      @Controller
      @RequestMapping("/book")
      public class BookController {
      
          @Autowired
          @Qualifier("BookServiceImpl")
          private BookService bookService;
      
          @RequestMapping("/allBook")
          public String list(Model model) {
              List<Books> list = bookService.queryAllBook();
              model.addAttribute("list", list);
              return "allBook";
          }
      }
      ```

   2. 编写首页 **index.jsp**

      ```html
      <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
      <!DOCTYPE HTML>
      <html>
      <head>
          <title>首页</title>
          <style type="text/css">
              a {
                  text-decoration: none;
                  color: black;
                  font-size: 18px;
              }
              h3 {
                  width: 180px;
                  height: 38px;
                  margin: 100px auto;
                  text-align: center;
                  line-height: 38px;
                  background: deepskyblue;
                  border-radius: 4px;
              }
          </style>
      </head>
      <body>
      
      <h3>
          <a href="${pageContext.request.contextPath}/book/allBook">点击进入列表页</a>
      </h3>
      </body>
      </html>
      ```

   3. 书籍列表页面 **allbook.jsp**

      ```jsp
      <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>书籍列表</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <!-- 引入 Bootstrap -->
          <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
      </head>
      <body>
      
      <div class="container">
      
          <div class="row clearfix">
              <div class="col-md-12 column">
                  <div class="page-header">
                      <h1>
                          <small>书籍列表 —— 显示所有书籍</small>
                      </h1>
                  </div>
              </div>
          </div>
      
          <div class="row">
              <div class="col-md-4 column">
                  <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增</a>
              </div>
          </div>
      
          <div class="row clearfix">
              <div class="col-md-12 column">
                  <table class="table table-hover table-striped">
                      <thead>
                      <tr>
                          <th>书籍编号</th>
                          <th>书籍名字</th>
                          <th>书籍数量</th>
                          <th>书籍详情</th>
                          <th>操作</th>
                      </tr>
                      </thead>
      
                      <tbody>
                      <c:forEach var="book" items="${requestScope.get('list')}">
                          <tr>
                              <td>${book.getBookID()}</td>
                              <td>${book.getBookName()}</td>
                              <td>${book.getBookCounts()}</td>
                              <td>${book.getDetail()}</td>
                              <td>
                                  <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
                                  <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
                              </td>
                          </tr>
                      </c:forEach>
                      </tbody>
                  </table>
              </div>
          </div>
      </div>
      ```

   4. BookController 类编写 ， 方法二：添加书籍

      ```java
      @RequestMapping("/toAddBook")
      public String toAddPaper() {
          return "addBook";
      }
      
      @RequestMapping("/addBook")
      public String addPaper(Books books) {
          System.out.println(books);
          bookService.addBook(books);
          return "redirect:/book/allBook";
      }
      ```

   5. 添加书籍页面：**addBook.jsp**

      ```jsp
      <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      
      <html>
      <head>
          <title>新增书籍</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <!-- 引入 Bootstrap -->
          <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
      </head>
      <body>
      <div class="container">
      
          <div class="row clearfix">
              <div class="col-md-12 column">
                  <div class="page-header">
                      <h1>
                          <small>新增书籍</small>
                      </h1>
                  </div>
              </div>
          </div>
          <form action="${pageContext.request.contextPath}/book/addBook" method="post">
              书籍名称：<input type="text" name="bookName"><br><br><br>
              书籍数量：<input type="text" name="bookCounts"><br><br><br>
              书籍详情：<input type="text" name="detail"><br><br><br>
              <input type="submit" value="添加">
          </form>
      
      </div>
      ```

   6. BookController 类编写 ， 方法三：修改书籍

      ```java
      @RequestMapping("/toUpdateBook")
      public String toUpdateBook(Model model, int id) {
          Books books = bookService.queryBookById(id);
          System.out.println(books);
          model.addAttribute("book",books );
          return "updateBook";
      }
      
      @RequestMapping("/updateBook")
      public String updateBook(Model model, Books book) {
          System.out.println(book);
          bookService.updateBook(book);
          Books books = bookService.queryBookById(book.getBookID());
          model.addAttribute("books", books);
          return "redirect:/book/allBook";
      }
      ```

   7. 修改书籍页面 **updateBook.jsp**

      ```jsp
      <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>修改信息</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <!-- 引入 Bootstrap -->
          <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
      </head>
      <body>
      <div class="container">
      
          <div class="row clearfix">
              <div class="col-md-12 column">
                  <div class="page-header">
                      <h1>
                          <small>修改信息</small>
                      </h1>
                  </div>
              </div>
          </div>
      
          <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
              <input type="hidden" name="bookID" value="${book.getBookID()}"/>
              书籍名称：<input type="text" name="bookName" value="${book.getBookName()}"/>
              书籍数量：<input type="text" name="bookCounts" value="${book.getBookCounts()}"/>
              书籍详情：<input type="text" name="detail" value="${book.getDetail() }"/>
              <input type="submit" value="提交"/>
          </form>
      
      </div>
      ```

   8. BookController 类编写 ， 方法四：删除书籍

      ```java
      @RequestMapping("/del/{bookId}")
      public String deleteBook(@PathVariable("bookId") int id) {
          bookService.deleteBookById(id);
          return "redirect:/book/allBook";
      }
      ```

   **配置Tomcat，进行运行！**



==**注意资源导出问题以及项目结构中的lib目录==**





