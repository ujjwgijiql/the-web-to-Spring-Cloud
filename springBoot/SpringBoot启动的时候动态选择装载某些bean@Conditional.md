# SpringBoot启动的时候动态选择装载某些bean

​        @ConditionalOnBean（上下文存在某个对象时，才会实例化一个bean）

​        @ConditionalOnClass（当给定的类名在类路径上存在，则实例化当前Bean）

​        @ConditionalOnExpression（表达式为true的时候，才会实例化一个Bean）

​        @ConditionalOnMissingBean（上下文中不存在某个对象时，才会实例化一个Bean）

​        @ConditionalOnMissingClass（当给定的类名在类路径上不存在，则实例化当前Bean）

​        @ConditionalOnNotWebApplication（不是web应用的时候，条件成立）

​        @ConditionalOnProperty（这个更配置文件有关，对配置属性的判断）



# 1、@Conditional注解

**作用：**@Conditional是Spring4新提供的注解，它的作用是按照一定的条件进行判断，满足条件的才给容器注册Bean。



### 1.1、概述

#### 1.1.1、@Conditional注解定义

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
```

#### 1.1.2、Condition

​        我们点进去看后，发现它是一个接口，有一个方法。

```java
@FunctionalInterface
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
```

#### 1.1.3、ConditionContext

​        它持有不少有用的对象，可以用来获取很多系统相关的信息，来丰富条件判断，接口定义如下

```java
public interface ConditionContext {
    /**
     * 获取Bean定义
     */
    BeanDefinitionRegistry getRegistry();
    /**
     * 获取Bean工程，因此就可以获取容器中的所有bean
     */
    @Nullable
    ConfigurableListableBeanFactory getBeanFactory();
    /**
     * environment 持有所有的配置信息
     */
    Environment getEnvironment();
    /**
     * 资源信息
     */
    ResourceLoader getResourceLoader();
    /**
     * 类加载信息
     */
    @Nullable
    ClassLoader getClassLoader();
}
```



### 1.2、案例

**需求：**根据当前系统环境的的不同实例不同的Bean，比如现在是Mac那就实例一个Bean,如果是Window系统实例另一个Bean。

#### 1.2.1、SystemBean

​        首先创建一个Bean类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class SystemBean {
    
    /** 系统名称 */
    private String systemName;
    
    /** 系统code */
    private String systemCode;
}
```

#### 1.2.2、通过Configuration配置实例化Bean

```java
@Slf4j
@Configuration
public class ConditionalConfig {
    /**
     * 如果WindowsCondition的实现方法返回true，则注入这个bean
     */
    @Bean("windows")
    @Conditional({WindowsCondition.class})
    public SystemBean systemWi() {
        log.info("ConditionalConfig方法注入 windows实体");
        return new SystemBean("windows系统","002");
    }
    /**
     * 如果LinuxCondition的实现方法返回true，则注入这个bean
     */
    @Bean("mac")
    @Conditional({MacCondition.class})
    public SystemBean systemMac() {
        log.info("ConditionalConfig方法注入 mac实体");
        return new SystemBean("Mac ios系统","001");
    }
}
```

#### 1.2.3、WindowsCondition和MacCondition

​        这两个类都实现了Condition接口, `只有matches方法返回true才会实例化当前Bean`。

**1）WindowsCondition**

```java
@Slf4j
public class WindowsCondition implements Condition {
    /**
     * @param conditionContext:判断条件能使用的上下文环境
     * @param annotatedTypeMetadata:注解所在位置的注释信息
     */
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        
        // 获取ioc使用的beanFactory
        ConfigurableListableBeanFactory beanFactory = conditionContext.getBeanFactory();
        
        // 获取类加载器
        ClassLoader classLoader = conditionContext.getClassLoader();
        
        // 获取当前环境信息
        Environment environment = conditionContext.getEnvironment();
        
        // 获取bean定义的注册类
        BeanDefinitionRegistry registry = conditionContext.getRegistry();
        
        // 获得当前系统名
        String property = environment.getProperty("os.name");
        
        // 包含Windows则说明是windows系统，返回true
        if (property.contains("Windows")){
            log.info("当前操作系统是：Windows");
            return true;
        }
        return false;
    }
}
```

**2) MacCondition**

```java
@Slf4j
public class MacCondition implements Condition {

   @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        Environment environment = conditionContext.getEnvironment();
        String property = environment.getProperty("os.name");
        if (property.contains("Mac")) {
            log.info("当前操作系统是：Mac OS X");
            return true;
        }
        return false;
    }
}
```

#### 1.2.4、测试类测试

```java
@SpringBootTest(classes = Application.class)
@RunWith(SpringRunner.class)
public class TestConditionOn {

    @Autowired
    private SystemBean windows;
    @Autowired
    private SystemBean mac;

    @Test
    public void test() {
        if (windows != null) {
            System.out.println("windows = " + windows);
        }
        if (mac != null) {
            System.out.println("linux = " + mac);
        }
    }
}
```

**运行结果**

​        通过运行结果可以看出

```java
2020-12-28 14:06:27.412  INFO 5216 --- [           main] s.boot.conditional.WindowsCondition      : 当前操作系统是：Windows
2020-12-28 14:06:27.955  INFO 5216 --- [           main] s.boot.conditional.ConditionalConfig     : ConditionalConfig方法注入 windows实体
2020-12-28 14:06:28.451  INFO 5216 --- [           main] spring.boot.conditional.TestConditionOn  : Started TestConditionOn in 2.454 seconds (JVM running for 4.742)
windows = SystemBean(systemName=windows系统, systemCode=002)
linux = SystemBean(systemName=windows系统, systemCode=002)
```

1、虽然配置两个Bean,但这里只实例化了一个Bean,因为我这边是Mac电脑，所以实例化的是mac的SystemBean

2、注意一点，我们可以看出 `window`并不为null,而是mac实例化的Bean。说明 只要实例化一个Bean的，不管你命名什么，都可以注入这个Bean。

**修改一下**

​        这里做一个修改,我们把`ConditionalConfig`中的这行代码注释掉。

```java
// @Conditional({MacCondition.class})
```

**再运行下代码**

```
2020-12-28 14:15:42.555  INFO 27036 --- [           main] s.boot.conditional.WindowsCondition      : 当前操作系统是：Windows
2020-12-28 14:15:43.015  INFO 27036 --- [           main] s.boot.conditional.ConditionalConfig     : ConditionalConfig方法注入 windows实体
2020-12-28 14:15:43.020  INFO 27036 --- [           main] s.boot.conditional.ConditionalConfig     : ConditionalConfig方法注入 mac实体
2020-12-28 14:15:43.341  INFO 27036 --- [           main] spring.boot.conditional.TestConditionOn  : Started TestConditionOn in 2.159 seconds (JVM running for 4.545)
windows = SystemBean(systemName=windows系统, systemCode=002)
linux = SystemBean(systemName=Mac ios系统, systemCode=001)
```

​        通过运行结果可以看出，配置类的两个Bean都已经注入成功了。

**注意** 当同一个对象被注入两次及以上的时候，那么你在使用当前对象的时候，名称一定要是两个bean名称的一个,否则报错。比如修改为

```java
@Autowired
private SystemBean windows;
@Autowired
private SystemBean mac;
@Autowired
private SystemBean linux;
```

​        再启动发现，报错了。

```
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'spring.boot.conditional.TestConditionOn': Unsatisfied dependency expressed through field 'linux'; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'spring.boot.conditional.SystemBean' available: expected single matching bean but found 2: windows,mac
```

​        意思很明显，就是上面只实例化成功一个SystemBean的时候，你取任何名字，反正就是把当前已经实例化的对象注入给你就好了。
但是你现在同时注入了两个SystemBean,你这个时候有个名称为linux，它不知道应该注入那个Bean,所以采用了报错的策略。



# 2、@ConditionalOnBean与@ConditionalOnClass、@ConditionalOnMissingBean、@ConditionalOnMissingClass

### 2.1、@ConditionalOnBean概念

**需求场景** 比如下面一种场景，我在实例化People对象的时候，需要注入一个City对象。这个时候问题来了，如果city没有实例化，那么下面就会报空指针或者直接报错。
        所以这里需求很简单，就是当前city存在则实例化people,如果不存在则不实例化people,这个时候**@ConditionalOnBean** 的作用来了。

```java
    @Bean
    public People people(City city) {
        //这里如果city实体没有成功注入 这里就会报空指针
        city.setCityName("千岛湖");
        city.setCityCode(301701);
        return new People("小小", 3, city);
    }
```

#### 2.1.1、@ConditionalOnBean注解定义

```java
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional(OnBeanCondition.class)
public @interface ConditionalOnBean {
    /**
     * 需要作为条件的类的Class对象数组
     */
    Class<?>[] value() default {};
    /**
     * 需要作为条件的类的Name,Class.getName()
     */
    String[] type() default {};
    /**
     *  (用指定注解修饰的bean)条件所需的注解类
     */
    Class<? extends Annotation>[] annotation() default {};
    /**
     * spring容器中bean的名字
     */
    String[] name() default {};
    /**
     * 搜索容器层级,当前容器,父容器
     */
    SearchStrategy search() default SearchStrategy.ALL;
    /**
     * 可能在其泛型参数中包含指定bean类型的其他类
     */
    Class<?>[] parameterizedContainer() default {};
}
```

### 2.2、@ConditionalOnBean示例

#### 2.2.1、Bean实体

**1）City类**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class City {
    /** 城市名称 */
    private String cityName;
    
    /** 城市code */
    private Integer cityCode;
}
```

**2）People类**

​        这里City作为People一个属性字段。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class People {
    /** 姓名 */
    private String name;
    
    /** 年龄 */
    private Integer age;
    
    /** 城市信息 */
    private City city;
}
```

#### 2.2.2、Config类

​        这里写个正常的配置类，City成功注入到IOC容器中。

```java
@Configuration
public class Config {
    
    @Bean
    public City city() {
        City city = new City();
        city.setCityName("千岛湖");
        return city;
    }
    
    @Bean
    public People people(City city) {
        // 这里如果city实体没有成功注入 这里就会报空指针
        city.setCityCode(301701);
        return new People("小小", 3, city);
    }
}
```

#### 2.2.3、Test测试类

```java
@SpringBootTest(classes = Application.class)
@RunWith(SpringRunner.class)
public class TestConditionOn {

    @Autowired(required=false)
    private People people;
    
    @Test
    public void test() {
        System.out.println("= = = = = = = = = = = = = ");
        System.out.println("people = " + people);
        System.out.println("= = = = = = = = = = = = = ");
    }
}
```

​        运行结果

```java
= = = = = = = = = = = = = 
people = People(name=小小, age=3, city=City(cityName=千岛湖, cityCode=301701))
= = = = = = = = = = = = = 
```

​        一切正常，这个很符合我们实际开发中的需求。但是如果有一种情况，就是我的city并没有被注入。我把上面这部分注视掉。

```java
//    @Bean
//    public City city() {
//        City city = new City();
//        city.setCityName("千岛湖");
//        return city;
//    }
```

​        **再运行测试类**

```java
Action:

Consider defining a bean of type 'spring.boot.conditional.onbean.City' in your configuration.
```

​        发现启动直接报错了，这当然不是我们希望看到的，我们是要当city已经注入那么实例化people，如果没有注入那么不实例化people。

```java
@Configuration
public class Config {
//    @Bean
//    public City city() {
//        City city = new City();
//        city.setCityName("千岛湖");
//        return city;
//    }

    /**
     * 这里加了ConditionalOnBean注解，就代表如果city存在才实例化people
     */
    @Bean
    @ConditionalOnBean(name = "city")
    public People people(City city) {
        // 这里如果city实体没有成功注入 这里就会报空指针
        city.setCityCode(301701);
        return new People("小小", 3, city);
    }
}
```

​        **再运行测试类**

```java
= = = = = = = = = = = = = 
people = null
= = = = = = = = = = = = = 
```

​        很明显，上面因为city已经注释调，所以也导致无法实例化people，所以people为null。

**注意 **有点要注意的，就是一旦使用@Autowired那就默认代表当前Bean一定是已经存在的，如果为null，会报错。所以这里要修改下。

```java
@Autowired(required=false) //required=false 的意思就是允许当前的Bean对象为null。
```

**总结** 讲了这个注解，其它三个注解的意思大致差不多，在实际开发过程中可以根据实际情况使用该注解。



# 3、@ConditionalOnProperty使用详解



## Spring Boot中的使用  

​      在Spring Boot的源码中，比如涉及到Http编码的自动配置、数据源类型的自动配置等大量的使用到了@ConditionalOnProperty的注解。

  

​        **HttpEncodingAutoConfiguration类中部分源代码：**

```java
@Configuration(proxyBeanMethods = false) @EnableConfigurationProperties(HttpProperties.class) @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET) @ConditionalOnClass(CharacterEncodingFilter.class) @ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true) public class HttpEncodingAutoConfiguration {
    // 省略内部代码 
}
```



​       **DataSourceConfiguration类中部分代码：**

```java
@Configuration(proxyBeanMethods = false) @ConditionalOnClass(org.apache.tomcat.jdbc.pool.DataSource.class) @ConditionalOnMissingBean(DataSource.class) @ConditionalOnProperty(name = "spring.datasource.type", havingValue = "org.apache.tomcat.jdbc.pool.DataSource", 	matchIfMissing = true) static class Tomcat {
    // 省略内部代码 
}
```

​        很显然，以上两个自动配置类中都通过@ConditionalOnProperty来控制自动配置是否生效，下面我们来了解一下它的源码和具体使用。



## @ConditionalOnProperty源码说明

​        **@ConditionalOnProperty注解类源码如下：**

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE, ElementType.METHOD })
@Documented
@Conditional(OnPropertyCondition.class)
public @interface ConditionalOnProperty {

	// 数组，获取对应property名称的值，与name不可同时使用
	String[] value() default {};

	// 配置属性名称的前缀，比如spring.http.encoding
	String prefix() default "";

	// 数组，配置属性完整名称或部分名称
	// 可与prefix组合使用，组成完整的配置属性名称，与value不可同时使用
	String[] name() default {};

	// 可与name组合使用，比较获取到的属性值与havingValue给定的值是否相同，相同才加载配置
	String havingValue() default "";

	// 缺少该配置属性时是否可以加载。如果为true，没有该配置属性时也会正常加载；反之则不会生效
	boolean matchIfMissing() default false;

}
```

​        其中在历史版本中还存在一个relaxedNames属性：

```java
//是否可以松散匹配
boolean relaxedNames() default true;
```

​        最新版本中已经不存在该属性了。

​        通过注解ConditionalOnProperty上的@Conditional(OnPropertyCondition.class)代码，可以看出ConditionalOnProperty属于@Conditional的衍生注解。生效条件由OnPropertyCondition来进行判断。



## 使用方法

​        关于@ConditionalOnProperty的使用方法，我们在上面的Spring Boot中的使用已经看到。

​        @ConditionalOnProperty的核心功能是通过属性name以及havingValue来实现的。

​        首先看matchIfMissing属性，用来指定如果配置文件中未进行对应属性配置时的默认处理：默认情况下matchIfMissing为false，也就是说如果未进行属性配置，则自动配置不生效。如果matchIfMissing为true，则表示如果没有对应的属性配置，则自动配置默认生效。

​        下面看name属性，name用来从application.properties中读取某个属性值。比如上面Tomcat的自动配置在配置文件为：

```properties
spring.datasource.type=org.apache.tomcat.jdbc.pool.DataSource
```

​        在matchIfMissing为false时，如果name值为空，则返回false；如果name不为空，则将该值与havingValue指定的值进行比较，如果一样则返回true，否则返回false。返回false也就意味着自动配置不会生效。

​        但是如果看HttpEncodingAutoConfiguration类上的属性配置发现并没有完全按照上面所说的name和havingValue配合使用。它是通过“prefix+value”作为属性的名称来进行配置：

```properties
spring.http.encoding.enabled=true
```

​        其中prefix指定了配置的统一前缀“spring.http.encoding”，而value指定了具体的属性名称为“enabled”。这里并没有设置havingValue的值，如果havingValue未指定值，默认情况下在属性配置中设置的值为true则生效（如上配置），false则不生效。





