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