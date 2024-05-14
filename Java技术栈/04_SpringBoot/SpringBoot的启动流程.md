# SpringBoot启动流程

Spring Boot的加载流程主要包括以下几个步骤：

1. 启动类：标注了`@SpringBootApplication`的类是Spring Boot应用的入口点。
2. `@SpringBootApplication`注解：这是一个组合注解，包含`@SpringBootConfiguration`（表示这是一个Spring Boot的配置类）、`@EnableAutoConfiguration`（开启自动配置）和`@ComponentScan`（组件扫描）。
3. `main`方法：包含`SpringApplication.run()`用于启动Spring Boot应用。
4. `SpringApplication.run()`：这个方法负责启动Spring应用上下文，它会创建一个`ApplicationContext`实例。
5. 自动配置：由`@EnableAutoConfiguration`触发，Spring Boot会尝试自动配置Spring容器，这涉及到条件化配置，只有满足特定条件的自动配置类才会被应用。
6. 环境配置：Spring Boot会根据`application.properties`或`application.yml`文件中的配置来加载和配置环境。
7. Bean加载：通过`@ComponentScan`，Spring Boot会扫描指定的包以及子包，将标注有`@Component`，`@Service`，`@Repository`等注解的类注册为Spring容器中的Bean。

> AI回答
>
> 1. **main方法启动**：Spring Boot应用通常通过一个带有`@SpringBootApplication`注解的启动类来启动。在这个类的`main`方法中，会调用`SpringApplication.run()`方法来启动应用。
> 2. **创建SpringApplication对象**：在`run()`方法被执行之前，会先创建一个新的`SpringApplication`实例。这个实例将会用于管理整个Spring Boot应用的启动过程。
> 3. **应用监听器**：接着，Spring Boot会创建并注册应用监听器`SpringApplicationRunListeners`，这些监听器会在启动过程中的不同阶段触发相应的事件。
> 4. **加载配置环境**：Spring Boot会加载配置环境`ConfigurableEnvironment`，并将`Environment`加入到监听对象中，以便在后续的自动化配置中使用这些配置信息。
> 5. **加载应用上下文**：随后，Spring Boot会加载应用上下文`ConfigurableApplicationContext`，这个上下文是Spring容器的核心，负责管理bean的生命周期和依赖关系。
> 6. **刷新上下文**：在上下文加载后，会调用`refreshContext(context)`方法，这个方法会完成Spring Boot的自动配置和bean的实例化工作。这是Spring Boot的一个重要特性，它通过扫描项目中的类路径、配置文件和各种starter来自动配置应用程序。
> 7. **启动内嵌服务器**：最后，Spring Boot会启动内嵌的服务器（如Tomcat），并初始化所有已注册的Servlet、Filter和Listener等Web组件。
> 8. **应用准备就绪**：一旦上述步骤完成，Spring Boot就会将应用准备好并开始接受外部请求。



# SpringBoot Bean的加载过程

[Spring Boot Bean 加载过程的用法详解](https://apifox.com/apiskills/spring-boot-bean-loading-process/#spring-boot-bean-%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)

SpringBoot Bean
