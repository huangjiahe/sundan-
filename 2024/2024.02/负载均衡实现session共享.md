# Session,Cookie,Token的区别

## 有状态协议和无状态协议的区别？

### 有状态协议

有状态协议是指在通信过程中，<u>服务器会保持客户端状态信息</u>。这意味着服务器需要存储关于客户端请求的历史信息，以便于连续的请求之间可以相互关联。服务器依赖这些信息来维护会话状态。

**特点**：

- **会话状态**：服务器会跟踪客户端的状态，每次交互都可能依赖于之前的交互。
- **资源消耗**：由于需要存储和管理状态信息，有状态协议通常会消耗更多的服务器资源。
- **复杂性**：服务器必须能够管理和同步状态信息，这可能会增加系统的复杂性。
- **容错性**：如果服务器失败，持久的会话状态可能会丢失，除非采取了持久化或冗余措施。

**示例**：

- FTP（文件传输协议）：在一个FTP会话中，服务器会保持有关用户状态的信息，如当前目录、认证状态等。
- Telnet：每个会话都是独立的，服务器需要跟踪每个客户端的会话状态。

### 无状态协议

无状态协议是指服<u>务器不会保留任何客户端的状态信息</u>。每次请求都被视为一个新的请求，不依赖于之前的请求或会话。服务器不会存储任何与请求相关的状态信息。

**特点**：

- **独立请求**：每个请求都是独立的，服务器不需要知道之前发生了什么。
- **资源消耗少**：服务器不需要存储状态信息，通常会消耗较少的资源。
- **简单**：服务器处理逻辑通常更简单，不需要跟踪客户端状态。
- **可伸缩性**：由于不需要同步状态信息，无状态协议通常更容易扩展。

**示例**：

- HTTP（超文本传输协议）：HTTP是无状态的，每个请求都是独立的，服务器不会保存之前的请求信息。
- DNS（域名系统）：解析域名到IP地址的请求是独立的，不需要了解任何历史状态。

## 为什么存在Cookie和Session？

在无状态的HTTP协议中，`Cookie`和`Session`是两种实现客户端和服务器之间维持状态的技术。它们弥补了HTTP无状态的不便。

## Cookie和Session的区别?

尽管Cookie和Session都被用来保存用户与服务器之间的状态信息，但它们的工作方式和使用场景存在显著差异。以下是Cookie和Session的一些主要区别：

### 存储位置

- **Cookie**：数据存储在客户端（通常是用户的浏览器）。浏览器有限制每个域名下Cookie的数量和大小（一般是每个域名下50个Cookie，每个Cookie约4KB）。
- **Session**：数据存储在服务器端。通常情况下，服务器端没有对Session数据量的限制，这取决于服务器的内存或者Session数据的持久化策略。

### 生命周期

- **Cookie**：可以设置Cookie的过期时间。如果不设置，它们通常是会话级别的，即当浏览器关闭时消失（会话Cookie）。如果设置了过期时间，Cookie可以持久存储在客户端，即使浏览器关闭也不会消失（持久化Cookie）。
- **Session**：Session的生命周期由服务器控制，通常是基于一定时间的不活动。一旦用户长时间未与网站交互，Session就会过期。

### 安全性

- **Cookie**：因为存储在客户端，所以安全性较低。Cookie数据容易被用户查看甚至篡改，如果存储敏感信息则需要进行加密处理。
- **Session**：更安全，因为数据储存在服务器端，用户无法直接访问。

### 性能

- **Cookie**：每次HTTP请求都会携带Cookie数据，这可能会增加请求的负载，但因为数据存储在客户端，服务器不需要维护状态，所以服务器性能消耗较小。
- **Session**：只需要传输一个会话标识符，这使得HTTP请求的额外负载很小，但是服务器需要花费资源来维护状态，尤其是在并发量大的应用中。

### 扩展性

- **Cookie**：因为数据存储在客户端，所以当应用需要扩展到多台服务器时，不需要特别的配置。
- **Session**：默认情况下，Session绑定在一台服务器上。在负载均衡的多服务器环境中，需要额外的机制如Session复制、集中式Session存储（如使用Redis等）来共享Session信息。

### 使用场景

- **Cookie**：适用于存储个性化用户设置（如语言偏好）、登录状态（记住我功能）和跟踪用户行为（如追踪用户访问网站的路径）。
- **Session**：适合存储需要保密的数据，如用户的购物车信息、用户ID，或者其他在服务器端处理的数据。

在实际的Web开发中，Cookie和Session往往是配合使用的。例如，Session ID通常存储在Cookie中，并在每次请求时发送给服务器，这样服务器就可以检索出对应的Session数据。同时，也可以利用Cookie来存储一些不太敏感的信息以减轻服务器的存储压力。

------

# 负载均衡场景下的session共享

在Java管理后台的项目中，实现负载均衡后的`session`共享通常有以下几种方法：

### 1. 粘性会话（Sticky Sessions）

粘性会话通过负载均衡器配置实现，确保来自同一用户的所有请求都被路由到同一台服务器。这样用户的会话数据就可以在该服务器上保持一致性。但这种方法的缺点是，如果那台服务器宕机，用户的会话数据就会丢失。

### 2. 会话复制（Session Replication）

在集群环境中，每个节点上的会话都会被复制到其他节点上。这样即使一台服务器失败，用户的会话数据仍然可以从其他节点上获取。例如，在Tomcat集群中，可以使用DeltaManager或BackupManager来实现会话复制。不过，这种方法会增加网络负载，并且随着集群节点数量的增加，效率可能会下降。

### 3. 集中式会话存储（Centralized Session Store）

将所有会话数据存储在一个中心位置，如数据库、内存数据存储（如Redis或Memcached）或持久化存储中。这样不管请求被哪个服务器处理，它都可以访问到相同的会话数据。

例如，使用Redis实现的步骤大致如下：

1. 配置Redis服务器。
2. 在你的Java应用中引入Redis客户端库（例如Jedis或Lettuce）。
3. 配置你的Java管理后台项目，使其将Session对象存储在Redis中。
4. 配置负载均衡器，以便它可以将请求分发到任何服务器实例。

### 4. 使用分布式缓存框架

使用分布式缓存框架如Apache Ignite、Hazelcast等也可以实现会话共享。这些框架通常提供了HTTP会话集群的原生支持。

### 示例：使用Spring Session + Redis实现Session共享

如果你的Java管理后台项目使用Spring框架，可以使用Spring Session来轻松地实现Session共享。

1. **添加依赖**：在项目的`pom.xml`文件中引入Spring Session和Redis的相关依赖。

```xml
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>io.lettuce.core</groupId>
    <artifactId>lettuce-core</artifactId>
</dependency>
<!-- 其他依赖... -->
```

2. **配置Spring Session**：在你的配置类中配置Spring Session以使用Redis。

```java
@EnableRedisHttpSession
public class Config {

    @Bean
    public LettuceConnectionFactory connectionFactory() {
        return new LettuceConnectionFactory(); // 配置你的Redis连接信息
    }
}
```

3. **配置负载均衡器**：确保你的负载均衡器（如Nginx、Apache HTTPD、AWS ELB等）配置正确，以便可以将流量均匀地分发到你的后台服务器实例。

采用这些方法可以实现Java管理后台项目在负载均衡环境下的`session`共享。你需要根据实际需求、资源和项目规模选择最合适的方案。

![image-20240215150906624](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240215150906624.png)

`spring:session:expirations:1707993840000` 这个键在 Redis 中是 Spring Session 用来处理过期会话的一部分。这个键后面的数字 `1707993840000` 代表的是会话过期的具体时间戳（毫秒为单位）。Spring Session 会定期检查这些特定的时间戳，来确定是否有会话已经过期。

键的值 `\xac\xed\x00\x05t\x00,expires:a19b13bf-b299-4695-b0ec-202a01f66efb` 是一个经过序列化的Java对象，这里使用的是 Java 序列化协议。具体到这个值：

- `\xac\xed`：序列化流的魔数，用于确认数据是 Java 序列化数据。
- `\x00\x05`：序列化流的版本号。
- `t`：表示接下来是一个 String 类型的对象。
- `\x00,` 后面跟着的是 String 的长度，但是这里看起来可能有点问题，因为逗号不是有效的长度表示。
- `expires:` 后面跟着的是实际的字符串，这应该是一个会话ID。

`a19b13bf-b299-4695-b0ec-202a01f66efb` 是一个 UUID 格式的字符串，它代表了一个特定的会话ID。在这种情况下，此会话ID关联的会话数据即将在指定的时间戳（`1707993840000`）时过期。

在Spring Session中，通常会配置一个任务来定期清理过期的会话。这个任务会检查这样的键来确定哪些会话已经过期，并进行清理。

如果你需要操作这些数据，通常会通过 Spring Session 的抽象层来进行，而不是直接操作 Redis 中的序列化数据。这是因为直接操作序列化的二进制数据容易出错，并且与 Spring Session 的抽象层相比，也更难以维护。