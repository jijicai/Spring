# 第九部分：操作指南

本节提供了一些常见的 “我该怎么做……” 问题的答案，这些问题在使用 Spring Boot 时经常出现。它的覆盖面并不详尽，但它确实覆盖了很多。

如果你有一个我们这里没有涉及的具体问题，你可能想看看 stackoverflow.com，看看是否有人已经提供了答案。这也是一个提出新问题的好地方（请使用 spring-boot 标签）。

我们也非常乐意扩展这个部分。如果你想添加操作指南，请向我们发送[拉取请求](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE)。

# 76、Spring Boot 应用程序

本节包含与 Spring Boot 应用程序直接相关的主题。

## 76.1、创建自己的 FailureAnalyzer

FailureAnalyzer 是一种很好的方法，可以在启动时拦截异常并将其转换为可读的消息，并包装在 FailureAnalysis 中。Spring Boot 为应用程序上下文相关的异常、 JSR-303 验证等提供了这样的分析器。你也可以创建自己的。

AbstractFailureAnalyzer 是 FailureAnalyzer 的一个方便的扩展，它检查要处理的异常中是否存在指定的异常类型。你可以在此基础上进行扩展，以便你的实现只有在异常实际存在时才有机会处理它。无论出于何种原因，如果你无法处理异常，请返回 null 以使另一个实现有机会处理异常。

FailureAnalyzer 实现必须在 META-INF/spring.factories 中注册。以下示例注册 ProjectConstraintViolationFailureAnalyzer：

    org.springframework.boot.diagnostics.FailureAnalyzer=\
    com.example.ProjectConstraintViolationFailureAnalyzer

注释：如果你需要访问 BeanFactory 或 Environment，那么 FailureAnalyzer 可以简单地分别实现 BeanFactoryAware 或 EnvironmentAware。

## 76.2、自动配置故障排除

Spring Boot 自动配置会尽最大努力 “做正确的事情”，但有时会失败，而且很难说出原因。

在任何 Spring Boot ApplicationContext 中都有一个非常有用的 ConditionEvaluationReport。如果启用 DEBUG 日志输出，你可以看到它。如果你使用 spring-boot-actuator（请参阅 [Actuator 章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-spring-boot-application.html)），还有一个 conditions 端点以 JSON 呈现报告。使用该端点调试应用程序，并查看 Spring Boot 在运行时添加了哪些功能（以及哪些功能尚未添加）。

通过查看源代码和 Javadoc，可以回答更多的问题。阅读代码时，请记住以下经验法则：

（1）查找名为 \*AutoConfiguration 的类并阅读它们的源代码。特别注意 @Conditional\* 注解，以找出它们启用的功能以及何时启用。将 --debug 添加到命令行或系统属性 -Ddebug，以在控制台上获取应用程序中做出的所有自动配置决策的日志。在正在运行的 Actuator 应用程序中，查看 conditions 端点（/actuator/conditions 或 JMX 等效项）以获取相同的信息。

（2）查找 @ConfigurationProperties 类（如 ServerProperties）并从中读取可用的外部配置选项。@ConfigurationProperties 注解有一个 name 属性，用作外部属性的前缀。因此，ServerProperties 具有 prefix=“server”，其配置属性为 server.port、server.address 和其他。在正在运行的 Actuator 应用程序中，查看 configprops 端点。

（3）查找 Binder 上的 bind 方法的用途，以轻松的方式将配置值显式地拉出 Environment。它经常与前缀一起使用。

（4）查找直接绑定到 Environment 的 @Value 注解。

（5）查找 @ConditionalOnExpression 注解，它根据 SpEL 表达式打开和关闭功能，通常使用从 Environment 中解析的占位符进行评估。

## 76.3、在 Environment 或 ApplicationContext 启动之前自定义它

SpringApplication 有 ApplicationListeners 和 ApplicationContextInitializers，用于将自定义应用于上下文或环境。Spring Boot 从 META-INF/spring.factories 加载了许多这样的自定义项供内部使用。有多种方法可以注册其他自定义设置：

（1）在运行 SpringApplication 之前，通过在 SpringApplication 上调用 addListeners 和  addInitializers 方法，以编程方式，针对每个应用程序。

（2）通过设置 context.initializer.classes 或 context.listener.classes 属性，以声明方式，针对每个应用程序。

（3）声明性地，对于所有应用程序，通过添加一个 META-INF/spring.factories 并打包一个应用程序都将其用作库的 jar 文件。

SpringApplication 将一些特殊的 ApplicationEvents 发送给监听器（有些甚至在创建上下文之前），然后为 ApplicationContext 发布的事件注册监听器。有关完整列表，请参阅“Spring Boot功能”部分中的第 23.5 节：应用程序事件和监听器。

也可以在使用 EnvironmentPostProcessor 刷新应用程序上下文之前自定义 Environment。每个实现都应该在 META-INF/spring.factories 中注册，如下面示例所示：

org.springframework.boot.env.EnvironmentPostProcessor=com.example.YourEnvironmentPostProcessor

该实现可以加载任意文件并将其添加到 Environment 中。例如，以下示例从类路径加载 YAML 配置文件：
```
public class EnvironmentPostProcessorExample implements EnvironmentPostProcessor {

    private final YamlPropertySourceLoader loader = new YamlPropertySourceLoader();

    @Override
    public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application) {
        Resource path = new ClassPathResource("com/example/myapp/config.yml");
        PropertySource<?> propertySource = loadYaml(path);
        environment.getPropertySources().addLast(propertySource);
    }

    private PropertySource<?> loadYaml(Resource path) {
        if (!path.exists()) {
            throw new IllegalArgumentException("Resource " + path + " does not exist");
        }
        try {
            return this.loader.load("custom-resource", path).get(0);
        }
        catch (IOException ex) {
            throw new IllegalStateException("Failed to load yaml configuration from " + path, ex);
        }
    }

}
```
提示：该 Environment 已经准备好了默认情况下 Spring Boot 加载的所有常用属性源。因此，可以从环境中获取文件的位置。前面的示例将 custom-resource 属性源添加到列表的末尾，以便在任何常见的其他位置定义的键具有优先权。自定义实现可以定义另一个顺序。

注意：虽然在 @SpringBootApplication 上使用 @PropertySource 似乎是在 Environment 中加载自定义资源的一种方便简便的方法，但我们不推荐它，因为 Spring Boot 在刷新 ApplicationContext 之前准备 Environment。用 @PropertySource 定义的任何键加载太晚，无法对自动配置产生任何影响。

## 76.4、构建 ApplicationContext 层次结构（添加父上下文或根上下文）

你可以使用 ApplicationBuilder 类创建父/子 ApplicationContext 层次结构。有关更多信息，请参见 “Spring Boot 功能” 部分中的第 23.4 节： Fluent 构建器 API。

## 76.5、创建非 web 应用程序

并非所有 Spring 应用程序都必须是 web 应用程序 (或 web 服务)。如果您想在 main 方法中执行一些代码，但也要引导 Spring 应用程序来设置要使用的基础设施，则可以使用 Spring Boot 的 SpringApplication 功能。SpringApplication 会更改其 ApplicationContext 类，这取决于它是否认为需要 web 应用程序。你可以做的第一件事是将与服务器相关的依赖项（如 servlet API）从类路径中去掉。如果不能这样做（例如，从同一代码库运行两个应用程序），则可以在 SpringApplication 实例上显式调用 setWebApplicationType(WebApplicationType.NONE) 或设置 applicationContextClass 属性（通过 Java API 或使用外部属性）。要作为业务逻辑运行的应用程序代码可以作为 CommandLineRunner 来实现，并作为 @Bean 定义放入上下文中。
