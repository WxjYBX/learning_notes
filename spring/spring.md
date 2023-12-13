---
title: spring学习
description: 这是spring框架开始的地方
---

# Spring

**一些重要的事情**

- spring官网:[Spring Framework](https://spring.io/projects/spring-framework#learn)

- spring下载地址:[repo.spring.io](https://repo.spring.io/ui/native/release/org/springframework/spring)

- spring-github地址:[spring-projects/spring-framework: Spring Framework (github.com)](https://github.com/spring-projects/spring-framework)

- spring-maven地址:[Maven Repository: spring (mvnrepository.com)](https://mvnrepository.com/tags/spring)
- 学习思路:spring->springMVC->springboot->springcloud->springclouddataflow

## 1. 依赖

- ```xml
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>6.0.7</version>
  </dependency>
  
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>6.0.7</version>
  </dependency>
  
  ```

## 2. IOC(控制反转)

***简单总结：对象由Spring创建、管理、装配！***

### 1. 主函数

- ```java
  // 只有正确配置pom.xml之后才能正确运行这段话
  ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
  UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("userServiceImpl");
  userServiceImpl.getUser();
  ```

### 2. 注意事项

- 一定要先在pom.xml导入依赖后，才能正常运行程序

  ```xml
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>6.0.7</version>
  </dependency>
  ```

- getBean运行之后，会直接创建所有Bean并调用构造函数初始化，不管这些Bean在接下来是否会被使用

### 3. 依赖注入(DI)：IOC的一种实现方式

- applicationContext.xml = daos.xml + services.xml(**一定要放在resources目录下**)

  - daos.xml

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="userDaoImpl" class="com.example.dao.UserDaoImpl">
        </bean>
    
        <bean id="userDaoMysqlImpl" class="com.example.dao.UserDaoMysqlImpl">
        </bean>
    
        <bean id="userDaoOracleImpl" class="com.example.dao.UserDaoOracleImpl">
        </bean>
    
    </beans>
    ```

  - services.xml

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="userServiceImpl" class="com.example.service.UserServiceImpl">
            <!--
                ref: 引用容器中创建好的对象;
                value: 具体的值，基本数据类型
             -->
            <property name="userDao" ref="userDaoMysqlImpl" />
        </bean>
    
    </beans>
    ```

- 构造器注入

  - 默认使用无参构造创建对象，一旦用有参构造覆盖掉无参构造就会报错！

  - 如果想要使用有参构造方式创建对象

    - ```xml
      <!-- 参数名赋值，简单明了 -->
      <beans>
          <bean id="exampleBean" class="examples.ExampleBean">
              <constructor-arg name="years" value="75000"/>
          	<constructor-arg name="ultimateAnswer" value="42"/>
          </bean>
      </beans>
      ```

    - ```xml
      <!-- 类型赋值，有缺陷，每种类型只能有一个参数 -->
      <bean id="exampleBean" class="examples.ExampleBean">
          <constructor-arg type="int" value="7500000"/>
          <constructor-arg type="java.lang.String" value="42"/>
      </bean>
      ```

    - ```xml
      <!-- 下标赋值(构造函数有两个参数的情况下) -->
      <bean id="exampleBean" class="examples.ExampleBean">
          <constructor-arg index="0" value="7500"/>
          <constructor-arg index="1" value="75000"/>
      </bean>
      ```

- set注入

  - 前置环境配置

    - pojo = Student + Address

  - applicationContext.xml

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="address" class="com.example.pojo.Address" >
            <property name="address" value="China"></property>
        </bean>
    
        <bean id="student" class="com.example.pojo.Student" >
            <!-- 普通值注入 value -->
            <property name="name" value="张三" />
            <!-- bean注入 ref -->
            <property name="address" ref="address" />
            <!-- 数组注入 array-->
            <property name="books">
                <array>
                    <value>三国演义</value>
                    <value>水浒传</value>
                    <value>西游记</value>
                    <value>红楼梦</value>
                </array>
            </property>
            <!-- 列表注入 list -->
            <property name="hobbies">
                <list>
                    <value>听歌</value>
                    <value>读书</value>
                    <value>写字</value>
                </list>
            </property>
            <!-- map注入 map -->
            <property name="card">
                <map>
                    <entry key="身份证" value="1234567890"></entry>
                    <entry key="手机号" value="123456789"></entry>
                </map>
            </property>
            <!-- set注入 set -->
            <property name="games" >
                <set>
                    <value>A</value>
                    <value>B</value>
                    <value>C</value>
                </set>
            </property>
            <!-- null注入 null -->
            <property name="wife">
                <null></null>
            </property>
            <!-- properties注入 props -->
            <property name="info">
                <props>
                    <prop key="学号">123456</prop>
                    <prop key="姓名">ADC</prop>
                    <prop key="年龄">23</prop>
                </props>
            </property>
        </bean>
    </beans>
    ```
    

- P命名空间与C命名空间

  - applicationContext.xml

  - ```xml
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:p="http://www.springframework.org/schema/p"
           xmlns:c="http://www.springframework.org/schema/c"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <!-- p命名空间，可以直接注入属性值：property -->
        <bean id="user" class="com.example.pojo.User" p:name="张三" p:age="66" />
    
        <!-- c命名空间，对象有有参构造器才可以使用,通过构造器注入 -->
        <bean id="user2" class="com.example.pojo.User" c:name="李四" c:age="55" />
    </beans>
    ```

### 4. Bean作用域

- 单例模式（getBean只生成一次对象，所有调用用的都是同一个对象）

  - xml

    ```xml
    <bean id="user2" class="com.example.pojo.User" c:name="李四" c:age="55" scope="singleton" />
    ```

  - java

    ```java
    User user = context.getBean("user2", User.class);
    User user2 = context.getBean("user2", User.class);
    System.out.println(user==user2); // true
    ```

- 原型模式（每次调用getBean都会创建一个新对象）

  - xml

    ```xml
    <bean id="user2" class="com.example.pojo.User" c:name="李四" c:age="55" scope="prototype" />
    ```

  - java

    ```java
    User user = context.getBean("user2", User.class);
    User user2 = context.getBean("user2", User.class);
    System.out.println(user==user2); // false
    ```

- 其他模式只在javaweb使用

### 5. Bean自动装配

- 第一种自动装配

- ```xml
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="cat11" class="com.example.pojo.Cat" />
      <bean id="dog11" class="com.example.pojo.Dog" />
  
      <!--
          byName 会自动在容器的上下文查找，与自己对象Set方法后面的值对应的beanid;
          byType 会自动在容器的上下文查找，与自己对象类型相同的bean，可以省略id;
      -->
      <bean id="person" class="com.example.pojo.Person" autowire="byType">
          <property name="name" value="小狂神呀" />
      </bean>
  
  </beans>
  ```

- 第二种自动装配

- ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd">
  
      <!-- 导入约束，配置注解支持 -->
      <context:annotation-config/>
  
  </beans>
  ```

- ```java
  // spring提供的
  @Autowired // 可以在对象的属性上使用，也可以在set方法上使用（使用autowired可以省略set方法）
  @Qualifier(value="xxx") // 可以手动进行指定装配唯一的对象，搭配Autowired使用
  
  // java提供的
  @Resource //默认匹配唯一的正确标识（多个相同类）或者是唯一的类
  ```

### 6. 使用注解开发

- **注意：如果要使用注解开发，必须导入spring-aop的包！必须导入约束和注解支持！**
- 等效注解组(功能相同：将类装配到容器中，名字不同)
  - @Component---pojo中使用
  - @Repository---dao中使用
  - @Service---service中使用
  - @Controller---controller中使用
- 别的注解
  - @Scope---用法同bean

### 7. 使用Java方式配置Spring

- 注解

  - 配置类注解

    ```java
    @Configuration //spring接管，代表这是一个配置类，效果等同于.xml配置文件
    @ComponentScan("com.example")
    @Import(MyConfig2.class) //引入其他Bean
    public class MyConfig {
    
        // 等同于<bean> id=方法名 class=返回值
        @Bean
        public User getUser(){
            return new User();
        }
    }
    ```

    

  - 测试类调用

    ```java
    ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
    User getUser = context.getBean("getUser", User.class);
    System.out.println(getUser.getName());
    ```

    


## 3. AOP(面向切面编程)

### 1. 代理模式(实现机制：在不改变原有代码的基础上增加新功能)

- 静态代理(代理类手动编写)

- 动态代理(代理类动态生成)

  - 基于接口的动态代理---JDK动态代理

  - 基于类的动态代理---cglib

  - java字节码实现---javasist

  - 代码实例

    - ProxyInvocationHandler.java

      ```java
      import java.lang.reflect.InvocationHandler;
      import java.lang.reflect.Method;
      import java.lang.reflect.Proxy;
      
      public class ProxyInvocationHandler implements InvocationHandler {
      
          // 获得被代理的接口
          private Object target;
      
          public void setTarget(Object target) {
              this.target = target;
          }
      
          // 生成得到代理类
          public Object getProxy(){
              return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
          }
      
          // 处理代理实例，返回结果
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
              log(method.getName());
              // 动态代理的本质是反射
              Object result = method.invoke(target, args);
              return result;
          }
      
          public void log(String msg){
              System.out.println("执行了"+ msg +"方法");
          }
      }
      
      ```

    - UserServie(interface)

      ```java
      public interface UserService {
          public void add();
          public void delete();
          public void update();
          public void query();
      }
      ```

    - UserServiceImpl(class)

      ```java
      public class UserServiceImpl implements UserService{
          public void add() {
              System.out.println("增加");
          }
      
          public void delete() {
              System.out.println("删除");
          }
      
          public void update() {
              System.out.println("更新");
          }
      
          public void query() {
              System.out.println("查找");
          }
      }
      ```

    - Client(class)

      ```java
      public class Client {
          public static void main(String[] args) {
              // 真实角色
              UserServiceImpl userService = new UserServiceImpl();
              // 代理角色，不存在
              ProxyInvocationHandler pih = new ProxyInvocationHandler();
      
              pih.setTarget(userService);
              // 动态生成代理类
              UserService proxy = (UserService) pih.getProxy();
      
              proxy.add();
              proxy.delete();
          }
      }
      ```

  - 认识一个接口：InvocationHandler，一个类：Proxy

    - InvocationHandler：调用处理程序返回结果
    - Proxy：生成动态代理实例

### 2. AOP

- 实例一：原生api接口

  - pom.xml

    - ```xml
      <dependencies>
          <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.9.19</version>
          </dependency>
      </dependencies>
      ```

  - log文件夹(业务代码外的控制)
    - Log(class)

    - ```java
      public class Log implements MethodBeforeAdvice {
          // method: 要执行的目标对象的方法
          // args: 参数
          // target: 目标对象
          @Override
          public void before(Method method, Object[] args, Object target) throws Throwable {
              System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
          }
      }
      ```

    - AfterLog(class)

    - ```java
      public class AfterLog implements AfterReturningAdvice {
          // returnValue: 返回值
          @Override
          public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
              System.out.println(target.getClass().getName() + "执行了" + method.getName() + "，返回值是" + returnValue);
          }
      }
      ```

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <!-- 配置Bean -->
          <bean id="userService" class="com.example.service.UserServiceImpl" />
          <bean id="log" class="com.example.log.Log" />
          <bean id="afterLog" class="com.example.log.AfterLog" />
      
          <!-- 方式一：使用原生Spring API接口 -->
          <!-- 配置aop: 需要导入aop约束 -->
          <aop:config>
              <!-- 切入点 expression:表达式，execution(要执行的位置！格式：* * * * *)-->
              <aop:pointcut id="pointcut" expression="execution(* com.example.service.UserServiceImpl.*(..))"/>
      
              <!-- 执行环绕增加 -->
              <aop:advisor advice-ref="log" pointcut-ref="pointcut" />
              <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut" />
          </aop:config>
      
      </beans>
      ```

  - MyTest.java

    - ```java
      public static void main(String[] args) {
              ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
              // 动态代理的是接口
              UserService userService = context.getBean("userService", UserService.class);
              userService.add();
          }
      ```

- 实例二：自定义切面

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <!-- 配置Bean -->
          <bean id="userService" class="com.example.service.UserServiceImpl" />
          <bean id="diy" class="com.example.diy.DiyPointCut" />
      
          <!-- 方式二：自定义类 -->
          <!-- 配置aop: 需要导入aop约束 -->
          <aop:config>
              <!-- 自定义切面， ref：要引用的类 -->
              <aop:aspect ref="diy" >
                  <!-- 切入点 -->
                  <aop:pointcut id="point" expression="execution(* com.example.service.UserServiceImpl.*(..))"/>
                  <!-- 通知 -->
                  <aop:before method="before" pointcut-ref="point" />
                  <aop:after method="after" pointcut-ref="point" />
              </aop:aspect>
          </aop:config>
      
      </beans>
      ```

  - DiyPointCut(class)

    - ```java
      public class DiyPointCut {
      
          public void before(){
              System.out.println("==方法执行前==");
          }
      
          public void after(){
              System.out.println("==方法执行后==");
          }
      }
      ```

- 实例三：注释实现aop

  - AnnotationPointCut(class)

    - ```java
      @Aspect
      public class AnnotationPointCut {
          @Before("execution(* com.example.service.UserServiceImpl.*(..))")
          public void before(){
              System.out.println("==方法执行前==");
          }
      
          @After("execution(* com.example.service.UserServiceImpl.*(..))")
          public void after(){
              System.out.println("==方法执行后==");
          }
      
          @Around("execution(* com.example.service.UserServiceImpl.*(..))")
          public void around(ProceedingJoinPoint jp) throws Throwable {
              System.out.println("环绕前");
      
              // 执行方法
              Object proceed = jp.proceed();
      
              System.out.println("环绕后");
          }
      }
      ```

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <!-- 配置Bean -->
          <bean id="userService" class="com.example.service.UserServiceImpl" />
          <bean id="annotationPointCut" class="com.example.diy.AnnotationPointCut" />
      
          <!-- 方式三：注解实现 -->
          <!-- 配置aop: 需要导入aop约束 -->
          <aop:aspectj-autoproxy />
      
      </beans>
      ```


## 4. 整合mybatis

### 1. 原始版本

- 依赖

  - pom.xml

    - ```xml
      <dependencies>
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.13.2</version>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>8.0.32</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>3.5.13</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-webmvc</artifactId>
              <version>6.0.6</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-jdbc</artifactId>
              <version>6.0.6</version>
          </dependency>
          <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.9.19</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis-spring</artifactId>
              <version>3.0.1</version>
          </dependency>
          <dependency>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
                  <version>1.18.26</version>
              </dependency>
      </dependencies>
      
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
      ```

- resources

  - mybatis-config.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-config.dtd">
      <configuration>
      
          <typeAliases>
              <package name="com.example.pojo"/>
          </typeAliases>
      
          <environments default="development">
              <environment id="development">
                  <transactionManager type="JDBC"/>
                  <dataSource type="POOLED">
                      <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                      <property name="url" value="jdbc:mysql://localhost:3306/spring_user?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
                      <property name="username" value="root"/>
                      <property name="password" value="123456"/>
                  </dataSource>
              </environment>
          </environments>
      
          <mappers>
              <mapper class="com.example.mapper.UserMapper"/>
          </mappers>
      </configuration>
      ```

- mapper

  - UserMapper(Interface)

    - ```java
      public interface UserMapper {
          public List<User> selectUser();
      }
      ```

  - UserMapper.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.example.mapper.UserMapper">
          <select id="selectUser" resultType="user" >
              select * from spring_user.user;
          </select>
      </mapper>
      ```

- pojo

  - User(class)

    - ```java
      @Data
      public class User {
          private int id;
          private String name;
          private String pwd;
      }
      ```

- MyTest.java

  - class

    - ```java
      @Test
      public void test() throws IOException {
          String resources = "mybatis-config.xml";
          InputStream inputStream = Resources.getResourceAsStream(resources);
      
          SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          SqlSession sqlSession = sessionFactory.openSession(true);
      
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          List<User> userList = mapper.selectUser();
      
          for (User user : userList) {
              System.out.println(user);
          }
      }
      ```


### 2. 版本一(只记录改动部分)

- mapper

  - UserMapperImpl

    - ```java
      public class UserMapperImpl implements UserMapper{
      
          // 所有操作都应该使用sqlSession执行，此处则是使用sqlSessionTemplate
          private SqlSessionTemplate sqlSession;
      
          public void setSqlSession(SqlSessionTemplate sqlSession){
              this.sqlSession = sqlSession;
          }
      
          @Override
          public List<User> selectUser() {
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              return mapper.selectUser();
          }
      }
      ```

- Resources

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <import resource="spring-dao.xml" />
      
          <bean id="userMapper" class="com.example.mapper.UserMapperImpl" >
              <property name="sqlSession" ref="sqlSession" />
          </bean>
      
      </beans>
      ```

  - spring-dao.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <!-- DataSource: 使用spring的数据源替换mybatis的配置 c3p0 dbcp druid
                  默认使用spring提供的jdbc -->
          <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
              <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
              <property name="url" value="jdbc:mysql://localhost:3306/spring_user?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
              <property name="username" value="root"/>
              <property name="password" value="123456"/>
          </bean>
      
          <!-- sqlSessionFactory -->
          <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
              <property name="dataSource" ref="datasource" />
              <!-- 绑定mybatis配置 -->
              <property name="configLocation" value="classpath:mybatis-config.xml" />
              <property name="mapperLocations" value="classpath:com/example/mapper/*.xml" />
          </bean>
      
          <!-- sqlSessionTemplate: 就是常用的sqlSession -->
          <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" >
          <!-- 只能使用构造器注入sqlSessionFactory,因为它没有set方法 -->
              <constructor-arg index="0" ref="sqlSessionFactory" />
          </bean>
      
      </beans>
      ```

- MyTest.java

  - class

    - ```java
      @Test
      public void test() throws IOException {
          ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
          for (User user : userMapper.selectUser()) {
              System.out.println(user);
          }
      }
      ```

### 3. 版本二(只记录差异部分)

- mapper

  - UserMapperImpl

    - ```xml
      public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
          @Override
          public List<User> selectUser() {
              SqlSession sqlSession = getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              return mapper.selectUser();
          }
      }
      ```

- resources

  - applicationContext.xml

    - ```xml
      <bean id="userMapper" class="com.example.mapper.UserMapperImpl" >
          <property name="sqlSessionFactory" ref="sqlSessionFactory" />
      </bean>
      ```

## 5. 事务

### 1. ACID原则

A：原子性、C：一致性、I：隔离性、D：持久性

### 2. 声明式事务

- mapper

  - UserMapper(Interface)

    - ```java
      public interface UserMapper {
          public List<User> selectUser();
      
          public int addUser(User user);
      
          public int deleteUser(int id);
      }
      ```

  - UserMapper.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.example.mapper.UserMapper">
          <select id="selectUser" resultType="user" >
              select * from spring_user.user;
          </select>
      
          <insert id="addUser" parameterType="User">
              insert into spring_user.user (id, name, pwd) values (#{id}, #{name}, #{pwd});
          </insert>
      
          <delete id="deleteUser" parameterType="int">
              delete from spring_user.user where id=#{id};
          </delete>
      
      </mapper>
      ```

  - UserMapperImpl

    - ```xml
      public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper {
          @Override
          public List<User> selectUser() {
      
              User user = new User(5, "xiaowang", "adc");
      
              SqlSession sqlSession = getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
      
              mapper.addUser(user);
              mapper.deleteUser(2);
      
              return mapper.selectUser();
          }
      
          @Override
          public int addUser(User user) {
              return getSqlSession().getMapper(UserMapper.class).addUser(user);
          }
      
          @Override
          public int deleteUser(int id) {
              return getSqlSession().getMapper(UserMapper.class).deleteUser(id);
          }
      }
      ```

- pojo

  - User

    - ```java
      @Data
      @AllArgsConstructor
      @NoArgsConstructor
      public class User {
          private int id;
          private String name;
          private String pwd;
      }
      ```

- resources

  - applicationContext.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd">
      
          <import resource="spring-dao.xml" />
      
          <bean id="userMapper" class="com.example.mapper.UserMapperImpl" >
              <property name="sqlSessionFactory" ref="sqlSessionFactory" />
          </bean>
      
      </beans>
      ```

  - Mybatis-config.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "https://mybatis.org/dtd/mybatis-3-config.dtd">
      <configuration>
      
          <typeAliases>
              <package name="com.example.pojo"/>
          </typeAliases>
      
      </configuration>
      ```

  - spring-dao.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xmlns:tx="http://www.springframework.org/schema/tx"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
              https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/aop
              https://www.springframework.org/schema/aop/spring-aop.xsd
              http://www.springframework.org/schema/tx
              https://www.springframework.org/schema/tx/spring-tx.xsd">
      
          <!-- DataSource: 使用spring的数据源替换mybatis的配置 c3p0 dbcp druid
                  默认使用spring提供的jdbc -->
          <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
              <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
              <property name="url" value="jdbc:mysql://localhost:3306/spring_user?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
              <property name="username" value="root"/>
              <property name="password" value="123456"/>
          </bean>
      
          <!-- sqlSessionFactory -->
          <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
              <property name="dataSource" ref="datasource" />
              <!-- 绑定mybatis配置 -->
              <property name="configLocation" value="classpath:mybatis-config.xml" />
              <property name="mapperLocations" value="classpath:com/example/mapper/*.xml" />
          </bean>
      
          <!-- sqlSessionTemplate: 就是常用的sqlSession -->
          <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" >
          <!-- 只能使用构造器注入sqlSessionFactory,因为它没有set方法 -->
              <constructor-arg index="0" ref="sqlSessionFactory" />
          </bean>
      
          <!--    配置声明式事务-->
          <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <constructor-arg ref="datasource" />
          </bean>
      
          <!--    结合AOP实现事务注入-->
          <!--    配置事务通知-->
          <tx:advice id="txAdvice" transaction-manager="transactionManager">
      <!--        指定给某些方法配置新特性-->
      <!--        配置事务的传播特性-->
              <tx:attributes>
                  <tx:method name="add" propagation="REQUIRED"/>
                  <tx:method name="delete" propagation="REQUIRED"/>
                  <tx:method name="update" propagation="REQUIRED"/>
                  <tx:method name="query" read-only="true"/>
                  <tx:method name="*"  propagation="REQUIRED"/>
              </tx:attributes>
          </tx:advice>
      
      <!--    配置事务切入-->
          <aop:config>
              <aop:pointcut id="txPointCut" expression="execution(* com.example.mapper.*.*(..))"/>
              <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
          </aop:config>
      </beans>
      ```

- MyTest

  - class

    - ```xml
      public class MyTest {
          @Test
          public static void main(String[] args) {
              ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
              UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
      
              List<User> userList = userMapper.selectUser();
              for (User user : userList) {
                  System.out.println(user);
              }
          }
      }
      ```



