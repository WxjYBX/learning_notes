---
title: ssm框架整合
description: spring+springMVC+mybatis整合小案例
---

# SSM框架案例

## 1. 环境配置

### 1. 依赖注入

- pom.xml

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.example</groupId>
        <artifactId>ssmbuild</artifactId>
        <version>1.0-SNAPSHOT</version>
    
        <properties>
            <maven.compiler.source>17</maven.compiler.source>
            <maven.compiler.target>17</maven.compiler.target>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
    
    <!--    依赖: junit, 数据库驱动， 连接池， servlet， jsp， mybatis， mybatis-spring， spring-->
        <dependencies>
    <!--        junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.13.2</version>
            </dependency>
    <!--        数据库驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.32</version>
            </dependency>
    <!--        数据库连接池-->
            <dependency>
                <groupId>com.mchange</groupId>
                <artifactId>c3p0</artifactId>
                <version>0.9.5.5</version>
            </dependency>
    <!--        Servlet - jsp-->
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
    <!--        mybatis-->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.5.13</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis-spring</artifactId>
                <version>3.0.1</version>
            </dependency>
    <!--        spring-->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>5.3.26</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>5.3.26</version>
            </dependency>
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjweaver</artifactId>
                <version>1.9.19</version>
            </dependency>
    <!--        lombok-->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.26</version>
            </dependency>
    
        </dependencies>
    
    
    <!--    静态资源导出-->
        <!-- 为了能够找到mapper.xml和config.xml增加这个，默认不用改动直接用就行 -->
        <build>
            <resources>
                <resource>
                    <directory>src/main/java</directory>
                    <includes>
                        <include>**/*.xml</include>
                        <include>**/*.properties</include>
                    </includes>
                </resource>
    
                <resource>
                    <directory>src/main/resources</directory>
                    <includes>
                        <include>**/*.xml</include>
                        <include>**/*.properties</include>
                    </includes>
                </resource>
            </resources>
        </build>
    </project>
    ```
    

### 2. mybatis层配置

- resources

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd">
      
          <import resource="spring-mvc.xml" />
          <import resource="spring-dao.xml" />
          <import resource="spring-service.xml" />
      
      </beans>
      ```

  - database.properties

    - ```xml
      jdbc.driver=com.mysql.jdbc.Driver
      # 如果使用的是mysql8.0+，必须要增加一个时区的配置即增加&serverTimeZone=Asia/Shanghai
      jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
      jdbc.username=root
      jdbc.password=123456
      ```

  - mybatis-config.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-config.dtd">
      <configuration>
      
      <!--    配置数据源，在ssm中交给spring去完成-->
          <typeAliases>
              <package name="com.example.pojo"/>
          </typeAliases>
      
      <!--    mapper配置-->
          <mappers>
              <mapper class="com.example.dao.BookMapper" />
          </mappers>
      </configuration>
      ```

- dao

  - BookMapper(Interface)

    - ```java
      public interface BookMapper {
          int addBook(Books books);
          int deleteBook(@Param("bookID") int id);
          int updateBook(Books books);
          Books queryBookById(@Param("bookID") int id);
          List<Books> queryAllBook();
      }
      ```

  - BookMapper.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.example.dao.BookMapper">
          <insert id="addBook" parameterType="Books">
              insert into ssmbuild.books (bookName, bookCounts, detail)
              values (#{bookName}, #{bookCounts}, #{detail});
          </insert>
      
          <delete id="deleteBook" parameterType="int">
              delete from ssmbuild.books
              where bookID = #{bookID};
          </delete>
      
          <update id="updateBook" parameterType="Books">
              update ssmbuild.books
              set bookName=#{bookName}, bookCounts=#{bookCounts}, detail=#{detail}
              where bookID=#{bookID};
          </update>
      
          <select id="queryBookById" parameterType="Books">
              select * from ssmbuild.books
              where bookID=#{bookID};
          </select>
      
          <select id="queryAllBook" parameterType="Books">
              select * from ssmbuild.books;
          </select>
      </mapper>
      ```

- pojo

  - Books

    - ```java
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

- service

  - BookService(Interface)

    - ```java
      public interface BookService {
          int addBook(Books books);
          int deleteBook(int id);
          int updateBook(Books books);
          Books queryBookById(int id);
          List<Books> queryAllBook();
      }
      ```

  - BookServiceImpl

    - ```java
      public class BookServiceImpl implements BookService{
      
          // service层调dao层: 组合Dao
          private BookMapper bookMapper;
      
          public void setBookMapper(BookMapper bookMapper) {
              this.bookMapper = bookMapper;
          }
      
          @Override
          public int addBook(Books books) {
              return bookMapper.addBook(books);
          }
      
          @Override
          public int deleteBook(int id) {
              return bookMapper.deleteBook(id);
          }
      
          @Override
          public int updateBook(Books books) {
              return bookMapper.updateBook(books);
          }
      
          @Override
          public Books queryBookById(int id) {
              return bookMapper.queryBookById(id);
          }
      
          @Override
          public List<Books> queryAllBook() {
              return bookMapper.queryAllBook();
          }
      }
      ```

### 3. spring层配置

- resources

  - spring-dao.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context
              https://www.springframework.org/schema/context/spring-context.xsd">
      
          <!-- 1. 关联数据库配置文件 -->
          <context:property-placeholder location="classpath:database.properties" />
      
      <!--    2. 连接池-->
      <!--    dbcp: 半自动化操作，不能自动连接-->
      <!--    c3p0：自动化操作，自动化的加载配置文件，自动设置到对象中-->
      <!--    druid： hikari：-->
          <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
              <property name="driverClass" value="${jdbc.driver}" />
              <property name="jdbcUrl" value="${jdbc.url}" />
              <property name="user" value="${jdbc.username}" />
              <property name="password" value="${jdbc.password}" />
      
      <!--        c3p0连接池的私有属性-->
              <property name="maxPoolSize" value="30" />
              <property name="minPoolSize" value="10" />
      <!--        关闭连接后不自动commit-->
              <property name="autoCommitOnClose" value="false" />
      <!--        获取连接超时时间-->
              <property name="checkoutTimeout" value="10000" />
      <!--        当获取连接失败重试次数-->
              <property name="acquireRetryAttempts" value="2" />
          </bean>
      
      <!--    3. sqlSessionFactory-->
          <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" >
              <property name="dataSource" ref="dataSource" />
      <!--        绑定mybatis配置文件-->
              <property name="configLocation" value="classpath:mybatis-config.xml" />
          </bean>
      
      <!--    4. 配置dao接口扫描包， 动态实现dao接口注入到Spring-->
          <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" >
      <!--        注入sqlSessionFactory -->
              <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
      <!--        要扫描的dao包-->
              <property name="basePackage" value="com.example.dao" />
          </bean>
      
      </beans>
      ```

  - spring-service.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context
              https://www.springframework.org/schema/context/spring-context.xsd">
      
      <!--    1. 扫描service下的包-->
          <context:component-scan base-package="com.example.service" />
      <!--    2. 将所有的业务类注入到Spring，可以通过配置或者注解实现-->
          <bean id="BookServiceImpl" class="com.example.service.BookServiceImpl" >
              <property name="bookMapper" ref="bookMapper" />
          </bean>
      <!--    3. 声明式事务配置-->
          <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
      <!--        注入数据源-->
              <property name="dataSource" ref="dataSource" />
          </bean>
      <!--    4. aop事务支持-->
      </beans>
      ```

### 4.springmvc层配置

- add framework support增加web目录

- resources

  - spring-mvc.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
      
      <!--    1.注解驱动-->
          <mvc:annotation-driven/>
      <!--    2.静态资源过滤-->
          <mvc:default-servlet-handler/>
      <!--    3.扫描包：controller-->
          <context:component-scan base-package="com.example.controller"/>
      <!--    4.视图解析器-->
          <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
              <property name="prefix" value="/WEB-INF/jsp/"/>
              <property name="suffix" value=".jsp"/>
          </bean>
      
      </beans>
      ```

- web/WEB-INF

  - web.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
               version="4.0">
      
      <!--    DispatcherServlet-->
          <servlet>
              <servlet-name>springmvc</servlet-name>
              <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
              <init-param>
                  <param-name>contextConfigLocation</param-name>
                  <param-value>classpath:applicationContext.xml</param-value>
              </init-param>
              <load-on-startup>1</load-on-startup>
          </servlet>
          <servlet-mapping>
              <servlet-name>springmvc</servlet-name>
              <url-pattern>/</url-pattern>
          </servlet-mapping>
      
      <!--    乱码过滤-->
          <filter>
              <filter-name>encodingFilter</filter-name>
              <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
              <init-param>
                  <param-name>encoding</param-name>
                  <param-value>utf-8</param-value>
              </init-param>
          </filter>
          <filter-mapping>
              <filter-name>encodingFilter</filter-name>
      <!--        过滤要对所有文件生效，包括jsp文件，所以要加*-->
              <url-pattern>/*</url-pattern>
          </filter-mapping>
      
      <!--    Session过期时间-->
          <session-config>
              <session-timeout>15</session-timeout>
          </session-config>
      </web-app>
      ```

### 5. Tomcat部署

## 2. 运行尝试

### 1. 错误日志

#### 1. org.apache.catalina.core.StandardContext.startInternal 一个或多个筛选器启动失败。

- 解决方法

  File->priject structure->artifacts->选择当前项目->WEB-INF下新建lib目录，将所有jar导入

#### 2. org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'bookController': Unsatisfied dependency expressed through field 'bookService'

- 解决方法

  web.xml配置文件中的内容出错contextConfigLocation应该设置为包含了所有文件导入的applicationContext.xml

  ```xml
  <!--正确代码-->
  <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:applicationContext.xml</param-value>
  </init-param>
  ```

- 过程中可能会出现的问题

  - org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection

    - 解决方法

      数据库没有开启，开启数据库

  - 请求的资源**不可用
    - 解决方法
      - 检查Tomcat打开项目的路径配置是否正确
      - 检查RequestMapping是否设置正确

## 3. 功能整合

### 1. 查询所有书籍

- controller

  - bookController

    - ```java
      @Controller
      @RequestMapping("/book")
      public class BookController {
          // controller调service层
          @Autowired
          @Qualifier("bookServiceImpl")
          private BookService bookService;
      
          // 查询全部的书籍并且返回书籍展示页面
          @RequestMapping("/allBook")
          public String list(Model model){
              List<Books> list = bookService.queryAllBook();
              model.addAttribute("list", list);
              return "allBook";
          }
      }
      ```

- web/WEB-INF/jsp

  - allBook.jsp

    - ```jsp
      <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
      <h1>书籍展示</h1>
      <%--书籍信息从后端数据库中读取，又从list中获取--%>
      <table>
          <thead>
              <tr>
                  <th>书籍编号</th>
                  <th>书籍名称</th>
                  <th>书籍数量</th>
                  <th>书籍详情</th>
              </tr>
          </thead>
          <tbody>
      <%--        <c:forEach var="book" items="${requestScope.get('list')}">--%>
              <c:forEach var="book" items="${list}">
                  <tr>
                      <td>${book.bookID}</td>
                      <td>${book.bookName}</td>
                      <td>${book.bookCounts}</td>
                      <td>${book.detail}</td>
                  </tr>
              </c:forEach>
          </tbody>
      </table>
      </body>
      </html>
      ```

- web

  - index.jsp

    - ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
        <head>
          <title>首页</title>
        </head>
        <body>
        <h3>
          <a href="${pageContext.request.contextPath}/book/allBook">进入书籍展示页面</a>
        </h3>
        </body>
      </html>
      ```

### 2. 添加书籍

- controller

  - BookController

    - ```java
      @Controller
      @RequestMapping("/book")
      public class BookController {
          // controller调service层
          @Autowired
          @Qualifier("bookServiceImpl")
          private BookService bookService;
      
          // 查询全部的书籍并且返回书籍展示页面
          @RequestMapping("/allBook")
          public String list(Model model){
              List<Books> list = bookService.queryAllBook();
              model.addAttribute("list", list);
              return "allBook";
          }
      
          // 展示新增书籍页面
          @RequestMapping("/toAddBook")
          public String toAddBook(){
              return "addBook";
          }
      
          // 新增书籍
          @RequestMapping("/addBook")
          public String addBook(Books book){
              bookService.addBook(book);
              return "redirect:/book/allBook";
          }
      }
      ```

- web/WEB-INF/jsp

  - addBook.jsp

    - ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
        <h1>新增书籍</h1>
        <form action="${pageContext.request.contextPath}/book/addBook" method="post" >
          <label>书籍名称</label>
          <input type="text" name="bookName" required/>
          <label>书籍数量</label>
          <input type="text" name="bookCounts" required/>
          <label>书籍详情</label>
          <input type="text" name="detail" required/>
          <input type="submit" value="提交">
        </form>
      </body>
      </html>
      ```

  - allBook.jsp

    - ```jsp
      <%--新增字段--%>
      <h2>
          <a href="${pageContext.request.contextPath}/book/toAddBook" >新增书籍</a>
      </h2>
      ```

### 3. 修改删除

- controller

  - BookController

    - ```java
      package com.example.controller;
      
      import com.example.pojo.Books;
      import com.example.service.BookService;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.beans.factory.annotation.Qualifier;
      import org.springframework.stereotype.Controller;
      import org.springframework.ui.Model;
      import org.springframework.web.bind.annotation.PathVariable;
      import org.springframework.web.bind.annotation.RequestMapping;
      
      import java.util.List;
      
      @Controller
      @RequestMapping("/book")
      public class BookController {
          // controller调service层
          @Autowired
          @Qualifier("bookServiceImpl")
          private BookService bookService;
      
          // 查询全部的书籍并且返回书籍展示页面
          @RequestMapping("/allBook")
          public String list(Model model){
              List<Books> list = bookService.queryAllBook();
              model.addAttribute("list", list);
              return "allBook";
          }
      
          // 展示新增书籍页面
          @RequestMapping("/toAddBook")
          public String toAddBook(){
              return "addBook";
          }
      
          // 新增书籍
          @RequestMapping("/addBook")
          public String addBook(Books book){
              bookService.addBook(book);
              return "redirect:/book/allBook";
          }
      
          // 展示修改书籍的界面
          @RequestMapping("/toUpdateBook")
          public String toUpdateBook(int bookID, Model model){
              Books book = bookService.queryBookById(bookID);
              model.addAttribute("QBook", book);
              return "updateBook";
          }
      
          // 修改书籍
          @RequestMapping("/updateBook")
          public String updateBook(Books book){
              bookService.updateBook(book);
              return "redirect:/book/allBook";
          }
      
          // 删除书籍
          @RequestMapping("/deleteBook/{bookID}")
          public String deleteBook(@PathVariable("bookID") int bookID){
              bookService.deleteBook(bookID);
              return "redirect:/book/allBook";
          }
      }
      ```

- web/WEB-INF/jsp

  - updateBook.jsp

    - ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
      <h1>修改书籍</h1>
      <form action="${pageContext.request.contextPath}/book/updateBook" method="post" >
          <input type="hidden" name="bookID" value="${QBook.bookID}" />
          <label>书籍名称</label>
          <input type="text" name="bookName" value="${QBook.bookName}" required/>
          <label>书籍数量</label>
          <input type="text" name="bookCounts" value="${QBook.bookCounts}" required/>
          <label>书籍详情</label>
          <input type="text" name="detail" value="${QBook.detail}" required/>
          <input type="submit" value="提交">
      </form>
      </body>
      </html>
      ```
