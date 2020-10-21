
# Spring框架基本
Spring是**轻量级**的**开源**的JavaEE框架。  

Spring可以解决企业应用开发的复杂性。  

## Spring有两个核心部分：**IOC** 和 **AOP** 
- IOC：控制反转，把创建对象的过程交给Spring进行管理。  

- AOP：面向切面编程，不修改源代码进行功能增强。  

## Spring特点：  
- 方便解耦，简化开发  

- AOP支持  

- 方便程序测试（JUnit） 

- 方便和其它框架进行整合

- 方便进行事务操作  

- 降低API开发难度  

# Spring核心jar包  
- Beans  

- Core 

- Context(上下文)  

- Expression(表达式)  

需要额外导入`commons-logging`日志包才能正常运行程序  

# IOC容器  
## IOC概念和原理  
### 什么是IOC 
- 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理  

- 使用IOC的目的：降低耦合度  

### IOC底层原理  
- xml解析、工厂模式、反射（把耦合度降到最低程度） 

- IOC过程：  
  - 第一步：xml配置文件，配置创建的对象  
   `<bean id="dao" class="com.fish.UserDao"></bean>`  

  - 第二步：有service类和dao类，创建工厂类。在工厂类中通过反射方式实例化dao类。（通过xml解析获得dao类的地址再调用Class类的`forName()`方法获得Class对象，Class对象通过`newInstance()`方法获得dao类对象。  

## IOC接口（BeanFactory）   
IOC思想基于**IOC容器**完成，IOC容器底层就是**对象工厂**。  

Spring提供IOC容器实现的两种方式：（两个接口）  
- `BeanFactory` ：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用。  

  **加载配置文件的时候不会创建对象，在获取（使用）对象时才去创建对象**。  

- `ApplicationContext`：`BeanFactory`接口的子接口，提供更多强大的功能，一般由开发人员使用。  
  **加载配置文件的时候就会把在配置文件中的对象进行创建。**
在服务器启动的时候就可以完成（比较耗时），避免影响用户体验。  

`ApplicationContext`接口的实现类：
- FileSystemXmlApplicationContext：到盘符路径下(绝对路径下)找配置文件 

- ClassPathXmlApplicationContext：当前工程相对路径下找配置文件  

## IOC操作Bean管理（基于XML）
### 什么是Bean管理  
- Spring创建对象（替代new） 

- Spring注入属性（替代get、set方法）  

### Spring创建对象 
- 在Spring配置文件中，使用`<bean>`标签，标签里添加对应的属性，就可以实现对象创建：    

  `<bean>`标签常用属性：    
  - id     对象别名（唯一标识） 使用`getBean()`方法的时候 通过id获取对象  
  - class  类全路径（包类路径）    
  - name   早期属性  可id作用一样 可以允许特殊符号

- 创建对象时候，默认执行无参构造方法，如果定义了有参构造方法要把无参构造方法显性表示出来，否则会报错。  

### Spring注入属性(xml) 
- DI：依赖注入，就是注入属性（在已创建对象的基础上完成）  
  第一种方式：使用`set()`方法进行注入(常用)      
    - 创建类，定义属性，并配置set方法  
    - 在spring配置文件配置对象创建，配置属性注入。（在`<bean>`标签内使用`<property name="" value=""></property>`标签  name-属性名   value-属性值）  
  
  第二种方式：使用**有参构造**注入
    - 创建类，定义属性，配置有参构造方法。  
    - 在`<bean>`标签内使用`<constructor-arg name="" value=""></constructor-arg>`标签  name-属性名   value-属性值  name可以用index代替  

  第三种方式：p名称空间注入（了解  是set方法和有参构造的简化）  
    - 添加p名称空间在配置文件中   
    ```xml 
    <beans xmlns="http://www.springframework.org/shema/beans" 
           xmlms:p="http://www.springframework.org/shema/p" >  
    ```

    - 进行属性注入，在`<bean>`标签里面进行操作  
    `<bean id="book" class="com.fish.Book" p:bname="断舍离" p:bauthor="山下英子"></bean>` p:bname  p:bauthor就是Book类的两个属性。  

  xml注入其它类型属性：  
    
    **字面量（固定值）**  
    - 空值(不指定value属性)   `<property namm="address"> <null/></property>`  

    - 属性值包含特殊符号（转义符号 | 把带特殊符号内容写到CDATA里[原样输出]）  
       输出 `<<南京>>`
       ```xml
       <property namm="address">
           <value><![CDATA[<<南京>>]]></value>
       </property>
       ```  
    - 注入属性-外部bean（对象注入 推荐使用 调用其它类的属性和方法）    
    1.创建两个类  service类和dao类  
    2.在service调用dao里面的方法  
    3.在spring进行配置    
      ```xml
      <!--在service类里创建dao类型属性 生成get方法  并配置bean--> 
      <bean id="userService" class="com.fish.UserService">
          <!--属性指定为service的dao成员并通过ref指定参考的bean-->
          <property name="userDao" ref="userDaoImpl"></property> 
      </bean>
      <bean id="userDaoImpl" class="com.fish.UserDaoImpl"></bean>
      ```

    - 注入属性-内部bean（级联表  对象注入 将对象的bean嵌套在自己bean的内部 不使用ref属性）  
    (1). 一对多关系 (部门与员工)  
      ```xml
      <bean id="empt" class="com.fish.Empt">
        <property name="ename" value="fish"></property> 
        <property name="gender" value="woman"></property> 
        <!--将部门对象通过属性的形式嵌入到bean中-->
        <property name="dept">
            <bean id="dept" class="com.fish.Dept">
                <property name="dname" value="产品部"></property> 
            </bean>
        </property> 
      </bean>

      <!--第二种写法（通过对象名.属性名的方式赋值） 此时员工类必须提供部门成员变量的get方法 否则无效-->  
      <bean id="empt" class="com.fish.Empt">
        <property name="ename" value="fish"></property> 
        <property name="gender" value="woman"></property> 
        
        <property name="dept" ref="dept"></property>
        <property name="dept.dname" value="技术部"></property>
      </bean>
      <bean id="dept" class="com.fish..Dept"></bean>
      ```
    - 注入集合属性(属性的value使用额外标签赋值)   
      ```xml
      <bean id="stu" class="com.fish.Student">
          <!-- 1.注入数组类型属性 也可以使用list --> 
          <property name="courses">
              <array>
                  <value>Java</value>
                  <value>C++</value>
              </array>
          </property> 
          <!-- 2.注入List类型属性 -->
          <property name="list">
              <list>
                  <value>Java</value>
                  <value>C++</value>
              </list>
          </property> 
          <!-- 3.注入Map类型属性 -->
          <property name="maps">
              <map>
                  <entry key="" value=""></entry>
                  <entry key="" value=""></entry>
              </map>
          </property>
          <!-- 4.注入Set类型属性 -->
          <property name="list">
              <set>
                  <value>Java</value>
                  <value>C++</value>
              </set>
          </property>  
      </bean>
      ```    
    - 注入日期类型属性  
      ```xml  
      <bean id="dateFormat" class="java.text.SimpleDateFormat">
        <constructor-arg value="yyyy-MM-dd"/>
      </bean>  
      <bean id="user" class="com.ralife.pojo.User">
        <property name="birthday">
            <bean factory-bean="dateFormat" factory-method="parse">
                <constructor-arg value="2020-1-3"></constructor-arg>
            </bean>
        </property>
      </bean>
      ```
    - 注入集合对象属性（学生学习多门课程  问题是要嵌入大量数据库中的课程咋整？）  

      ```xml
      <!--1.在xml中定义多个课程bean 并注入属性值--> 
      <bean id="course1" class="com.fish.Course">
          <property name="cname" value="Java API"></property> 
      </bean>
      <bean id="course2" class="com.fish.Course">
          <property name="cname" value="JUnit"></property> 
      </bean>
      <!--2.在list标签中嵌入<ref>标签 --> 
      <bean id="stu" class="com.fish.Student">
          <property name="courseList">
              <list>
                  <ref bean="course1"></ref>
                  <ref bean="course2"></ref>
              </list>
          </property>  
      </bean>
      ```

    - 将集合属性设置为公用部分    
（1）在Spring配置文件中引入名称空间 util  
（2）使用util标签完成list集合注入提取（util:list  util:set  util:map都是一样的）   
       ```xml
      <!--1.提取list集合类型属性注入--> 
      <util:list id="bookList">
          <value>Spring</value>
          <value>Java</value>
      </util:list>
      <!--2.bean内提取list集合类型属性注入使用 -->
      <bean id="book" class="com.fish.Book">
          <property name="list" ref="bookList"></property> 
      </bean>
      ```    
## IOC操作Bean管理（FactoryBean）   
Spring有两种类型bean，一种普通bean，另外一种工厂bean（FactoryBean）。  

**普通bean：** 在配置文件中定义bean类型就是返回类型(`<bean>`标签的class指定的定义什么类型就返回什么类型)   

**工厂Bean：** 在配置文件定义bean类型可以和返回类型不一样  
第一步：创建类，让这个类作为工厂bean，实现FactoryBean接口。  

第二步：在实现接口方法时根据需求修改返回类型     
## IOC操作Bean的创建方式  
- 通过无参构造函数创建(id class 如果没有无参构造函数则会创建失败)
```xml
<bean id="jdbcTemplete" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```
- 通过工厂的普通方法创建(id factory-bean factory-method)
```java
public class BeanFactory {
    public IUserDao getUserDaoImplBean(){
        IUserDao dao = new UserDaoImpl();
        return dao;
    }
}
```
```xml  
<bean id="beanFactory" class="com.ralife.factory.BeanFactory"></bean>
<bean id="iUserDao" factory-bean="beanFactory" factory-method="getUserDaoImplBean"></bean>
```
- 通过工厂中的静态方法创建(id class factory-method)
```java
public class BeanFactory {
    //静态方法将对象加入到容器中
    public static IUserDao getUserDaoImpl(){
        IUserDao dao = new UserDaoImpl();
        return dao;
    }
}
```
```xml
<bean id="userStaticDao" class="com.ralife.factory.BeanFactory" factory-method="getUserDaoImpl"></bean>
```
## IOC操作Bean管理（bean的作用域）
- 在Spring里面，可以设置bean实例是单实例还是多实例。  

- 默认情况下，bean是一个单实例对象。  

- 通过`<bean>`标签的`scope`属性可以指定bean的作用域。`scope`属性常用值：  
  - **singleton** ：默认值  单实例  加载spring配置文件时候就会创建单实例对象

  - **prototype** ：多实例  在调用`getBean()`方法的时候才创建多实例对象  

  - request 相当于web的request

  - session 相当于web的session

## IOC操作Bean管理（bean的生命周期）  
bean的生命周期是指从创建bean到销毁bean的整个过程。  
1. 通过构造器创建bean实例（无参数构造 `<bean>`标签指定id和class属性 ）  

2. 为bean的属性设置值和对其他bean引用（调用set方法）  

   把bean实例传递bean后置处理器的方法（`postProcessBeforeInitialization()`） 

3. 调用bean的初始化的方法（使用`@PostConstruct`注解标识  需要进行配置初始化方法）   
  在类里面写好初始化方法 然后在配置文件中为`<bean>`标签添加`init-method`属性指定值为初始化方法名   

  把bean实例传递bean后置处理器的方法（`postProcessAfterInitialization()`）

4. bean可以使用了（对象获取到了）  

5. 当容器关闭的时候，调用bean的销毁的方法（使用`@PreDestory`注解标识  需要进行配置销毁的方法）  
  在类里面写销毁方法 然后在配置文件中为`<bean>`标签添加`destroy-method`属性指定值为销毁方法名   

6. 在bean初始化之前和之后包含bean的后置处理器，执行步骤为：  
  - 创建类，实现`BeanPostProcessor`接口，创建后置处理器  实现接口的两个方法（只要实现了这个接口就会当做后置处理器执行）

  - 配置文件中对后置处理器进行配置       

## IOC操作Bean管理（xml自动装配）  
通过在`<bean>`标签内使用`<property>`标签设置属性值为xml手动装配。  

**自动装配：** 根据指定装配规则（属性名称/属性类型），Spring自动将匹配的属性值进行注入。

通过bean标签`autowire`属性配置自动装配，常用的值有：  
- **byName** ：根据属性名称进行注入   **注入值bean的id值和类的属性名称一样**  
   ```xml
      <bean id="emp" class="com.fish.Emp" autowire="byName">
          <!--通过autowire属性替代property-->
          <!--<property name="userDao" ref="userDaoImpl"></property>--> 
      </bean>
       <!--如果使用byName Dept bean的id必须和Emp里Dept类型属性名保持一致-->
      <bean id="dept" class="com.fish.Dept"></bean>
      ```
- **byType** ：根据属性类型进行注入，**配置文件中相同类的bean不能定义多个**，不然会找不到要使用哪个bean。  

   ```xml
      <bean id="emp" class="com.fish.Emp" autowire="byType">
          <!--通过autowire属性替代property-->
          <!--<property name="userDao" ref="userDaoImpl"></property>--> 
      </bean>
       <!--如果使用byName Dept bean的id必须和Emp里Dept类型属性名保持一致-->
      <bean id="dept" class="com.fish.Dept"></bean>
      ```

## IOC操作Bean管理（引入外部属性文件  以数据库为例）    
- 直接配置数据库信息 (不能复用)    
  （1）配置连接池  
  （2）引入连接池依赖jar包     
  ```xml  
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">  
      <property name="driverClassName" value="com.mysql.jdbc.Driver"></property> 
      <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property> 
      <property name="username" value="root"></property> 
      <property name="password" value="123456"></property> 
  </bean>   
  ```   

- 引入外部属性文件配置数据库连接池  
  （1）创建外部属性文件，properties格式文件，写数据库信息(jdbc.properties)   
   ```properties  
  prop.driverClass=com.mysql.jdbc.Driver  
  prop.url=jdbc:mysql://localhost:3306/userDb  
  prop.userName=root  
  prop.password=123456
  ```   
  （2）把外部properties属性文件引入到spring配置文件中  
      引入context名称空间  
    `<context:property-placeholder location="classpath:jdbc.properties">`  

  （3）在bean中通过`${}`获取属性文件中的值  
    ```xml  
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">  
      <property name="driverClassName" value="${prop.driverClass}"></property> 
      <property name="url" value="${prop.url}"></property> 
      <property name="username" value="${prop.userName}"></property> 
      <property name="password" value="${prop.password}"></property> 
  </bean>   
  ```  

## IOC操作Bean管理（基于注解）  
- 注解基本概念：注解是**代码特殊标记**，格式：@注解名称(属性名称=属性值,属性名称=属性值...)  

- 注解可以作用在类、方法、属性上  

- 使用注解目的：简化xml配置  

### 创建对象   
Spring针对Bean管理中创建对象提供注解：  

- `@Component`（普通组件）  

- `@Service`（业务逻辑层/service层）  

- `Controller`（Web层）

- `Repository`（Dao层）  

上面四个注解功能是一样的，都可以用来创建bean实例  

基于注解方式实现对象创建：  

- 1.引入依赖(`spring-aop`)  

- 2.开启组件扫描（告诉spring哪些类需要注解  需要使用context名称空间）    
```xml    
<!--  
  1.如果扫描多个包，多个包之间用逗号隔开或直接扫描包的上层目录  
-->
<context:component-scan base-package="com.fish"></context:component-scan>
```  
- 3.创建类，在类上面添加创建对象注解  
```java    
//在注解里面value属性值可以省略不写，默认值是类名称，首字母小写  
@Component(value="userService") //相当于<bean id="userService" class="...">  
public class UserService{
  public void add(){
    System.out.println("service add .....");
  }
}
``` 
- 4.开启组件扫描细节问题  
```xml    
<!-- 示例1（设置哪些内容需要扫描）
  如果不希望扫描包中所有注解 可自定义规则  
  user-default-filters="false"  表示现在不使用默认filter，自己配置filter  
  context:include-filter     设置扫描哪些内容  
-->
<context:component-scan base-package="com.fish" user-default-filters="false">
    <context:include-filter type="annotation"  expression="org.springframework.stereotype.Controller"/>
</context:component-scan>  

<!-- 示例2（设置哪些内容不需要扫描）
  context:exclude-filter     设置不扫描哪些内容  
-->
<context:component-scan base-package="com.fish">
    <context:exclude-filter type="annotation"  expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```  

### 注入属性   
实现步骤：   

第一步：把service和dao对象创建，在service和dao类添加创建对象注解  

第二步：在service注入dao对象，在service类添加dao类型属性，在属性上使用注解（不需要再提供set方法）    

- `@AutoWired` ：根据属性类型进行自动装配
```java  
@Service //相当于<bean id="userService" class="...">  
public class UserService{  

  @Autowired
  private UserDao userDao;  

  public void add(){
    System.out.println("service add .....");
    userDao.add();
  }
}
```  

- `@Qualifier` ：根据属性名称进行注入（如果dao类存在多个对象 就可以通过名称进行注入）    

**加在属性上时必须和`@AutoWired`搭配使用**，只有加在方法参数上时才能独立使用。  
```java  
@Service(value="userService")  //相当于<bean id="userService" class="...">  
public class UserService{  

  //1.加在属性上 必须和@Autowired搭配使用
  @Autowired
  @Qualifier(value="userDaoImpl")
  private UserDao userDao;  

  public void add(){
    System.out.println("service add .....");
    userDao.add();
  }

  //2.作为方法参数名标识时 可单独使用   
  @Bean(name = "jdbcTemplate")
  @Scope("protoType")
  public JdbcTemplate createJdbcTemplete(@Qualifier("dataSource1") DruidDataSource dataSource){
      JdbcTemplate jdbcTemplate = new JdbcTemplate();
      jdbcTemplate.setDataSource(dataSource);
      return jdbcTemplate;
  }

  @Bean(name = "dataSource1")
  public DruidDataSource createDuruidDataSource1(){
      DruidDataSource dataSource = new DruidDataSource();
      dataSource.setDriverClassName(driverClassName);
      dataSource.setUrl(url);
      dataSource.setUsername(username);
      dataSource.setPassword(password);
      return dataSource;
  }

  @Bean(name = "dataSource2")
  public DruidDataSource createDuruidDataSource2(){
      DruidDataSource dataSource = new DruidDataSource();
      dataSource.setDriverClassName(driverClassName);
      dataSource.setUrl(url);
      dataSource.setUsername(username);
      dataSource.setPassword(password);
      return dataSource;
  }
}
  ```
- `@Resource` ：可以根据类型注入，也可以根据名称注入(不推荐使用)  

  `@Resource`：根据类型注入   `@Resource(name="对象名")` ：根据名称注入  

- `@Value` ：注入普通类型属性  

### 完全注解开发（springboot）  
- 1.创建配置类，替代xml配置文件  
```java  
@Configuration  //作为配置类，替代xml配置文件    
@ComponentScan(basePackages={"com.fish"})  //开启组件扫描 
public class SpringConfig{  
}
  ```  
**当配置类作为`AnnotationConfigApplicationContext`对象创建的参数时，`@Configuration`可以不写；** 如果没有则必须在配置类上加`@Configuration`，同时在主配置类的`@ComponentScan`中加入该配置类的路径。(或者在主配置类中加上`@Import(配置类名.class)`注解)
- 2.编写测试类  
```java  
@Test
public void testService(){    
  //使用AnnotationConfigApplicationContext（注解配置上下文）获取上下文
  //此时SpringConfig类上可以不加@Configuration注解
  ApplicationContext context =   
    new AnnotationConfigApplicationContext(SpringConfig.class);
    UserService userService = context.getBean("userService",UserService.class);  
    userService.add();
}
  ```     
- 3.注解方式加载配置文件(数据库配置文件)  
```xml
<!--jdbc.properties-->
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/small?useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=123456
```
```java  
//主配置类
@Configuration
@ComponentScan("com.ralife")//开启组件扫描
@Import(JdbcConfig.class)//导入子配置类
@PropertySource("classpath:jdbc.properties")//加载外部配置文件
public class SpringConfiguration {
}
//jdbc配置类
public class JdbcConfig {
    @Value("${jdbc.driverClassName}")
    private String driverClassName;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean(name = "jdbcTemplate")
    @Scope("protoType")
    public JdbcTemplate createJdbcTemplete(@Qualifier("dataSource") DruidDataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    @Bean(name = "dataSource")
    public DruidDataSource createDuruidDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```
# AOP(面向切面编程)  
## 概念  
不通过修改源代码方式，在主干功能添加新功能。（通常用于提取项目中的重复代码）  

## 底层原理  
AOP底层使用动态代理：  
- 第一种 有接口情况，使用JDK动态代理（使用Proxy类的newProxyInstance(当前类加载器, 要进行功能增强的接口数组, 功能增强的实现类[需要实现InvocationHandler接口])  方法进行增强）   
创建**接口实现类代理对象**，在代理类里实现功能增强 
```java  
//需要代理的接口
public interface IProduct {
    /**
     * 销售
     * @param money
     */
    void saleProduct(Double money);

    /**
     * 售后
     * @return
     */
    Integer afterSale();
}
//需要代理的接口实现类
public class Product implements IProduct{
    public void saleProduct(Double money) {
        System.out.println("生产者销售所得："+money);
    }

    public Integer afterSale() {
        return 1;
    }
}
/**
 * 模拟一个消费者
 */
public class Client {
    public static void main(String[] args) {
        Double money = 10000.0;
        final Product product = new Product();
        /**
         * 动态代理：
         *   特点：字节码随用随创建  随用随加载
         *   作用：不修改源码的基础上进行方法的增强
         *   分类：
         *     基于接口的动态代理（JDK动态代理  使用JDK官方提供的Proxy类）
         *     基于子类的动态代理（CGLIB动态代理  使用第三方jar包）
         *   基于接口的动态代理：
         *     使用Proxy类中的newProxyInstance方法
         *     要求：被代理类至少实现一个接口，如果没有则不能使用
         *     newProxyInstance的参数：
         *       ClassLoader：用于加载代理对象字节码，写的是被代理对象的类加载器；固定写法。
         *       Class<?>[] interfaces：用于让代理对象(接口)和被代理对象(接口实现类)有相同的方法；写的是要进行功能增强的接口数组；固定写法。
         *       InvocationHandler：功能增强的实现类[需要实现InvocationHandler接口 一般都是匿名内部类]
         */
        IProduct proxyInstanceProduct = (IProduct)Proxy.newProxyInstance(product.getClass().getClassLoader(), product.getClass().getInterfaces(), new InvocationHandler() {
            /**
             * 执行被代理对象的任何接口方法都会经过该方法
             * @param proxy   代理对象的引用
             * @param method  当前执行的方法
             * @param args    当前执行方法所需的参数
             * @return        和被代理对象方法有相同的返回值
             * @throws Throwable
             */
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                Object returnValue = null;
                if(method.getName()=="saleProduct"){
                    //执行被代理对象方法
                    Double money = (Double)args[0];
                    returnValue = method.invoke(product,args);
                    //进行方法增强
                    returnValue=money*0.8;
                    System.out.println("代理商分成后所得："+returnValue);
                }
                return returnValue;
            }
        });
        enhancerProduct.saleProduct(10000.0);
    }
}
```
- 第二种 无接口情况，使用CGLIB动态代理  
创建当前类子类的代理对象，子类重写（类似于装饰模式）  
```java
//被代理类
public class Product {
    public void saleProduct(Double money) {
        System.out.println("生产者销售所得："+money);
    }

    public void  afterSale() {
        System.out.println("售后");
    }
}
//代理类
public class Client {
    public static void main(String[] args) {
        final Product product = new Product();
        /**
         * 基于子类的动态代理(CGLIB)：
         *   涉及的类：Enhancer（第三方cglib库）
         *   使用Enhancer类的create()方法
         *   创建代理对象的要求：
         *     被代理类不能是最终类（可以被继承）
         *   create()的参数：
         *     Class：指定被代理对象的字节码；固定写法。
         *     Callback：功能增强的实现类[需要实现Callback子接口MethodInterceptor]
         */
        Enhancer.create(product.getClass(), new MethodInterceptor() {
            /**
             * 执行被代理对象的任何方法都会经过该方法
             * @param proxy   被代理对象
             * @param method  当前执行的方法
             * @param args    当前方法所需的参数
             * @param methodProxy  当前执行方法的代理对象
             * @return  和被代理对象方法的返回值相同
             * @throws Throwable
             */
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                //执行被代理对象方法
                Double returnValue = (Double) method.invoke(product,args);
                //进行方法增强
                returnValue*=0.8;
                return returnValue;
            }
        });
    }
}
```
## AOP术语  
- 连接点：类里面可以增强的方法都是连接点。 

- 切入点：实际被真正增强的方法，称为切入点。 

- 通知（增强）：实际增强的逻辑部分称为通知（增强）。比如登录里的权限判断功能。  
  通知有多种类型：  
  1.前置通知（`@Before`）切入点方法之前执行   
  2.后置通知（`@AfterReturning`）切入点方法之后执行 遇到异常不会执行  
  3.环绕通知（`@Around`）切入点方法之前和之后都会执行 (JDK动态代理的整个invoke方法  有明确的切入点方法调用)  
  4.异常通知（`@AfterThrowing`）只有在切点方法中出现异常才会执行   
  5.最终通知（类似finally）（`@After`）有异常也会执行

- 切面：是一个动作，把通知应用到切入点的过程。  

## AOP操作
- Spring框架一般都是基于`AspectJ`实现AOP操作。   

- `AspectJ`不是Spring组成部分，属于独立的AOP框架，一般将`AspectJ`和Spring框架一起使用进行AOP操作。  

- 基于`AspectJ`实现AOP操作方式：   
   
（1）基于xml配置文件实现（配置方式实现方法增强）  

第一步：引入相关依赖  
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring-version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>${spring-version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>${spring-version}</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.6</version>
    </dependency>
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>3.3.0</version>
    </dependency>
    <dependency>
        <groupId>aopalliance</groupId>
        <artifactId>aopalliance</artifactId>
        <version>1.0</version>
    </dependency>
</dependencies>
```
第二步：编写业务代码  

第三步：配置XML
  ```xml
  <!--spring中基于XML的AOP配置步骤
        1、把通知bean加入到spring容器中
        2、引入aop名称空间，使用aop:config标签表明开始aop配置
        3、使用aop:aspect标签表明配置切面
            id属性：切面唯一标识
            ref属性：指定通知类bean的Id
        4、使用aop:aspect内部标签来指定具体的通知类型
            aop:before:配置前置通知
              method属性：指定通知类中具体的通知方法作为前置通知
              pointcut属性：指定切入点表达式（对哪些业务层的方法进行增强）
        5、切入点表达式的写法：
          关键字：excution(表达式)
          表达式：访问修饰符  返回值  包名.包名.包名...类名.方法名(参数列表)
          标准的表达式写法：
              public void com.ralife.service.impl.AccountServiceImpl.saveAccount(com.ralife.entity.Account)
          访问修饰符可以省略
          返回值可以使用通配符 * 表示任意返回值 * com.ralife.service.impl.AccountServiceImpl.saveAccount(com.ralife.entity.Account)
          包名可以使用通配符 * 表示任意包 但是有几级包就必须写几个*   * *.*.*.*.AccountServiceImpl.saveAccount(com.ralife.entity.Account)
          包名可以使用..表示当前包及其子包  * *..AccountServiceImpl.saveAccount(com.ralife.entity.Account)
          类名和方法名都可以使用*来实现通配 * *..*.*(com.ralife.entity.Account)
          参数列表：
              可以直接写数据类型：
                  基本类型直接写名称 int
                  引用类型写包名.类名的方式
              使用通配符 * 表示任意类型，但必须有参数  * *..*.*(*)
              使用..表示有无参数均可，有参数是任意类型  * *..*.*(..)
          全通配写法：
              * *..*.*(..)
          推荐写法：切到业务层实现类下的所有方法
              * com.ralife.service.impl.*.*(..)
    -->

    <!--将业务层加入到spring容器中-->
    <bean id="accountService" class="com.ralife.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"></property>
    </bean>

    <!--将日志通知类加入到spring容器中-->
    <bean id="logger" class="com.ralife.logger.Logger"></bean>

    <!--在业务代码层中实现aop-->
    <aop:config>
        <!--切入点写在aop:aspect标签外部，表示所有切面共享-->
    <!--<aop:pointcut id="point" expression="execution(* com.ralife.service.impl.*.*(..))"/>-->
        <!--配置切面(将通知应用到切入点)-->
        <aop:aspect id="logAspect" ref="logger">
            <!--可以将切入点单独提取出来配置
                写在aop:aspect标签内部：表示只有当前切面（通知类）有效
                写在aop:aspect标签外部：表示所有切面（通知类）有效 此时按约束必须写在aop:aspect标签之前
            -->
            <aop:pointcut id="point" expression="execution(* com.ralife.service.impl.*.*(..))"/>
            <!--配置通知类型，并建立通知方法和切入点方法的关联 使用pointcut配置表达式太麻烦 可以改为pointcut-ref-->
            <!--<aop:before method="printBeforeLogger" pointcut="execution(* com.ralife.service.impl.*.*(..))"></aop:before>-->
            <aop:before method="printBeforeLogger" pointcut-ref="point"></aop:before>
            <aop:after-returning method="printAfterReturningLogger" pointcut-ref="point"></aop:after-returning>
            <aop:after-throwing method="printAfterThrowingLogger" pointcut-ref="point"></aop:after-throwing>
            <aop:after method="printAfterLogger" pointcut-ref="point"></aop:after>
        </aop:aspect>
    </aop:config>
  ```
  当我们配置了环绕通知后，切入点方法(service内中的方法)没有执行，而通知方法执行了。（动态代理的环绕通知有明确的切入点方法调用，而我们代码中没有[指定的所有方法都是切入点方法]）  

  解决办法：  
  - 使用ProceedingJoinPoint接口的proceed()方法（明确调用切入点方法）  

  - 该接口可作为环绕通知的方法参数，在程序执行时，spring框架会为我们提供该接口的实现类供我们使用。  

  环绕通知是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式。（编码方式实现方法增强）
  ```java
  /**
     * 环绕通知----通过编码方式实现方法增强
     * @param proceedingJoinPoint
     * @return
     */
    public Object printAroundLogger(ProceedingJoinPoint proceedingJoinPoint){
        Object returnValue = null;
        try {
            Object[] args = proceedingJoinPoint.getArgs();
            System.out.println("Before-方法执行前打印日志");

            returnValue = proceedingJoinPoint.proceed(args);

            System.out.println("AfterReturning-方法执行后打印日志");
        } catch (Throwable throwable) {
            System.out.println("AfterThrowing-方法执行异常打印日志");
            throwable.printStackTrace();
        }finally {
            System.out.println("After-方法执行最终打印日志");
        }
        return returnValue;
    }
  ```
（2）基于注解方式实现（常用）  
部分注解：
```xml
<!--1.开启组件扫描-->
<context:component-scan base-package="com.ralife"></context:component-scan>
<!--2.开启aop-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```  
全注解：
```java
@Configuration
@ComponentScan(basePackages = "com.ralife")
@EnableAspectJAutoProxy
public class SpringConfig {
}

@Component(value = "logger")
@Aspect//表示当前类是一个切面类
public class Logger {
    @Pointcut("execution(* com.ralife.service.impl.*.*(..))")
    private void point(){}

    @Before(value = "point()")
    public void printBeforeLogger() {
        System.out.println("Before-方法执行前打印日志");
    }
    @AfterReturning(value = "point()")
    public void printAfterReturningLogger() {
        System.out.println("AfterReturning-方法执行后打印日志");
    }
    @AfterThrowing(value = "point()")
    public void printAfterThrowingLogger() {
        System.out.println("AfterThrowing-方法执行异常打印日志");
    }
    @After(value = "point()")
    public void printAfterLogger() {
        System.out.println("After-方法执行最终打印日志");
    }

    /**
     * 环绕通知----通过编码方式实现方法增强
     * @param proceedingJoinPoint
     * @return
     */
    @Around(value = "point()")
    public Object printAroundLogger(ProceedingJoinPoint proceedingJoinPoint){
        Object returnValue = null;
        try {
            Object[] args = proceedingJoinPoint.getArgs();
            System.out.println("Before-方法执行前打印日志");

            returnValue = proceedingJoinPoint.proceed(args);

            System.out.println("AfterReturning-方法执行后打印日志");
        } catch (Throwable throwable) {
            System.out.println("AfterThrowing-方法执行异常打印日志");
            throwable.printStackTrace();
        }finally {
            System.out.println("After-方法执行最终打印日志");
        }
        return returnValue;
    }
}
```
- 实现步骤：  
  1.在项目工程里面引入AOP相关依赖  
  2.切入点表达式  
  作用：让 `AspectJ`知道要增强哪个类里面的哪些方法。   

    语法结构：  
    `execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))`  

## AOP操作（`AspectJ`注解 最常用）
1. 创建类，在类里面定义方法  

2. 创建增强类（编写增强逻辑）  
在增强类里面创建方法，让不同方法代表不同通知类型  

3. 进行通知配置  
  - 在spring配置文件中，开启注解扫描(使用context名称空间)  

  - 使用注解创建User和UserProxy对象  

  - 在增强类上面添加注解`@Aspect`  

  - 在spring配置文件中开启生成代理对象(使用aop名称空间)   

4. 配置不同类型的通知  
  - 在增强类里面作为通知的方法上面添加通知类型注解，使用切入点表达式配置内容。    

5. **相同切入点抽取**(使用`@Pointcut`注解) 改动时只需要改一个  

6. 有多个增强类对同一方法进行增强，设置增强类优先级  
  - 在增强类上面添加`@Order(数字类型值)`注解，数字类型值越小优先级越高。  

7. 完全使用注解开发（使用配置类）     

### Spring基于AOP事务控制案例（转账）  

# JDBCTemplate  
## 定义  
- Spring框架对JDBC进行了封装，使用JDBCTemplate方便实现对数据库操作。  

## 准备工作  
- 引入依赖  
```xml
<properties>
    <spring-version>5.2.9.RELEASE</spring-version>
</properties>
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.49</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.2.1</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>${spring-version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>${spring-version}</version>
    </dependency>
    <!--整合其它数据库相关框架[mybatis...]所需要的jar包-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>${spring-version}</version>
    </dependency>
</dependencies>
```
- 在spring配置文件配置数据库连接池(加载外部配置文件方法)   
```properties
# jdbcConfig.properties
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306//small?useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=123456
```
```xml
<!--1.开启加载外部配置文件-->
<context:property-placeholder location="classpath:jdbcConfig.properties"></context:property-placeholder>
<!--2.配置数据源  这里使用alibaba德鲁伊数据源-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```
- 配置JdbcTemplate对象，注入DataSource   
```xml
<!--3.创建jdbcTemplete对象-->
<bean id="jdbcTemplete" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```
- 创建service类，创建dao类，在dao注入jdbcTemplate对象（配置文件中开启组件扫描）   
```xml
<!--4.将持久层加入到spring容器中-->
<bean id="userDao" class="com.ralife.dao.impl.UserDaoImpl">
    <property name="jdbcTemplate" ref="jdbcTemplete"></property>
</bean>
<!--5.将业务层加入到spring容器中-->
<bean id="userService" class="com.ralife.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```   
## JdbcTemplete 操作数据库
```java
public class UserDaoImpl implements IUserDao {
    private JdbcTemplate jdbcTemplate;

    //查询所有  jdbcTemplate.query(sql,RowMapper)
    public List<User> findAll() {
        List<User> users = jdbcTemplate.query("selct * from user",new BeanPropertyRowMapper<User>(User.class));
        return users;
    }

    //查询单个  jdbcTemplate.queryForObject(sql,RowMapper,Object ..args)
    public User findById(Integer id) {
        User user = jdbcTemplate.queryForObject("select * from user where id=?",
                new BeanPropertyRowMapper<User>(User.class),id);
        return user;
    }

    //查询总记录条数  jdbcTemplate.queryForObject(sql,Class<T>returnType)
    public Integer findTotal() {
        Integer total = jdbcTemplate.queryForObject("select count(*) from user",Integer.class);
        return total;
    }

    //模糊查询  jdbcTemplate.query(sql,Object[] args,RowMapper)
    public List<User> findByName(String username) {
        Object[] args = {username};
        List<User> users = jdbcTemplate.query("select * from user where username like ?",
                args,new BeanPropertyRowMapper<User>(User.class));
        return users;
    }

    //添加用户  jdbcTemplate.update(sql,Object ..args)
    public void addUser(User user) {
        jdbcTemplate.update("insert into user(username,sex,address,birthday) values (?,?,?,?)",
                user.getUsername(),user.getSex(),user.getAddress(),user.getBirthday());
    }

    //删除用户  jdbcTemplate.update(sql,Object ..args)
    public void deleteUser(Integer id) {
        jdbcTemplate.update("delete from user where id = ?",id);
    }

    //更新用户  jdbcTemplate.update(sql,Object ..args)  可变参可以用数组代替
    public void updateUser(User user) {
        jdbcTemplate.update("update user set username=?,sex=?,address=?,birthday=? where id =?",
                user.getUsername(),user.getSex(),user.getAddress(),user.getBirthday(),user.getId());
    }

    //批量操作(增、删、改都一样)  jdbcTemplate.batchUpdate(sql,List<Object[]>)
    public void batchUpdateUser(List<Object[]> objs) {
        String sql = "insert into user(username,sex,address,birthday) values (?,?,?,?)";
        jdbcTemplate.batchUpdate(sql,objs);
    }
    //一对多  多表查询  jdbcTemplate.query(sql,RowMapper) 需要定义pojo类封装查询结果 比较麻烦
    public List<UserAccount> findAllAccount() {
        String sql = "select u.*,a.id as aid,a.money,a.uid from user u,account a where u.id = a.uid";
        List<UserAccount> list = jdbcTemplate.query(sql,new BeanPropertyRowMapper<UserAccount>(UserAccount.class));
        return list;
    }

    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
}
```
# Spring事务管理  
## 概念 
事务是数据库操作最基本的单元，逻辑上的一组操作，要么都成功，如果有一个失败所有操作都失败。  

## 事务的四个特性（ACID）
- 原子性(atomicity) ：不可分割，要么都成功，要么都失败。（线程安全）

- 一致性(consistency) ：使一个一致性状态变成另一个一致性状态。（用户下单和商品库存）  

- 隔离性(isolation) ：一个事务不能被另一个事务干扰。（几个用户对同一商品进行下单）  

- 持久性(durability) ：事务一旦提交，对数据库的改变是永久的。  

## Spring事务操作   
### 事务操作步骤（编程式）
- 业务逻辑层(Service层)添加事务  

- 进行事务操作  

- 没有发生异常，提交事务 

- 发生异常，回滚事务    

### 环境搭建（转账业务）  
- 创建数据库 添加用户表和账户表  

- 新建maven项目 添加相关依赖  
```xml  
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.1</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>${spring-version}</version>
</dependency>
```  
- dao层添加转入和转出操作  
```java
//转入
public void addMoney(Integer id, Double money) {
    String sql = "update account set money=money+? where id=?";
    jdbcTemplate.update(sql,money,id);
}

//转出
public void reduceMoney(Integer id, Double money) {
    String sql = "update account set money=money-? where id=?";
    jdbcTemplate.update(sql,money,id);
}
```
- service层调用dao的两个方法
```java
@Transactional(propagation = Propagation.REQUIRED)
    public void transfer(Integer source, Integer target, Double money) {
        //1.转出
        accountDao.reduceMoney(source,money);
        int i = 10/0;
        //2.转入
        accountDao.addMoney(target,money);
        System.out.println("transfer success!");
    }
}
```
### Spring 事务管理的方式  
- 编程式  

- 声明式(注解、XML  底层使用AOP原理  最常用) 
### Spring 事务管理API
Spring提供一个接口`PlatformTransactionManager`，代表事务管理器，这个接口针对不同框架提供不同的实现类。


## Spring 基于注解声明式事务管理
- 第一步：创建事务管理器，注入数据源
```xml
<bean id="transactionManage" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```
- 第二步：spring配置文件引入tx名称空间  

- 第三步：开启事务注解`<tx:annotation-driven transaction-manager="transactionManage"></tx:annotation-driven>`  

- 第四步：在service类上添加事务注解`@Transactional`  
  
  **在类上加事务注解，类中所有方法都将添加事务，在方法上加，只有当前方法添加事务，且会覆盖类上的事务。**   
### 事务注解常用属性  
- propagation：事务传播行为（7种）  

- isolation：事务隔离级别(默认使用数据的隔离级别)  

- timeout：超时回滚(默认为-1，不设置超时回滚 以秒为单位 超过指定时间就回滚)  

- readOnly：是否只读(默认为false，如果指定为true则只能用于查询)  

- rollbackFor：回滚（设置出现哪些异常时回滚事务）

- noRollbackFor：不回滚（设置出现哪些异常时不回滚事务）

### 事务不隔离存在的问题  
- **脏读**：一个未提交事务读取另外一个未提交事务的数据。    

- **不可重复读**：同一个事务内，两次相同的查询返回了不同的结果。（一个未提交事务读取到另一个提交事务修改的数据。）(事务2在事务1第二次读取时，提交了数据。导致事务1前后两次读取的数据不一致。)  

- **幻读(虚读)**：一个未提交的事务读取到另一个提交事务添加的数据。（事务1第二次查询时，读到了事务2提交的数据。）  

- 不可重复读和幻读的区别：前者针对的是**值**的不同，后者指的是**总记录条数**的不同。
### 事务的隔离级别    
- ISOLATION_DEFAULT ：使用数据库默认级别(mysql为repentable_read , oracle为read_uncommitted)  
- ISOLATION_READ_UNCOMMITTED ：最低级别，可读没提交的事务。(脏读、不可重复读、幻读都可能发生)  
- ISOLATION_READ_COMMITTED ：允许读取并发事务已提交的数据。（不可重复读、幻读都可能发生）
- ISOLATION_REPENTABLE_READ ：对同一字段的多次读取结果一致。（幻读）
- ISOLATION_SERIALIZABLE：序列化执行，完全服从ACID的隔离级别。

### 事务传播行为
有事务的方法调用没事务的方法或者没事务的方法调用有事务的方法或者有事务的方法调用有事务的方法时该怎么处理。  

- 支持当前事务  
  - propagation_**required** 默认值；存在事务，加入当前事务；不存在，创建新事物。  
  - propagation_**supports** 存在事务，加入当前事务；不存在，非事务方式继续运行。
  - propagation_**mandatory** 存在事务，加入当前事务；不存在，抛出异常。

- 不支持当前事务  
  - propagation_**requires_new** 创建新事务；当前存在事务，将当前事务挂起。
  - propagation_**not_supported** 以非事务方式运行；当前存在事务，将当前事务挂起。
  - propagation_**never** 以非事务方式运行；当前存在事务，抛出异常。 

- 其它情况 propagation_**nested**   
  - 当前存在事务，创建一个新事务作为当前事务的嵌套事务来运行；
  - 当前不存在事务，创建一个新事务。  
  - 外层事务抛出异常回滚，那么内层事务必须回滚，反之内层事务并不影响外层事务。
 
## Spring 基于XML配置文件声明式事务管理
```xml
<!--第一步：创建事务管理器，注入数据源-->
<bean id="transactionManage" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!--第二步：配置通知-->
<tx:advice id="txAdvice">
    <tx:attributes>
        <!--指定哪种规则的方法上面添加事务  可以用通配符*表示以什么开头的所有方法-->
        <tx:method name="transfer" isolation="SERIALIZABLE"/>
        <!--<tx:method name="transfer*"></tx:method>-->
    </tx:attributes>
</tx:advice>
<!--第三步：配置切入点及切面-->
<aop:config>
    <!--配置切入点-->
    <aop:pointcut id="servicePoint" expression="execution(* com.ralife.service.impl.*.*(..))"/>
    <!--配置切面  将通知应用到切入点中-->
    <aop:advisor advice-ref="txAdvice" pointcut-ref="servicePoint"></aop:advisor>
</aop:config>
```
## Spring 基于完全注解声明式事务管理
```java
//JdbcConfig.java
public class JdbcConfig {
    @Value("${jdbc.driverClassName}")
    private String driverClassName;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean("jdbcTemplete")
    public JdbcTemplate getJdbcTemplete(DruidDataSource dataSource){
        return new JdbcTemplate(dataSource);
    }

    @Bean("dataSource")
    public DruidDataSource getDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
//TransactionConfig.java
public class TransactionConfig {
    @Bean("transactionManager")
    public PlatformTransactionManager getTransactionManager(DruidDataSource dataSource){
        return new DataSourceTransactionManager(dataSource);
    }
}

//主配置类
@Configuration
@ComponentScan("com.ralife")
@Import({TransactionConfig.class,JdbcConfig.class})
@EnableTransactionManagement
@PropertySource("jdbcConfig.properties")
public class SpringConfiguration {
}
```
# Spring5中的新功能
- 整个Spring5框架的代码基于Java8，运行时兼容JDK9  

- 自带通用日志封装

- 支持@Nullable注解（可以加在属性、方法、参数上 表示可以为空）  

- 支持函数式风格(`GenericApplicationContext`)  

- 支持整合JUnit5

- 新增Webflux（Reactor实现）  
  - 用于Web开发，功能和SpringMVC类似。  
  - 是一种异步非阻塞的框架。  
  - 采用函数式编程。 

- 异步和同步针对调用者：      
  调用者发送请求后，如果等待对方回应之后才去做其它事就是同步，如果不等对方回应就去做其它事就是异步。  

- 阻塞和非阻塞针对被调用者：  
  被调用者接受请求后，做完请求任务之后才给出反馈就是阻塞，受到请求之后马上给出反馈然后再去做任务就是非阻塞。  

- SpringMVC和Webflux的异同点  
  - 同：两个框架都可以使用注解，都运行在Tomcat容器中。  
  - 异：  
    SpringMVC采用命令式编程，Webflux采用异步响应式编程。 
    SpringMVC是同步阻塞的方式，基于SpringMVC+Servlet+Tomcat。  
    SpringWebflux是异步非阻塞方式，基于SpringWebflux+Reactor+Netty 