# Maven能做什么  
- 添加第三方jar包  

- jar包之间的依赖关系  
- 处理jar包之间的冲突  
- 获取第三方jar包  
- 将项目拆分为多个功能模块  
- 实现项目的分布式部署

# Maven是什么  
 - Maven 是一个项目管理和整合(**自动化构建**)工具。Maven 为开发者提供了一套完整的**构建生命周期**框架。开发团队几乎不用花多少时间就能够自动完成工程的基础构建配置，因为 Maven 使用了一个标准的目录结构和一个默认的构建生命周期。  

 - 在有多个开发团队环境的情况下，Maven 能够在很短的时间内使得每项工作都按照标准进行。因为大部分的工程配置操作都非常简单并且可复用，在创建报告、检查、构建和测试自动配置时，Maven 可以让开发者的工作变得更简单。  
 - Maven 能够帮助开发者完成以下工作：  
 构建、文档生成、报告、依赖、SCMs、发布、分发、邮件列表...  

## 构建的概念   
构建就是以我们编写的Java代码、框架配置文件、国际化等其它资源、JSP页面和图片等静态资源作为“原材料”，去“生产”出一个可运行的“项目”的**过程**。

## 构建环节   
- 清理：删除以前的编译结果，为重新编译做好准备；  

- 编译：将Java源程序编译为字节码文件；  
- 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性；  
- 报告：在每一次测试后以标准格式的记录和展示测试结果；  
- 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装部署；（Java工程对应jar包，Web工程对应war包）  
- 安装：在Maven环境下特指将打包结果（jar包或war包）安装到本地仓库中；  
- 部署：将打包结果部署到远程仓库或将war包部署到服务器上。  

## Maven构建命令  
- mvn clear            清理

- mvn compile          编译mian路径下的所有源程序
- mvn test-compile     编译test路径下的所有源程序
- mvn test             测试+形成报告
- mvn package          打包
- mvn install          安装到本地仓库  

# Maven使用  
## 配置环境变量  
MAVEN_HOME : Maven解压后的路径  

PATH : %MAVEN_HOME%/bin   

## Maven联网问题  
- Maven的核心程序并不包含具体功能，仅负责宏观调度。具体功能由插件完成。Maven会默认到本地仓库查找插件。如果没有就从远程仓库下载，此时如果不能联网就无法执行Maven具体功能。  

- 为了解决这一问题，可以将Maven本地仓库指向一个在联网情况下下载好的目录。  
- Maven默认的本地仓库为`\.m2\repository`目录（当前用户家目录），可在Maven的核心配置文件`maven\conf\setting.xml`中进行修改：  
```xml   
<localRepository>仓库位置</localRepository>  
<localRepository>D:\RepMaven</localRepository>
```  
# Maven核心概念  
## 1. POM（ProjectObjectModel）  
项目对象模型。将Java工程的相关信息封装为对象作为便于操作和管理的模型。是Maven工程的核心配置。(pom.xml)   
所有的 POM 都继承自一个父 POM（无论是否显式定义了这个父 POM）。父 POM 也被称作 Super POM，它包含了一些可以被继承的默认设置。  

## 2. 约定的目录结构  
**约定>配置>编码**，能用配置解决的问题就不编码，能基于约定的就不进行配置。而Maven正是因为指定了特定文件的保存的目录才能够对我们的Java工程进行自动化构建。  

## 3. 坐标（导入jar包）  
Maven使用以下三个向量在仓库中唯一的确定一个Maven工程：  
- groupId ：公司或组织的域名倒序+当前项目名称  

- artifactId ：当前项目的模块名称
- version ：当前模块的版本  

一个`pom.xml`文件中必须包含这三个坐标。  

如果模块安装到本地仓库 可通在`groupId\artifactId\version`目录下找到对应的jar包  

## 4. 依赖管理  
### 依赖的范围(`pom.xml`中`<dependency>`的`<scope>`子元素)  
- compile     

  main和test目录下的Java代码都可以访问这个范围的依赖。   

  部署到Tomcat服务器上运行时要放在WEB-INF的lib目录下。  

- test    

  只有test目录下的Java代码可以访问这个范围的依赖。  

  部署到Tomcat服务器上运行时**不会**放在WEB-INF的lib目录下。  

  例如：对JUnit的依赖。仅仅是测试部分依赖。    

- provided   
  main和test目录下的Java代码都可以访问这个范围的依赖。  

  部署到Tomcat服务器上运行时**不会**放在WEB-INF的lib目录下。  

  例如：servlet-api在服务器上运行时，Servlet容器会提供相关API，部署的时候不需要放到lib目录下。  

- runtime、import、system等 

### 依赖的传递性   
当存在**间接依赖**的情况，主工程对间接依赖的jar包可以是否能访问得看这个jar包引入时的依赖范围--**只有依赖范围为compile时才可以访问**。  

### 依赖的原则：解决jar包的冲突  
1. 路径最短者优先     

    B依赖A，C依赖B，如果A、B相同类型的jar包版本不同，C会与B保持一致。

2. 路径相同时先声明者优先(`<dependency>标签配置的先后顺序`)    

    C依赖A，C依赖B，如果A、B相同类型的jar包版本不同，C会与先声明者版本保持一致。  

### 依赖的排除  
有时候为了确保程序正确，可以将有可能重复的间接依赖排除。(自已pom.xml中的版本和间接依赖的版本可能不一致)    

我们使用`<exclusions>`标签进行依赖排除的配置：  
```xml   
<!--依赖排除-->
<exclusions>  
  <exclusion>  
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <!--不需要进行版本配置 默认会排除掉除自己定义的之外所有版本-->
  </exclusion>
</exclusions>
```
### 统一管理jar包的版本  
如果我们导入了一个外部框架的多个jar包到当前项目中，如果不进行统一管理则每次需要更换jar包版本时都需要修改每一个依赖的版本，非常繁琐；而这可以通过统一管理进行解决：  
```xml   
<!--统一管理Spring的jar包的版本-->  
<properties>
    <spring.version>4.0.0.RELEASE</spring.version>
</properties>  

<dependencies>  
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
  </dependency>
</dependencies>
```
### 创建外部依赖      
如果在远程仓库和中央仓库中，依赖不能被满足，我们就需要导入外部依赖来解决这个问题。基本步骤为：  

- 第一步：在`src`目录下添加`lib`目录。  

- 第二步：复制任何jar（`ldapjdk.jar`）文件到`lib`目录下。  

- 在`pom.xml`中进行相关配置 :  
```xml   
<dependencies>  
   <dependency>
      <!--指定 groupId 为 library 的名称-->
      <groupId>ldapjdk</groupId>  
      <!--指定 artifactId 为 library 的名称-->
      <artifactId>ldapjdk</artifactId>  
      <!--指定作用域（scope）为系统-->
      <scope>system</scope>
      <version>1.0</version>  
      <!--指定相对于工程位置的系统路径-->
      <systemPath>${basedir}\src\lib\ldapjdk.jar</systemPath>
   </dependency>
</dependencies>
``` 

## 5. 仓库管理  
### 分类  
- 本地仓库：当前本机电脑上所有Maven工程服务。  

- 远程仓库：  
  - 私服：架设在当前局域网环境下，为当前局域网范围内所有Maven工程服务。(一般是公司内部自编的仓库)  

  - 中央仓库：架设在Internet上，为全世界所有Maven项目服务。  
  - 中央仓库镜像：架设在各个大洲，为中央仓库分担流量。减轻中央仓库的压力，同时快速响应用户请求。  
  本地仓库找不到的jar包会从远程仓库下载到本地仓库中。  

### 仓库中的文件  
- Maven的插件  

- 我们自己开发的项目模块  
- 第三方框架或工具的jar包  

不管是什么样的jar包，在仓库都是按照坐标生成目录，所以可以通过统一方式进行查询或依赖。    

### Maven 依赖搜索顺序  
- 第一步：在本地仓库中搜索，如果找不到，执行第二步，如果找到了则执行其他操作。  

- 第二步：在中央仓库中搜索，如果找不到，并且有一个或多个私服已经设置，则执行第四步，如果找到了则下载到本地仓库中已被将来引用。 
- 第三步：如果远程仓库没有被设置，Maven 将简单的停滞处理并抛出错误（无法找到依赖的文件）。 
- 第四步：在一个或多个私服中搜索依赖的文件，如果找到则下载到本地仓库已被将来引用，否则 Maven 将停止处理并抛出错误（无法找到依赖的文件）。  

**无论会执行哪步最终如果能找到依赖文件都会下载到本地仓库中**。

## 6. 生命周期   
Maven生命周期定义了各个构建环节的执行顺序，有了这个清单，Maven就可以自动化的执行构建命令了。Maven有三个**相互独立**的生命周期：   
### Clean 生命周期   
在进行真正的构建之前进行一些清理工作。包含以下阶段：  
- pre-clean ：清理之前要做的事。 

- clean ：开始清理。（**移除所有上一次构建生成的文档**）
- post-clean ：清理之后要做的事。

### Default (or Build) 生命周期  
构建的核心部分，包括编译、测试、打包、安装、部署等等。包含以下23个阶段：  
生命周期阶段|描述
:-|:-  
validate|检查工程配置是否正确，完成构建过程的所有必要信息是否能获取到。  
initialize|初始化构建状态，例如设置属性。
generate-sources|生成编译阶段需要包含的任何源码文件。
process-sources|处理源代码。
generate-resources|生成工程中需要包含的资源文件。
process-resources|拷贝和处理资源文件到目的目录中，为打包阶段做准备。
**compile**|编译工程源码。
process-classes|处理编译生成的文件。（加强|优化...）
generate-test-sources|生成编译阶段需要包含的任何测试源代码。
process-test-sources|处理测试源代码。
**test-compile**|编译测试源代码到测试目的目录。
process-test-classes|处理测试字节码文件。
**test**|使用适当的单元测试框架（例如JUnit）运行测试。
prepare-package|打包前必要的准备操作。
**package**|获取编译后的代码（classes和test-classes），并按照可发布的格式进行打包。（jar、war、ear等）
pre-integration-test|集成测试执行前，执行所需操作。
integration-test|处理和部署必须的工程包到集成测试能够运行的环境中，开始集成测试。
post-integration-test|集成测试执行后执行必要的操作。
verify|运行检查操作来验证工程包是否有效，并满足质量要求。
**install**|安装工程包到本地仓库中。该仓库可以作为本地其它工程的依赖。
deploy|部署。（拷贝最终的工程包到远程仓库中）
### Site 生命周期    
生成项目报告、站点、发布站点等。包含以下几个阶段：  
- pre-site ：执行一些需要在生成站点文档之前完成的工作。 

- site ：生成项目的站点文档。
- post-site ：执行一些需要在生成站点文档之后完成的工作。并且为部署做准备。
- site-depoly ：将生成的站点文档部署到特定的服务器上。    

常用的是site阶段和site-deploy阶段，用以生成和发布Maven站点。    

### 注意事项
- 当一个阶段通过 Maven 命令调用时，例如 `mvn compile`，只有**该阶段之前以及包括该阶段在内**的所有阶段会被执行。  

- 不同的 maven 目标将根据打包的类型（JAR / WAR / EAR），被绑定到不同的 Maven 生命周期阶段。  

- 资源文件在编译之前就已经处理好了。

## 7. 插件和目标   
- Maven的核心仅仅是定义了抽象的生命周期，具体任务都是交由插件完成的。  

- 每个插件都能实现多个功能，每个功能就是一个插件的目标。  
- Maven的生命周期和插件的目标相互绑定，以完成某个具体的构建任务。  

例如：Default (or Build) 生命周期中的`compile`阶段就是插件`maven-compiler-plugin`的一个功能（目标）。    

### 插件类型  
- Build plugins : 在构建时执行，并在 pom.xml 的 元素中配置。

- Reporting plugins : 在网站生成过程中执行，并在 pom.xml 的 元素中配置。  

### 常用插件  
插件|描述  
:-|:-  
clean | 构建之后清理目标文件。删除目标目录。  
compiler | 编译 Java 源文件。 
surefile | 运行 JUnit 单元测试。创建测试报告。  
jar | 从当前工程中构建 JAR 文件。   
war | 从当前工程中构建 WAR 文件。  
javadoc | 为工程生成 Javadoc。  
antrun | 从构建过程的任意一个阶段中运行一个 ant 任务的集合。  


## 8. 继承    
- 由于非compile范围的依赖信息是不能在“依赖链”中传递的，所以有需要的工程只能单独配置。（例如JUnit范围为test，相当于一个项目的每个模块都需要配置一次，修改版本时会非常繁琐）  

- 使用继承机制就可以**将这样的依赖信息统一提取到父工程模块中进行统一管理**。    

### 创建父工程
创建父工程和一般的java工程操作一致，唯一需要注意的是:**打包方式要设置为pom**。（父工程中只有一个pom文件其它啥也没有）  

### 子工程中引用父工程
在子工程中通过`<parent>`标签可以导入父工程的依赖。如果子工程的`groupId`和`version`与父工程重复则可以删除：  
```xml 
<parent>
    <groupId>com.ralife.small</groupId>
    <artifactId>Parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>   

    <!--指定从当前子工程的pom.xml出发，查找父工程pom.xml的相对路径-->  
    <relativePath>../Parent/pom.xml</relativePath>
</parent>
```
### 在父工程中管理依赖   
将父工程中的`<dependencies>`标签，用`<dependencyManagement>`标签括起来：  
```xml   
<dependencyManagement>  
  <dependencies>  
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.9</version>  
      <scope>test</scope>
    </dependency>
  </dependencies>
</dependencyManagement> 
```  
在子项目中重新指定需要的依赖，删除范围和版本号：  
```xml     
<dependencies>  
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
  </dependency>
</dependencies>
```

## 9. 聚合     
将多个工程拆分为模块后，需要手动逐个安装到仓库后依赖才能够生效。修改源码后也需要逐个手动进行clean操作，而使用了聚合之后就可以**批量进行Maven工程的安装、清理工作**。   

### 配置聚合  
在总的聚合工程（一般是Parent工程）中使用`<modules>` `<module>` 标签组合，指定模块工程的相对路径即可：    
```xml 
<modules>  
  <module>../子工程1</module>
  <module>../子工程2</module>
  <module>../子工程3</module>
</modules>
```
# Maven 工程文档  
Maven通过`site`命令可自动创建工程文档，并保存在`项目名\target\site`文件夹下，点击index.html即可查看文档。    

Maven 使用称作` Doxia `的文件处理引擎创建文档，它将多个源格式的文件转换为一个共通的文档模型。要编写工程文档，你可以使用以下能够被 Doxia 解析的几种常用格式来编写:  
- XDoc   Maven 1.x版的文档格式

- FML    FAQ文档格式
- XHTML  可扩展 HTML 格式

## Maven 工程模板  
- Maven 使用**原型**（Archetype）概念为用户提供了大量不同类型的工程模版（614 个  quickstart为203）。Maven 使用下面的命令帮助用户快速创建 java 项目：  
`mvn archetype:generate`     

- 原型是一个 Maven 插件，它的任务是根据模板创建一个项目结构。    

# Maven 快照 
- 快照是一个特殊的版本，它表示当前开发的一个副本。与常规版本不同，Maven 为每一次构建从远程仓库中检出一份新的快照版本。每次只要将更新的代码快照发布到远程仓库，如果有调用者也会自动更新为最新的快照版本。  

## 快照和版本的区别  
- 对于版本，Maven 一旦下载了指定的版本，它将不会尝试从仓库里再次下载一个新的版本。想要下载新的代码，版本需要被升级（更换版本号）。  

- 对于快照，每次用户接口团队构建他们的项目时，Maven 将自动获取最新的快照。  

- 虽然，对于快照，Maven 每次自动获取最新的快照，但你可以在任何 maven 命令中使用 -U 参数强制 maven 下载最新的快照。  

# Eclipse中配置Maven  
- Eclipse现在的版本一般内置了Maven插件所以不需要额外安装。  

- 指定Maven核心程序的位置。(`Window\Preferences\Maven\Installations`)  
- 指定Maven核心程序配置文件的位置。(`Window\Preferences\Maven\User Setting`)  
- 建议最好不要使用默认的仓库，可以在`setting.xml`文件中自定义默认的仓库。  
- 第一次导入手动创建的maven工程不能选`General`里的导入方式，而应该直接以maven项目的形式导入。  
- 在Eclipse里面运行maven项目：选择`Run As`里的`Maven test`。  
- 解决测试时的中文乱码问题：  
  在当前工程的pom.xml里面加入设置测试字符集的插件  
```xml 
<build>
<plugins>   
<plugin>
    <!--升级到新版本解决控制台乱码问题-->
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.7.2</version>
    <configuration>
        <!--解决测试时中文乱码问题-->  
        <forkMode>once</forkMode><!--在一个进程中进行所有测试，默认是once-->
        <argLine>-Dfile.encoding=UTF-8</argLine>
    </configuration>
</plugin>
</plugins>
</build>
```  

# Maven配置Web工程  
- 创建项目时选择打包方式为war包（默认缺失web.xml文件）  

- 通过右键项目名--ConfigureBuildPath--ProjectFacets--取消动态Web工程--应用--再构上--将web.xml和WebContent选上--应用  

# Maven酷站  
我们可以到 <http://mvnrepository.com> 搜索需要的jar包的依赖信息。
