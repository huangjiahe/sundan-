# Spring的单例bean是否有线程安全问题？

## 有状态的bean

维护了内部状态，如购物车列表。这个是非线程安全的。可以使用ThreadLocal来解决线程安全问题。

```java
public class ShoppingCart {
    private List<String> items = new ArrayList<>();

    public void addItem(String item) {
        items.add(item);
    }

    public void removeItem(String item) {
        items.remove(item);
    }

    public List<String> getItems() {
        return items;
    }
}
```

## 无状态的bean

没有维护内部状态，是线程安全的。

```java
public class MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending message: " + message);
    }
}
```

# Spring和SpringBoot的优点

## Spring的优点

### IOC控制反转

它管理对象的创建，配置和生命周期。实现了对象之间的松耦合。

### DI依赖注入

对象间的依赖关系由容器来管理。

### AOP面向切面编程

在不修改源码的情况下对现有代码进行增强。

### 事务管理

通过简单的配置或者注解，可以实现事务的管理。

### 集成性

与其他框架的集成支持。

## SpringBoot的优点

### 自动配置:

Spring Boot 的自动配置特性可以根据类路径中的依赖自动配置应用程序所需的 Bean。例如,当类路径中存在 `spring-webmvc` 依赖时,Spring Boot 会自动配置 `DispatcherServlet`、`ViewResolver` 等 Bean,无需手动配置。

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

在上面的代码中,`@SpringBootApplication` 注解结合了 `@Configuration`、`@EnableAutoConfiguration` 和 `@ComponentScan` 注解,启用了 Spring Boot 的自动配置功能。

### 起步依赖:

Spring Boot 提供了一组起步依赖,将相关的依赖打包在一起,简化了依赖管理。例如,要开发一个 Web 应用程序,只需要引入 `spring-boot-starter-web` 起步依赖即可。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

引入 `spring-boot-starter-web` 起步依赖后,Spring Boot 会自动配置 Web 应用程序所需的常见组件,如 Tomcat、Spring MVC 等。

### 内嵌容器:

Spring Boot 内置了 Tomcat、Jetty 等 Web 容器,可以直接打包成可执行的 JAR 文件,无需单独安装和配置外部容器。

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

在上面的代码中,通过运行 `main` 方法,Spring Boot 会启动内嵌的 Web 容器,无需额外的配置。

### 生产就绪特性:

Spring Boot 提供了多种生产就绪特性,如健康检查、指标监控等。例如,Spring Boot Actuator 提供了一组 REST 端点,用于监控和管理应用程序。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

引入 `spring-boot-starter-actuator` 起步依赖后,Spring Boot 会自动配置 Actuator 的端点,如 `/health`、`/metrics` 等,可以用于检查应用程序的健康状况和监控指标。

### 约定优于配置:

Spring Boot 遵循"约定优于配置"的原则,提供了合理的默认配置和约定。例如,Spring Boot 约定将静态资源放在 `src/main/resources/static` 目录下,无需额外配置。

```
src/main/resources/
  |- static/
      |- css/
      |- js/
      |- images/
```

按照 Spring Boot 的约定组织静态资源目录结构,Spring Boot 会自动将其映射到 Web 应用程序的根路径下。

以上是一些使用源码举例说明 Spring Boot 优点的示例。Spring Boot 的自动配置、起步依赖、内嵌容器、生产就绪特性和约定优于配置等特性,大大简化了应用程序的开发、配置和部署过程,提高了开发效率和生产力。

# Spring用到了哪些设计模式

## 简单工厂

BeanFactory，getBean方法传入bean的名称来获取Bean对象。

```java
@Override
public Object getBean(String name) throws BeansException {
    assertBeanFactoryActive();
    return getBeanFactory().getBean(name);
}
```

## 工厂方法

FactoryBean。Spring在使用getBean()时，就会自动调用该bean的getObject()方法。

## 单例模式

一个类仅有一个实例。

## 观察者模式

监听器的实现。

## 模板模式

jdbcTemplate

## 代理模式

Aop动态代理。

# AOP的实现方式

## 静态代理

在编译器织入Java字节码。AspectJ。代理类需要与目标对象实现一样的接口。

## 动态代理

在运行时创建，AOP不会去修改字节码，而是在内存临时生成一个代理对象，在运行期间对业务方法进行增强，不会生成新类。

### JDK动态代理

如果目标类实现了接口，JDK动态代理目标类。使用反射技术实现。

### CGLIB

如果目标类没有实现接口，cglib动态创建目标类的子类对象，在子类对象中增强目标类。需要引入asm.jar。

# Spring通知的类型

## Before

## After

## After-returing

## After-throwing

## Around





# Spring和SpringBoot的启动过程

## Spring

### 创建ApplicationContext

1. 根据xml或者配置注解创建ApplicationContext实例。
2. 在 Spring 中,ApplicationContext 是一个核心接口,它代表了 Spring 的 IoC 容器。**ApplicationContext 负责实例化、配置和管理 Bean 的生命周期。**它还提供了访问 Bean 的方法,以及一些其他的功能,如事件发布、国际化支持等。
3. 常见的ApplicationContext包括ClassPathXmlApplicationContext，从类路径下的 XML 配置文件中加载 Bean 的定义,并创建 ApplicationContext。
4. AnnotationConfigApplicationContext，从 Java 配置类中加载 Bean 的定义,并创建 ApplicationContext。
5. XmlWebApplicationContext，在 Web 应用程序中,从 XML 配置文件中加载 Bean 的定义,并创建 ApplicationContext。

### 加载和解析配置

1. 读取配置文件或扫描注解,获取 Bean 的定义信息。
2. 加载配置的核心接口是 BeanDefinitionReader,常见的实现类有 XmlBeanDefinitionReader 和 AnnotatedBeanDefinitionReader。

### 实例化和初始化Bean

1. 根据 Bean 的定义信息,通过反射创建 Bean 的实例。
2. 实例化 Bean 的核心接口是 BeanFactory,常见的实现类有 DefaultListableBeanFactory。
3. 执行 Bean 的初始化方法,如 `@PostConstruct` 注解标注的方法。
4. 进行依赖注入,将依赖的 Bean 注入到当前 Bean 中。

### 发布事件

1. 发布 ApplicationContext 初始化完成的事件,如 ContextRefreshedEvent。
2. 事件发布的核心接口是 ApplicationEventPublisher,常见的实现类有 SimpleApplicationEventMulticaster。

### 应用程序运行

1. Spring 容器初始化完成后,应用程序开始运行。
2. Bean 可以通过 ApplicationContext 获取和使用。

## SpringBoot

### 创建 SpringApplication

- 创建 SpringApplication 实例,可以通过 `new SpringApplication(Class<?>... primarySources)` 或 `SpringApplication.run(Class<?> primarySource, String... args)` 方法创建。
- 相关源码:
```java
public SpringApplication(Class<?>... primarySources) {
    this(null, primarySources);
}

public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
    return run(new Class<?>[] { primarySource }, args);
}
```

### 执行 run 方法

- 调用 SpringApplication 的 `run` 方法开始启动过程。
- 相关源码:
```java
public ConfigurableApplicationContext run(String... args) {
    // ...
    ConfigurableApplicationContext context = null;
    // ...
    try {
        // ...
        context = createApplicationContext();
        exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
                new Class[] { ConfigurableApplicationContext.class }, context);
        prepareContext(context, environment, listeners, applicationArguments, printedBanner);
        refreshContext(context);
        afterRefresh(context, applicationArguments);
        // ...
    }
    // ...
}
```

### 加载配置

- 根据 `@SpringBootConfiguration` 注解找到引导类(通常是主类)。
- 加载 `application.properties` 或 `application.yml` 配置文件。
- 相关源码:
```java
private Class<?> deduceMainApplicationClass() {
    try {
        StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
        for (StackTraceElement stackTraceElement : stackTrace) {
            if ("main".equals(stackTraceElement.getMethodName())) {
                return Class.forName(stackTraceElement.getClassName());
            }
        }
    }
    catch (ClassNotFoundException ex) {
        // Swallow and continue
    }
    return null;
}
```

### 创建 ApplicationContext

- 根据应用程序类型(Web 应用或非 Web 应用)创建相应的 ApplicationContext 实例。
- 相关源码:
```java
protected ConfigurableApplicationContext createApplicationContext() {
    Class<?> contextClass = this.applicationContextClass;
    if (contextClass == null) {
        try {
            switch (this.webApplicationType) {
            case SERVLET:
                contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);
                break;
            case REACTIVE:
                contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);
                break;
            default:
                contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
            }
        }
        catch (ClassNotFoundException ex) {
            throw new IllegalStateException(
                    "Unable create a default ApplicationContext, please specify an ApplicationContextClass",
                    ex);
        }
    }
    return (ConfigurableApplicationContext) BeanUtils.instantiateClass(contextClass);
}
```

### 刷新 ApplicationContext

- 加载和解析配置,创建和初始化 Bean,类似于 Spring 的启动过程。
- 执行 `@PostConstruct`、`@EventListener` 等注解标注的方法。
- 相关源码:
```java
private void refreshContext(ConfigurableApplicationContext context) {
    refresh(context);
    if (this.registerShutdownHook) {
        try {
            context.registerShutdownHook();
        }
        catch (AccessControlException ex) {
            // Not allowed in some environments.
        }
    }
}

protected void refresh(ApplicationContext applicationContext) {
    Assert.isInstanceOf(AbstractApplicationContext.class, applicationContext);
    ((AbstractApplicationContext) applicationContext).refresh();
}
```

### 发布事件

- 发布 ApplicationStartedEvent、ApplicationReadyEvent 等事件,表示应用程序已经启动和准备就绪。
- 相关源码:
```java
private void callRunners(ApplicationContext context, ApplicationArguments args) {
    List<Object> runners = new ArrayList<>();
    runners.addAll(context.getBeansOfType(ApplicationRunner.class).values());
    runners.addAll(context.getBeansOfType(CommandLineRunner.class).values());
    AnnotationAwareOrderComparator.sort(runners);
    for (Object runner : new LinkedHashSet<>(runners)) {
        if (runner instanceof ApplicationRunner) {
            callRunner((ApplicationRunner) runner, args);
        }
        if (runner instanceof CommandLineRunner) {
            callRunner((CommandLineRunner) runner, args);
        }
    }
}
```

### 执行 CommandLineRunner 和 ApplicationRunner

- 如果有实现 CommandLineRunner 或 ApplicationRunner 接口的 Bean,会在应用程序启动后执行它们的 `run` 方法。
- 相关源码:
```java
private void callRunner(ApplicationRunner runner, ApplicationArguments args) {
    try {
        (runner).run(args);
    }
    catch (Exception ex) {
        throw new IllegalStateException("Failed to execute ApplicationRunner", ex);
    }
}

private void callRunner(CommandLineRunner runner, ApplicationArguments args) {
    try {
        (runner).run(args.getSourceArgs());
    }
    catch (Exception ex) {
        throw new IllegalStateException("Failed to execute CommandLineRunner", ex);
    }
}
```

### 应用程序运行

- Spring Boot 应用程序启动完成,开始处理请求和执行业务逻辑。

以上是 Spring Boot 启动过程的关键步骤以及相关的 Java 类名和源码片段。Spring Boot 通过 SpringApplication 类简化了应用程序的启动流程,自动配置了许多常用的功能,并提供了一些扩展点(如 CommandLineRunner 和 ApplicationRunner)来执行自定义的启动逻辑。

## 异同点

Spring 和 Spring Boot 的启动过程有一些相似之处,但也存在一些区别。它们的异同点可以总结如下:

### 相同点

### 都需要创建 ApplicationContext

- Spring 和 Spring Boot 都需要创建 ApplicationContext 作为 IoC 容器,用于管理 Bean 的生命周期。
- 常见的 ApplicationContext 实现包括 ClassPathXmlApplicationContext、AnnotationConfigApplicationContext 等。

### 都会加载和解析配置

- Spring 和 Spring Boot 都会加载和解析配置信息,包括 XML 配置文件、Java 配置类等。
- 加载和解析配置的目的是获取 Bean 的定义信息,如 Bean 的类型、依赖关系等。

### 都会实例化和初始化 Bean

- 在加载和解析配置后,Spring 和 Spring Boot 都会根据 Bean 的定义信息,通过反射创建 Bean 的实例。
- 然后,它们会执行 Bean 的初始化方法,如 `@PostConstruct` 注解标注的方法,并进行依赖注入。

### 都会发布事件

- Spring 和 Spring Boot 都提供了事件发布和监听的机制。
- 在启动过程中,它们会发布一些关键的事件,如 ContextRefreshedEvent、ApplicationStartedEvent 等,以通知相关的监听器。

### 不同点

### 启动方式不同

- Spring 通常需要手动创建 ApplicationContext 实例,如 `new ClassPathXmlApplicationContext("applicationContext.xml")`。
- Spring Boot 提供了 SpringApplication 类,简化了启动过程。通过调用 `SpringApplication.run(Class<?> primarySource, String... args)` 方法即可启动应用程序。

### 自动配置

- Spring 需要手动配置 Bean 的定义信息,通过 XML 配置文件或 Java 配置类来描述 Bean 的属性和依赖关系。
- Spring Boot 提供了自动配置的功能,根据类路径中的依赖自动推断和配置 Bean。这减少了大量的手动配置工作。

### 内嵌容器

- Spring 一般需要单独部署到外部的 Web 容器,如 Tomcat、Jetty 等。
- Spring Boot 内置了 Web 容器,如 Tomcat,可以直接打包成可执行的 JAR 文件,无需额外的 Web 容器。

### 启动扩展点

- Spring 的启动扩展点主要通过监听器来实现,如 ApplicationListener。
- Spring Boot 除了支持监听器外,还提供了 CommandLineRunner 和 ApplicationRunner 接口,用于在应用程序启动后执行一些自定义的逻辑。

### 配置文件格式

- Spring 主要使用 XML 格式的配置文件,如 applicationContext.xml。
- Spring Boot 支持更多的配置文件格式,如 application.properties、application.yml 等,并且提供了更加方便的配置方式,如 @Value 注解。

总的来说,Spring Boot 在 Spring 的基础上进行了封装和扩展,简化了应用程序的配置和部署过程。它通过自动配置、内嵌容器、启动扩展点等特性,使得开发者能够更加快速和便捷地构建 Spring 应用程序。同时,Spring Boot 也继承了 Spring 的核心功能和概念,如 IoC 容器、AOP 等,保持了与 Spring 的兼容性。

理解 Spring 和 Spring Boot 的启动过程异同,可以帮助我们更好地选择适合的开发框架,并根据项目的需求进行适当的配置和定制。

# Spring的事务传播机制

## 编程式事务

通过TransactionTemplate实现。

## 声明式事务

`@Transactional` 注解开启声明式事务。

| 属性                   | 类型                               | 描述                                   |
| ---------------------- | ---------------------------------- | -------------------------------------- |
| value                  | String                             | 可选的限定描述符，指定使用的事务管理器 |
| propagation            | enum: Propagation                  | 可选的事务传播行为设置                 |
| isolation              | enum: Isolation                    | 可选的事务隔离级别设置                 |
| readOnly               | boolean                            | 读写或只读事务，默认读写               |
| timeout                | int (in seconds granularity)       | 事务超时时间设置                       |
| rollbackFor            | Class对象数组，必须继承自Throwable | 导致事务回滚的异常类数组               |
| rollbackForClassName   | 类名数组，必须继承自Throwable      | 导致事务回滚的异常类名字数组           |
| noRollbackFor          | Class对象数组，必须继承自Throwable | 不会导致事务回滚的异常类数组           |
| noRollbackForClassName | 类名数组，必须继承自Throwable      | 不会导致事务回滚的异常类名字数组       |

# Spring事务传播机制

Spring 的事务传播机制是指在一个事务方法中调用另一个事务方法时,事务如何传播和处理的机制。Spring 提供了七种事务传播行为,通过 `@Transactional` 注解的 `propagation` 属性来指定。

## REQUIRED (默认):

- 如果当前存在事务,则加入到当前事务中。
- 如果当前没有事务,则创建一个新的事务。
- 是默认的事务传播行为。

## SUPPORTS:

- 如果当前存在事务,则加入到当前事务中。
- 如果当前没有事务,则以非事务方式执行。

## MANDATORY:

- 如果当前存在事务,则加入到当前事务中。
- 如果当前没有事务,则抛出异常。

## REQUIRES_NEW:

- 创建一个新的事务,如果当前存在事务,则将当前事务挂起。
- 无论当前是否存在事务,都会创建一个新的事务。

## NOT_SUPPORTED:

- 以非事务方式执行,如果当前存在事务,则将当前事务挂起。

## NEVER:

- 以非事务方式执行,如果当前存在事务,则抛出异常。

## NESTED:

- 如果当前存在事务,则创建一个嵌套事务作为当前事务的子事务。
- 如果当前没有事务,则与 REQUIRED 行为类似,创建一个新的事务。

## 嵌套事务

NESTED 和 REQUIRED 都是 Spring 事务传播行为中的两种,它们在处理事务时有一些区别。

REQUIRED 传播行为:
- 如果当前存在事务,则加入到当前事务中。
- 如果当前没有事务,则创建一个新的事务。
- 属于外部事务的一部分,与外部事务共享同一个事务上下文。
- 外部事务回滚,REQUIRED 事务也会回滚。

NESTED 传播行为:
- 如果当前存在事务,则创建一个嵌套事务作为当前事务的子事务。
- 如果当前没有事务,则与 REQUIRED 行为类似,创建一个新的事务。
- 嵌套事务拥有自己的事务上下文,与外部事务的提交和回滚相互独立。
- 嵌套事务可以独立地提交或回滚,而不影响外部事务的状态。
- 如果外部事务回滚,嵌套事务也会被回滚。
- 如果嵌套事务回滚,外部事务可以选择是否继续执行或回滚。（通过捕获异常）

区别:
1. 事务上下文:
   - REQUIRED 传播行为加入到当前事务中,共享同一个事务上下文。
   - NESTED 传播行为创建一个嵌套事务,拥有自己独立的事务上下文。

2. 提交和回滚:
   - REQUIRED 传播行为与外部事务的提交和回滚关联,一起提交或回滚。
   - NESTED 传播行为可以独立地提交或回滚,不影响外部事务的状态。

3. 回滚影响:
   - 如果外部事务回滚,REQUIRED 传播行为的事务也会回滚。
   - 如果外部事务回滚,NESTED 传播行为的嵌套事务也会回滚。但是,如果嵌套事务回滚,外部事务可以选择是否继续执行或回滚。

示例代码:
```java
@Service
public class UserService {

    @Transactional
    public void updateUser(User user) {
        // 更新用户信息
        // ...
        createLog(user);
    }

    @Transactional(propagation = Propagation.NESTED)
    public void createLog(User user) {
        // 创建日志
        // ...
        if (/* 某个条件 */) {
            throw new RuntimeException("日志创建失败");
        }
    }
}
```

在这个例子中,`updateUser` 方法使用默认的 REQUIRED 传播行为,而 `createLog` 方法使用 NESTED 传播行为。

- 如果 `createLog` 方法执行成功,嵌套事务会提交,并且外部事务 `updateUser` 也会提交。
- 如果 `createLog` 方法抛出异常,嵌套事务会回滚,但是外部事务 `updateUser` 可以选择是否继续执行或回滚。

NESTED 传播行为提供了更细粒度的事务控制,允许在一个事务中创建独立的子事务,子事务可以独立地提交或回滚,而不影响外部事务的状态。这在某些场景下可以提供更灵活的事务管理。