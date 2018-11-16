# 初识 Maven

#### Maven 是什么

- 工具: 帮助我们查找相应的 jar 包, 以及依赖包, 并且关联到项目当中
- 框架: 通过配置文件查找相应 jar 包, 形成依赖

#### Maven 安装
- apache-maven-3.5.4-bin.zip
- http://maven.apache.org/download.cgi

配置环境变量：
`vi ~/.zshrc`
添加语句：
`export PATH=${PATH}:/Users/dllo/documents/apache-maven-3.5.4/bin/`

让环境变量生效：
`source .zshrc`

测试：
`mvn -version`


#### Maven 使用

案例: 使用 junit 测试包

junit: 测试工具

正常情况下, 程序都是从 main 方法开始.
使用 junit 测试包可以把程序的任意位置作为入口.


#### 新建 maven 项目

- GroupId: 组id, com.ric.maven.nas
- ArtifactId: 项目名称, testMaven
- version: 版本号, 区别不同版本
- 新建工程时可以配置字段 `Name: archetypeCatalog` `Value: internal` 以避免 maven 自检, 加速
- 配置文件 pom.xml 中, 配置所需的 jar
- https://mvnrepository.com/

```xml
	<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

注意: 一个 dependency 就是一个依赖的 jar 包环境
```

- Maven 原理
	- 配置文件 pom.xml
		Maven 是基于工程对象的模型, 即 Project Object Model [POM]

	- 资源库
		>只要在 pom.xml 中配置了 jar 环境, 首先查找本地资源库, 若没有, 就回查询中央资源库. 绝大多数情况下, 中央资源库可以满足各种需求. 特殊情况可以自己配置远程资源库.

		- 本地资源库: `~/.m2 文件夹` 用于存放所需要的 jar 包
		- 中央资源库: `http://repo1.maven.org/maven2(http://search.maven.org)`
		- 远程资源库:

		```
		<repositories>
            <repository>
                <id>java.net</id>
                <url>https://maven.java.net/content/repositories/public/</url>
            </repository>
        </repositories>
		```

		- 使用 maven 模板, 使用不同的模板, 仅仅是布局风格不用和依赖有所差别, 都可以通过更改配置文件 pom.xml 进行设置
			- Maven-archetype-quickstart SE项目
			- Maven-archetype-webapp WEB项目

		- 使用 Maven 创建普通的 SE 项目, 访问数据库 bookstore, 查看所有的图书并打印
