# 教程地址  
[教程地址](https://www.bilibili.com/video/BV1Et411Y7tQ?p=1)
# Spring Boot入门  

# Spring Boot配置     
##  配置文件  
- Spring Boot使用一个全局配置文件，支持两种格式。（可以在这修改自动配置默认值）  
  - `application.properties`  

  - `application.yml`    
- 配置文件存放在`src/main/resources`目录或者`类路径/config`下  

- yml是YAML（YAML Ain`t Markup Language）语言的文件，**以数据为中心**，比json、xml更适合做配置文件。  
yml文件：    
  ```yml
  server:  
    port: 8081
  ```   
  xml文件： 
  ```xml
  <server>
    <port>8081</port>
  </server> 
  ```   
## YAML语法  
### 1. YAML基本语法  
  - 使用**缩进**表示层级关系。  

  - 缩进时不允许使用Tab键，只允许使用空格。  
  - 缩进的空格数目不重要，只要相同层级的元素**左对齐**即可。  
  - 大小写敏感。  

### 2. YAML支持的三种数据结构     
- 对象：键值对的集合   
  - 通过K: V的形式表达（**：后面必须得加空格**）  
  - 第一行只写对象名 在下一行写对象属性和值的关系 注意缩进
    ```yml
    student:  
      name: fish  
      age: 23
    ```       
  - 支持行内写法  
    ```yml
    student: {name: fish,age: 23}
    ```  

- 数组：一组按次序排列的值   
  - 用`-  值`表示数组中的一个元素  
     ```yml
      age:  
        - 12  
        - 14  
        - 23
      ```    
  - 支持行内写法  
      ```yml
      age: [12,14,23]
      ```        

- 字面量：单个的、不可再分的值（数字、字符串、布尔）   
  - 通过K: V的形式表达（：后面必须得加空格） 

  - 字符串默认不用加上单引号或者双引号  

  - "" 双引号：不会转义字符串里的特殊字符   name: "aa\nlist" 输出 aa换行list  

  - '' 单引号：会转义特殊字符 特殊字符最终只是一个普通的字符串数据   name: "aa\nlist" 输出 aa\nlist   

  ```yml
  name: fish
  ```   

### 3. 配置文件注入  
yml配置文件：  
```yml
person:
    name: fish
    age: 23
    boss: false
    birth: 2018/11/11
    maps: {k1: v1,k2,v2}
    lists:
      - 12
      - 32
      - 45
    dog:
      name: tom
      age: 23
```   
JavaBean：  
```java
/**
 * 通过 @ConfigurationProperties 注解将配置文件中属性的值映射到这个组件中
 * @ConfigurationProperties : 告诉Spring Boot将本类中所有属性和配置文件中相关的配置进行绑定。
 * prefix : 指定配置文件中的哪个对象下面的属性进行一一映射。
 *
 * 只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能。
 * 所以必须先使用类注解将当前组件加入到容器中。
 * */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;  
    //省略了get、set方法  
}  

public class Dog {
    private String name;
    private String age;
}
```  
项目首次使用` @ConfigurationProperties`注解需要先添加相关依赖：  
```xml  
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```  
通过属性文件也可以实现注入: 
```properties 
# idea默认使用的是utf-8，而properties文件用的assc编码需要在File--Setting--搜索file encoding 将转成assc码选上
# 配置person的值
person.name=fish
person.age=23
person.birth=2020/12/21
person.boss=false
person.maps.k1=v1
person.maps.k2=v4
person.lists=[12,34,45]
person.dog.name=tom
person.dog.age=12
```  
## @Value和@ConfigurationProperties注解的区别   
描述| @ConfigurationProperties | @Value  
:-|:-|:-  
功能|批量注入配置文件中的属性(作用在类上) | 一个个指定(作用在属性上)   
松散绑定(松散语法)|支持|不支持  
SpEL(Spirng表达式)|不支持|支持  
JSR30数据校验|支持|不支持  
复杂类型封装|支持|不支持    
- 如果我们只是在某个业务逻辑中需要获取一下配置文件中的某项值（单个属性值），使用@Value注解。  

- 如果我们专门编写了一个JavaBean来和配置文件进行映射（对象所有属性值），使用@ConfigurationProperties注解。    

- 松散绑定：使用`@ConfigurationProperties`注解，配置文件中`last-name`和`lastName`最终都可以被识别并表示的是同一个属性。  

- 数据校验`@Validated`：  
```java  
@Component
@ConfigurationProperties(prefix = "person")  
@Validated //JSR30数据校验注解 表示当前类需要进行校验 可在属性上配置具体的校验规则
public class Person {
    //@Value("${person.name}")  //获取配置文件中的值  
    @Email                      //name必须是邮箱类型 @Value不支持
    private String name;;  
    //@Value("#{12*2}")         //获取表达式中的值 只有@Value支持
    private Integer age;
    //@Value("true")
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;    

}  
```
## @PropertySource和@ImportResource  
### @PropertySource
- @PropertySource**只能用于properties文件**,用来加载指定的配置文件。   

- @ConfigurationProperties(prefix = "person")默认从全局配置文件(application.properties)中获取值。如果不是全局文件需要使用@PropertySource注解。  

- @PropertySource格式：  
  ```java  
  @PropertySource(value={"classpath:person.properties"})
  public class Person {}
  ```
### @ImportResource
- @ImportResource用来读取外部配置文件。（导入Spring配置文件`bean.xml`，让配置文件里的内容生效。不推荐使用）    

- Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别，想让Spring的配置文件生效，加载进来需要使用@ImportResource注解。  

- @ImportResource注解一般标注在主配置类上。  
  ```java  
  @ImportResource(locations={"classpath:bean.xml"})
  @SpringBootApplication  
  public class SpringBootConfigApplication {
      public static void main(String[] args){
          SpringBootApplication.run(...);
      }
  }
  ```
- Spring Boot推荐使用**配置类**的方式给容器中添加组件，而不是导入Spring配置文件的形式：  
```java
@Configuration  //指明当前类是一个配置类，用来代替Spring配置文件  
public class MyAppConfig{
  @Bean  //将方法的返回值添加到容器中，容器中这个组件的默认id为方法名
  public HelloService helloService(){
    return new HelloService();
  }
}
```   
## 配置文件占位符  
- 随机数    
```properties  
${random.value}  ${random.int}  ${random.long}  
${random.int(10)}  ${random.int[1024,65536]}  
person.age=${random.int}
```

- 占位符获取之前配置的值，如果没有可以使用 : 指定默认值。
```properties  
person.name=fish 
#之前没声明过dept属性，如果不设默认值，person.dept会当做字符串显示。
person.dog.name=${person.dept:model}_dog
```
## Profile  
Profile是Spring对不同环境提供不同配置功能的支持，可以通过激活、指定参数等方式**快速切换环境**（开发、测试、应用）。    

### 1. 多Profile文件(比较麻烦 不推荐)     
- 我们在主配置文件编写的时候，文件名可以是`application-{profile}.properties/yml`。  

  - 开发环境：`application-dev.properties/yml`  

  - 测试环境：`application-test.properties/yml`   

  - 生产环境：`application-prod.properties/yml`   

- 默认使用的是`application.properties/yml`。

### 2. yml支持多文档块方式  
- yml使用` --- `来隔离多个文档块  

- 如果有多个环境推荐最好使用这种方式   
```yml
#默认端口
server:  
  port: 8081    
#通过active属性可以指定其它文档块的端口
spring:   
  profiles:  
    active: dev  

---
#开发环境的端口  
server:  
  port: 8082  
spring: 
  profiles: dev 

---
#生产环境的端口  
server:  
  port: 8083  
spring: 
  profiles: prod
```

### 3. 激活指定profile 
- 在配置文件`application.properties/yml`中指定需要激活的环境：  
```properties  
#通过激活属性active指定具体的环境
spring.profiles.active=dev
```  
- 以命令行的形式激活指定环境：  
  - 打成jar包后修改：`java -jar projectname-0.0.1-SNAPSHOT.jar -- spring.profiles.active=dev;`  

  - 以Program arguments的形式激活：将参数设置为`-- spring.profiles.active=dev`

- 虚拟机参数形式：和Program arguments在同一个地方，将VIM options设置为`-Dspring.profiles.active=dev`  

## 配置文件加载位置  
- 默认的主配置文件放在`src/main/resources`目录下(等同于`classpath:/`)  

- spring boot启动会扫描以为位置的application.properties/yml文件作为Spring的默认配置文件  
  - file:./config/    

  - file:./ (当前项目的的第一层子目录 和src同级)  
  - classpath:/config/  
  - classpath:/(src/main/resources)  

- 以上是按照优先级从高到低的顺序，所有位置的文件都会被加载，**高优先级配置内容会覆盖低优先级配置内容**。  

- 我们也可以通过配置`spring.config.location`来改变默认值(运维时非常方便)    
  - 只能在打包之后通过命令行的方式执行  

  - 可以和原来的配置形成互补配置  

  - 配置方式：`java -jar projectname-0.0.1-SNAPSHOT.jar -- spring.config.location=F:/application.properties;`

## 外部配置加载顺序  
SpringBoot也可以从以下位置加载配置；**优先级从高到低**；高优先级配置覆盖低优先级配置，所有配置会形成互补配置：  
1. **命令行参数**    
  - 所有的配置都可以在命令行上进行指定：`java -jar project-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc`    

  - 多个配置用空格分开，配置格式为：` --配置项=值`

2. 来自java:comp/env的JNDI属性   

3. Java系统属性（System.getProperties()）
4. 操作系统环境变量  
5. RandomValuePropertySource配置的random.*属性值   

**由jar包外向jar包内进行寻找；优先加载带proﬁle;**  

6. jar包外部的application-{proﬁle}.properties或application.yml(带spring.proﬁle)配置文件  

7. jar包内部的application-{proﬁle}.properties或application.yml(带spring.proﬁle)配置文件

**再来加载不带proﬁle**  

8. jar包外部的application.properties或application.yml(不带spring.proﬁle)配置文件  

9. jar包内部的application.properties或application.yml(不带spring.proﬁle)配置文件  

10. @Conﬁguration注解类上的@PropertySource 

11. 通过SpringApplication.setDefaultProperties指定的默认属性   

所有支持的配置加载来源：[参考官方文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-external-config)  

## 自动配置原理  
[配置文件能配置的属性参照](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#common-application-properties)    

### 自动配置原理（重要）   
- SpringBoot启动的时候加载主配置类，开启了自动配置功能 **@EnableAutoConfiguration**。   
  
- **@EnableAutoConfiguration**的作用：    
  - 利用EnableAutoConfigurationImportSelector给容器中导入一些组件  

  - 可以查看selectImports()方法的内容  

  - List<String> configurations = getCandidateConfigurations  (annotationMetadata, attributes);获取候选的配置  
    ```java  
    //扫描所有jar包类路径下  META-INF/spring.factories
    //把扫描到的这些文件的内容包装成properties对象  
    //从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中   
    //每一个这样的 xxxAutoConﬁguration类都是容器中的一个组件，都加入到容器中；用他们来做自动配置； 
    SpringFactoriesLoader.loadFactoryNames();  
    ```
    
- 每一个自动配置类进行自动配置功能；   

- 以**HttpEncodingAutoConﬁguration（Http编码自动配置）** 为例解释自动配置原理；  
```java
@Configuration   //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件。
@EnableConfigurationProperties(HttpEncodingProperties.class)  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中。
 
@ConditionalOnWebApplication //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；这里是判断当前应用是否是web应用，如果是，当前配置类生效。
 
@ConditionalOnClass(CharacterEncodingFilter.class)  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
 
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  //判断配置文件中是否存在某个配置  spring.http.encoding.enabled；如果不存在，判断也是成立的。
//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；  

public class HttpEncodingAutoConfiguration {
    //他已经和SpringBoot的配置文件映射了   
    private final HttpEncodingProperties properties;   
    //只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpEncodingProperties properties{ 
        this.properties = properties;        
    }    
   
    @Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件？    
    public CharacterEncodingFilter characterEncodingFilter() {    
    CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();        
    filter.setEncoding(this.properties.getCharset().name());        
    filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));        
    filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));        
    return filter;        
}    
// 根据当前不同的条件判断，决定这个配置类是否生效。   
// 一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；  
```

- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装；配置文件能配置什么就可以参照某个功能对应的这个属性类。     

- 总结：  
  - SpringBoot启动会加载大量的自动配置类  

  - 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；  

  - 我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）  

  - 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；  

  - xxxAutoConﬁgurartion：自动配置类；给容器中添加组件；  

  - xxxxProperties:封装配置文件中相关属性；

### 细节   
**@Conditional派生注解**   

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置类里面的所有内容才生效；   
注解|作用 
:-|:-
@Conditional扩展注解 | 判断是否满足当前指定条件 
@ConditionalOnJava | 系统的java版本是否符合要求
@ConditionalOnBean | 容器中存在指定Bean；
@ConditionalOnMissingBean | 容器中不存在指定Bean；
@ConditionalOnExpression | 满足SpEL表达式指定
@ConditionalOnClass | 系统中有指定的类
@ConditionalOnMissingClass | 系统中没有指定的类
@ConditionalOnSingleCandidate | 容器中只有一个指定的Bean，或者这个Bean是首选Bean
@ConditionalOnProperty | 系统中指定的属性是否有指定的值
@ConditionalOnResource | 类路径下是否存在指定资源文件
@ConditionalOnWebApplication | 当前是web环境
@ConditionalOnNotWebApplication | 当前不是web环境
@ConditionalOnJndi | JNDI存在指定项  

自动配置类必须在一定的条件下才能生效；我们可以通过**启用 debug=true属性**；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；  

# Spring Boot与日志    
## 日志框架     
市面上的日志框架：  
日志门面（日志的抽象层）| 日志实现  
-|- 
~~JCL(Jakarta Commons Logging)~~  **SLF4j**(Simple Logging Facade for Java)  ~~jboss-logging~~ | Log4j  JUL(java.util.logging)  Log4j2 **Logback**   

- 左边选一个门面（抽象层）、右边来选一个实现；    

- **Spring Boot 使用的是 SLF4j + Logback** ；  

- Spring使用的是JCL日志门面，所以一般在导入spring相关依赖时首先就会将JCL排除。   

- SLF4j、Log4j和Logback是同一个人编写的  

## SLF4j使用   
以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是**调用日志抽象层里面的方法**；   

**1. 简单案例**   

给系统里面导入slf4j的jar和 logback的实现jar  
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
 
public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```     
SLF4j支持的实现模式：  

![SLF4j支持的模式](images/05-SLF4j支持的模式.png)  

每一个日志的实现框架都有自己的配置文件。使用slf4j以后，配置文件还是做成日志实现框架自己本身的配置文件；

**2. 存在的问题**    

如果我们在Spring Boot中集成了很多框架，而不同框架使用了不同类型的日志模式，如何进行统一？  

SLF4j给出了以下解决方案：    

![SLF4j适配其它日志框架](images/05-SLF4j适配其它日志框架.png)    

- 将系统中其他日志框架先排除出去；  

- 用中间包来替换原有的日志框架；(实现是一样的 只是都改成使用SLF4j的接口)  

- 我们导入slf4j其他的实现
 
## Spring Boot日志间的关系      
- Spring Boot 使用`spring‐boot‐starter‐logging`启动器做日志功能   

- 底层依赖关系:    

  ![日志底层依赖关系](images/05-SpringBoot日志底层依赖关系.png)   

- SpringBoot底层也是使用slf4j+logback的方式进行日志记录  

- SpringBoot通过中间替换包的形式把其他的日志都替换成了slf4j   

- 如果我们要引入其他框架 一定要把这个框架的默认日志依赖移除掉换成中间替换包   

例如Spring默认使用的是JCL日志框架，在添加Spring依赖时要做如下配置：  
```xml   
<dependency>   
  <groupId>org.springframework</groupId>       
  <artifactId>spring‐core</artifactId>       
  <exclusions>       
    <exclusion>           
    <groupId>commons‐logging</groupId>               
    <artifactId>commons‐logging</artifactId>               
    </exclusion>           
  </exclusions>       
</dependency>   
```
**SpringBoot能自动适配所有的日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可；**  

## 日志使用  
- SpringBoot默认帮我们配置好了日志，并使用info级别：  
```java
//记录器     
Logger logger = LoggerFactory.getLogger(getClass());   
    
@Test     
public void contextLoads() {     
//System.out.println();         
 
//日志的级别由低到高   trace<debug<info<warn<error         
//可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效         
logger.trace("这是trace日志...");         
logger.debug("这是debug日志...");         
//SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别(root级别)       
logger.info("这是info日志...");         
logger.warn("这是warn日志...");         
logger.error("这是error日志...");         
}     
```
- 日志输出格式：  
```properties   
%d表示日期时间，       
%thread表示线程名，       
%‐5level：级别从左显示5个字符宽度       
%logger{50} 表示logger名字最长50个字符，否则按照句点分割。        
%msg：日志消息，       
%n是换行符    

%d{yyyy‐MM‐dd HH:mm:ss.SSS} [%thread] %‐5level %logger{50} ‐ %msg%n
```  
- SpringBoot修改日志的默认配置  
```properties
#将日志级别调至trace级别 即输出所有类型的日志 
logging.level.com.atguigu=trace
 
#logging.path=
# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file=G:/springboot.log
# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log
 
#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy‐MM‐dd} [%thread] %‐5level %logger{50} ‐ %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy‐MM‐dd} === [%thread] === %‐5level === %logger{50} ==== %msg%n
```
- `logging.file`和`logging.path`区别    

**logging.file**|**logging.path**|Description 
-|-|- 
(none)|(none)|只在控制台输出    
指定文件名|(none)|输出日志到指定文件中  
(none)|指定目录|输出日志到指定目录中  

**logging.file** 只支持输出到指定文件中，**logging.path** 只支持输出到指定目录中。

- 指定配置     

给类路径下(main资源文件夹下)放上每个日志框架自己的配置文件即可；SpringBoot就不使用他默认配置的了。 
Logging System | Customization  
-|-  
Logback|`logback-spring.xml`、`logback-spring.groovy`、`logback.xml`、`logback.groovy`  
Log4j2|`log4j2-spring.xml`、`log4j2.xml`  
JUL(Java Util Logging)|`logging.properties`  

logback.xml：直接就被日志框架识别了；  

logback-spring.xml：日志框架就不直接加载日志的配置项，由SpringBoot解析日志配置，可以使用SpringBoot的高级Proﬁle功能。（需要手动配置才会生效）  

```xml
<springProfile name="staging">
    <!‐‐ configuration to be enabled when the "staging" profile is active ‐‐>
   可以指定某段配置只在某个环境下生效   
</springProfile>
```  
如：  
```xml
<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
<!‐‐  
日志输出格式：
  %d表示日期时间，             
  %thread表示线程名，             
  %‐5level：级别从左显示5个字符宽度             
  %logger{50} 表示logger名字最长50个字符，否则按照句点分割。              
  %msg：日志消息，             
  %n是换行符             
‐‐>
  <layout class="ch.qos.logback.classic.PatternLayout">
    <!--指定开发版本使用的日志格式-->
    <springProfile name="dev">
      <pattern>%d{yyyy‐MM‐dd HH:mm:ss.SSS} ‐‐‐‐> [%thread] ‐‐‐> %‐5level 
%logger{50} ‐ %msg%n</pattern>
    </springProfile>
    <springProfile name="!dev">
      <pattern>%d{yyyy‐MM‐dd HH:mm:ss.SSS} ==== [%thread] ==== %‐5level 
%logger{50} ‐ %msg%n</pattern>
    </springProfile>
        </layout>
    </appender>
``` 
如果使用logback.xml作为日志配置文件(会自动加载 无需再配置)，还要使用proﬁle功能，会有以下错误：    

`no applicable action for [springProfile]`    

## 切换日志框架  
可以按照slf4j的日志适配图，进行相关的切换；（不推荐）   

切换为log4j2：  
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring‐boot‐starter‐web</artifactId>
  <!--1. 排除掉spring boot 默认的日志框架-->
  <exclusions>
    <exclusion>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring‐boot‐starter‐logging</artifactId>
    </exclusion>
  </exclusions>
</dependency>  

<!--2. 添加log4j2启动器--> 
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring‐boot‐starter‐log4j2</artifactId>
</dependency>
```
# Spring Boot与Web开发  


# Spring Boot与Docker  


# Spring Boot与数据库访问  

# Spring Boot启动配置原理  

# Spring Boot自定义starters