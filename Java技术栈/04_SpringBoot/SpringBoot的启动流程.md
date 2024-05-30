# 1. SpringBoot启动流程

SpringBoot构建的Web应用启动流程可以概括为以下几个步骤：

**1.初始化SpringApplication**

一切从`SpringApplication`类开始，它是SpringBoot应用的入口。当你运行一个带有`main`方法的类并调用`SpringApplication.run(YourMainClass.class, args)`时，Spring Boot的启动流程就正式启动了。这个方法会初始化一个`SpringApplication`实例，并开始执行一系列初始化工作。

**2.加载资源和配置**

- **加载配置文件**：Spring Boot会自动查找并加载`application.properties`或`application.yml`等配置文件，这些配置文件可以位于类路径下或特定的目录中，用于定制化应用的配置。
- **环境准备**：根据配置和激活的Profile（如dev、prod），Spring Boot会创建并配置`Environment`对象，用于后续的环境感知配置。

**3.扫描和注册Bean**

- **自动配置**：Spring Boot会根据项目依赖的jar包自动配置Spring的Bean。它通过`@EnableAutoConfiguration`注解来触发，扫描特定的jar包下的`META-INF/spring.factories`文件，根据这些配置自动注册Bean。
- **组件扫描**：通过`@ComponentScan`注解，Spring Boot会扫描指定包及其子包下的所有组件（如使用`@Service`、`@Repository`、`@Controller`、`@Component`等注解标记的类），并将它们注册为Bean。

**4.初始化Web容器**

- **嵌入式Web服务器**：Spring Boot根据项目依赖自动选择并初始化一个嵌入式的Web服务器，如Tomcat、Jetty或Undertow，无需手动部署WAR文件到外部服务器。
- **配置Web应用**：Spring Boot会自动配置`DispatcherServlet`和其他必要的Web组件，如错误处理、静态资源处理器等。

**5.启动应用**

- **初始化完毕**：所有的Bean都已注册并初始化完毕，包括自定义的和自动配置的Bean。
- **启动Web服务器**：嵌入式的Web服务器启动并监听指定的端口，等待接收HTTP请求。
- **启动完成回调**：如果配置了`CommandLineRunner`或`ApplicationRunner` Bean，此时会执行它们的`run`方法，可以用来执行应用启动后的初始化任务。

**6.接收请求**

- **处理HTTP请求**：当客户端发送HTTP请求时，Spring MVC的`DispatcherServlet`根据请求URL匹配对应的Controller方法，执行业务逻辑，然后返回响应给客户端。

# 2. 配置文件加载流程

- 通过@value注入
- 通过@ConfigurationProperties注入
- 通过框架自带对象Environment实现属性动态注入
- 通过@PropertySource注解实现外部配置文件注入属性值
- yml 外部配置文件动态注入
- Java原生态方式注入属性值

# 3. Bean的加载过程

[Spring Boot Bean 加载过程的用法详解](https://apifox.com/apiskills/spring-boot-bean-loading-process/#spring-boot-bean-%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)

**1. 启动应用程序**：Spring Boot 应用程序通常通过`main`方法启动，其中包含一个包含`@SpringBootApplication`注解的类。这个注解标志着这是一个 Spring Boot 应用程序，它会触发自动配置和 bean 加载过程。
**2. 自动配置**：Spring Boot 自动配置机制会根据类路径上的依赖和应用程序的配置文件来自动配置应用程序。它会创建一些常见的 bean，例如`DataSource`、`EntityManagerFactory`、`TransactionManager`等，以及一些通用的配置。
**3. 扫描组件**：Spring Boot 会自动扫描应用程序包及其子包，查找带有`@Component`、`@Service`、`@Repository`等注解的类。这些注解表明类是 Spring 的组件，并且应该被 Spring 容器管理。
**4. 创建 bean 定义**：当 Spring Boot 发现一个带有上述注解的类时，它会创建一个对应的 bean 定义。这个 bean 定义包含有关如何创建和配置 bean 的信息。
**5. 初始化 bean**：Spring Boot 会实例化并初始化 bean。这包括调用构造函数（如果有的话）、设置属性、调用初始化方法（如果有的话）等操作。
**6. 依赖注入**：如果 bean 依赖于其他 bean，Spring Boot 会使用依赖注入机制将这些依赖注入到 bean 中。这是通过在构造函数、setter 方法或字段上使用`@Autowired`注解来实现的。
**7. Post-Processing（后处理）**：Spring Boot 还支持 Bean 后处理器。这些后处理器允许你在 bean 初始化之前或之后执行自定义逻辑。你可以使用`@PostConstruct`和`@PreDestroy`注解定义初始化和销毁回调方法。
**8. Bean 就绪**：一旦所有 bean 都被初始化和配置，应用程序就准备好运行了。



总的来说，Spring Boot 的 bean 加载过程是自动的、基于注解的，它大大简化了传统的 Spring 应用程序的配置工作。Spring Boot 会根据应用程序的依赖和配置文件自动进行大部分工作，从而使开发者能够更专注于业务逻辑的实现。同时，Spring Boot 也提供了一些可扩展性选项，以便在需要时进行自定义配置和操作。
