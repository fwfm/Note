# Spring框架基本概述  
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

-  使用IOC的目的：降低耦合度  

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
- 在Spring配置文件中，使用`<bean>`标签，标签里添加对应的属性，就可以实现对象创建    

  `<bean>`标签常用属性：    
  - id     对象别名（唯一标识）  使用`getBean()`方法的时候 通过id获取对象  
  - class  类全路径（包类路径）    
  - name   早期属性  可id作用一样  可以允许特殊符号

- 创建对象时候，默认执行无参构造方法，如果定义了有参构造方法要把无参构造方法显性表示出来，否则会报错。  

### Spring注入属性(xml) 
- DI：依赖注入，就是注入属性（在已创建对象的基础上完成）  
  第一种方式：使用`set()`方法进行注入    
    - 创建类，定义属性，并配置set方法  
    - 在spring配置文件配置对象创建，配置属性注入。（在`<bean>`标签内使用`<property name="" value=""></property>`标签  name-属性名   value-属性值）  
  
  第二种方式：使用**有参构造**注入(常用)  
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
    字面量（固定值）  
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

3. 调用bean的初始化的方法（需要进行配置初始化方法）   
  在类里面写好初始化方法 然后在配置文件中为`<bean>`标签添加`init-method`属性指定值为初始化方法名   

  把bean实例传递bean后置处理器的方法（`postProcessAfterInitialization()`）

4. bean可以使用了（对象获取到了）  

5. 当容器关闭的时候，调用bean的销毁的方法（需要进行配置销毁的方法）  
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

**必须和`@AutoWired`搭配使用**  
```java  
@Service(value="userService")  //相当于<bean id="userService" class="...">  
public class UserService{  

  @Autowired
  @Qualifier(value="userDaoImpl")
  private UserDao userDao;  

  public void add(){
    System.out.println("service add .....");
    userDao.add();
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

- 2.编写测试类  
```java  
@Test
public void testService(){    
  //使用AnnotationConfigApplicationContext（注解配置上下文）获取上下文
  ApplicationContext context =   
    new AnnotationConfigApplicationContext(SpringConfig.class);
    UserService userService = context.getBean("userService",UserService.class);  
    userService.add();
}
  ```  
# AOP(面向切面编程)  
## 概念  
不通过修改源代码方式，在主干功能添加新功能。  

## 底层原理  
AOP底层使用动态代理：  
- 第一种 有接口情况，使用JDK动态代理（使用Proxy类的newProxyInstance(当前类加载器, 要进行功能增强的接口数组, 功能增强的实现类[需要实现InvocationHandler接口])  方法进行增强）   
创建接口实现类代理对象，在代理类里实现功能增强 

- 第二种 无接口情况，使用CGLIB动态代理  
创建当前类子类的代理对象，子类重写（类似于装饰模式）  

## AOP术语  
- 连接点：类里面可以增强的方法都是连接点。 

- 切入点：实际被真正增强的方法，称为切入点。 

- 通知（增强）：实际增强的逻辑部分称为通知（增强）。比如登录里的权限判断功能。  
  通知有多种类型：  
  1.前置通知（`@Before`）   
  2.后置通知（`@AfterReturning`）遇到异常不会执行  
  3.环绕通知（`@Around`）切点方法之前和之后都会执行  
  4.异常通知（`@AfterThrowing`）只有在切点方法中出现异常才会执行   
  5.最终通知（类似finally）（`@After`）有异常也会执行

- 切面：是一个动作，把通知应用到切入点的过程。  

## AOP操作
- Spring框架一般都是基于`AspectJ`实现AOP操作。   

- `AspectJ`不是Spring组成部分，属于独立的AOP框架，一般将`AspectJ`和Spring框架一起使用进行AOP操作。  

- 基于`AspectJ`实现AOP操作方式：  

  （1）基于xml配置文件实现  

  （2）基于注解方式实现（常用）  

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

## AOP操作（`AspectJ`配置文件  了解）  

# JDBCTemplate  
## 定义  
- Spring框架对JDBC进行了封装，使用JDBCTemplate方便实现对数据库操作。  

## 准备工作  
- 引入依赖  

- 在spring配置文件配置数据库连接池  

- 配置JdbcTemplate对象，注入DataSource   

- 创建service类，创建dao类，在dao注入jdbcTemplate对象（配置文件中开启组件扫描）  
