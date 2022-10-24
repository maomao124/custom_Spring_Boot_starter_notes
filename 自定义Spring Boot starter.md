



# Spring Boot starter

Spring Boot大大简化了项目初始搭建以及开发过程，而这些都是通过Spring Boot提供的starter来完成的





## starter介绍

spring boot 在配置上相比spring要简单许多, 其核心在于spring-boot-starter, 在使用spring boot来搭建一个项目时, 只需要引入官方提供的starter, 就可以直接使用, 免去了各种配置。starter简单来讲就是引入了一些相关依赖和一些初始化的配置。

Spring官方提供了很多starter，第三方也可以定义starter。为了加以区分，starter从名称上进行了如下规范：



* Spring官方提供的starter名称为：spring-boot-starter-xxx，例如Spring官方提供的spring-boot-starter-web
* 第三方提供的starter名称为：xxx-spring-boot-starter，例如由mybatis提供的mybatis-spring-boot-starter







## starter原理

Spring Boot之所以能够帮我们简化项目的搭建和开发过程，主要是基于它提供的起步依赖和自动配置。





### 起步依赖

起步依赖，其实就是将具备某种功能的坐标打包到一起，可以简化依赖导入的过程。例如，我们导入spring-boot-starter-web这个starter，则和web开发相关的jar包都一起导入到项目中了



![image-20221024191739642](img/自定义Spring Boot starter/image-20221024191739642.png)





```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- This module was also published with a richer model, Gradle metadata,  -->
  <!-- which should be used instead. Do not delete the following line which  -->
  <!-- is to indicate to Gradle or any Gradle module metadata file consumer  -->
  <!-- that they should prefer consuming it instead. -->
  <!-- do_not_remove: published-with-gradle-metadata -->
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <version>2.7.1</version>
  <name>spring-boot-starter-web</name>
  <description>Starter for building web, including RESTful, applications using Spring MVC. Uses Tomcat as the default embedded container</description>
  <url>https://spring.io/projects/spring-boot</url>
  <organization>
    <name>Pivotal Software, Inc.</name>
    <url>https://spring.io</url>
  </organization>
  <licenses>
    <license>
      <name>Apache License, Version 2.0</name>
      <url>https://www.apache.org/licenses/LICENSE-2.0</url>
    </license>
  </licenses>
  <developers>
    <developer>
      <name>Pivotal</name>
      <email>info@pivotal.io</email>
      <organization>Pivotal Software, Inc.</organization>
      <organizationUrl>https://www.spring.io</organizationUrl>
    </developer>
  </developers>
  <scm>
    <connection>scm:git:git://github.com/spring-projects/spring-boot.git</connection>
    <developerConnection>scm:git:ssh://git@github.com/spring-projects/spring-boot.git</developerConnection>
    <url>https://github.com/spring-projects/spring-boot</url>
  </scm>
  <issueManagement>
    <system>GitHub</system>
    <url>https://github.com/spring-projects/spring-boot/issues</url>
  </issueManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.7.1</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-json</artifactId>
      <version>2.7.1</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.7.1</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.3.21</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.21</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
</project>
```









### 自动配置

自动配置，就是无须手动配置xml，自动配置并管理bean，可以简化开发过程。那么Spring Boot是如何完成自动配置的呢？

自动配置涉及到如下几个关键步骤：



* 基于Java代码的Bean配置
* 自动配置条件依赖
* Bean参数获取
* Bean的发现
* Bean的加载







#### 基于Java代码的Bean配置

当我们在项目中导入了mybatis-spring-boot-starter这个jar后，可以看到它包括了很多相关的jar包



![image-20221024192213176](img/自定义Spring Boot starter/image-20221024192213176.png)





![image-20221024192257927](img/自定义Spring Boot starter/image-20221024192257927.png)







其中在mybatis-spring-boot-autoconfigure这个jar包中有如下一个**MybatisAutoConfiguration**自动配置类：



![image-20221024192415882](img/自定义Spring Boot starter/image-20221024192415882.png)





打开这个类，截取的关键代码如下：

![image-20221024192536911](img/自定义Spring Boot starter/image-20221024192536911.png)



![image-20221024192619874](img/自定义Spring Boot starter/image-20221024192619874.png)



![image-20221024192632207](img/自定义Spring Boot starter/image-20221024192632207.png)







**@Configuration**和**@Bean**这两个注解一起使用就可以创建一个基于java代码的配置类，可以用来替代传统的xml配置文件。

**@Configuration** 注解的类可以看作是能生产让Spring IoC容器管理的Bean实例的工厂。

**@Bean** 注解的方法返回的对象可以被注册到spring容器中。





所以上面的**MybatisAutoConfiguration**这个类，自动帮我们生成了SqlSessionFactory和SqlSessionTemplate这些Mybatis的重要实例并交给spring容器管理，从而完成bean的自动注册。







#### 自动配置条件依赖

要完成自动配置是有依赖条件的。





|              注解               |                       功能说明                       |
| :-----------------------------: | :--------------------------------------------------: |
|       @ConditionalOnBean        |  仅在当前上下文中存在某个bean时，才会实例化这个Bean  |
|       @ConditionalOnClass       |      某个class位于类路径上，才会实例化这个Bean       |
|    @ConditionalOnExpression     |       当表达式为true的时候，才会实例化这个Bean       |
|    @ConditionalOnMissingBean    | 仅在当前上下文中不存在某个bean时，才会实例化这个Bean |
|   @ConditionalOnMissingClass    | 某个class在类路径上不存在的时候，才会实例化这个Bean  |
| @ConditionalOnNotWebApplication |           不是web应用时才会实例化这个Bean            |
|       @AutoConfigureAfter       |        在某个bean完成自动配置后实例化这个bean        |
|      @AutoConfigureBefore       |        在某个bean完成自动配置前实例化这个bean        |







#### Bean参数获取

要完成mybatis的自动配置，需要我们在配置文件中提供数据源相关的配置参数，例如数据库驱动、连接url、数据库用户名、密码等。那么spring boot是如何读取yml或者properites配置文件的的属性来创建数据源对象的？

在我们导入mybatis-spring-boot-starter这个jar包后会传递过来一个spring-boot-autoconfigure包，在这个包中有一个自动配置类**DataSourceAutoConfiguration**



![image-20221024193310519](img/自定义Spring Boot starter/image-20221024193310519.png)





我们可以看到这个类上加入了**EnableConfigurationProperties**这个注解，继续跟踪源码到**DataSourceProperties**这个类



![image-20221024193358997](img/自定义Spring Boot starter/image-20221024193358997.png)





可以看到这个类上加入了**ConfigurationProperties**注解，这个注解的作用就是把yml或者properties配置文件中的配置参数信息封装到**ConfigurationProperties**注解标注的bean(即**DataSourceProperties**)的相应属性上





@**EnableConfigurationProperties**注解的作用是使@**ConfigurationProperties**注解生效。







#### Bean的发现

spring boot默认扫描启动类所在的包下的主类与子类的所有组件，但并没有包括依赖包中的类，那么依赖包中的bean是如何被发现和加载的？

我们需要从Spring Boot项目的启动类开始跟踪，在启动类上我们一般会加入SpringBootApplication注解



![image-20221024194425682](img/自定义Spring Boot starter/image-20221024194425682.png)





![image-20221024194530754](img/自定义Spring Boot starter/image-20221024194530754.png)





**SpringBootConfiguration**：作用就相当于**Configuration**注解，被注解的类将成为一个bean配置类

**ComponentScan**：作用就是自动扫描并加载符合条件的组件，最终将这些bean加载到spring容器中

**EnableAutoConfiguration** ：这个注解很重要，借助@**Import**的支持，收集和注册依赖包中相关的bean定义





继续跟踪**EnableAutoConfiguration**注解源码：



![image-20221024194708797](img/自定义Spring Boot starter/image-20221024194708797.png)





@**EnableAutoConfiguration**注解引入了@**Import**这个注解。

**Import**：导入需要自动配置的组件





继续跟踪**AutoConfigurationImportSelector**类源码：



![image-20221024195114315](img/自定义Spring Boot starter/image-20221024195114315.png)





**AutoConfigurationImportSelector**类的getCandidateConfigurations方法中的调用了SpringFactoriesLoader类的loadFactoryNames方法







![image-20221024195214086](img/自定义Spring Boot starter/image-20221024195214086.png)





**SpringFactoriesLoader**的**loadFactoryNames**静态方法可以从所有的jar包中读取META-INF/spring.factories文件，而自动配置的类就在这个文件中进行配置：





![image-20221024195247959](img/自定义Spring Boot starter/image-20221024195247959.png)





spring.factories文件内容如下：



![image-20221024195309694](img/自定义Spring Boot starter/image-20221024195309694.png)



```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.mybatis.spring.boot.autoconfigure.MybatisLanguageDriverAutoConfiguration,\
org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration
```





这样Spring Boot就可以加载到**MybatisAutoConfiguration**这个配置类了











#### Bean的加载

在Spring Boot应用中要让一个普通类交给Spring容器管理，通常有以下方法：

* 使用 @Configuration与@Bean 注解

* 使用@Controller @Service @Repository @Component 注解标注该类并且启用@ComponentScan自动扫描

* 使用@Import 方法

其中Spring Boot实现自动配置使用的是@Import注解这种方式，AutoConfigurationImportSelector类的selectImports方法返回一组从META-INF/spring.factories文件中读取的bean的全类名，这样Spring Boot就可以加载到这些Bean并完成实例的创建工作。

















## 自定义starter

