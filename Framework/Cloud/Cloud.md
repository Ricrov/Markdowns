# Spring Cloud
#### 整体配置
> `gradle init` 定义当前路径为 Gradle 工程, 自动生成配置文件

- `settings.gradle` 中引入创建的模块
  ```java
  rootProject.name = 'cloud-demo'
  include ':config-module',
        ':eureka-server',
        ':user-module',
        ':user-ribbon-module'
  ```

#### 根目录 `build.gradle` 配置
- 配置镜像源
```java
repositories {
        // 配置阿里镜像
        maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
        mavenCentral()
        // SpringCloud 组件镜像源
        maven { url "https://repo.spring.io/milestone" }
    }
```

- `allprojects {}` 中的引入是全局生效的
  - 配置镜像源
  ```java
  repositories {
      maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
      mavenCentral()
      maven { url "https://repo.spring.io/milestone" }
  }
  ```

  - 引入插件
  ```java
  apply plugin: 'java'
  apply plugin: 'idea'
  apply plugin: 'org.springframework.boot'
  apply plugin: 'io.spring.dependency-management'
  ```

  - ext 声明变量
    - springCloud 版本
  ```java
  ext {
        springCloudVersion = 'Greenwich.M1'
  }
  ```

  - 依赖包: 全局生效
  ```java
  dependencies {
      runtimeOnly('org.springframework.boot:spring-boot-devtools')
      compileOnly('org.projectlombok:lombok')
      testImplementation('org.springframework.boot:spring-boot-starter-test')
  }
  ```

####  模块中的配置
`config-module` 等模块中的 build.gradle 是针对此模块的依赖

## 启动
##### config-module
依赖: `implementation('org.springframework.cloud:spring-cloud-config-server')`
用来统一管理所有模块的配置
- application.yml
```java
server:
  port: 8760
spring:
  cloud:
    config:
      server:
        native:
          // 去哪个路径搜索配置文件, 其中的配置文件是给其他模块用的
          search-locations: classpath:/shared
  profiles:
    active: native
  application:
    // 配置的模块起名
    name: config-module
```

#### user-module
`bootstrap.yml` 此配置文件会在 application.yml 之前加载
```java
spring:
  application:
    // 必须和 application.yml 中搜索的配置文件同名
    name: user-module
  cloud:
    config:
      uri: http://localhost:8760
      fail-fast: true
  profiles:
    active: dev
```

- 关于依赖`implementation('org.springframework.cloud:spring-cloud-starter-config')`




## Eureka
#### 模块管理
#### 负载均衡
当有多台服务器可以提供同一种服务时, 可以从每个端口取出不同的数据以减轻服务器负担



## 创建模块
- 创建 Directory: order-module
src/main/java
src/main/resouces

- 复制一份 build.gradle 至模块根目录
- settings.gradle 增加 include ':order-module'
