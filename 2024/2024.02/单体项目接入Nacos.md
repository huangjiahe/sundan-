# 单体项目接入Nacos

## pom.xml文件引入nacos相关依赖

```xml
<!--原本的xml中的springboot版本-->
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
<version>2.3.2.RELEASE</version>
```

通过查阅[spring-cloud-alibaba版本管理规范](https://github.com/alibaba/spring-cloud-alibaba/blob/2022.x/README-zh.md)确定需要引入的spirng-cloud-alibaba版本为2.2.x。引入nacos的相关依赖

```xml
<dependency>
 <groupId>com.alibaba.cloud</groupId>
 <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
 <groupId>com.alibaba.cloud</groupId>
 <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

------

## SpringBoot微服务向Nacos注册

### 定义配置文件

```yaml
bootstrap.yaml

spring:
  profiles:
    active: dev
  cloud:
    nacos:
      config:
        shared-configs[0]:
          data-id: application.yaml
          refresh: true
```

```yaml
bootstrap-dev.yaml

spring:
  profiles:
    active: dev
  application:
    name: cto-framework-standard-web
  cloud:
    nacos:
      config:
        server-addr: ${spring.cloud.nacos.server-addr}
        username: nacos
        password: password
        namespace: 3944c638-5540-41e9-a69c-96e5396b62xx
        file-extension: yaml
        refresh-enabled: true
      server-addr: 127.0.0.1:8848
```

#### 配置文件加载规则：

在 Spring Cloud 的应用中，`bootstrap.yaml`（或`bootstrap.properties`）和`application.yaml`（或`application.properties`）是两个关键的配置文件，它们在应用启动时都会被加载，但是它们的加载时机和用途有所不同：

1. **bootstrap.yaml**:
    - 这个文件是在应用程序上下文的正常配置之前加载的，也就是说，它在`application.yaml`之前加载。
    - `bootstrap.yaml`主要用于配置一些引导应用程序的基础设置，比如从配置中心加载配置的设置。
    - 在 Spring Cloud 应用中，`bootstrap.yaml`常用于配置和初始化与Spring Cloud相关的组件，比如服务发现和配置中心（如Eureka、Consul、Nacos等）。

2. **application.yaml**:
    - 这个文件包含了应用程序主要的配置信息。
    - 它在Spring Boot应用启动时被加载，用于配置应用级别的各种属性，如数据库连接、自定义配置等。
    - 如果你的应用在不同环境中运行（如开发环境、测试环境、生产环境），你可能需要为每个环境创建不同的`application-{profile}.yaml`文件，例如`application-dev.yaml`、`application-test.yaml`和`application-prod.yaml`。

在你提供的例子中：

- `bootstrap.yaml` 配置了从 Nacos 配置中心加载配置的基础信息。它指定了一个`shared-configs`配置项，这意味着将加载一个共享的配置文件`application.yaml`，并且此配置文件的改动会触发应用刷新。

- `bootstrap-dev.yaml` 看起来像是一个环境特定的引导配置文件，用于开发环境。它不仅设置了应用程序名称，而且指定了 Nacos 配置中心的具体连接信息，如服务器地址、命名空间、用户名和密码等。这样，在开发环境中，应用程序会使用这些特定的配置来连接 Nacos 服务。

通常，你不需要在同一个应用中同时有 `bootstrap.yaml` 和 `bootstrap-dev.yaml`。你可以只使用 `bootstrap.yaml` 并为不同的环境配置不同的 Profile-specific 引导配置文件，如 `bootstrap-dev.yaml`, `bootstrap-test.yaml`, `bootstrap-prod.yaml` 等。这样一来，每个环境都可以有专门的配置，如不同的服务地址或资源限制。

在实际应用中，你可以选择其中一种方式来组织你的配置：

- **单一`bootstrap.yaml`**: 包含所有环境共享的配置，并使用`application-{profile}.yaml`来定义特定环境的配置。
- **Profile-specific的`bootstrap-{profile}.yaml`**: 为每个环境创建一个特定的引导配置文件，并在其中定义该环境特有的配置。

根据你的项目需要，选择最适合的配置方式。如果你的`bootstrap-dev.yaml`文件中的配置是开发环境特有的，并且这些配置必须在应用程序上下文初始化之前读取，那么保留`bootstrap-dev.yaml`是有意义的。记得在不同的环境中，通过设置环境变量`SPRING_PROFILES_ACTIVE`来激活相应的配置文件。

#### bootstrap.yaml的active: dev引导的是哪个文件?

在 Spring Boot 应用中，`bootstrap.yaml` 或 `bootstrap.properties` 文件中的 `spring.profiles.active` 属性用于激活特定的配置文件（profile）。当设置了 `spring.profiles.active: dev`，Spring Boot 将会查找并加载与激活的 profile 相关的配置文件。

所以，如果你在 `bootstrap.yaml` 中有这样的配置：

```yaml
spring:
  profiles:
    active: dev
```

Spring Boot 会尝试加载以下文件中的配置（以及 `bootstrap.yaml` 中的配置）：

- `application-dev.properties` 或 `application-dev.yaml`（对于 `application` 配置）
- `bootstrap-dev.properties` 或 `bootstrap-dev.yaml`（对于 `bootstrap` 配置）

这里的 `-dev` 后缀对应于激活的 profile 名称。Spring Boot 会合并 `bootstrap.yaml`、`bootstrap-dev.yaml` 和 `application.yaml`、`application-dev.yaml` 中的配置。如果有重叠的配置项，profile-specific 的文件（例如 `*-dev.yaml`）中的配置将覆盖掉默认的配置。

请注意，`bootstrap.yaml` 是在应用程序的早期启动阶段加载的，主要用于配置如 Spring Cloud Config Client 或其他需要在应用程序上下文完全启动之前读取的设置。而 `application.yaml` 或相应的 profile-specific 文件是在稍后加载的，用于配置应用程序的其它部分。

一旦配置中心（例如 Nacos、Consul、Spring Cloud Config Server 等）的连接信息被加载，Spring Boot 应用就可以从配置中心拉取相应环境的配置。这意味着，实际的配置数据可能并不完全存储在本地文件中，而是存储在配置中心服务上。

#### 不同配置源的情况下，配置的加载规则是如何定义的？

当使用Spring Cloud Config（或类似的配置中心，比如Nacos）时，通常有两种配置源：

1. 本地配置文件（如`bootstrap.yaml`、`application.yaml`及其环境特定的变体，例如`application-dev.yaml`等）。
2. 远程配置源（如Nacos配置中心、Spring Cloud Config Server等）。

加载配置的优先级由Spring Cloud和Spring Boot的环境抽象定义。在大多数情况下，远程配置源的优先级高于本地文件。这意味着，如果远程配置中心和本地文件中有同名的配置属性，远程配置中心中的值将覆盖本地文件中的值。

在启动过程中，首先加载`bootstrap.yaml`中的配置，它用于初始化应用程序上下文，包括连接到配置中心的必要信息。如果在`bootstrap.yaml`中指定了远程配置中心，并且配置了profile为`dev`，它将尝试连接到远程配置中心并获取对应`dev`环境的配置。

如果远程配置中心连接成功，它将拉取远程配置并与本地配置合并。合并规则通常是远程配置覆盖本地配置，但是这可以通过设置不同的属性源优先级来改变。

在你的例子中，当`bootstrap.yaml`中设置了profile为`dev`时，应用程序会尝试从Nacos加载与`dev`相关的配置。如果Nacos中确实存在`dev`相关的配置，它们将会覆盖本地文件中的同名设置。

总结来说，如果你的应用程序配置了远程配置源（比如Nacos），并且远程配置源可用且包含了相应的配置数据，则远程配置源中的配置将覆盖本地文件中的同名配置。

------

### 启动类增加@EnableDiscoveryClient注解

```java
@EnableScheduling
@EnableTransactionManagement
@Import(FdfsClientConfig.class)
@EnableMBeanExport(registration = RegistrationPolicy.IGNORE_EXISTING)
@EnableCaching
@SpringBootApplication
@EnableDiscoveryClient
//@ComponentScan(basePackages = {"com.eims.cto.framework.**","sundan.rabbitmq.**"})
public class DistApp extends SpringBootServletInitializer {

	static Logger logger = LoggerFactory.getLogger(DistApp.class);

	public static void main(String[] args) {
		logger.info("******** DistApp Start ... ********");
		SpringApplication.run(DistApp.class, args);
		logger.info("******** DistApp End ... ********");
	}
	
	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(this.getClass());
	}

}
```

