# 第四部分：Spring Boot 功能

本节将深入讨论 Spring Boot 的细节。在这里，你可以了解可能想要使用和自定义的关键功能。如果你还没有这样做，你可以能需要阅读“第二部分：入门”和“第三部分：使用 Spring Boot”，以便你有一个良好的基础知识。

# 23、SpringApplication

SpringApplication 类提供了一种方便的方法来引导从 main() 方法启动的 Spring 应用程序。在大多数场景下，可以委托给静态的 SpringApplication.run 方法，如下面示例所示：
```
public static void main(String[] args) {
SpringApplication.run(MySpringConfiguration.class, args);
}
```
当你的应用程序启动时，你应该会看到类似以下输出的内容：

    .   ____          _            __ _ _
    /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
    \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
    =========|_|==============|___/=/_/_/_/
    :: Spring Boot ::   v2.1.6.RELEASE
    
    2013-07-31 00:08:16.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
    2013-07-31 00:08:16.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
    2014-03-04 13:09:54.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
    2014-03-04 13:09:56.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)

默认情况下，将显示 INFO 级别的日志信息，包括一些相关的启动详细信息，例如启动应用程序的用户。如果需要 INFO 级别以外的日志，可以设置它，如中第 26.4 节“日志级别”中所述。

## 23.1、启动失败

如果你的应用程序启动失败，注册的 FailureAnalyzers 将有机会提供专用的错误消息和修复问题的具体操作。例如，如果你在端口 8080 上启动 web 应用并且端口已在使用，则你应该会看到类似以下信息的内容：

    ***************************
    APPLICATION FAILED TO START
    ***************************
    
    Description:
    
    Embedded servlet container failed to start. Port 8080 was already in use.
    
    Action:
    
    Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.

注释：Spring Boot 提供了许多 FailureAnalyzer 实现，你可以[添加自己的实现](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-spring-boot-application.html#howto-failure-analyzer)。

如果没有故障分析程序能够处理异常，你仍然可以显示完整的条件报告，以便更好地理解出错的原因。为此，需要为以下类启用 debug 属性或启用 DEBUG 日记记录：

    org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener

例如，如果使用 java -jar 来运行应用，则可以按如下方式启用 debug 属性：

    $ java -jar myproject-0.0.1-SNAPSHOT.jar --debug

## 23.2、自定义 Banner

可以通过将 banner.txt 文件添加到类路径或将 spring.banner.location 属性设置为此类文件的位置来更改在启动时打印的 banner（横幅）。如果文件的编码不是 UTF-8，则可以设置 spring.banner.charset。除了文本文件，还可以将 banner.gif、banner.jpg 或 banner.png 图像文件添加到类路径中，或设置 spring.banner.image.location 属性。图像被转换成 ASCII 艺术表现形式并打印在任何文本横幅的上方。

在 banner.txt 文件中，可以使用以下任何占位符：

表 23.1：Banner 变量

|变量（Variable）				|描述（Description）|
|----|--------|
|${application.version}				|在 MANIFEST.MF 中声明的应用的版本号，例如，Implementation-Version：1.0 被打印为 1.0。|
|${application.formatted-version}		|应用程序的版本号，在 MANIFEST.MF 中声明并格式化以供显示（用括号括起来，前缀为 v）。例如（v1.0）。|
|${spring-boot.version}				|正在使用的 Spring Boot 版本。例如：2.1.6.RELEASE。|
|${spring-boot.formated-version}			|正在使用的 Spring Boot 版本，格式化以供显示（用括号括起来，前缀为v）。例如：2.1.6.RELEASE。|
|${Ansi.NAME}（或 ${AnsiColor.NAME}、${AnsiBackground.NAME}、${AnsiStyle.NAME}）|其中 NAME 是 ANSI 转义代码的名称。有关详细信息，请参见 AnsiPropertySource。|
|${application.title}				|在 MANIFEST.MF 中声明的应用标题。例如，Implementation-Title：MyApp 打印为 MyApp。|

    提示：如果要以编程方式生成横幅，则可以使用 SpringApplication.setBanner(...) 方法。使用 org.springframework.boot.Banner 接口冰实现你自己的 printBanner() 方法。

你还可以使用 spring.main.banner-mode 属性来确定是否必须在 System.out（控制台）上打印横幅，发送到配置的记录器（日志）或根本不生成横幅（关闭）。

打印出来的 banner 被注册为一个单例 bean，名称如下：springBootBanner。

注释：YAML 将 off 映射为 false，因此在想禁用应用中的 banner 时确保添加引号，如下面示例所示：
```yaml
spring:
    main:
        banner-mode: "off"
```
## 23.3、自定义 SpringApplication

如果你不喜欢 SpringAppliction 的默认设置，那么你可以创建一个本地实例并定制它。例如，要关掉 banner，可以写：
```
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setBannerMode(Banner.Mode.OFF);
    app.run(args);
}
```
注释：传递给 SpringApplication 的构造函数参数是 Spring bean 的配置源。在大多数情况下，这些都是对 @Configuration 类的引用，但它们也可以是对 XML 配置或应该扫描的包的引用。

也可以使用 application.properties 文件配置 SpringApplication。详见第 24 章：外部化配置。

有关配置选项的完整列表，请参阅 [SpringApplication Javadoc](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/SpringApplication.html)。

## 23.4、Fluent 构建器 API

如果需要构建 ApplicationContext 层次结构（具有父子关系的多个上下文），或者如果希望使用“fluent”构建器 API，则可以使用 SpringApplicationBuilder。

SpringApplicationBuilder 允许你将多个方法调用链接在一起，并包括 parent 和 child 方法，这些方法允许你创建层次结构，如下面示例所示：
```
new SpringApplicationBuilder()
    .sources(Parent.class)
    .child(Application.class)
    .bannerMode(Banner.Mode.OFF)
    .run(args);
```
注释：创建 ApplicationContext 层次结构时有一些限制。例如，Web 组件必须包含在子上下文中，并且父上下文和子上下文都使用相同的环境。有关详细信息，请参阅 [SpringApplicationBuilder Javadoc](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/builder/SpringApplicationBuilder.html)。

## 23.5、应用程序事件和监听器

除了常见的 Spring 框架事件（如ContextRefreshedEvent）之外，SpringApplication 还发送一些附加的应用程序事件。

注释：有些事件实际上是在创建 ApplicationContext 之前触发的，因此不能将监听器注册为 @Bean。你可以使用 SpringApplication.addListeners(...) 方法或 SpringApplicationBuilder.listeners(...) 方法注册它们。如果希望自动注册这些监听器而不管创建应用的方式，则可以将 META-INF/spring.factories 文件添加到项目中并且通过使用 org.springframework.context.ApplicationListener 键来引用监听器，如下面示例所示：

    org.springframework.context.ApplicationListener=com.example.project.MyListener

应用程序运行时，将按以下顺序发送应用程序事件：

    （1）ApplicationStartingEvent 在运行开始时但在任何处理之前发送，监听器和初始化器的注册除外。
    （2）ApplicationEnvironmentPreparedEvent 在上下文中使用的环境已知时但在创建上下文之前发送。
    （3）ApplicationPreparedEvent 只在开始刷新之前但在加载 bean 定义之后发送。
    （4）ApplicationStartedEvent 在刷新上下文之后但在调用任何应用程序和命令行的运行器之前发送。
    （5）ApplicationReadyEvent 在调用任何应用程序和命令行运行器之后发送。这表明应用已准备好服务请求。
    （6）ApplicationFailedEvent 在启动出现异常时发送。

提示：你通常不需要使用应用程序事件，但知道它们的存在是很方便的。在内部，Spring Boot 使用事件来处理各种任务。

应用程序事件通过使用 Spring 框架的事件发布机制发送。此机制的一部分确保在子上下文中发布给监听器的事件也在任何祖先上下文中发布给监听器。因此，如果应用程序使用 SpringApplication 实例的层次结构，则监听器可能会接收到同一类型应用程序事件的多个实例。

为了允许监听器区分其上下文的事件和子上下文的事件，它应该请求注入其应用程序上下文，然后将注入的上下文与事件的上下文进行比较。上下文可以通过实现 ApplicationContextAware 注入，如果监听器是 bean，则可以使用 @Autowired 注入。

## 23.6、Web 环境

SpringApplication 视图代表你创建正确类型的 ApplicationContext。用于确定 WebApplicationType 的算法相当简单：

    （1）如果存在 Spring MVC，则使用一个 AnnotationConfigServletWebServerApplicationContext。
    （2）如果 Spring MVC 不存在而 Spring WebFlux 存在，则使用一个 AnnotationConfigReactiveWebServerApplicationContext。
    （3）否则，使用 AnnotationConfigApplicationContext。

这意味着，如果你在同一个应用程序中使用 Spring MVC 和 Spring WebFlux 的新 WebClient，则默认使用 Spring MVC。你可以通过调用 setWebApplicationType(WebApplicationType) 轻松地覆盖它。

还可以完全控制调用 setApplicationContextClass(...) 所使用的 ApplicationContext 类型。

    提示：在 JUnit 测试中使用 SpringApplication 时，通常需要调用 setWebApplicationType(WebApplication.NONE)。

## 23.7、访问应用程序参数

如果需要访问传递给 SpringApplication.run() 的应用程序参数，可以注入 org.springframework.boot.ApplicationArguments bean。ApplicationArguments 接口提供对原始 String[] 参数、解析过的选项和非选项参数的访问，如下面的示例所示：
```
import org.springframework.boot.*;
import org.springframework.beans.factory.annotation.*;
import org.springframework.stereotype.*;

@Component
public class MyBean {

    @Autowired
    public MyBean(ApplicationArguments args) {
        boolean debug = args.containsOption("debug");
        List<String> files = args.getNonOptionArgs();
        // if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
    }

}
```
    提示：Spring Boot 还向 Spring Environment 注册 CommandLinePropertySource。这还允许你使用 @Value 注解注入单个应用程序参数。

## 23.8、使用 ApplicationRunner 或 CommandLineRunner

如果在 SpringApplication 启动后需要运行某些特定代码，可以实现 ApplicationRunner 或 CommandLineRunner 接口。这两个接口以相同的方式工作，并提供一个单独的 run 方法，该方法只在 SpringApplication.run() 完成之前被调用。

CommandLineRunner 接口以简单的字符串数组形式提供对应用程序参数的访问，而 ApplicationRunner 使用前面讨论的 ApplicationArguments 接口。以下示例显示了带有 run 方法的 CommandLineRunner：
```
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

    public void run(String... args) {
        // Do something...
    }

}	
```
如果定义了几个必须按特定顺序调用的 CommandLineRunner 或 ApplicationRunner bean，则可以另外实现 org.springframework.core.Ordered 接口或使用 org.springframework.core.annotation.Order 注解。

## 23.9、应用退出

每个 SpringApplication 向 JVM 注册一个关闭钩子，以确保 ApplicationContext 在退出时优雅地关闭。可以使用所有标准的 Spring 声明周期回调（例如：DisposableBean 接口或 @PreDestroy 注解）。

另外，如果 beans 希望在调用 SpringApplication.exit() 时返回特定的退出代码，则它们可以实现 org.springframework.boot.ExitCodeGenerator 接口。然后，这个退出代码可以传递给 System.exit() 以将它作为状态代码返回，如下面的示例所示：
```
@SpringBootApplication
public class ExitCodeApplication {

    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
        return () -> 42;
    }

    public static void main(String[] args) {
        System.exit(SpringApplication.exit(SpringApplication.run(ExitCodeApplication.class, args)));
    }

}
```
此外，ExitCodeGenerator 接口可以由异常实现。当遇到这样的异常时，Spring Boot 将返回实现的 getExitCode() 方法提供的退出代码。

## 23.10、管理（Admin）功能

可以通过指定 spring.application.admin.enabled 属性来为应用程序启用与管理相关的功能。这将在 MBeanServer 平台上公开 SpringApplicationAdminMXBean。你可以使用此功能远程管理 Spring Boot 应用程序。这个特性对于任何服务包装器实现都很有用。
```
提示：如果你想知道应用程序在哪个 HTTP 端口上运行，请使用 local.server.port 键获取该属性值。
```
    注意：启用此功能时要小心，因为 MBean 会公开一个方法来关闭应用程序。
