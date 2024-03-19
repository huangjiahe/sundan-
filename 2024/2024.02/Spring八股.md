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

Spring容器管理对象的创建，配置和生命周期。实现了对象之间的松耦合。

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

# IOC和DI

## IOC控制反转

IOC将对象的创建，管理和生命周期由应用程序交由Sring容器管理。降低了类之间的耦合，对象创建和初始化交给Spring容器管理，在需要的时候只需向容器进行申请。

## DI依赖注入

是IOC的实现方式。由容器负责将依赖对象注入到目标对象中。对象的依赖关系由容器进行管理。

setter方法，构造函数，字段注入。

# IOC容器初始化过程

## 资源定位(Resource Location):

- 通过 `ResourceLoader` 接口,IoC 容器可以从不同的源(如类路径、文件系统、URL 等)加载配置文件。
- 常见的资源实现包括 `ClassPathResource`、`FileSystemResource`、`UrlResource` 等。
- PathMatchingResourcePatternResolver是资源定位的默认加载器，支持以上的资源加载方式。

## 加载配置文件(Configuration Loading):

- IoC 容器读取配置文件(如 XML 文件或 Java 配置类),并将其转化为 IoC 容器内部的数据结构表示。
- 对于 XML 配置文件,使用 `BeanDefinitionReader` 接口(如 `XmlBeanDefinitionReader`)来解析 XML,并将解析结果封装成 `BeanDefinition` 对象。
- 对于 Java 配置类,使用 `ConfigurationClassPostProcessor` 来处理 `@Configuration` 注解的类,并将配置类中的 `@Bean` 方法转化为 `BeanDefinition` 对象。

## 注册 `BeanDefinition`(BeanDefinition Registration):

- 将解析得到的 `BeanDefinition` 对象注册到 IoC 容器的 `BeanFactory` 中。
- `BeanFactory` 接口是 IoC 容器的核心接口,它维护了 Bean 的创建、配置和生命周期。
- 常见的 `BeanFactory` 实现包括 `DefaultListableBeanFactory`、`XmlBeanFactory` 等。

## BeanDefinition 合并(BeanDefinition Merging):

- 对于存在父子关系的 `BeanDefinition`,需要进行合并处理。
- 子 `BeanDefinition` 可以继承父 `BeanDefinition` 的属性,同时可以覆盖或添加自己的属性。
- `BeanDefinition` 合并由 `BeanDefinitionRegistryPostProcessor` 接口的实现类(如 `ConfigurationClassPostProcessor`)来完成。

## Bean 实例化(Bean Instantiation):

- 根据注册的 `BeanDefinition`,IoC 容器通过反射或者 CGLIB 动态代理的方式创建 Bean 实例。
- Bean 实例化由 `InstantiationStrategy` 接口的实现类(如 `CglibSubclassingInstantiationStrategy`)来完成。
- InstantiationStrategy 接口有两个实现类:

  SimpleInstantiationStrategy:使用反射的方式创建 Bean 实例。
  CglibSubclassingInstantiationStrategy:使用 CGLIB 动态代理的方式创建 Bean 实例。
  默认情况下,Spring 使用 SimpleInstantiationStrategy 作为实例化策略。

## 属性注入(Dependency Injection):

- 根据 `BeanDefinition` 中的属性配置,IoC 容器将依赖对象注入到 Bean 实例中。
- 常见的注入方式包括构造函数注入、setter 方法注入和字段注入。
- 属性注入由 `AutowiredAnnotationBeanPostProcessor`、`CommonAnnotationBeanPostProcessor` 等 `BeanPostProcessor` 接口的实现类来完成。

## Bean 初始化(Bean Initialization):

- 在属性注入完成后,IoC 容器会调用 Bean 的初始化方法。
- 初始化方法可以通过实现 `InitializingBean` 接口的 `afterPropertiesSet()` 方法或者使用 `@PostConstruct` 注解来指定。
- Bean 初始化由 `BeanPostProcessor` 接口的实现类(如 `InitDestroyAnnotationBeanPostProcessor`)来处理。

## Bean 销毁(Bean Destruction):

- 当 IoC 容器关闭时,会调用 Bean 的销毁方法。
- 销毁方法可以通过实现 `DisposableBean` 接口的 `destroy()` 方法或者使用 `@PreDestroy` 注解来指定。
- Bean 销毁由 `DestructionAwareBeanPostProcessor` 接口的实现类(如 `InitDestroyAnnotationBeanPostProcessor`)来处理。

以上就是 IoC 容器的初始化过程的关键步骤。在整个过程中,Spring 框架提供了许多扩展点,通过实现不同的接口或者使用注解,可以对 IoC 容器的行为进行定制和扩展。

这些接口和类的源码可以在 Spring 框架的源码中找到,它们位于 `org.springframework` 包及其子包中,如 `org.springframework.beans`、`org.springframework.context` 等。

了解 IoC 容器的初始化过程,可以更好地理解 Spring 框架的工作原理,并根据需要进行定制和扩展。同时,对于 Spring 框架的源码,建议进行深入学习和研究,以便更好地掌握 Spring 的核心机制和设计思想。

# Bean的生命周期

Bean 的生命周期是指 Bean 从创建到销毁的整个过程。在 Spring 容器中,Bean 的生命周期由容器管理,通过配置文件或注解来定义 Bean 的创建、初始化、使用和销毁的过程。

以下是 Bean 生命周期的详细描述:

## 实例化(Instantiation):

- Spring 容器根据配置文件或注解中的信息,通过反射或 CGLIB 动态代理的方式创建 Bean 实例。
- 此时,Bean 的属性还未被设置,Bean 处于未初始化状态。

## 属性注入(Dependency Injection):

- Spring 容器根据配置文件或注解中的信息,将 Bean 的依赖关系注入到 Bean 实例中。
- 依赖注入可以通过构造函数注入、setter 方法注入或字段注入等方式进行。

## 回调 Aware 接口方法:

- 如果 Bean 实现了 Spring 提供的 Aware 接口,Spring 容器会调用相应的方法,例如:
  - `BeanNameAware`: 调用 `setBeanName()` 方法,传递 Bean 的名称。
  - `BeanFactoryAware`: 调用 `setBeanFactory()` 方法,传递 `BeanFactory` 实例。
  - `ApplicationContextAware`: 调用 `setApplicationContext()` 方法,传递 `ApplicationContext` 实例。

## BeanPostProcessor 前置处理:

- 如果有 `BeanPostProcessor` 实现类,Spring 容器会在 Bean 初始化之前调用它们的 `postProcessBeforeInitialization()` 方法。
- 这允许对 Bean 进行自定义的前置处理,例如修改 Bean 的属性值或应用一些特定的逻辑。

## 初始化(Initialization):

- 如果 Bean 实现了 `InitializingBean` 接口,Spring 容器会调用它的 `afterPropertiesSet()` 方法。
- 如果 Bean 配置了 `init-method` 属性,Spring 容器会调用指定的初始化方法。
- 在这一步,Bean 已经准备好被使用。

## BeanPostProcessor 后置处理:

- 如果有 `BeanPostProcessor` 实现类,Spring 容器会在 Bean 初始化之后调用它们的 `postProcessAfterInitialization()` 方法。
- 这允许对 Bean 进行自定义的后置处理,例如生成代理对象或应用一些特定的逻辑。

## 使用(In Use):

- Bean 已经完全初始化,可以被应用程序使用。
- Bean 的生命周期受 Spring 容器管理,容器负责维护 Bean 的作用域和生命周期。

## 销毁(Destruction):

- 当 Spring 容器关闭或 Bean 被销毁时,会触发 Bean 的销毁过程。
- 如果 Bean 实现了 `DisposableBean` 接口,Spring 容器会调用它的 `destroy()` 方法。
- 如果 Bean 配置了 `destroy-method` 属性,Spring 容器会调用指定的销毁方法。
- 在销毁过程中,Bean 可以释放资源、关闭连接等。

以上就是 Bean 的完整生命周期。通过了解 Bean 的生命周期,可以更好地理解 Spring 容器对 Bean 的管理过程,并根据需要自定义 Bean 的创建、初始化和销毁行为。

Spring 提供了多个扩展点,例如 `BeanPostProcessor`、`InitializingBean`、`DisposableBean` 等接口,允许开发者在 Bean 的生命周期的不同阶段执行自定义的逻辑。这使得 Spring 框架具有很高的灵活性和可扩展性。

# BeanFactory和FactoryBean的区别

BeanFactory 和 FactoryBean 是 Spring 框架中两个重要的概念,它们的区别如下:

## BeanFactory:

- BeanFactory 是 Spring IoC 容器的核心接口,提供了 IoC 容器的基本功能。
- BeanFactory 负责管理 Bean 的生命周期,包括 Bean 的创建、初始化、销毁等。
- BeanFactory 通过 getBean() 方法来获取 Bean 实例,根据 Bean 的名称或类型来查找对应的 Bean。
- BeanFactory 是一个工厂,用于生产和管理 Bean,它本身不直接创建 Bean 实例。

示例:
```java
// 创建 BeanFactory 实例
BeanFactory beanFactory = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));

// 获取 Bean 实例
MyBean myBean = (MyBean) beanFactory.getBean("myBean");
```

## FactoryBean:

- FactoryBean 是一个特殊的 Bean,它是一个工厂 Bean,用于创建其他 Bean 实例。
- FactoryBean 是一个接口,实现该接口的类可以自定义 Bean 的创建逻辑。
- 当调用 getBean() 方法获取 FactoryBean 本身时,返回的是 FactoryBean 实例;当调用 getBean() 方法获取 FactoryBean 创建的 Bean 时,返回的是 FactoryBean 的 getObject() 方法返回的 Bean 实例。
- FactoryBean 通常用于创建复杂的 Bean 实例,例如动态代理对象、远程服务对象等。

示例:
```java
// 定义一个 FactoryBean 实现类
public class MyBeanFactory implements FactoryBean<MyBean> {
    @Override
    public MyBean getObject() throws Exception {
        // 创建和配置 MyBean 实例
        MyBean myBean = new MyBean();
        // ...
        return myBean;
    }

    @Override
    public Class<?> getObjectType() {
        return MyBean.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}

// 在配置文件中声明 FactoryBean
<bean id="myBeanFactory" class="com.example.MyBeanFactory"/>

// 获取 FactoryBean 创建的 Bean 实例
MyBean myBean = (MyBean) beanFactory.getBean("myBeanFactory");
```

总结:
- BeanFactory 是 Spring IoC 容器的核心接口,用于管理 Bean 的生命周期和提供 Bean 实例。
- FactoryBean 是一个特殊的 Bean,用于创建其他 Bean 实例,它定义了 Bean 的创建逻辑。
- BeanFactory 通过 getBean() 方法获取 Bean 实例,而 FactoryBean 通过 getObject() 方法创建 Bean 实例。
- BeanFactory 负责管理容器中的所有 Bean,而 FactoryBean 负责创建特定的 Bean 实例。
- 项目中，MybatisPlusConfig#MybatisSqlSessionFactoryBean  可以对数据连接进行配置。

理解 BeanFactory 和 FactoryBean 的区别对于深入理解 Spring 框架的 IoC 容器和 Bean 的创建机制非常重要。根据具体的需求和场景,可以灵活运用 BeanFactory 和 FactoryBean 来组织和管理应用程序中的 Bean。

# Bean注入容器有哪些方式

Bean 注入容器有以下几种方式:

## 基于 XML 配置文件

- 在 XML 配置文件中使用 `<bean>` 标签来定义 Bean,并通过 `<property>` 或 `<constructor-arg>` 标签来配置 Bean 的属性和构造函数参数。
- 示例:
  ```xml
  <bean id="myBean" class="com.example.MyBean">
      <property name="myProperty" value="value"/>
      <constructor-arg index="0" value="arg1"/>
  </bean>
  ```

## 基于注解

- 使用注解来标识 Bean 和依赖关系,如 `@Component`、`@Service`、`@Repository`、`@Controller` 等。
- 使用 `@Autowired` 或 `@Inject` 注解来自动装配 Bean 的依赖关系。
- 示例:
  ```java
  @Component
  public class MyBean {
      @Autowired
      private MyDependency myDependency;
      // ...
  }
  ```

## 基于 Java 配置类

- 使用 Java 配置类来定义 Bean 和依赖关系,通过 `@Configuration` 注解标识配置类,并使用 `@Bean` 注解标识 Bean 的创建方法。
- 示例:
  ```java
  @Configuration
  public class MyConfiguration {
      @Bean
      public MyBean myBean() {
          MyBean myBean = new MyBean();
          myBean.setMyDependency(myDependency());
          return myBean;
      }
      
      @Bean
      public MyDependency myDependency() {
          return new MyDependency();
      }
  }
  ```

## 基于注解扫描

- 通过配置组件扫描,让 Spring 自动扫描指定包下的类,并根据注解来创建 Bean。
- 使用 `<context:component-scan>` 标签或 `@ComponentScan` 注解来配置组件扫描。
- 示例:
  ```xml
  <context:component-scan base-package="com.example"/>
  ```
  ```java
  @Configuration
  @ComponentScan("com.example")
  public class MyConfiguration {
      // ...
  }
  ```

以上是 Bean 注入容器的几种常见方式。可以根据项目的需求和习惯选择适合的方式来配置和管理 Bean。通常情况下,基于注解和 Java 配置类的方式越来越流行,因为它们更加简洁、类型安全,并且易于维护和重构。

不同的注入方式可以混合使用,以达到最佳的配置效果。例如,可以使用 XML 配置文件来定义一些基础的 Bean,然后使用注解来标识和装配其他的 Bean。

选择合适的 Bean 注入方式可以提高开发效率,增强代码的可读性和可维护性。同时,也要考虑团队的习惯和项目的规模,以确保团队成员能够理解和遵循一致的 Bean 配置方式。

# Bean的作用域

Spring 框架中定义了多种 Bean 的作用域,用于确定 Bean 的生命周期和可见范围。以下是 Spring 支持的几种常见的 Bean 作用域:

## singleton(默认)

- 单例作用域,表示在整个应用程序上下文中,只会创建并管理 Bean 的一个实例。
- 所有对该 Bean 的请求都会返回同一个实例,Bean 的状态在整个应用程序中是共享的。
- 适用于无状态的 Bean,如服务层、DAO 层等。

## prototype

- 原型作用域,表示每次请求该 Bean 时,都会创建一个新的实例。
- 每个请求都有自己的 Bean 实例,Bean 的状态在请求之间是独立的。
- 适用于有状态的 Bean,如 Web 层的表单 Bean 等。

## request

- 请求作用域,表示在一次 HTTP 请求中,只会创建并使用一个 Bean 实例。
- 每个 HTTP 请求都有自己的 Bean 实例,请求结束后,Bean 实例将被销毁。
- 适用于 Web 应用程序中的请求级别的 Bean,如请求参数的处理等。

## session

- 会话作用域,表示在一个 HTTP 会话中,只会创建并使用一个 Bean 实例。
- 同一个 HTTP 会话共享一个 Bean 实例,会话结束后,Bean 实例将被销毁。
- 适用于 Web 应用程序中的会话级别的 Bean,如用户登录信息等。

## application

- 应用作用域,表示在整个 Web 应用程序中,只会创建并使用一个 Bean 实例。
- 该作用域类似于 singleton 作用域,但仅限于 Web 应用程序的上下文中。
- 适用于应用程序范围内共享的 Bean,如应用程序配置信息等。

## websocket

- WebSocket 作用域,表示在一个 WebSocket 会话中,只会创建并使用一个 Bean 实例。
- 同一个 WebSocket 会话共享一个 Bean 实例,会话结束后,Bean 实例将被销毁。
- 适用于 WebSocket 应用程序中的会话级别的 Bean。

除了以上常见的作用域,Spring 还支持自定义作用域,可以通过实现 Scope 接口来创建自己的作用域。

在使用 Bean 时,可以通过 `@Scope` 注解或 XML 配置文件中的 `scope` 属性来指定 Bean 的作用域。例如:

```java
@Component
@Scope("prototype")
public class MyBean {
    // ...
}
```

```xml
<bean id="myBean" class="com.example.MyBean" scope="prototype"/>
```

选择合适的 Bean 作用域取决于 Bean 的用途和生命周期要求。对于大多数情况,默认的 singleton 作用域已经足够满足需求。但对于有状态的 Bean 或需要独立实例的场景,可以考虑使用 prototype 或其他作用域。

合理使用 Bean 的作用域可以优化应用程序的性能和资源利用,同时也能够满足不同场景下的业务需求。

# Spring自动装配的方式

Spring 提供了多种自动装配的方式,用于简化 Bean 之间的依赖关系配置。以下是 Spring 支持的几种常见的自动装配方式:

1. byType(根据类型自动装配):
   - 根据 Bean 的类型进行自动装配。
   - 当容器中存在唯一一个与依赖类型匹配的 Bean 时,就会自动装配该 Bean。
   - 如果存在多个与依赖类型匹配的 Bean,则会抛出异常。
   - 配置方式:
     - XML: `<bean autowire="byType"/>`
     - 注解: `@Autowired`

2. byName(根据名称自动装配):
   - 根据 Bean 的名称进行自动装配。
   - 将依赖属性的名称与容器中 Bean 的名称进行匹配,如果找到对应的 Bean,就会自动装配。
   - 如果没有找到对应名称的 Bean,则不会自动装配。
   - 配置方式:
     - XML: `<bean autowire="byName"/>`
     - 注解: `@Autowired @Qualifier("beanName")`

3. constructor(构造函数自动装配):
   - 通过构造函数进行自动装配。
   - 容器会根据构造函数的参数类型,在容器中查找匹配的 Bean 进行自动装配。
   - 如果找到多个匹配的 Bean,则会根据参数名称进行进一步匹配。
   - 配置方式:
     - XML: `<bean autowire="constructor"/>`
     - 注解: `@Autowired` 标注在构造函数上

4. autodetect(自动检测):
   - 根据 Bean 的自省机制(Introspection)决定使用 constructor 还是 byType 方式进行自动装配。
   - 如果 Bean 有默认的构造函数,则使用 byType 方式自动装配;否则,使用 constructor 方式自动装配。
   - 配置方式:
     - XML: `<bean autowire="autodetect"/>`

5. no(默认,不进行自动装配):
   - 默认情况下,Spring 不会进行自动装配。
   - 需要手动配置 Bean 之间的依赖关系。
   - 配置方式:
     - XML: `<bean autowire="no"/>`

除了以上的自动装配方式,Spring 还提供了基于注解的自动装配方式,如 `@Autowired`、`@Inject`、`@Resource` 等。这些注解可以标注在属性、构造函数或 setter 方法上,用于自动装配依赖的 Bean。

使用自动装配可以简化 Bean 之间的配置,减少显式的依赖关系声明。Spring 会根据指

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