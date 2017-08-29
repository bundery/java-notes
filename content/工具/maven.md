# Maven
> Maven是基于项目对象模型(POM)、可以通过一小段描述信息来管理项目的构建、报告和文档的软件项目管理工具。

## Maven标准的项目结构

| 目录               | 描述                   |
|--------------------|------------------------|
| src/main/java      | 源代码                 |
| src/main/resources | 资源文件               |
| src/main/filters   | 资源过滤               |
| src/main/config    | 配置文件               |
| src/main/scripts   | 脚本                   |
| src/main/webapp    | web程序源代码          |
| src/test/java      | 测试的源代码           |
| src/test/resources | 测试的资源             |
| src/test/filters   | 测试的资源过滤         |
| src/it             | 集成测试（主要用于插件） |
| src/assembly       | Assembly descriptors   |
| src/site           | 自动生成的网站          |
| pom.xml            | maven配置文件          |
| target/classes     | 编译后的二进制文件       |

最基本的是pom.xml，src/main/java，src/main/resources，src/test/java，src/test/resources。

配置文件：

```xml
<!-- groupId&artifactId&version组成了唯一的坐标，执行install时安装到本地仓库的目录及名称 -->
  <groupId>com.mycompany.app</groupId> 包名
  <artifactId>my-app</artifactId> 项目名，也用于生成的jar包名
  <packaging>jar</packaging> 默认为jar，后面详述
  <version>1.0-SNAPSHOT</version> 版本
  <!-- version：snapshot快照(开发版本)，alpha内部测试，beta公测，Release稳定，GA正式发布 ，大版本号.分支版本号.小版本号 0.0.1-->
  <name>my-app</name> 用于文档
  <url>http://maven.apache.org</url> 用于文档
  <dependencies>
    <dependency> 一个外部依赖
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <!-- 插件的使用范围，默认compile，即编译测试运行都有效-->
      <scope>test</scope>
      <!-- 排除传递依赖列表 -->
      <exclusions>
        <exclusion>
        </exclusion>
      <exclusions>
    </dependency>
  </dependencies>
  <!-- 依赖的管理，并不会被运行，不会被实际依赖，被定义在父模块中，供子类继承 -->
  <dependencyManagement>
    <dependencies>
        <dependency>
        </dependency>
    </dependencies>
  </dependcyManagement>
<!-- 继承 -->
<parent></parent>
<build>
  <!--执行package时生成到target目录下的名称 -->
	<finalName>a</finalName>
	<plugins>
		<plugin>
      ...插件
		</plugin>
	</plugins>
</build>
```

### 依赖冲突
1. 短路径优先
    + A->B->C->X(jar)
    + A->D->X(jar) 优先选用这个
2. 先声明优先
  如果路径长度相同，则选用声明在前面的

### 聚合
> 如果想一次构建两个项目，而不是到两个模块的目录下分别执行mvn命令，就要使用聚合，聚合就是专门解决该需求的。为了能够使用一条命令就能构建a和b两个模块，需要创建一个额外的base模块，然后通过该模块构建整个项目的所有模块。module中配置的是各个子模块的相对路径，如当前pom位于../Base/pom.xml，则聚合的俩个项目路径分别为../Base/a/及../Base/b。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.bundery</groupId>
	<artifactId>base</artifactId>
	<packaging>pom</packaging>
	<version>0.0.1-SNAPSHOT</version>
  <modules>
    <module>a</module>
    <module>b</module>
  </modules>
</project>
```

### 继承
> 当各个模块中都依赖某些共有jar时，如spring、common、log4j等等，这时可以新定义一个模块，作为所有模块的父模块，所有这些配置均在该新模块中配置，各个子模块只需继承就好了，这样就可以统一管理依赖jar的version等。由于dependency对于不同的模块依赖是不一样的，如果把各个模块所有的dependency都移到parent中的话，会导致所有的子模块都依赖父模块中依赖的所有jar，这是很不灵活的。所以父模块中使用dependencymManager管理依赖，在dependencyManagement元素下的依赖声明不会引入到实际的依赖，必须在子模块中显示声明dependency才会生效。为了方便也可以把聚合的配置直接写入到继承中。

父模块：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.bundery</groupId>
	<artifactId>base</artifactId>
	<packaging>pom</packaging>
	<version>0.0.1-SNAPSHOT</version>
  <properties>
    <spring.version>4.3.10.RELEASE</spring.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
			  <groupId>org.springframework</groupId>
			  <artifactId>spring-context</artifactId>
		  	<version>${spring.version}</version>
		  </dependency>
        ......
    </dependencies>
  </dependencyManagement>

  <modules>
    <module>a</module>
    <module>b</module>
  </modules>
</project>
```

子模块：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
  <parent>
	  <groupId>com.bundery</groupId>
	  <artifactId>base</artifactId>
	  <version>0.0.1-SNAPSHOT</version>
    <relativePath>../base/pom.xml</relativePath> 
  </parent>
  <artifactId>a</artifactId>

  <dependencies>
    <dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
		</dependency>
  </dependencies>
</project>
```

parent下的子元素groupId、artifactId和version指定了父模块的坐标，这三个元素是必须的，元素relativePath表示父模块POM的相对路径，当项目构建时，maven会首先根据relativePath检查父POM，如果找不到，再从本地仓库查找。relativePath的默认值是../pom.xml，也就是说，maven默认父POM在上一层目录下。。dependency只需配置groupId及artifactId而不需要配置version及scope，这样就能方便配置并可以统一管理版本。另外，groupId、version、properties、repositories、dependencies、dependcyManagement等元素都被子模块所继承。

## 构建周期
maven有三个内置周期，每个周期都由不同的步骤所组成。

- clean(清理target文件夹)
    + pre-clean：执行清理前的工作
    + clean：清理上次构建生成的所有文件
    + postclean：执行清理后的文件
- default(默认：构建项目)
    + validate：校验
    + test：单元测试
    + package：打包成jar或war等到本项目的target目录下
    + integration-test：集成测试
    + verify：校验
    + install：部署到本地代码库，供其他项目本地调用
    + deploy：部署到远程代码库，供他人或项目远程调用
- site(生成项目站点)
    + pre-site 在生成项目站点前要完成的工作
    + site 生成项目的站点文档
    + post-site 在生成项目站点后要完成的工作
    + site-deploy 发布生成的站点到服务器上

### 插件
某个步骤的执行实际上是调用某些plugin的goal，一个周期步骤可以关联多个插件goal。

1. 内置关联的goal

|          步骤          |               plugin的goal               |
| ---------------------- | ---------------------------------------- |
| process-resources      | resources:resources                      |
| compile                | compiler:compile(插件可以有多个goal)     |
| process-test-resources | resources:testResources                  |
| test-compile           | compiler:testCompile(插件可以有多个goal) |
| test                   | surefire:test                            |
| package                | jar:jar                                  |
| install                | install:install                          |
| deploy                 | deploy:deploy                            |

2. 手动关联goal

添加插件，并把插件的goal关联到特定步骤，goal本身都有个默认相关联的步骤，也可以关联到多个步骤。

```xml
<build>
    <plugins>
		<plugin>
			<groupId>org.apache.tomcat.maven</groupId>
			<artifactId>tomcat7-maven-plugin</artifactId>
			<version>2.2</version>
			<executions>
				<execution>
					<phase>test</phase>
					<goals>
						<goal>redeploy</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
	</plugins>
</build>
```
