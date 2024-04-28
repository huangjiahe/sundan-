# OpenFeign介绍

在Spring Cloud中，OpenFeign是一个声明式的HTTP客户端，它的作用是简化HTTP客户端的开发，特别是用于与RESTful API进行通信。相比于RestTemplate，OpenFeign的优势在于：

- 更加简洁的代码：使用OpenFeign，您只需要定义一个接口，而不需要写任何实现代码。在接口上使用注解即可描述API的细节，OpenFeign会自动生成实现类并处理所有的HTTP细节。
- 更好的可读性和可维护性：OpenFeign使用注解来描述API，这使得代码更加易于阅读和理解。此外，OpenFeign的接口和实现分离，使得代码更加易于维护。
- 内置的负载均衡：OpenFeign内置了负载均衡功能，可以与Spring Cloud的服务发现组件（如Nacos）集成，从而自动地选择合适的服务实例进行请求。这使得在分布式系统中进行服务调用更加容易。
- 更加灵活的扩展性：OpenFeign是高度可配置的，可以通过自定义的编码器和解码器来处理不同的请求和响应格式。同时，OpenFeign还支持自定义的拦截器来实现更加复杂的请求和响应处理逻辑。

# 如何使用OpenFeign

## 在消费者端引用OpenFeign

### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

### 增加@EnableFeignClients注解

```java
@SpringBootApplication
@ComponentScan(basePackages = {"cn.sundan.**"})
@Slf4j
@EnableDiscoveryClient
@EnableSwagger2
@EnableAsync
@EnableScheduling
@EnableFeignClients(basePackages = {"cn.sundan.**"})
public class SundanWarehouseApplication {
    public static void main(String[] args) {
        log.info("******** SundanWarehouseApplication Start ... ********");
        SpringApplication.run(SundanWarehouseApplication.class,args);
        log.info("******** SundanWarehouseApplication end ... ********");
    }
}

```

### 声明接口

```java
/**
*  家电医院评价、售后的远程调用
*  标注表示该接口是对名为"sundan-hospital"的服务进行调用，这个服务名称通常是在配置文件中指定的，用于实现服务发现。
* */
@FeignClient(name = "sundan-hospital",configuration = FeignConfig.class)
public interface HospitalEvaluateFeign {

    /**
     *  去评价
     * */
    @GetMapping("app/hospital/goodsEvaluation/toEvaluation")
    CommonResult toEvaluation(@RequestParam long orderId, @RequestParam Integer type, @RequestParam Boolean isWareHouse);
    
    /**
    *  发表评价
    * */
    @PostMapping("app/hospital/goodsEvaluation/publishGoodsEvaluation")
    CommonResult pushEvaluation(@RequestBody List<WareHouseEvaluateScoreDto> dto, @RequestParam Boolean isWareHouse);
    
    
    /**
    *  小程序-申请售后
    * */
    @PostMapping("app/hospital/afterSale/submitorderAfterSale")
    CommonResult submitOrderAfterSale(@RequestBody AppOrderAfterSaleDto dto, @RequestParam Boolean isWareHouse);
    
    /**
    *  后台-审核
    * */
    @PostMapping("mgn/hospital/orderAfterSales/audit")
    CommonResult audit(@RequestBody WOrderAfterSaleAuditDto dto,@RequestParam Boolean isWareHouse);
    
    /**
    *  后台-退款
    * */
    @PostMapping("mgn/hospital/orderAfterSales/wRefundOrder")
    CommonResult<Object> wRefundOrder(@RequestBody PaymentAllDto dto);
  
  	/**
    *  路径参数
    * */
  	@PostMapping("/user/{uid}")
    User createUser(@PathVariable("uid") String uid, @RequestBody User user);
}
```

# OpenFeign注解说明

| 注解         | 作用对象     | 用途                                                         |
| ------------ | ------------ | ------------------------------------------------------------ |
| @RequestLine | Method       | 定义请求的HttpMethod和UriTemplate。在花括号 {expression} 中的值将使用对应的@Param注释参数进行解析。 |
| @Param       | Parameter    | 定义一个模板变量，其值将用作通过注释值提供的名称解析相应的模板表达式。如果值不存在，则尝试从字节码方法参数名称获取名称（如果使用了-parameters标志编译代码）。 |
| @Headers     | Method, Type | 定义HeaderTemplate；这是UriTemplate的变体，它使用@Param注释值来解析相应的表达式。当用于Type时，模板将应用于每个请求。当用于Method时，模板仅适用于带注释的方法。 |
| @QueryMap    | Parameter    | 定义一组名称值对或POJO，以扩展成查询字符串。                 |
| @HeaderMap   | Parameter    | 定义一组名称值对，以扩展成Http Headers。                     |
| @Body        | Method       | 定义Template，类似于UriTemplate和HeaderTemplate，它使用@Param注释值来解析相应的表达式。 |

# 可配置项列表

| Name                                                         | Default                                       |
| ------------------------------------------------------------ | --------------------------------------------- |
| spring.cloud.openfeign.autoconfiguration.jackson.enabled     | FALSE                                         |
| spring.cloud.openfeign.circuitbreaker.enabled                | FALSE                                         |
| spring.cloud.openfeign.circuitbreaker.group.enabled          | FALSE                                         |
| spring.cloud.openfeign.client.config                         |                                               |
| spring.cloud.openfeign.client.decode-slash                   | TRUE                                          |
| spring.cloud.openfeign.client.default-config                 | default                                       |
| spring.cloud.openfeign.client.default-to-properties          | TRUE                                          |
| spring.cloud.openfeign.client.refresh-enabled                | FALSE                                         |
| spring.cloud.openfeign.compression.request.enabled           | FALSE                                         |
| spring.cloud.openfeign.compression.request.mime-types        | [text/xml, application/xml, application/json] |
| spring.cloud.openfeign.compression.request.min-request-size  | 2048                                          |
| spring.cloud.openfeign.compression.response.enabled          | FALSE                                         |
| spring.cloud.openfeign.encoder.charset-from-content-type     | FALSE                                         |
| spring.cloud.openfeign.httpclient.connection-timeout         | 2000                                          |
| spring.cloud.openfeign.httpclient.connection-timer-repeat    | 3000                                          |
| spring.cloud.openfeign.httpclient.disable-ssl-validation     | FALSE                                         |
| spring.cloud.openfeign.httpclient.enabled                    | TRUE                                          |
| spring.cloud.openfeign.httpclient.follow-redirects           | TRUE                                          |
| **spring.cloud.openfeign.httpclient.hc5.enabled**            | **FALSE**                                     |
| **spring.cloud.openfeign.httpclient.hc5.pool-concurrency-policy** |                                               |
| **spring.cloud.openfeign.httpclient.hc5.pool-reuse-policy**  |                                               |
| **spring.cloud.openfeign.httpclient.hc5.socket-timeout**     | **5**                                         |
| **spring.cloud.openfeign.httpclient.hc5.socket-timeout-unit** |                                               |
| spring.cloud.openfeign.httpclient.max-connections            | 200                                           |
| spring.cloud.openfeign.httpclient.max-connections-per-route  | 50                                            |
| spring.cloud.openfeign.httpclient.ok-http.read-timeout       | 60s                                           |
| spring.cloud.openfeign.httpclient.time-to-live               | 900                                           |
| spring.cloud.openfeign.httpclient.time-to-live-unit          |                                               |
| spring.cloud.openfeign.micrometer.enabled                    | TRUE                                          |
| spring.cloud.openfeign.oauth2.enabled                        | FALSE                                         |
| spring.cloud.openfeign.oauth2.load-balanced                  | FALSE                                         |
| spring.cloud.openfeign.okhttp.enabled                        | FALSE                                         |

# 使用HttpClient 5替换URLConnection

OkHttpClient和Apache HttpClient 5的Feign客户端可以通过将spring.cloud.openfeign.okhttp.enabled或spring.cloud.openfeign.httpclient.hc5.enabled设置为true来使用，并将它们放在类路径上。您可以通过提供org.apache.hc.client5.http.impl.classic.CloseableHttpClient类型的bean来自定义所使用的HTTP客户端，当使用Apache HC5时。

```xml
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-hc5</artifactId>
</dependency>
spring.cloud.openfeign.httpclient.hc5.enabled=true
```

您还可以通过在spring.cloud.openfeign.httpclient.xxx属性中设置值来进一步自定义http客户端。以httpclient为前缀的那些属性将适用于所有客户端，以httpclient.hc5为前缀的那些属性将适用于Apache HttpClient 5，以httpclient.okhttp为前缀的那些属性将适用于OkHttpClient。您可以在附录中找到可自定义的完整属性列表。

从Spring Cloud OpenFeign 4开始，不再支持Feign Apache HttpClient 4。我们建议使用Apache HttpClient 5代替。

```markdown
在Java开发中，使用HTTP客户端库来执行网络请求是一种常见的需求。`URLConnection` 是Java标准库中提供的一个用于执行HTTP请求的基础类，而 `httpClient5`（如 `feign-hc5` 中使用的Apache HttpClient 5）则是一个更现代、功能更丰富的HTTP客户端库。下面我将对比这两者的一些主要优劣点：

### 1. 功能丰富性

- **HttpClient 5**:
  - 支持HTTP/1.1和HTTP/2。
  - 提供了更灵活的配置选项，如连接管理、连接池、超时设置等。
  - 支持自动重试和请求重定向。
  - 内置支持多种身份验证机制（如基础认证、摘要认证）。
  - 支持异步非阻塞通信。

- **URLConnection**:
  - 只支持基本的HTTP/1.1功能。
  - 配置选项有限，例如没有内置的连接池管理。
  - 没有内置的重试机制和较为基础的重定向处理。
  - 身份验证支持较为基本，可能需要手动处理。
  - 主要是同步阻塞通信，不支持异步操作。

### 2. 使用简便性

- **HttpClient 5**:
  - API设计现代，支持链式调用，使用起来更加方便和直观。
  - 需要额外添加依赖，对于简单的用途可能稍显繁琐。

- **URLConnection**:
  - 无需额外依赖，使用标准Java库即可完成。
  - API较为原始，使用时可能需要手动管理更多的细节（如错误处理、连接关闭等）。

### 3. 性能

- **HttpClient 5**:
  - 优化了性能，特别是在高并发和多线程环境下，通过连接池管理等高级功能提供更好的性能。
  - 支持异步模式可以更有效地利用系统资源。

- **URLConnection**:
  - 在性能上通常足够用于简单或低并发的应用，但在高并发场景下可能不如专门的HTTP客户端库。

### 4. 社区和维护

- **HttpClient 5**:
  - 由Apache维护，拥有活跃的开发和维护团队。
  - 社区支持广泛，容易找到解决方案和最佳实践。

- **URLConnection**:
  - 作为Java的一部分，由Oracle维护。
  - 社区支持相对有限，可能在处理复杂问题时寻找答案较为困难。

### 总结

选择使用 `httpClient5` 还是 `URLConnection` 取决于你的具体需求。如果你需要一个功能全面、性能优越并且可以灵活配置的HTTP客户端，`httpClient5` 是一个很好的选择。相反，如果你的应用非常简单，或者你不想引入外部依赖，`URLConnection` 可能会更适合。对于现代互联网应用，特别是需要处理高并发或者需要更复杂HTTP功能的情况，`httpClient5` 通常是更优的选择。
```

# 超时处理

我们可以在默认客户端和命名客户端上配置超时。OpenFeign使用两个超时参数：

- connectTimeout：可以防止由于长时间的服务器处理时间而阻塞调用方。
- readTimeout：从连接建立时开始应用，当返回响应时间过长时会触发它。

如果服务器未运行或不可用，则包会被拒绝连接。通信将以错误消息或回退方式结束。如果connectTimeout设置得非常低，这可能会在之前发生。执行查找并接收这样的数据包所需的时间构成了此延迟的重要部分。它取决于涉及DNS查找的远程主机，因此可能会发生变化。

# OpenFeign请求/响应压缩

您可以考虑为Feign请求启用请求或响应GZIP压缩。您可以通过启用以下属性之一来实现：

```properties
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.response.enabled=true
```

Feign请求压缩为您提供了类似于您为Web服务器设置的设置：

```properties
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json
spring.cloud.openfeign.compression.request.min-request-size=2048
```

- spring.cloud.openfeign.compression.request.enabled=true：启用OpenFeign客户端请求压缩，默认值为false。如果设置为true，则客户端将在请求时启用压缩。
- spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json：配置支持压缩的请求的MIME类型列表。默认情况下，Spring Cloud OpenFeign仅支持application/json类型的请求压缩。通过此配置，您可以添加其他MIME类型以启用压缩。
- spring.cloud.openfeign.compression.request.min-request-size=2048：配置压缩请求的最小字节数。如果请求大小小于此值，则不会进行压缩处理。默认值为2048字节。

这些配置允许您在使用Spring Cloud OpenFeign进行客户端请求时启用压缩功能，以减少网络传输量，从而提高应用程序性能。

由于OkHttpClient使用“透明”压缩，如果存在content-encoding或accept-encoding头，则禁用它，因此当feign.okhttp.OkHttpClient存在于类路径上并且spring.cloud.openfeign.okhttp.enabled设置为true时，我们不启用压缩。

# OpenFeign日志记录



每个创建的Feign客户端都会创建一个记录器。默认情况下，记录器的名称是用于创建Feign客户端的接口的完整类名。Feign日志记录仅对DEBUG级别做出响应。

```properties
logging.level.root=warn
logging.level.com.itlaoqi.consumerserviceopenfeign.feignclient=debug
```

Feign配置类(家电医院是FULL)

```java
package com.itlaoqi.consumerserviceopenfeign.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfiguration {
    @Bean
    Logger.Level feignLoggerLevel() {
    	return Logger.Level.BASIC;
    }
}
```

应用配置类

@FeignClient(value = "provider-service",configuration = FeignConfiguration.class)

```java
package com.itlaoqi.consumerserviceopenfeign.feignclient;

import com.itlaoqi.consumerserviceopenfeign.config.FeignConfiguration;
import com.itlaoqi.consumerserviceopenfeign.dto.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@FeignClient(value = "provider-service",configuration = FeignConfiguration.class)
public interface ProviderServiceFeignClient {
    @GetMapping("/hello")
    public String hello();

    @PostMapping("/user/{uid}")
    User createUser(@PathVariable("uid") String uid, @RequestBody User user);

    @GetMapping("/list") //?page=xxx&rows=xxx
    List<User> query(@RequestParam("page") int page, @RequestParam("rows") int rows);

}
```

您可以每个客户端配置Logger.Level对象，告诉Feign要记录多少。选择如下：

- NONE，无日志记录（默认）。
- BASIC，仅记录请求方法、URL、响应状态代码和执行时间。
- HEADERS，记录基本信息以及请求和响应头。
- FULL，记录请求和响应的头、正文和元数据。

例如，以下内容将Logger.Level设置为FULL：

```java
@Configuration
public class FeignConfiguration {
    @Bean
    Logger.Level feignLoggerLevel() {
    	return Logger.Level.FULL;
    }
}
```

- Full

```plain
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] ---> POST http://provider-service/user/10 HTTP/1.1
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] Content-Length: 86
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] Content-Type: application/json
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] 
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] {"uid":"10","username":"testUser","password":"testPassword","nickname":"testNickname"}
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] ---> END HTTP (86-byte body)
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] <--- HTTP/1.1 200  (106ms)
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] connection: keep-alive
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] content-type: application/json
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] date: Thu, 30 Mar 2023 08:23:26 GMT
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] keep-alive: timeout=60
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] transfer-encoding: chunked
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] 
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] {"uid":"10","username":"testUser","password":"testPassword","nickname":"testNickname"}
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] <--- END HTTP (86-byte body)
```

- HEADERS

```plain
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] ---> POST http://provider-service/user/10 HTTP/1.1
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] Content-Length: 86
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] Content-Type: application/json
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] ---> END HTTP (86-byte body)
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] <--- HTTP/1.1 200  (94ms)
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] connection: keep-alive
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] content-type: application/json
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] date: Thu, 30 Mar 2023 08:26:12 GMT
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] keep-alive: timeout=60
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] transfer-encoding: chunked
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] <--- END HTTP (86-byte body)
```

- BASIC

```plain
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] ---> POST http://provider-service/user/10 HTTP/1.1
[p-nio-80-exec-1] c.i.c.f.ProviderServiceFeignClient       : [ProviderServiceFeignClient#createUser] <--- HTTP/1.1 200  (89ms)
```

- NONE

无

# @FeignClient转发到URL

| Example                                                      | Details                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| @FeignClient(name="testClient", url="http://localhost:8081") | 该注解的url属性提供了Feign客户端调用的目标服务地址，而不涉及负载均衡。不使用配置属性中提供的URL。 |
| @FeignClient(name="testClient", url="...") and the property defined in application.yml as spring.cloud.openfeign.client.config.testClient.url=http://localhost:8081 | 如果配置属性中提供了URL，那么Feign客户端会直接使用该URL，也不会涉及负载均衡。 |
| @FeignClient(name="testClient") and the property defined in application.yml as spring.cloud.openfeign.client.config.testClient.url=http://localhost:8081 | 如果没有在注解中指定url属性，那么Feign客户端将会使用配置属性中提供的URL进行服务调用，同样不涉及负载均衡。如果设置了spring.cloud.openfeign.client.refresh-enabled=true，那么在Spring RefreshScope Support中描述的情况下，可以刷新配置属性中定义的URL。 |
| @FeignClient(name="testClient")                              | 如果注解中指定了name属性，那么Feign客户端将使用服务发现（例如Nacos）获取服务的地址列表，并使用负载均衡算法选择一个服务地址进行调用。 |

# 面试题

~~~markdown
### 2. Feign是什么？
Feign是一个声明式的Web服务客户端，它使得编写Web服务客户端变得更加简单。它的目标是通过为服务调用提供一个简洁的模板来减少编码的复杂度。Feign通过注解来配置请求，从而使得接口的定义足以表达客户端的意图。

### 3. OpenFeign是什么？
OpenFeign（或Spring Cloud OpenFeign）是Spring Cloud在Netflix Feign的基础上支持的一个开源项目。它不仅扩展了Feign的功能，还使其更容易集成到Spring Boot应用中。OpenFeign为服务调用提供了一种更简单的方法，支持Spring MVC的注解，并且能够与Eureka等服务发现组件自然集成。

### 4. Feign和OpenFeign有什么区别？
- **Feign** 是Netflix开发的，主要用于简化HTTP API客户端的开发过程。
- **OpenFeign** 是Spring Cloud对Feign的扩展，它提供了对Spring MVC注解的支持，并且与Spring Ecosystem（如服务发现、配置管理）更好地集成。

### 5. 环境准备
要开始使用OpenFeign，你需要：
- Java 8或更高版本
- Spring Boot
- Maven或Gradle作为构建工具
- 添加Spring Cloud OpenFeign的依赖到你的项目中

### 6. 创建服务提供者
服务提供者是一个简单的Spring Boot应用，它提供一些可通过网络调用的API。例如，一个REST控制器，它提供一个GET方法。

### 7. 创建服务消费者
服务消费者同样是一个Spring Boot应用，它会使用OpenFeign来调用服务提供者的API。你需要定义一个Feign客户端接口，使用Spring MVC的注解来映射服务提供者的端点。

### 8. OpenFeign如何传参？
使用Feign传参，可以通过`@RequestParam`, `@PathVariable`, `@RequestHeader`等注解在接口方法的参数上定义，与使用Spring MVC时类似。

### 9. 超时如何处理？
在`application.properties`或`application.yml`中配置连接和读取超时：
```properties
# application.properties
spring.cloud.openfeign.httpclient.connection-timeout=2000
spring.cloud.openfeign.httpclient.read-timeout=3000
```

### 10. 如何开启日志增强？
在你的Feign配置中，可以设置日志级别：
```java
@Configuration
public class FeignConfig {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```
并在`application.properties`中设置日志级别：
```properties
logging.level.<your-feign-client-package>=DEBUG
```

### 11. 如何替换默认的httpclient？
通过创建一个`Client` bean来替换默认的HTTP客户端，例如使用OkHttp：
```java
@Bean
public Client feignClient() {
    return new OkHttpClient();
}
```

### 12. 如何通讯优化？
- 使用连接池
- 设置合适的超时
- 使用异步请求（如果支持）

### 13. 如何熔断降级？
使用Hystrix或Resilience4j来实现熔断和降级：
```java
@FeignClient(name = "stores", fallback = StoreFallback.class)
public interface StoreClient {
    // 定义方法
}

@Component
class StoreFallback implements StoreClient {
    // 降级方法实现
}
```
这些步骤可以帮助你在使用OpenFeign时构建一个健壮和高效的服务调用链。
~~~

```java
/**
 * 降级方法
 *
 * @ClassName AuthFeign
 * @Description TODO
 * @Author JingH Pu
 * @Data 2022/7/18 9:33
 */
@FeignClient(name = "sundan-auth",
        configuration = FeignConfig.class,
        fallback = AuthFeignFallBack.class,
        path = "/oauth")
public interface AuthFeign {

    @PostMapping(value = "/token",
            consumes = {MediaType.APPLICATION_FORM_URLENCODED_VALUE})
    CommonResult<Oauth2TokenDto> postAccessToken(Map<String, ?> queryParam, @RequestHeader(value = "client_id") String clientId);
    
    
/**
 * @ClassName AuthFeignFallBack
 * @Description TODO
 * @Author JingH Pu
 * @Data 2022/7/18 14:15
 */
@Component
public class AuthFeignFallBack implements AuthFeign {

    @Override
    public CommonResult<Oauth2TokenDto> postAccessToken(Map<String, ?> queryParam,String clientId) {
        return new CommonResult<>(ResultCode.REQUEST_FAIL.getCode(),ResultCode.REQUEST_FAIL.getMessage());
    }
}    
```

# httpClient5的连接池

~~~markdown
Apache HttpClient 5 是 Apache HttpComponents 项目的一部分，提供了丰富的 HTTP 客户端实现，支持 HTTP/1.1 和 HTTP/2。HttpClient 5 提供了对连接池的内置支持，这对于提升应用性能和有效管理多个并发HTTP连接非常有用。

### 连接池的基本概念
连接池允许HTTP客户端重用现有的连接而不是每次请求时都建立新连接，这可以显著减少建立连接的开销，提高了应用程序的性能，尤其是在高负载和高并发的情况下。

### 配置HttpClient 5的连接池
在使用HttpClient 5时，你可以通过`PoolingHttpClientConnectionManager`来配置连接池。以下是一个基本的示例，展示如何设置和使用连接池：

```java
import org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManager;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.client5.http.classic.HttpClient;
import org.apache.hc.core5.http.io.entity.EntityUtils;
import org.apache.hc.client5.http.classic.methods.HttpGet;
import org.apache.hc.client5.http.classic.HttpResponse;

public class HttpClientExample {
    public static void main(String[] args) throws IOException {
        // 创建连接池管理器
        PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager();
        // 设置最大连接数
        cm.setMaxTotal(100);
        // 设置每个路由的默认最大连接
        cm.setDefaultMaxPerRoute(20);

        // 创建HttpClient
        HttpClient httpClient = HttpClients.custom()
                .setConnectionManager(cm)
                .build();

        try {
            // 创建HttpGet请求
            HttpGet httpget = new HttpGet("http://example.com");
            // 执行请求
            HttpResponse response = httpClient.execute(httpget);
            // 打印响应内容
            System.out.println(EntityUtils.toString(response.getEntity()));
        } finally {
            // 释放资源
            httpClient.close();
        }
    }
}
```

### 关键配置选项
- **setMaxTotal(int max)**: 设置连接池中的最大连接数。
- **setDefaultMaxPerRoute(int max)**: 设置每个路由的默认最大连接数，路由是指运行在特定位置（如同一个服务器）的一组连接。

### 使用连接池的优势
- **性能提升**: 重用连接减少了连接和断开连接的频繁开销。
- **资源优化**: 控制并发连接数，避免过多的连接导致资源耗尽。
- **更加健壮**: 在高并发环境下，连接池提供了更好的稳定性和响应速度。

配置和使用连接池是提高HTTP客户端性能的关键策略之一，尤其是对于需要高频率执行HTTP请求的应用程序而言。
~~~

