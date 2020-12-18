# Spring Boot 排除自动配置的 4 种方法，关键时刻很有用！

​    Spring Boot 提供的自动配置非常强大，某些情况下，自动配置的功能可能不符合我们的需求，需要我们自定义配置，这个时候就需要排除/禁用 Spring Boot 某些类的自动化配置了。
​    比如：数据源、邮件，这些都是提供了自动配置的，我们需要排除 Spring Boot 的自动化配置，交给我们自己来自定义，该如何做呢？



## 方法1

使用 <code>@SpringBootApplication</code> 注解的时候，使用 exclude 属性进行排除指定的类：
```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class, MailSenderAutoConfiguration.class})
public class Application {
    // ...
}
```

自动配置类不在类路径下的时候，使用 excludeName 属性进行排除指定的类名全路径：

```java
@SpringBootApplication(excludeName = {"org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration", "org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration"})
public class Application {
    // ...
}
```

这个注解集成了 <code>@EnableAutoConfiguration</code> 注解及其里面的参数.



## 方法2

单独使用 <code>@EnableAutoConfiguration</code> 注解的时候：

```java
@...
@EnableAutoConfiguration
(exclude = {DataSourceAutoConfiguration.class, MailSenderAutoConfiguration.class})
public class Application {
    // ...
}
```

自动配置类不在类路径下的时候，使用 excludeName 属性进行排除指定的类名全路径：

```java
@...
@EnableAutoConfiguration {"org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration", "org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration"})
public class Application {
    // ...
}
```



## 方法3

使用 Spring Cloud 和 <code>@SpringCloudApplication</code> 注解的时候：

```java
@...
@EnableAutoConfiguration
(exclude = {DataSourceAutoConfiguration.class, MailSenderAutoConfiguration.class})
@SpringCloudApplication
public class Application {
    // ...
}
```

Spring Cloud 必须建立在 Spring Boot 应用之上，所以这个不用多解释了。



## 方法4

终极方案，不管是 Spring Boot 还是 Spring Cloud 都可以搞定，在配置文件中指定参数 <code>spring.autoconfigure.exclude</code> 进行排除：

```properties
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
    org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration
```

或者还可以这样写：

```properties
spring.autoconfigure.exclude[0]=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
spring.autoconfigure.exclude[1]=org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration
```

如果你用的是 yaml 配置文件，可以这么写：

```properties
spring:     
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
      - org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration
```

知道了这 4 种排除方法，我们使用 Spring Boot 的自动配置功能就游刃有余了。