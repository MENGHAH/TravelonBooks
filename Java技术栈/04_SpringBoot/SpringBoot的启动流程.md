# SpringBoot启动流程

Spring Boot的加载流程主要包括以下几个步骤：

1. 启动类：标注了`@SpringBootApplication`的类是Spring Boot应用的入口点。
2. `@SpringBootApplication`注解：这是一个组合注解，包含`@SpringBootConfiguration`（表示这是一个Spring Boot的配置类）、`@EnableAutoConfiguration`（开启自动配置）和`@ComponentScan`（组件扫描）。
3. `main`方法：包含`SpringApplication.run()`用于启动Spring Boot应用。
4. `SpringApplication.run()`：这个方法负责启动Spring应用上下文，它会创建一个`ApplicationContext`实例。
5. 自动配置：由`@EnableAutoConfiguration`触发，Spring Boot会尝试自动配置Spring容器，这涉及到条件化配置，只有满足特定条件的自动配置类才会被应用。
6. 环境配置：Spring Boot会根据`application.properties`或`application.yml`文件中的配置来加载和配置环境。
7. Bean加载：通过`@ComponentScan`，Spring Boot会扫描指定的包以及子包，将标注有`@Component`，`@Service`，`@Repository`等注解的类注册为Spring容器中的Bean。



# SpringBoot Bean的加载过程

[Spring Boot Bean 加载过程的用法详解](https://apifox.com/apiskills/spring-boot-bean-loading-process/#spring-boot-bean-%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)

