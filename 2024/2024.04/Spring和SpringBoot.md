# 什么是Spring开发框架？

Spring开发框架是很多模块的集合，使用这些模块可以协助提高我们的开发效率。**项目中使用的Spring版本是5.0.7，SpringBoot版本是2.3.2。**

# 列举一下Spring的模块(基于5.0.7版本)

## spring-core

模块主要是包含Spring框架基本的核心工具类，Spring的其他组件都要使用到这个包里的类，Core模块是其他组件的基本核心。

## spring-beans

模块是所有应用都要用到的，它包含访问配置文件、创建和管理Bean以进行IOC和DI相关操作的所有类。

##  spring-context

构建于core和beans模块基础之上，提供了一种类似于JNDI注册器的框架式的对象访问方法。Context继承了Beans的特性，为Spring提供了大量的扩展，添加了国际化、事件传播、资源加载和对Context的透明创建的支持。**ApplicationContext接口是Context模块的关键。**

##  spring-jdbc

模块提供了一个JDBC抽象层，它可以消除冗长的JDBC编码和解析数据厂商特有的错误代码。这个模块包含了spring对JDBC数据访问进行封装的所有类。

##  spring-orm

模块是对象-关系映射API，如JPA、JDO、Hibernate、iBates等，提供了一个交互层。利用ORM分装包，可以混合使用所有的Spring提供的特性进行O/R映射。

##  spring-oxm

模块提供了一个对Object/XML映射实现的抽象层，Object/XML映射实现包括JAXB、Castor、XMLBean、JiBX和Xstream。

##  spring-jms

模块主要包含了一些制造和消费消息的特征。

##  spring-tx

模块支持编程和声明性的事务管理，这些事务类必须实现特定的接口，并且对所有的POJO都适用。

##  spring-aspects

模块提供了对AspectJ的集成支持。

##  spring-aop

模块提供了一个符合AOP联盟标准的面向切面编程的实现，它让你可以定义例如方法拦截器和切点，从而将逻辑代码分开，降低它们的耦合性。

## spring-jcl

这个模块是spring源码里面最基础的模块，没有去依赖其他模块，它是为了兼容不同版本的日志系统。利用异常去做业务控制，采用适配器模式和工厂模式。

## spring-expression

模块提供了一个强大的表达式语言用于在运行时查询和操作对象。它是JSP2.1规范中定义的一个扩展。该语言支持设置获取属性的值、属性的分配、方法的调用，访问数组上下文、容器和索引器、逻辑和算术运算符、命名变量以及从Spring的IOC容器中根据名称检索对象。它也支持list投影、选择和一般的list聚合。

# 什么是IOC？如何实现的？

IOC（Inversion Of Controll，控制反转）是一种设计思想，就是将原本在程序中手动创建对象的控制权，交给IOC容器来管理，并由IOC容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。IOC容器就像是一个工厂一样，在需要对象的时候只需要向容器进行申请。

Spring 中的 IoC 的实现原理就是工厂模式加反射机制。

```java
interface Fruit {
     public abstract void eat();
}
class Apple implements Fruit {
    public void eat(){
        System.out.println("Apple");
    }
}
class Orange implements Fruit {
    public void eat(){
        System.out.println("Orange");
    }
}
class Factory {
    public static Fruit getInstance(String ClassName) {
        Fruit f=null;
        try {
            f=(Fruit)Class.forName(ClassName).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
}
class Client {
    public static void main(String[] a) {
        Fruit f=Factory.getInstance("io.github.dunwu.spring.Apple");
        if(f!=null){
            f.eat();
        }
    }
}
------
著作权归@pdai所有
原文链接：https://pdai.tech/md/interview/x-interview-2.html
```

# 什么是AOP？有哪些AOP的概念？

AOP（Aspect-Oriented Programming，面向切面编程）能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可扩展性和可维护性。

Spring AOP是基于动态代理的，如果要代理的对象实现了某个接口，那么Spring AOP就会使用JDK动态代理去创建代理对象；而对于没有实现接口的对象，就无法使用JDK动态代理，转而使用CGlib动态代理生成一个被代理对象的子类来作为代理。

![img](https://www.pdai.tech/images/spring/spring-interview-3.png)

当然也可以使用AspectJ，Spring AOP中已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。使用AOP之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样可以大大简化代码量。我们需要增加新功能也方便，提高了系统的扩展性。日志功能、事务管理和权限管理等场景都用到了AOP。

## AOP包含的概念

#### Jointpoint（连接点）

具体的切面点点抽象概念，可以是在字段、方法上，Spring中具体表现形式是PointCut（切入点），仅作用在方法上。

#### Advice（通知）

 在连接点进行的具体操作，如何进行增强处理的，分为前置、后置、异常、最终、环绕五种情况。

#### 目标对象

被AOP框架进行增强处理的对象，也被称为被增强的对象。

#### AOP代理

AOP框架创建的对象，简单的说，代理就是对目标对象的加强。Spring中的AOP代理可以是JDK动态代理，也可以是CGLIB代理。

#### Weaving（织入）

将增强处理添加到目标对象中，创建一个被增强的对象的过程

总结为一句话就是：在目标对象（target object）的某些方法（jointpoint）添加不同种类的操作（通知、增强处理），最后通过某些方法（weaving、织入操作）实现一个新的代理目标对象。

## AOP的应用场景

AspectJ在项目中的使用，用来统计指定模块的pv。记录日志等。

```java
/**
 * pom.xml引入
 */
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
</dependency>


/**
 * 统计指定模块的pv
 *
 * @author huangjiahee@outlook.com
 * @since 2023/11/08
 */
@Aspect
@Component
public class ModuleCounterAspect {

  private ModuleCountService moduleCountService;

  @Autowired
  public ModuleCounterAspect(final ModuleCountService moduleCountService) {
    this.moduleCountService = moduleCountService;
  }

  @Around("execution(* com.eims.cto.framework.happiness.app.AppGoodsController.goodsDetail(..))")
  @Deprecated
  public Object count(ProceedingJoinPoint joinPoint) throws Throwable {
    moduleCountService.addCountCache(GOODS_DETAIL_USER_COUNT, 1L);
    return joinPoint.proceed();
  }
}
```

## AOP的通知类型

特定 JoinPoint 处的 Aspect 所采取的动作称为 Advice。Spring AOP 使用一个 Advice 作为拦截器，在 JoinPoint “周围”维护一系列的拦截器。

### **前置通知**（Before advice）  

这些类型的 Advice 在 joinpoint 方法之前执行，并使用 @Before 注解标记进行配置。

### **后置通知**（After advice） 

这些类型的 Advice 在连接点方法之后执行，无论方法退出是正常还是异常返回，并使用 @After 注解标记进行配置。

### **返回后通知**（After return advice） 

这些类型的 Advice 在连接点方法正常执行后执行，并使用@AfterReturning 注解标记进行配置。

### **环绕通知**（Around advice） 

这些类型的 Advice 在连接点之前和之后执行，并使用 @Around 注解标记进行配置。

### **抛出异常后通知**（After throwing advice） 

仅在 joinpoint 方法通过抛出异常退出并使用 @AfterThrowing 注解标记配置时执行。

## AOP的实现方式

### 静态代理

- 在编译期对Java字节码织入。
- AspectJ。
- 代理类需要实现和目标对象一样的接口。

### 动态代理

- 在程序运行期对业务方法进行增强，不会生成新类。
- JDK动态代理。反射技术。如果目标类实现类接口。
- cglib动态代理。如果目标类未实现接口。引入asm.jar。

## 谈谈你对CGLib的理解？

JDK 动态代理机制只能代理实现接口的类，一般没有实现接口的类不能进行代理。使用 CGLib 实现动态代理，完全不受代理类必须实现接口的限制。

CGLib 的原理是对指定目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对 final 修饰的类进行代理。

```java
//CGLib 的调用流程就是通过调用拦截器的 intercept 方法来实现对被代理类的调用。而拦截逻辑可以写在 intercept 方法的 invokeSuper(o, objects);的前后实现拦截。

public class CGLibDemo {

    // 需要动态代理的实际对象
    static class Sister  {
        public void sing() {
            System.out.println("I am Jinsha, a little sister.");
        }
    }

    static class CGLibProxy implements MethodInterceptor {

        private Object target;

        public Object getInstance(Object target){
            this.target = target;
            Enhancer enhancer = new Enhancer();
            // 设置父类为实例类
            enhancer.setSuperclass(this.target.getClass());
            // 回调方法
            enhancer.setCallback(this);
            // 创建代理对象
            return enhancer.create();
        }

        @Override
        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            System.out.println("introduce yourself...");
            Object result = methodProxy.invokeSuper(o,objects);
            System.out.println("score...");
            return result;
        }
    }

    public static void main(String[] args) {
        CGLibProxy cgLibProxy = new CGLibProxy();
        //获取动态代理类实例
        Sister proxySister = (Sister) cgLibProxy.getInstance(new Sister());
        System.out.println("CGLib Dynamic object name: " + proxySister.getClass().getName());
        proxySister.sing();
    }
}
------
著作权归@pdai所有
原文链接：https://pdai.tech/md/interview/x-interview-2.html
```

## Spring AOP和AspectJ AOP有什么区别？

Spring AOP是属于运行时增强，而AspectJ是编译时增强。Spring AOP基于代理（Proxying），而AspectJ基于字节码操作（Bytecode Manipulation）。

Spring AOP已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。AspectJ相比于Spring AOP功能更加强大，但是Spring AOP相对来说更简单。

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择AspectJ，它比SpringAOP快很多。

## Spring中的bean的作用域有哪些？

### singleton(默认)

- 单例作用域,表示在整个应用程序上下文中,只会创建并管理 Bean 的一个实例。
- 所有对该 Bean 的请求都会返回同一个实例,Bean 的状态在整个应用程序中是共享的。
- 适用于无状态的 Bean,如服务层、DAO 层等。

### prototype

- 原型作用域,表示每次请求该 Bean 时,都会创建一个新的实例。
- 每个请求都有自己的 Bean 实例,Bean 的状态在请求之间是独立的。
- 适用于有状态的 Bean,如 Web 层的表单 Bean 等。

### request

- 请求作用域,表示在一次 HTTP 请求中,只会创建并使用一个 Bean 实例。
- 每个 HTTP 请求都有自己的 Bean 实例,请求结束后,Bean 实例将被销毁。
- 适用于 Web 应用程序中的请求级别的 Bean,如请求参数的处理等。

### session

- 会话作用域,表示在一个 HTTP 会话中,只会创建并使用一个 Bean 实例。
- 同一个 HTTP 会话共享一个 Bean 实例,会话结束后,Bean 实例将被销毁。
- 适用于 Web 应用程序中的会话级别的 Bean,如用户登录信息等。

### application

- 应用作用域,表示在整个 Web 应用程序中,只会创建并使用一个 Bean 实例。
- 该作用域类似于 singleton 作用域,但仅限于 Web 应用程序的上下文中。
- 适用于应用程序范围内共享的 Bean,如应用程序配置信息等。

### websocket

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

# @Component和@Bean的区别是什么？

1. 作用对象不同。@Component注解作用于类，而@Bean注解作用于方法。
2. @Component注解通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用@ComponentScan注解定义要扫描的路径）。@Bean注解通常是在标有该注解的方法中定义产生这个bean，告诉Spring这是某个类的实例，当我需要用它的时候还给我。
3. @Bean注解比@Component注解的自定义性更强，而且很多地方只能通过@Bean注解来注册bean。比如当引用第三方库的类需要装配到Spring容器的时候，就只能通过@Bean注解来实现。

@Bean注解的使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
}
```

上面的代码相当于下面的XML配置：

```xml
<beans>
    <bean id="transferService" class="com.yanggb.TransferServiceImpl"/>
</beans>
```

下面这个例子是无法通过@Component注解实现的：

```java
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

# 将一个类声明为Spring的bean的注解有哪些？

我们一般使用@Autowired注解去自动装配bean。而想要把一个类标识为可以用@Autowired注解自动装配的bean，可以采用以下的注解实现：

1. @Component注解。通用的注解，可标注任意类为Spring组件。如果一个Bean不知道属于哪一个层，可以使用@Component注解标注。
2. @Repository注解。对应持久层，即Dao层，主要用于数据库相关操作。
3. @Service注解。对应服务层，即Service层，主要涉及一些复杂的逻辑，需要用到Dao层（注入）。
4. @Controller注解。对应Spring MVC的控制层，即Controller层，主要用于接受用户请求并调用Service层的方法返回数据给前端页面。

# Spring框架中用到了哪些设计模式？

1. 工厂设计模式：Spring使用工厂模式通过BeanFactory和ApplicationContext创建bean对象。
2. 代理设计模式：Spring AOP功能的实现。
3. 单例设计模式：Spring中的bean默认都是单例的。
4. 模板方法模式：Spring中的jdbcTemplate、hibernateTemplate等以Template结尾的对数据库操作的类，它们就使用到了模板模式。
5. 包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
6. 观察者模式：Spring事件驱动模型就是观察者模式很经典的一个应用。
7. 适配器模式：Spring AOP的增强或通知（Advice）使用到了适配器模式、Spring MVC中也是用到了适配器模式适配Controller。

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

# 什么是SpringBoot？

Spring Boot 是 Spring 开源组织下的子项目，是 Spring 组件一站式解决方案，主要是简化了使用 Spring 的难度，简省了繁重的配置，提供了各种启动器，开发者能快速上手。

- 用来简化Spring应用的初始搭建以及开发过程，使用特定的方式来进行配置
- 创建独立的Spring引用程序main方法运行
- 嵌入的tomcat无需部署war文件
- 简化maven配置
- 自动配置Spring添加对应的功能starter自动化配置
- SpringBoot来简化Spring应用开发，约定大于配置，去繁化简

# 为什么使用SpringBoot？

## 独立运行

Spring Boot 而且内嵌了各种 servlet 容器，Tomcat、Jetty 等，现在不再需要打成war 包部署到容器中，Spring Boot 只要打成一个可执行的 jar 包就能独立运行，所有的依赖包都在一个 jar 包内。

## 简化配置

spring-boot-starter-web 启动器自动依赖其他组件，简少了 maven 的配置。

## 自动配置

Spring Boot 能根据当前类路径下的类、jar 包来自动配置 bean，如添加一个 springboot-starter-web 启动器就能拥有 web 的功能，无需其他配置。

## 无代码生成和XML配置

Spring Boot 配置过程中无代码生成，也无需 XML 配置文件就能完成所有配置工作，这一切都是借助于条件注解完成的，这也是 Spring4.x 的核心功能之一。

## 应用监控

Spring Boot 提供一系列端点可以监控服务及应用，做健康检测。
