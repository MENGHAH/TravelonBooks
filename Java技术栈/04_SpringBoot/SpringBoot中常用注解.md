# 参考文章

[SpringBoot注解大全（超详细）](https://blog.csdn.net/weixin_55772633/article/details/131882825)



# 1. 常用注解

## 1.1 启动注解

### 1.1.1 @SpringBootApplication

@SpringBootApplication 是一个注解，它是 Spring Boot 应用的入口注解，用于表示一个应用程序的主类。这个注解通常被放置在包含 main() 方法的类上。@SpringBootApplication 是一个组合注解，整合了以下三个注解的功能：

- @SpringBootConfiguration：标记该类是一个配置类，其中可能包含用 @Bean 注解标记的方法，用于定义 Bean 对象。

- @EnableAutoConfiguration：通过启用 Spring Boot 的自动配置机制，根据项目的依赖和配置信息来自动配置应用程序。
- @ComponentScan： 扫描指定包及其子包下的组件（包括 @Component、@Service、@Repository 等注解标记的类），将它们注册为 Spring 的组件。

这个组合注解简化了 Spring Boot 应用的配置，可以减少开发者的工作量。通常情况下，我们将 @SpringBootApplication 注解放置在项目的主类上。

```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

## 1.2 配置文件相关注解

### 1.2.1  @Configuration  
 @Configuration 是 Spring Framework 中的一个注解，用于标记一个类为配置类。配置类是一种特殊的类，它用于定义 Bean 对象以及其他配置信息。  在使用 @Configuration 注解标记的类中，我们可以使用 @Bean 注解来定义方法，这些方法会**在 Spring 容器初始化时被调用**，并且返回一个对象作为 Bean。

```java
@Configuration
public class AppConfig {
    @Bean
    public UserService userService() {
        return new UserServiceImpl();
    }
}
```

在上述示例中，AppConfig 类被标记为配置类，使用 @Bean 注解定义了一个方法 userService()，该方法将返回一个 UserService 对象作为 Bean。Spring 容器在初始化时将会调用 userService() 方法并将其返回的对象注册为一个 Bean，可以在其他地方进行依赖注入。除了 @Bean 注解，@Configuration 类中还可以使用其他 Spring 注解，例如 @Value、@Autowired 等，以实现更复杂的配置和依赖注入逻辑。    

### 1.2.2 @ComponentScan
@ComponentScan 是 Spring Framework 中的一个注解，用于指定需要被 Spring 扫描并加入到应用上下文的组件。该注解可以用在配置类上或者作为单独的注解使用。  当 @ComponentScan 注解用在配置类上时，它会告诉 Spring 在指定的包及其子包下扫描注解标记的类，并将它们注册为 Spring 的组件（如 @Component、@Service、@Repository 等）。  

```java
@Configuration
@ComponentScan("com.example")
public class AppConfig {
    // 配置类的其他内容...
}
```


上述示例中，@ComponentScan 注解指定了扫描的包路径为 “com.example”，Spring 将会在该包及其子包下扫描并注册所有标记为组件的类。

## 1.3 注入bean相关注解

### 1.3.1 @Bean
 @Bean 是 Spring Framework 中的一个注解，用于告诉 Spring 容器将被注解标记的方法的返回值作为一个 Bean 注册到容器中。通常情况下，我们需要在 Spring 容器中创建和管理对象。通过 @Bean 注解，我们可以自定义方法来返回或创建这些对象，并将它们注册为 Spring 的 Bean，使得它们可以在其他地方进行依赖注入、使用和管理。

```java
@Configuration
public class AppConfig {
    @Bean
    public UserService userService() {
        return new UserServiceImpl();
    }
}
```

 在上述示例中，@Bean 注解标记了 userService() 方法，该方法返回一个 UserService 对象。当 Spring 容器初始化时，会调用 userService() 方法，并将其返回的对象注册为一个 Bean。这样，在其他需要使用 UserService 的地方，可以通过依赖注入的方式获取该 Bean 的实例，并使用它提供的服务。

除了简单的方法返回对象，@Bean 注解还支持更复杂的配置：

- 可以通过 name 属性指定 Bean 的名称，例如 @Bean(name = "myBean")。
- 可以通过 initMethod 属性指定在实例化 Bean 之后调用的初始化方法，例如 @Bean(initMethod = "init")。
- 可以通过 destroyMethod 属性指定在销毁 Bean 之前调用的销毁方法，例如 @Bean(destroyMethod = "cleanup")。
- 可以通过 @Scope 注解指定 Bean 的作用域，如单例(@Scope("singleton"))、原型(@Scope("prototype"))等。

### 1.3.2  @Component

`@Component` 是最基本的注解，用于将一个普通的类标记为 Spring 的组件。它是@Repository、@Service、@Controller三个注解的父注解。通常用于标记一般性的业务逻辑类。

```java
@Component
public class UserService {
    // 类的实现...
}
```

### 1.3.3 @Repository
 @Repository 用于标记数据访问对象（DAO），通常用于与数据库交互的类。它是对 @Component 的特化，用于提供更具体的语义。

```java
@Repository
public class UserRepositoryDao {
    // 数据访问的实现...
}
```

### 1.3.4 @Service

` @Service` 用于标记服务类，通常用于业务逻辑的处理和组装。它是对 `@Component` 的特化，提供了更具体的语义。

```java
@Service
public class UserService {
    // 服务类的实现...
}
```

### 1.3.5 @Controller
@Controller 用于标记控制器类，通常用于 Web 应用程序中处理用户请求和返回响应。它是对 @Component 的特化，用于提供更具体的语义。

```java
@Controller
public class UserController {
    // 控制器类的实现...
}
```


这些注解的作用类似，都是用来将类标记为 Spring 组件，使其被 Spring 容器扫描和管理。使用它们的好处是可以使代码更加清晰和易读，并在代码层面表示出组件的角色和职责。在使用时，根据类的具体功能和用途选择合适的注解来标记类。同时，这些注解也可以与其他注解结合使用，实现更复杂的功能和特性。


### 1.3.6 @Scope
@Scope在和@Component注解一起修饰在类上，作为类级别注解时，@Scope表示该类实例的范围。在和@Bean一起修饰在方法上，作为方法级别注解时，@Scope表示该方法返回的实例的范围。

常见的作用域范围包括：

- singleton（默认）：单例作用域，每个容器中只存在一个共享的 Bean 实例。
- prototype：原型作用域，每次请求/获取 Bean 时都会创建一个新的实例。
- request：请求作用域，每次 HTTP 请求都会创建一个新的 Bean 实例。
- session：会话作用域，每个用户会话都会创建一个新的 Bean 实例。
- application：应用作用域，整个应用程序生命周期内只有一个 Bean 实例。
- websocket：WebSocket 作用域，每个 WebSocket 连接都会创建一个新的 Bean 实例。

```java
@Component
@Scope("prototype")
public class MyService {
    // 类的实现...
}
```


在上述示例中，MyService 类被标记为一个组件，并使用 @Scope("prototype") 注解将该组件的作用域设置为原型模式，每次通过容器获取 MyService 的实例时都会创建一个新的对象。

### 1.3.7 @Autowired

通过 @Autowired 注解，我们可以自动将 Spring 容器中匹配类型的 Bean 注入到目标对象中，避免了手动处理依赖对象的创建和管理，提高了代码的可维护性和可测试性。

## 1.4 SpringMVC相关注解

### 1.4.1 @RestController 复合注解
@RestController 是一个复合注解，它是在 Spring Framework中对 @Controller 和 @ResponseBody 进行了整合的注解。@Controller 注解用于标记类为控制器，表示该类是一个处理用户请求的控制器。而 @ResponseBody 注解用于标记方法返回的结果作为响应体返回给客户端。@RestController 注解默认将方法返回的对象转换为 JSON 或 XML 格式，并作为响应体返回给客户端。

### 1.4.2 @RequestMapping

@RequestMapping 是 Spring Framework 中的一个注解，用于映射请求的 URL 路径到控制器类或方法上。

通过 @RequestMapping 注解，我们可以定义控制器类或方法处理的请求 URL，以及与之关联的 HTTP 请求方法、请求参数、请求头等条件。@RequestMapping 注解可以应用于类级别和方法级别。在类级别上，@RequestMapping 注解用于定义控制器类处理的根路径。

### 1.4.3 @RequestBody

通过 @RequestBody 注解，我们可以将请求体中的数据自动绑定到控制器方法的参数上，从而方便地获取请求中发送的数据。数据绑定的方式通常是根据请求体的内容类型（如 JSON、XML 等）进行自动转换，并将转换后的对象作为方法的参数传入。注解实现接收http请求体的内容类型（如 JSON、XML 等），将json数据转换为java对象

### 1.4.4 @ResponseBody 

@ResponseBody 是 Spring Framework 中的一个注解，用于将方法的返回值直接作为响应体返回给客户端。 通过 @ResponseBody 注解，我们可以在控制器方法中将方法的返回值转换为合适的格式（如 JSON、XML 等），并将其作为响应体返回给客户端。将返回对象转化为合适的格式（如 JSON、XML 等）响应给客户端。

### 1.4.5  @RequestParam 

@RequestParam 是 Spring Framework 中的一个注解，用于从请求参数中获取值并绑定到方法参数上。通过 @RequestParam 注解，我们可以将请求中的参数值自动绑定到控制器方法的参数上，方便地获取请求中传递的参数值。@RequestParam 注解可以应用于方法参数上。

```java
@RestController
@RequestMapping("/api")
public class MyController {
    @GetMapping("/hello")
    public String hello(@RequestParam("name") String name) {
        return "Hello, " + name + "!";
    }
    // ...
}
```

在上述示例中，hello() 方法的参数 name 被标注了 @RequestParam(“name”) 注解，表示从请求参数中获取名为 “name” 的值，并将其绑定到 name 参数上。

@RequestParam 注解还支持一些属性，如 required、defaultValue 等：

- required：指定参数是否是必需的，默认为 true。如果设置为 true，但请求中没有对应的参数值，会抛出异常；如果设置为 false，但请求中没有对应的参数值，方法参数会被赋予 null。
- defaultValue：指定参数的默认值。如果请求中没有该参数，会使用 defaultValue 指定的值。

### 1.4.6  @PathVariable 

通过 @PathVariable 注解，我们可以在控制器方法中提取 URL 路径中的变量，并将其值绑定到方法参数上，以便进一步处理。@PathVariable 注解可以应用于方法参数上。

```JAVA
@RestController
@RequestMapping("/api")
public class MyController {
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable("id") Long userId) {
        // 根据用户 ID 查询用户信息
        // ...
    }
}
```

在上述示例中，getUser() 方法使用了 @PathVariable 注解，参数 userId 将会自动绑定到路径中的 id 变量上。例如，当请求的 URL 为 /api/users/123 时，userId 的值将被设置为 123。

@PathVariable 注解还支持一些属性，如 required：

- required：指定该路径变量是否是必需的，默认为 true。如果设置为 true，但请求的 URL 中不包含对应的路径变量，会抛出异常；如果设置为 false，但请求的 URL 中不包含对应的路径变量，方法参数会被赋予 null。

### 1.4.7  @RequestHeader 
通过 @RequestHeader 注解，我们可以在控制器方法中提取请求头中的特定字段值，并将其绑定到方法参数上，以便进一步处理。@RequestHeader 注解可以应用于方法参数上。

```java
@RestController
@RequestMapping("/api")
public class MyController {
    @GetMapping("/hello")
    public String hello(@RequestHeader("User-Agent") String userAgent) {
        return "Hello! Your User-Agent is: " + userAgent;
    }
    // ...
}
```

## 1.5 导入相关注解
### 1.5.1 @ImportResource导入配置文件注解

通过 @ImportResource 注解，我们可以在使用纯注解驱动的 Spring 应用程序中，引入传统的基于 XML 的配置文件。@ImportResource 注解可以应用于配置类上。

```java
@Configuration
@ImportResource("classpath:applicationContext.xml")
public class AppConfig {
   // 配置类的相关代码
}
```


在上述示例中，@ImportResource 注解引入了名为 applicationContext.xml 的配置文件，该配置文件位于类路径下。

可以导入单个或多个 XML 配置文件，多个文件之间用逗号分隔。

```java
@Configuration
@ImportResource({"classpath:applicationContext.xml", "classpath:otherContext.xml"})
public class AppConfig {
   // 配置类的相关代码
}
```

 ### 1.5.2 @Import导入组件注解

通过 @Import 注解，我们可以将其他的 Java 类或配置类导入到当前的配置类中，以便让 Spring 容器识别并管理这些组件。@Import 注解可以应用于配置类上。

```java
@Configuration
@Import({BeanA.class, BeanB.class})
public class AppConfig {
   // 配置类的相关代码
}
```


在上述示例中，@Import 注解引入了 BeanA 类和 BeanB 类，这两个类会被纳入到当前的配置类 AppConfig 所管理的 Spring 容器中。

可以导入单个或多个组件类，多个类之间用逗号分隔。除了导入普通的组件类，@Import 注解还支持导入其他的配置类。

```java
@Configuration
@Import({BeanA.class, BeanB.class, AnotherConfig.class})
public class AppConfig {
   // 配置类的相关代码
}
```


在上述示例中，@Import 注解除了导入 BeanA 类和 BeanB 类外，还导入了另一个配置类 AnotherConfig。这样，AppConfig 所管理的 Spring 容器中既有 BeanA 和 BeanB 组件，同时也会应用 AnotherConfig 所包含的配置信息。通过 @Import 注解导入的组件会与当前的配置类一起被纳入到 Spring 容器中，并进行相应的管理和使用。

### 1.5.3  @PropertySource注解
- 引入单个properties文件：@PropertySource(value = {"classpath : xxxx/xxx.properties"})
- 引入多个properties文件：@PropertySource(value = {"classpath : xxxx/xxx.properties"，"classpath : xxxx.properties"}) 

