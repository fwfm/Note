# 1. mybatis 入门  
## 框架概述  
> 框架是我们软件开发中的一套**解决方案**(半成品)，不同的框架解决的是不同的问题。  

框架封装了很多细节，使开发者可以使用极简的方式实现功能。大大提高开发效率。
## mybatis 的概述  
mybatis是基于Java的持久层(dao层)框架，它封装了jdbc操作的很多细节使开发者只需要关注sql语句本身，而无需关注驱动、创建连接等繁杂的过程；它使用了ORM思想实现了结果集的封装。  
> ORM:(Object Relational Mapping 对象关系映射) 把数据库表和实体类及实体类的属性对应起来，让我们操作实体类就可以实现操作数据库表。
## mybatis 入门案例    
### 01 环境搭建  
1. 安装部署好mysql数据库   

2. 创建maven项目 导入mybatis相关依赖  
```xml
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.6</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.49</version>
    </dependency>
</dependencies>
```
3. 创建实体类和dao接口  

4. 创建mybatis的主配置文件(`mybatis-configuration.xml`)      
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mysql的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/small"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件(每个dao独立的配置文件)的位置-->
    <mappers>
        <!--使用XML配置文件实现 必须和Dao的路径完全相同-->
        <mapper resource="com/ralife/firstweb/dao/IUserDao.xml"></mapper>
        <!--使用注解实现-->
        <!--<mapper class="com.ralife.firstweb.dao.IUserDao"></mapper>-->
    </mappers>
</configuration>
```
5. 创建映射配置文件(`IUserDao.xml`)  
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ralife.firstweb.dao.IUserDao">
    <select id="findAll" resultType="com.ralife.firstweb.entity.User">
        select * from user;
    </select>
</mapper>
```

**注意事项：**  
- IUserDao和IUserMapper是一样的    

- 在idea中创建目录和包的时候是不一样的    
  
  包在创建时：com.ralife.dao 是三级目录

  目录在创建时：com.ralife.dao 是一级目录

- mybatis的映射配置文件位置必须和dao接口的包结构相同  

- 映射配置文件的mapper标签namespace属性的值必须是dao接口的全限定类名(完整类路径)  

- 映射配置文件的操作配置(select insert update delete ...) **id属性的值必须是dao接口的方法名**    

- 满足后三个条件 直接定义接口就行 不需要写dao的实现类（用的最多）  

- **映射配置文件通过namespace和具体操作的id来实现完整定位** 

### 02 入门案例    
- psvm快捷创建main函数  

- 使用步骤：  
```java
//1. 读取配置文件(`mybatis-configuration.xml`)
InputStream in = Resources.getResourceAsStream("mybatis-configuration.xml");
//2. 创建SqlSessionFactory工厂（构建者模式  把对象的创建细节隐藏  使用者直接调用方法即可拿到对象）
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(in);
//3. 使用工厂生产SqlSession对象(工厂模式  不直接通过new关键字创建对象[反射机制] 降低类之间的依赖性)
SqlSession session = factory.openSession();
//4. 使用SqlSession创建Dao接口的代理对象（代理模式  AOP 不改变源代码情况下实现功能增强）
IUserDao dao = session.getMapper(IUserDao.class);
//5. 使用代理对象执行方法
List<User> users = dao.findAll();
//6. 释放资源
session.close();
in.close();
```  
- 注意事项：    
  
  映射配置文件的每个操作必须指定返回类型（`resultType`属性指定为entity类的全路径）   

- mybatis基于注解的入门案例：  
  
  把映射配置文件移除 在Dao接口方法上用相应的注解进行替换 并指定SQL语句  

  将主配置文件中的mapper标签的`resource`属性（映射配置文件路径）替换为`class`属性（Dao接口类路径）

## 自定义mybatis框架 
通过XML解析技术自定义mybatis框架。  

1. 根据配置文件(`mybatis-configuration.xml`)的信息创建Connection对象  

2. 获取预处理对象PreparedStatement(传入sql语句)  

3. 执行查询得到ResultSet对象  

4. 遍历结果集用于封装(利用反射机制) 

5. 返回list  

# 2. mybatis 基本使用
## mybatis 的单表CRUD操作  
- 使用XML配置文件方式：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ralife.firstweb.dao.IUserDao">
    <!--查询所有用户-->
        <select id="findAll" resultType="com.ralife.firstweb.entity.User">
        select * from user;
    </select>
    <!--保存用户-->
    <insert id="saveUser" parameterType="com.ralife.firstweb.entity.User">
        <!--获取保存到数据库的id keyProperty代表要返回的值名称 keyColumn代表数据库中对应的列名 order取值为after代表插入后的行为 -->
        <selectKey keyProperty="id" keyColumn="id" order="AFTER" resultType="java.lang.Integer">
            select last_insert_id();
        </selectKey>
        insert into user(username,birthday,sex,address) values (#{username},#{birthday},#{sex},#{address});
    </insert>
    <!--更新用户-->
    <update id="updateUser" parameterType="com.ralife.firstweb.entity.User">
        update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address} where id=#{id};
    </update>
    <!--删除用户-->
    <delete id="deleteUser" parameterType="java.lang.Integer">
        delete from user where id=#{uid};
    </delete>
    <!--根据id获取用户-->
    <select id="getUserById" parameterType="java.lang.Integer">
        select * from user where id=#{uid};
    </select>
    <!--根据名称模糊查询-->
    <select id="findByName" parameterType="java.lang.String">
        <!--通过Statement操作 不安全 且参数名必须指定为value 否则会报错-->
        <!--select * from user where username like '%${value}%';-->
        <!--通过PreParedStatement操作 安全-->
        select * from user where username like #{username};
    </select>
    <!--获总取记录条数-->
    <select id="findTotal" resultType="INT">
        select count(id) from user;
    </select>
</mapper>
```
```java
@Test
public void testInsert() throws IOException {
    InputStream  in = Resources.getResourceAsStream("mybatis-configuration.xml");
    SqlSession session = new SqlSessionFactoryBuilder().build(in).openSession();
    IUserDao dao = session.getMapper(IUserDao.class);
    User user = new User();
    user.setUsername("entity");
    user.setSex("男");
    user.setBirthday(new Date());
    user.setAddress("BeiJing");
    dao.saveUser(user);
    //非查询事件都需要提交否则会发生事务回滚 无法正常完成
    session.commit();
    session.close();
    in.close();
}
```    
**注意事项：**  
- 配置mapper.xml时如果有结果返回则需要为操作指定`resultType`属性，如果需要传参则指定`parameterType`属性。

- 当`parameterType`属性值为简单类型时有多种写法(INT INTEGER java.lang.Integer都可以)，且在写sql语句时名字可以随便取。

## mybatis 的参数和返回值设置   
**参数值设置：**
### 传递pojo对象
mybatis使用OGNL表达式解析对象字段的值，#{}或者${}括号中的值为POJO属性名称。  
> OGNL：使用 `对象名.属性名` 的方式获取对象的属性而不是通过get()方法获取。  

### 传递pojo包装对象  
解决传递复杂类型参数问题，可以额外编写一个QueryVo实体类封装复杂参数，将`parameterType`属性值指定为vo实体类全路径 并通过OGNL方式获取实体类属性：   
QueryVo.java
```java
public class QueryVo {
    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
}
```
IUserDao.xml
```xml
<select id="findByNameVo" parameterType="com.ralife.firstweb.entity.QueryVo" resultType="com.ralife.firstweb.entity.User">
        select * from user where username like #{user.username}
</select>
```
**返回值设置：**  
### 实体类的属性名和数据库表字段名不对应  
如果不设置映射关系，在做查询操作时无法正常封装结果集。 

解决方案：  
- 在写SQL语句时设置别名和实体类的属性名相对应(执行效率最高 但开发效率降低 每个操作都需要写别名)：
```xml
<select id="findAll" resultType="com.ralife.firstweb.entity.User">
        select id as userId,username as userName,sex as userSex,birthday as userBirthday,address as userAddress from user;
</select>
```
- 使用mybatis的`<resultMap>`标签配置查询结果的列名和实体类的属性名对应关系(开发效率高 只需要配置一次 所有操作只要指定resultMap就可以使用)：
```xml
<resultMap id="userMap" type="com.ralife.firstweb.entity.User">
    <!--主键配置 property对应实体类属性名 column对应查询结果列名-->
    <id property="userId" column="id"></id>
    <!--非主键配置 property必须严格区分大小写-->
    <result property="userName" column="username"></result>
    <result property="userSex" column="sex"></result>
    <result property="userBirthday" column="birthday"></result>
    <result property="userAdress" column="address"></result>
</resultMap>  

<!--必须将resultType改为resultMap才能对应起来-->
<select id="findAll" resultMap="userMap">
        select * from user;
</select>
```
## mybatis 的dao编写(不推荐使用)
```java
public class ImplUserDao implements IUserDao{
    private SqlSessionFactory factory;

    public ImplUserDao(SqlSessionFactory factory){
        this.factory = factory;
    }
    @Override
    public List<User> findAll() {
        SqlSession session = factory.openSession();
        //指定映射配置文件中对应的方法
        List<User> list = session.selectList("com.ralife.firstweb.dao.IUserDao.findAll");
        session.close();
        return list;
    }

    @Override
    public void saveUser(User user) {
        SqlSession session = factory.openSession();
        session.insert("com.ralife.firstweb.dao.IUserDao.saveUser",user);
        session.commit();
        session.close();
    }

    @Override
    public void updateUser(User user) {
        SqlSession session = factory.openSession();
        session.update("com.ralife.firstweb.dao.IUserDao.updateUser",user);
        session.commit();
        session.close();
    }
}
```    
### PreparedStatement对象的执行方法  
- `execute`：可以执行CRUD中的任意一种语句，返回值为boolean类型，表示是否有结果集。  

- `executeUpdate`：可以执行CUD语句，无法执行查询语句，返回值时影响数据库记录的行数。  

- `executeQuery` ：只能执行查询语句，返回值为执行结果封装好的ResultSet对象。

## mybatis 配置的细节(标签的使用) 
- `<properties>`标签：加载外部配置文件，使用resource属性时**配置文件必须在类路径下**；使用url属性时必须按照url格式指定路径。(协议  主机  端口  URI)   
> URI:统一资源标识符 在应用中可以唯一定位一个资源。

```xml
<configuration>
    <properties resource="jdbc.properties"></properties>
    <!--配置环境-->
    <environments default="mysql">
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```
- `<typeAliases>`标签：配置entity中类(实体类)的别名 。    

映射文件中的parameterType属性简单类型的名字可以随便取就是因为使用了别名。
```xml
<typeAliases>
    <!--type属性指定的是实体类全限定类名 alias属性指定别名 当指定了别名就不再区分大小写 实体类多的时候配置麻烦-->
<!--<typeAlias type="com.ralife.firstweb.entity.User" alias="user"></typeAlias>-->
    <!--package用于指定要配置别名的包 指定后该包下所有的实体类都会注册别名 类名就是别名 不区分大小写-->
    <package name="com.ralife.firstweb.entity" />
</typeAliases>
```  
指定映射配置文件时也可以使用`<package>`标签，无需为每个配置文件都加个`<mapper>`:  
```xml
<!--指定映射配置文件(每个dao独立的配置文件)的位置-->
<mappers>
    <!--使用XML配置文件实现-->
    <!--<mapper resource="com/ralife/firstweb/dao/IUserDao.xml"></mapper>-->
    <package name="com.ralife.firstweb.dao"/>
    <!--使用注解实现-->
    <!--<mapper class="com.ralife.firstweb.dao.IUserDao"></mapper>-->
</mappers>
```  
# 3. mybatis 的深入和多表  
## mybatis 的连接池    
> 连接池: 用于存储连接的容器(集合对象 线程安全 不能多个线程拿到同一个连接 集合必须实现队列的特性)  

### mybatis 连接池的配置方式  
主配置文件`mybatis-configuration.xml`中的`<dataSource type="POOLED">`标签,其中type属性表示采用何种连接池方式：   
- POOLED ：采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现：(从池中拿连接)    
  
  先判断空闲池是否有连接，如果有直接拿，没有去活动池，判断活动池空间是否已满，没满创建新连接，满了就先释放最老的一个连接，再将这个连接分配给请求者。

- UNPOOLED ：采用传统获取连接的方式，虽然也实现了javax.sql.DataSource接口，但并没有使用池的思想。(每次获取连接都需要新建)  

- JNDI ：采用服务器提供的JNDI技术获取DataSource对象，不同服务器所能拿到的DataSource是不一样的，如果不是web或maven的war工程，是不能使的。    
## mybatis 事务控制及设计的方法      
- mybatis通过sqlsession对象的commit方法和rollback方法实现事务的提交和回滚。  

- openSession()方法如果传一个布尔类型的参数可指定自动提交事务 而无需每一个操作都手动提交一次。
## mybatis 基于XML配置的动态SQL语句使用 
- 根据条件查询 `<if> <where>`标签：
```xml
<!--按条件查询用户-->
<!--方法1：test属性中的值和#{}内的值都是实体类中属性名 and后面的名字是数据库列名 需要添加一个永远成立的条件(where 1=1) 不推荐使用-->
<!--<select id="findByCondition" parameterType="com.ralife.firstweb.entity.User">
    select * from user where 1=1
    <if test="username != null">
        and username = #{username}
    </if>
    <if test="sex != null">
        and sex = #{sex}
    </if>
</select>-->
<!--方法2：使用where标签替代 (where 1=1)-->
<select id="findByCondition" parameterType="com.ralife.firstweb.entity.User">
    select * from user
    <where>
        <if test="username != null">
            and username = #{username}
        </if>
        <if test="sex != null">
            and sex = #{sex}
        </if>
    </where>
</select>
```  
- 在指定集合中查找`<foreach>`标签：  
```xml
<!--foreach #{}中的值必须和item属性(类似别名)的值保持一致 如果有传参并需要返回结果集，则必须同时指定parameterType和resultType属性-->
<select id="findByCollection" parameterType="queryvo" resultType="user">
    select * from user
    <where>
        <if test="ids != null and ids.size()>0">
            <foreach collection="ids" open="and id in(" close=")" item="uid" separator=",">
                #{uid}
            </foreach>
        </if>
    </where>
</select>
```
- 抽取重复的SQL语句`<sql id=""> <include refid="">`标签：  
```xml
<sql id="baseUser">
    select * from user
</sql>  

<select id="findAll" resultType="user">
    <include refid="baseUser"></include>
</select>
```
## mybatis 多表查询  
### 表之间的关系  
- 一对多（对用户：一个用户可以有多个订单）  

- 多对一（对订单：多个订单可能是同一用户）  

- 一对一（一个人只能有一个身份证号） 

- 多对多（一个学生可以学多门课程 一门课程可以有多个学生一起学）  

- 特例：一个订单只能属于一个用户，所以mybatis把多对一看成一对一  

### mybatis 多表查询（基于XML） 
#### 一对多查询（多中添加外键）
- 建立两张表：用户表和账户表，让它们具备一对多的关系(在账户表中添加外键)  

- 建立两个实体类：用户实体类和账户实体类，让它们能体现出一对多的关系(用户实体应该包含一个账户实体集合属性，账户实体应该包含一个用户实体属性)  

- 建立两个配置文件

- 实现配置：查询用户时，可以同时得到用户下所有账户信息；查询账户时，可以得到所属用户信息。（结果集使用resultMap进行封装 不同配置文件需要指定不同的resultMap子标签）
```java
public class Account implements Serializable {
    private Integer id;
    private Double money;
    private Integer uid;
    //设置一对一的关系(一个账户只能同时被一个用户拥有)
    private User user;
}

public class User implements Serializable {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;
    //设置一对多的关系（一个用户可以同时拥有多个账户）
    private List<Account> accounts;
}
```
```xml
<!-- IAccountDao.xml -->
<mapper namespace="com.ralife.firstweb.dao.IAccountDao">
    <!--一对一查询结果集封装 使用association-->
    <resultMap id="accountMap" type="account">
        <!--column表示查询语句封装的列名 property表示pojo类中对应的属性名-->
        <id property="id" column="aid"></id>
        <result property="money" column="money"></result>
        <result property="uid" column="uid"></result>
        <!--一对一查询  使用association的column属性-->
        <association property="user" column="uid" javaType="user">
            <id property="id" column="id"></id>
            <result property="username" column="username"></result>
            <result property="sex" column="sex"></result>
            <result property="birthday" column="birthday"></result>
            <result property="address" column="address"></result>
        </association>
    </resultMap>
    <select id="findAll" resultMap="accountMap">
        select a.id as aid,a.money,a.uid,u.id,u.username,u.sex,u.birthday,u.address from account a,`user` u where a.uid = u.id;
    </select>
</mapper>

 <!-- IUserDao.xml -->
<mapper namespace="com.ralife.firstweb.dao.IUserDao">
    <!--一对多查询结果集封装 使用collection-->
    <resultMap id="userMap" type="user">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="sex" column="sex"></result>
        <result property="birthday" column="birthday"></result>
        <result property="address" column="address"></result>
        <collection property="accounts" ofType="account">
            <id property="id" column="aid"></id>
            <result property="uid" column="uid"></result>
            <result property="money" column="money"></result>
        </collection>
    </resultMap>

    <select id="findAllAccount" resultMap="userMap">
        select u.*,a.id as aid,a.uid,a.money from `user` u left join account a on u.id = a.uid;
    </select>
</mapper>
```
#### 多对多查询（需要使用中间表）
```java
public class User implements Serializable {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;
    private List<Role> roles;
}
public class Role {
    private Integer roleId;
    private String roleName;
    private String roleDesc;
    //配置多对多
    private List<User> users;
}
```
```xml
 <!-- IUserDao.xml resultMap和一对多配置相同 只是sql语句有所改变 -->
<resultMap id="userRoleMap" type="user">
    <id column="id" property="id"></id>
    <result column="username" property="username"></result>
    <result column="sex" property="sex"></result>
    <result column="birthday" property="birthday"></result>
    <result column="address" property="address"></result>
    <collection property="roles" ofType="role">
        <id column="roleId" property="roleId"></id>
        <result column="roleName" property="roleName"></result>
        <result column="roleDesc" property="roleDesc"></result>
    </collection>
</resultMap>
<select id="findAllRole" resultMap="userRoleMap">
    select u.*,r.* from user u
        left outer join role_user ru on u.id = ru.uid
        left outer join role r on r.roleId = ru.rid
</select>  

 <!-- IRoleDao.xml -->
 <resultMap id="roleMap" type="role">
    <id column="roleId" property="roleId"></id>
    <result column="roleName" property="roleName"></result>
    <result column="roleDesc" property="roleDesc"></result>
    <collection property="users" ofType="user">
        <id column="id" property="id"></id>
        <result column="username" property="username"></result>
        <result column="sex" property="sex"></result>
        <result column="birthday" property="birthday"></result>
        <result column="address" property="address"></result>
    </collection>
</resultMap>

<!--查询所有角色 同时查看角色所赋予的用户信息-->
<select id="findRoleUser" resultMap="roleMap">
    select r.*,u.* from role r
        left outer join role_user ru on r.roleId = ru.rid
        left outer join `user` u on u.id = ru.uid
</select>
```  
## JNDI数据源  
> JNDI: Java Naming and Directory Interface。是SUN公司推出的一套规范。目的是模仿windows系统中的注册表，实现在服务器中注册数据源。
# 4. mybatis 的缓存和注解开发  
## mybatis 延迟加载策略
- 延迟加载：在真正使用数据时才发起查询，不用的时候不查询。(按需加载  懒加载)  
  查询用户时，用户的账户信息应该是什么时候使用，什么时候查询出来。  

- 立即加载：不管用不用，只要一调用方法，马上发起查询。  
  查询账户时，账户所属用户信息应该时随着账户查询一起查询出来。  

- 关联对象是一的情况下(多对一  一对一)通常采用立即加载。  

- 关联对象是多的情况下(多对多 一对多)通常采用延迟加载。

- 一对一延迟加载案例
    - 使用`<association>`标签的select属性指定查询方法，column属性指定查询方法所需要的参数值(必须指定)  
    - 配置主配置文件`<settings>`标签  
```xml
<!--mybatis-configuration.xml-->
<!--配置参数-->
<settings>
    <!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>

<!--IAccountDao.xml-->
<resultMap id="lazyMap" type="account">
    <!--column表示查询语句封装的列名 property表示pojo类中对应的属性名-->
    <id property="id" column="id"></id>
    <result property="money" column="money"></result>
    <result property="uid" column="uid"></result>
    <association property="user" column="uid" javaType="user" select="com.ralife.firstweb.dao.IUserDao.getUserById"></association>
</resultMap>
<select id="findLazyAll" resultMap="lazyMap">
    select * from account
</select>

<!--IUserDao.xml-->
<!--根据id获取用户-->
<select id="getUserById" resultType="user" parameterType="java.lang.Integer">
    select * from user where id=#{id};
</select>
```
- 一对多延迟加载案例(同上)
    - 使用`<collection>`标签的select属性指定查询方法，column属性指定查询方法所需要的参数值(必须指定)  
    - 配置主配置文件`<settings>`标签

## mybatis 中的缓存
### 什么是缓存
存在于内存中的临时数据。  

### 为什么使用缓存 
减少和数据库的交换次数，提高执行效率。
### 缓存数据要求  
适用于缓存：  
- 经常**查询**并且不经常改变的。  
- 数据的正确与否对最终结果影响不大的。  

不适用于缓存：
- 经常改变的数据。  
- 数据的正确与否对最终结果影响很大的。（商品库存、银行汇率、股市牌价...）
### Mybatis中的一级缓存和二级缓存  
**一级缓存:（存对象）**
- 指的是Mybatis中 **SqlSession** 对象的缓存。    
  
- 当我们执行查询之后，查询的结果会同时存入到 SqlSession 为我们提供的一块区域中(结构是一个Map)，当我们再次查询同样的数据，mybatis会先去 SqlSession 中查询是否有，有的话直接拿出来用，没有再去数据库中查询。  
- 当 SqlSession 对象消失(执行update insert delete close clearCache方法)时，mybatis的一级缓存也会消失。  

**二级缓存:（存数据）**  
- 指的是Mybatis中 **SqlSessionFactory** 对象的缓存。由同一个SqlSessionFactory 对象创建的SqlSession共享其缓存。  

- 使用二级缓存步骤：  
  - 主配置文件开启二级缓存（默认开启）`<settings><setting name="cacheEnabled" value="true"/></settings>`  
  - POJO配置文件开启二级缓存 `<cache/>` 
  - 当前操作开启二级缓存（在select 标签中配置）`<select useCache="true">`
## mybatis 的注解开发(CRUD)  
### 环境搭建  
去除POJODao配置文件，改用注解实现。只要配置文件存在(不管用不用)，注解就会报错。  

### 单表CRUD  
```java
public interface IUserDao {

    @Select("select * from user")
    List<User> findAll();

    @Insert("insert into user(username,sex,address,birthday) values(#{username},#{sex},#{address},#{birthday})")
    void addUser(User user);

    @Update("update user set username=#{username},sex=#{sex},address=#{address},birthday=#{birthday} where id=#{id}")
    void updateUser(User user);

    @Delete("delete from user where id=#{id}")
    void deleteUser(Integer id);

    /**
     * 根据范围进行查询(必须使用<script>才能有效)
     * @param ids
     * @return
     */
    @Select("<script>select * from user where id in <foreach collection='ids' open='(' item='uid' separator=',' close=')'>#{uid}</foreach></script>")
    List<User> findByIdCollection(List<Integer> ids);

    @Select("select * from user where id=#{id}")
    User findById(Integer id);

    /**
     * 模糊查询
     * @param username
     * @return
     */
    @Select("select * from user where username like #{username}")
    List<User> findByName(String username);

    @Select("select count(*) from user")
    Integer findTotal();
}
```  
### 多表查询（使用结果集映射注解封装返回的结果集）  
#### 注解解决实体类字段和数据库表字段不对应问题  
```java
@Select("select * from user")
//设置id 使用@ResultMap(value={"userMap"})注解可指定对应的@Results
@Results(id="userMap",value = {
        //设置id为true  表示该字段对应数据库表主键
        @Result(id = true,column = "id",property = "userId"),
        @Result(column = "username",property = "userName"),
        @Result(column = "sex",property = "userSex"),
        @Result(column = "address",property = "userAddress"),
        @Result(column = "birthday",property = "userBirthday")
})
List<User> findAll();  

@Select("select * from user where id = #{userId}")
//如果只有一个结果映射 value可以省略 直接使用@ResultMap("userMap")
@ResultMap(value={"userMap"})
User findById(Integer userId);
```
#### 使用结果集映射注解封装返回的结果集  
- 一对一映射结果集设置
```java  
/**
    * 查询所有账户 同时展示账户所属的用户信息
    * column 指定POJO类在数据库中对应的列名
    * property 指定实体类中对应的属性
    * one 使用@One注解实现一对一映射关系 select属性指定用到的查询方法
    * @return
    */
@Select("select * from account")
@Results(id = "accountMap",value = {
        @Result(id = true,column = "id",property = "id"),
        @Result(column = "money",property = "money"),
        @Result(column = "uid",property = "uid"),
        @Result(column = "uid",property = "user",one = @One(fetchType = FetchType.EAGER,select = "com.ralife.dao.IUserDao.findById"))
})
List<Account> findAll();
```  
- 一对多映射结果集设置
```java  
//1. IUserDao.java
 /**
    * 一对多关系映射（使用@Many注解  懒加载）
    * property 指定关联对象是多的POJO集合
    * column 指定需要查询的表的主键
    * @Many 指定使用一对多关系映射  通过select设置要执行的方法的全限定方法名
    * @return
    */
@Select("select * from user")
@Results(id = "userMap",value = {
        @Result(id = true,column = "id",property = "id"),
        @Result(column = "username",property = "username"),
        @Result(column = "sex",property = "sex"),
        @Result(column = "birthday",property = "birthday"),
        @Result(column = "address",property = "address"),
        @Result(column = "id",property = "accounts",many = @Many(select = "com.ralife.dao.IAccountDao.findByUid",fetchType = FetchType.LAZY)),
})
List<User> findUserAccount();  

//2. IAccountDao.java
@Select("select * from account where uid=#{userId}")
List<Account> findByUid(Integer userId);
```
### 缓存的配置  
- mybatis默认开启一级缓存，无需手动配置。

- mybatis开启二级缓存：  

  - 主配置文件添加`<settings><setting name="cacheEnabled" value="true"/></settings>`  

  - DAO接口上使用`@CacheNamespace(blocking = true)`