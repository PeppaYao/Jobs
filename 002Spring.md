## Spring学习

### 1、优势

- 方便解耦，简化开发
- AOP编程的支持
- 声明式事务的支持
- 方便集成各种框架

### 2、Spring开发的步骤

- 导入spring开发的基本包坐标
- 编写DAO接口和实现类
- 创建Spring核心配置文件
- 在Spring配置文件中配置USerDAOImpl
- 使用Spring的API获得Bean实例

IOC底层是用反射实现的

Spring动态代理实现方式 

### 3、Spring的特性

- 轻量
- 控制反转IOC
- 面向容器
- 面向切面AOP
- 框架灵活

spring实现了**对象的配置化生成**和**对象的生命周期管理**，因此，可以理解为spring是面向容器的。

- 对象的配置化生成
- 对象的生命周期管理

#### 4、控制反转IOC

Spring的控制反转指一个对象依赖的其他对象将会在容器的初始化完成后**主动将其依赖的对象传递给它**，而不需要这个对象自己创建或者查找其依赖的对象。

Spring基于控制反转技术实现系统对象之间依赖的解耦。

spring通过一个配置文件描述bean和bean之间的依赖关系，**利用java的反射功能实例化bean**，并建立bean之间的依赖关系。

### 5、面向切面编程AOP

面向切面编程技术通过**分离系统逻辑**和**业务逻辑**来提高系统的**内聚性**。

是通过**预编译方式**和**运行期动态代理**实现程序功能的统一维护的一种技术。

利用AOP可以**对业务逻辑的各个部分进行隔离**，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发效率。

作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强。

优势：减少重复代码，提高开发效率，方便维护。

实际上，AOP的底层是通过Spring提供的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，再去调用目标对象的方法，从而完成功能的增强。

常用的动态代理技术

- JDK代理：基于接口的动态代理技术
- CGLib代理：基于父类的动态代理



### 6、Spring Bean的作用域

- **Singleton 单例(默认)**
- **Prototype 原型**
- Request 请求级别
- Session 会话级别
- Global Session 全局会话

如果scope是单例模式，则创建的所有对象的地址相同，如果是多例模式则对象不同。

```java
public class SpringTest {
    @Test
    public void test(){
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao1 = (UserDao) app.getBean("userDao");
        UserDao userDao2 = (UserDao) app.getBean("userDao");
        userDao1.save();
        userDao2.save();
        System.out.println(userDao1);
        System.out.println(userDao2);
    }
}
```

单例模式singleton

```java
com.bytedance.dao.impl.UserDaoImpl@4d50efb8
com.bytedance.dao.impl.UserDaoImpl@4d50efb8
```

多例模式prototype

```java
com.bytedance.dao.impl.UserDaoImpl@3cb1ffe6
com.bytedance.dao.impl.UserDaoImpl@3dfc5fb8
```

- 当scope的取值为singleton时

	Bean的实例化个数：1个

	Bean的实例化实际：**当Spring核心文件被加载时，实例化配置的Bean实例**

	Bean的生命周期：

	- 对象创建：当应用加载，创建容器时，对象就被创建了。

	- 对象运行：只要容器在，对象一直活着。
	- 对象销毁：当应用被卸载，销毁容器时，对象就被销毁。

- 当scope的取值为prototype时

	Bean的实例化个数：多个

	Bean的实例化实际：**当调用getBean()方法时实例化Bean**

	Bean的生命周期：

	- 对象创建：当使用对象时，创建新的对象实例。
	- 对象运行：只对象在使用中，对象一直活着。
	- 对象销毁：当对象长时间不用时，被Java的垃圾回收器回收了。

	>init_method 类的初始化方法
	>
	>destroy_method 类的销毁方法

### 7、Spring Bean的生命周期

- 实例化Bean
- Bean配置
- 执行setBeanName(String)

### 8、依赖注入4种

- 构造器注入
- set方法注入
- 静态工厂注入
- 实例工厂注入

### 9、自动装配的方式5种

- no
- byName
- byType
- constructor
- autodetect

### 10、AOP的5中通知类型

- 前置通知
- 后置通知
- 成功通知
- 异常通知
- 环绕通知

### 11、AOP的2中代理方式

- JDK
- CGLib

Spring提供了**JDK和CGLib** 2种方式来生成代理对象，具体生成代理对象的方式由AopProxyFactory根据AdvisedSupport对象的配置来决定。

Spring默认的代理对象生成策略为：如果是**目标类接口**，则使用**JDK动态代理技术**，否则使用**CGLib动态代理方式**。

### 12、Spring快速入门

[视频链接](https://www.bilibili.com/video/BV1WZ4y1H7du?p=8)

代码一

```java
import com.bytedance.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
// * 开发步骤
// * 1. 导入坐标
// * 2. 创建bean
// * 3. 创建applicationContext.xml
// * 4. 在配置文件中进行配置
// * 5. 创建ApplicationContext对象getBean



public class UserDaoDemo {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();
    }
}
```

代码二

```java
package com.bytedance.dao.impl;

import com.bytedance.dao.UserDao;

public class UserDaoImpl implements UserDao {

    public void save() {
        System.out.println("save running...");
    }
}
```

代码三

```java
package com.bytedance.dao;

public interface UserDao{
    public void save();
}

```

代码四

```xaml
<bean id="userDao" class="com.bytedance.dao.impl.UserDaoImpl"></bean>
```

### 13、Bean实例化三种方式

- 无参构造方法实例化
- 工厂静态方法实例化
- 工厂实例方法实例化

```xaml
<!--无参构造-->   
<bean id="userDao" class="com.bytedance.dao.impl.UserDaoImpl"></bean>

<!--静态工厂-->
 <bean id="userDao" class="com.bytedance.factory.FactoryStatic" factory-method="getUserDao"> </bean>

<!--工厂实例: 先创建工厂的bean，然后获取工厂中的方法-->
 <bean id="dynamicfactory" class="com.bytedance.factory.DynamicFactory"></bean>
    <bean id="userDao" factory-bean="dynamicfactory" factory-method="getUserDao"> </bean>
```

### 14、依赖注入DI

dependency injection

在编写程序时，通过控制反转，把对象的创建交给了spring,但是代码中不可能出现没有依赖的情况。

IOC解耦只是降低了他们的依赖关系，但是不会消除。

业务层仍然会调用持久层的方法。

业务层和持久层的依赖关系，在使用spring之后，就让spring来维护了。

简单的说，就是**坐等框架把持久层对象传入业务层，而不用我们自己去获取**

**怎么将UserDao注入到UserService内部呢？**

- 构造方法(有参构造constructor-arg)
- set方法

set方法的实现

```xml
<bean id="userDao" class="com.bytedance.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.bytedance.service.Impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
```

在service层实现set方法

```java
private UserDao userDao;
public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
}
```

也可以用P命名空间的注入

### 15、依赖注入的数据类型

- 普通数据类型
- 引用数据类型
- 集合数据类型

普通数据类型的注入 需要实现setter

```xml
<bean id="userDao" class="com.bytedance.dao.impl.UserDaoImpl">
        <property name="username" value="yaojun"></property>
        <property name="age" value="18"></property>
</bean>
```

toString时啥

Properties类型时啥

**集合的注入**

```xaml
<bean id="userDao" class="com.bytedance.dao.impl.UserDaoImpl">
       <property name="stringList">
           <list>
               <value>a</value>
               <value>bb</value>
               <value>ccc</value>
           </list>
       </property>
        <property name="userMap">
            <map>
                <entry key="u1" value-ref="user1"></entry>
                <entry key="u2" value-ref="user2"></entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="p1">ppp1</prop>
                <prop key="p2">ppp2</prop>
                <prop key="p3">ppp3</prop>
            </props>
        </property>
    </bean>
    <bean id="userService" class="com.bytedance.service.Impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
    <bean id="user1" class="com.bytedance.domain.User">
        <property name="name" value="zhangsan"></property>
        <property name="age" value="15"></property>
    </bean>
    <bean id="user2" class="com.bytedance.domain.User">
        <property name="name" value="lisi"></property>
        <property name="age" value="20"></property>
    </bean>
```

## SpringBoot笔记

### 1、快速创建springboot应用

1. 创建maven工程
2. 添加父依赖

  所有springboot工程都必须继承spring-boot-starter-parent

```java
  <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.3.2.RELEASE</version>
   </parent>
```

  

1. 添加Web功能起步依赖

  ```java 
  <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  </dependencies>
  ```

  

2. 创建controller

  生命该类是一个springboot的引导类

  ```java
  @org.springframework.stereotype.Controller
  public class Controller {
      @RequestMapping("/hello")
      @ResponseBody
      public String quick(){
          return "hello world!";
      }}
  ```

  ### 2、原理分析

  - 起步依赖原理分析

  	进行底层的maven依赖，有确定的版本号

  - 自动配置原理分析

  ### 3、注解分析

  @SpringBootApplication等价于以下三个注解加起来

  ```java
  @SpringBootConfiguration = @Configuration
  @EnableAutoConfiguration 允许自动配置的核心注解
  @ComponentScan 在springbootapplication下的包及其子包中的bean都会被扫描
  ```

  @EnableAutoConfiguration

  自动加载默认的配置，yaml和properties和yml

  ```
  server.port=8081
  #当前web应用的名称，URL路径前缀。
  server.servlet.context-path=/demo
  ```

  许多配置都有默认值，如果想使用自己的配置替换默认的配置的话，就可以使用application.yaml和application.properties和application.yml进行配置。

  三种配置同时存在时会进行覆盖yml < yaml < properties

  ### 4、使用yaml进行配置

  语法：key: value 注意value前面有一个空格。

  ```yaml
  #普通数据配置
  name: zhangsan
  #对象的配置
  Person:
    name: zhangsan
    age: 18
    addr: chengdu
  #行内对象配置 用得比较少
  Student: {name: zhangsan, age: 18, addr: chengdu}
  #配置集合
  City:
    - tianjin
    - beijing
    - shanghai
  #行内集合
  Cities: [beijing, tianjin, shanghai]
  #对象集合
  Stu:
    - name: zhangsan
      age: 18
    - name: lucy
      age: 20
  #行内对象集合
  Students: [{name: zhangsan,age: 18}, {name: lucy, age: 20}]
  #map配置
  map:
    key1: value1
    key2: value2
  ```

  ### 5、@value注解加载配置文件

  通过@value注解将配置文件中的值映射到spring管理的bean的字段上

  优点：精确匹配，但是一对一比较繁琐。

  ```yaml
  @Value("${name}")
  private String name;
  ```

  ### 6、@ConfigurationProperties注解加载配置文件

  需要添加执行器，在写代码时有提示。先写属性再写配置。

  ```yaml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```

  必须实现所有属性的getter和setter方法

  ```yaml
  package com.bytedance.controller;
  
  import org.springframework.boot.context.properties.ConfigurationProperties;
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  @Controller
  @ConfigurationProperties(prefix = "person")
  public class Controller2 {
      private String name;
      private String addr;
      private int age;
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getAddr() {
          return addr;
      }
  
      public void setAddr(String addr) {
          this.addr = addr;
      }
  
      public int getAge() {
          return age;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
      @RequestMapping("/quick3")
      @ResponseBody()
      public String quick3(){
          return this.name+this.addr;
      }
  }
  ```

  ### 7、Java配置

  Java配置主要靠Java类和以歇注解来达到xml配置一样的效果。

  ```java
  @Configuration:声明一个类作为配置类，代替xml文件
  @Bean：声明在方法上，将方法的返回值加入Bean容器，代替<bean>标签
  @Value：属性注入
  @PropertySource：指定外部属性文件
  @AutoWired：它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。 通过 @Autowired的使用来消除 set ，get方法。
  @ConfigurationProperties(prefix="jdbc")：读取资源配置类 通过实现setter进行配置
  @EnableConfigurationProperties(JDBC.class) 启用资源配置读取类
  ```

  属性注入

  - Autowired()
  - 构造函数
  - 方法形参
  - ConfigurationProperties(prefix="jdbc")直接加在方法上

  ## springboot 2.X

  - 对于业务类使用注解

  对于MVC开发，控制器使用@Controller,业务层使用@Service，持久层使用@Repository。

  - 对于公用的Bean使用xml

  如数据库redis,第三方资源等

  SpringBoot框架是让Spring更加容易得到快速的使用。Spring Boot以注解为主的开发。

  约定优于配置，这是Spring Boot的主导思想。

  使得开发人员的配置更少，更加直接地开发项目。

  

  Spring MVC的视图解析器的作用主要是定位视图，也就是当控制器只是返回一个逻辑名称的时候，是没有办法直接对应找到视图的，这就需要视图解析器进行解析了。

  IoC是一种通过描述来生成或者获取对象的技术，而这个技术不是Spring甚至不是Java独有的。

  

  **在Spring中把每一个需要管理的对象称为Spring Bean（简称Bean），而Spring管理这些Bean的容器，被我们称为Spring IoC容器（或者简称IoC容器）。**

  

  Spring IoC容器是一个管理Bean的容器，在Spring的定义中，它要求所有的IoC容器都需要实现接口BeanFactory，它是一个顶级容器接口。

  ```java
  @SpringBootApplication
  ```

  