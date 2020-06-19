# 78、嵌入式 Web 服务器

每个 Spring Boot web 应用程序都包括一个嵌入式 web 服务器。此功能导致了许多操作方法问题，包括如何更改嵌入式服务器以及如何配置嵌入式服务器。本节回答了这些问题。

## 78.1、使用其他 Web 服务器

许多 Spring Boot starters 包括默认的嵌入式容器。

（1）对于 servlet 堆栈应用程序，spring-boot-starter-web 通过包括 spring-boot-starter-tomcat 包括 Tomcat，但是你可以使用 spring-boot-starter-jetty 或 spring-boot-starter-undertow。

（2）对于反应式堆栈应用程序，spring-boot-starter-webflux 通过包括 spring-boot-starter-reactor-netty 包括 Reactor，但是你可以使用 spring-boot-starter-tomcat、spring-boot-starter-jetty 或 spring-boot-starter-undertow。

切换到不同的 HTTP 服务器时，除了包含所需的依赖项之外，还需要排除默认依赖项。Spring Boot 为 HTTP 服务器提供了单独的 starters，以帮助使此过程尽可能简单。

下面的 Maven 示例展示了如何为 Spring MVC 排除 Tomcat 并包含 Jetty：
```
<properties>
    <servlet-api.version>3.1.0</servlet-api.version>
</properties>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- Exclude the Tomcat dependency -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- Use Jetty instead -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```
注释：Servlet API 的版本已被覆盖，因为与 Tomcat 9 和 Undertow 2.0 不同，Jetty 9.4 不支持 Servlet 4.0。

下面的 Gradle 示例展示如何为 Spring WebFlux 排除 Netty 并包括 Undertow：
```
configurations {
    // exclude Reactor Netty
    compile.exclude module: 'spring-boot-starter-reactor-netty'
}

dependencies {
    compile 'org.springframework.boot:spring-boot-starter-webflux'
    // Use Undertow instead
    compile 'org.springframework.boot:spring-boot-starter-undertow'
    // ...
}
```
注释：使用 WebClient 类需要 spring-boot-starter-reactor-netty，因此即使需要包含不同的 HTTP 服务器，也可能需要对 Netty 保持依赖。

## 78.2、禁用 Web 服务器

如果你的类路径包含启动 web 服务器所需的信息，Spring Boot 将自动启动它。要禁用此行为，请在 application.properties 中配置 WebApplicationType，如下面示例所示：

    spring.main.web-application-type=none

## 78.3、更改 HTTP 端口

在独立应用程序中，主 HTTP 端口默认为 8080，但可以使用 server.port 设置（例如，在 application.properties 中或作为系统属性）。由于 Environment 值的松散绑定，你还可以使用 SERVER_PORT（例如，作为 OS（操作系统）环境变量）。

要完全关闭 HTTP 端点，但仍然创建 web 应用程序上下文，请使用 server.port =-1。（这样做有时对测试有用。）

有关更多详细信息，请参见“Spring Boot 功能”部分中的[第 29.4.4 节：定制嵌入式 Servlet 容器](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-customizing-embedded-containers)，或 [ServerProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/ServerProperties.java) 源代码。

## 78.4、使用随机未分配的 HTTP 端口

要扫描可用端口（使用操作系统本机来防止冲突），请使用 server.port=0。

## 78.5、在运行时发现 HTTP 端口

你可以通过日志输出或 ServletWebServerApplicationContext 的 WebServer 访问服务器正在运行的端口。获得它并确保它已经被初始化的最好方法是添加一个 ApplicationListener<ServletWebServerInitializedEvent> 类型的 @Bean，并在发布时将容器从事件中拉出。

使用 @SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT) 的测试也可以使用 @LocalServerPort 注解将实际端口注入字段，如下面示例所示：
```
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)
public class MyWebIntegrationTests {

    @Autowired
    ServletWebServerApplicationContext server;

    @LocalServerPort
    int port;

    // ...

}
```
注释：@LocalServerPort 是 @Value("${local.server.port}") 的元注解。不要尝试在常规应用程序中注入该端口。正如我们刚刚看到的，只有在容器初始化后才设置该值。与测试相反，应用程序代码回调是在早期处理的（在该值实际可用之前）。

## 78.6、启用 HTTP 响应压缩

Jetty、Tomcat 和 Undertow 支持 HTTP 响应压缩。它可以在 application.properties 中启用，如下所示：

    server.compression.enabled=true

默认情况下，要执行压缩，响应长度必须至少为 2048 字节。你可以通过设置 server.compression.min-response-size 属性来配置此行为。

默认情况下，只有当响应的内容类型为以下类型之一时，才会压缩响应：

    text/html
    text/xml
    text/plain
    text/css
    text/javascript
    application/javascript
    application/json
    application/xml

可以通过设置 server.compression.mime-types 属性来配置此行为。

## 78.7、配置 SSL

可以通过设置各种 server.ssl.* 属性（通常在 application.properties 或 application.yml 中）以声明方式配置 SSL。以下示例展示如何在 application.properties 中设置 SSL 属性：

    server.port=8443
    server.ssl.key-store=classpath:keystore.jks
    server.ssl.key-store-password=secret
    server.ssl.key-password=another-secret

有关所有受支持属性的详细信息，请参阅 [Ssl](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/web/server/Ssl.java)。

使用配置（如上述示例）意味着应用程序不再支持端口 8080 上的普通 HTTP 连接器。Spring Boot 不支持通过 application.properties 配置 HTTP 连接器和 HTTPS 连接器。如果你想同时拥有两个，则需要以编程方式配置其中一个。我们建议使用 application.properties 来配置 HTTPS，因为 HTTP 连接器更容易以编程方式配置。有关示例，请参见 [spring-boot-sample-tomcat-multi-connectors](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-samples/spring-boot-sample-tomcat-multi-connectors) 示例项目。

## 78.8、配置 HTTP/2

你可以使用 server.http2.enabled 配置属性在 Spring Boot 应用程序中启用 HTTP/2 支持。这种支持取决于所选的 web 服务器和应用程序环境，因为 JDK8 不支持该协议。

注释：Spring Boot 不支持 h2c，即 HTTP/2 协议的明文版本。因此，你必须首先配置 SSL。

### 78.8.1、Undertow 中的 HTTP/2

从 Undertow 1.4.0+ 开始，支持 HTTP/2，对 JDK8 没有任何附加要求。

### 78.8.2、Jetty 中的 HTTP/2

从 Jetty 9.4.8 开始，[Conscrypt 库](https://www.conscrypt.org/)也支持 HTTP/2。要实现这种支持，你的应用程序需要有两个额外的依赖项：org.eclipse.jetty:jetty-alpn-conscrypt-server 和 org.eclipse.jetty.http2:http2-server。

### 78.8.3、Tomcat 中的 HTTP/2

默认情况下，Spring Boot 附带 Tomcat 9.0.x，它在使用 JDK 9 或更高版本时支持开箱即用的 HTTP/2。或者，如果主机操作系统上安装了 libtcnative 库及其依赖项，则可以在 JDK 8 上使用 HTTP/2。

库文件夹必须提供给 JVM 库路径（如果尚未提供）。你可以通过一个 JVM 参数来实现，比如 -Djava.library.path=/usr/local/opt/tomcat native/lib。在官方的 Tomcat 文档中有更多关于这个的信息。

在没有本地支持的 JDK 8 上启动 Tomcat 9.0.x 会记录以下错误：

    ERROR 8787 --- [           main] o.a.coyote.http11.Http11NioProtocol      : The upgrade handler [org.apache.coyote.http2.Http2Protocol] for [h2] only supports upgrade via ALPN but has been configured for the ["https-jsse-nio-8443"] connector that does not support ALPN.

此错误不是致命的，应用程序仍然以 HTTP/1.1 SSL 支持启动。

### 78.8.4、Reactor Netty 中的 HTTP/2

默认情况下，spring-boot-webflux-starter 使用 Reactor Netty 作为服务器。Reactor Netty 可以使用 JDK 9 或更高版本的 JDK 支持为 HTTP/2 配置。对于 JDK 8 环境，或者为了获得最佳的运行时性能，该服务器还支持带有本地库的 HTTP/2。要实现这一点，你的应用程序需要有一个附加的依赖项。

Spring Boot 管理 io.netty:netty-tcnative-boringssl-static “uber jar” 的版本，包含所有平台的本地库。开发者可以选择使用分类器仅导入所需的依赖项（请参阅 [Netty 官方文档](https://netty.io/wiki/forked-tomcat-native.html)）。

## 78.9、配置 Web 服务器

通常，你应该首先考虑使用许多可用配置键中的一个，并通过在 application.properties（或 application.yml、environment 等。请参见第 77.8 节：发现外部属性的内置选项）中添加新条目来自定义 web 服务器。在这里，server.* 命名空间非常有用，它包括 server.tomcat.*、server.jetty.* 等用于服务器特定功能的命名空间。见[附录A：常用应用程序属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/common-application-properties.html)的列表。

前面的小节已经介绍了许多常见的用例，例如压缩、SSL 或 HTTP/2。但是，如果为你的用例的配置键不存在，那么你应该查看 [WebServerFactoryCustomizer](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/web/server/WebServerFactoryCustomizer.html)。你可以声明这样一个组件并访问与你的选择相关的服务器工厂：你应该为所选服务器（Tomcat、Jetty、Reactor Netty、Undertow）和所选 web 堆栈（Servlet或Reactive）选择变体。

下面的示例是使用 spring-boot-starter-web（Servlet 堆栈）的 Tomcat：
```
@Component
public class MyTomcatWebServerCustomizer
        implements WebServerFactoryCustomizer<TomcatServletWebServerFactory> {

    @Override
    public void customize(TomcatServletWebServerFactory factory) {
        // customize the factory here
    }
}
```
此外，Spring Boot 还提供：

|服务器		|Servlet 堆栈				|Reactive 堆栈|
|---|---|---|
|Tomcat		|TomcatServletWebServerFactory		|TomcatReactiveWebServerFactory|
|Jetty		|JettyServletWebServerFactory		|JettyReactiveWebServerFactory|
|Undertow	|UndertowServletWebServerFactory		|UndertowReactiveWebServerFactory|
|Reactor		|N/A					|NettyReactiveWebServerFactory|

一旦你获得了对 WebServerFactory 的访问权，你通常可以向其中添加定制器来配置特定的部分，如连接器、服务器资源或服务器本身 -- 所有这些都使用特定于服务器的 APIs。

作为最后的手段，你还可以声明自己的 WebServerFactory 组件，它将覆盖 Spring Boot 提供的组件。在这种情况下，你不能再依赖 server 命名空间中的配置属性。

## 78.10、向应用程序添加 Servlet 、过滤器或监听器

在 servlet 堆栈应用程序中，即使用 spring-boot-starter-web，有两种方法可以将 Servlet、Filter、ServletContextListener 和 Servlet API 支持的其他监听器添加到应用程序中：

（1）第 78.10.1 节：使用 Spring Bean 添加 Servlet、Filter 或 Listener

（2）第 78.10.2 节：使用类路径扫描添加 Servlets、Filters 或 Listeners

### 78.10.1、使用 Spring Bean 添加 Servlet 、过滤器或监听器

要使用 Spring bean 添加 Servlet 、Filter 或 Servlet *Listener，必须为其提供 @Bean 定义。当你想要注入配置或依赖项时，这样做非常有用。但是，你必须非常小心，以免它们引起太多其他 bean 的急切初始化，因为它们必须在应用程序生命周期的早期安装在容器中。（例如，让它们依赖于你的 DataSource 或 JPA 配置不是一个好主意。）你可以通过在第一次使用时而不是在初始化时延迟初始化 bean 来解决这些限制。

对于 Filters 和 Servlets，你还可以通过添加 FilterRegistrationBean 或 ServletRegistrationBean 来添加映射和初始化参数，而不是添加到底层组件中。

注释：如果没有在过滤器注册上指定 dispatcherType，则使用 REQUEST。这与 Servlet 规范的默认调度器类型保持一致。

像任何其他 Spring bean 一样，你可以定义 Servlet 过滤器 bean 的顺序；请确保检查[“注册 Servlets、Filters 和 Listeners 作为 Spring Beans” 部分](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-embedded-container-servlets-filters-listeners-beans)。

**禁用 Servlet 或 Filter 的注册**

如前所述，任何 Servlet 或 Filter bean 都会自动注册到 servlet 容器中。要禁用特定 Filter 或 Servlet bean 的注册，请为其创建一个注册 bean，并将其标记为已禁用，如下面示例所示：
```
@Bean
public FilterRegistrationBean registration(MyFilter filter) {
    FilterRegistrationBean registration = new FilterRegistrationBean(filter);
    registration.setEnabled(false);
    return registration;
}
```
### 78.10.2、使用类路径扫描添加 Servlet 、过滤器或监听器

@WebServlet、@WebFilter 和 @WebListener 注解类可以通过使用 @ServletComponentScan 注解 @Configuration 类并指定包含要注册的组件的包来自动注册到嵌入式 servlet 容器中。默认情况下，@ServletComponentScan 从带注解类的包中扫描。

## 78.11、配置访问日志记录

访问日志可以通过其各自的命名空间为 Tomcat、Undertow 和 Jetty 配置。

例如，以下设置使用[自定义模式](https://tomcat.apache.org/tomcat-8.5-doc/config/valve.html#Access_Logging)记录对 Tomcat 的访问。

    server.tomcat.basedir=my-tomcat
    server.tomcat.accesslog.enabled=true
    server.tomcat.accesslog.pattern=%t %a "%r" %s (%D ms)

注释：日志的默认位置是相对于 Tomcat 基本目录的 logs 目录。默认情况下，logs 目录是一个临时目录，因此你可能希望修复 Tomcat 的基本目录或使用日志的绝对路径。在上面的示例中，相对于应用程序的工作目录，日志在 my-tomcat/logs 中可用。

可以以类似的方式配置 Undertow 的访问日志记录，如下面示例所示：

    server.undertow.accesslog.enabled=true
    server.undertow.accesslog.pattern=%t %a "%r" %s (%D ms)

日志存储在相对于应用程序工作目录的 logs 目录中。可以通过设置 server.undertow.accesslog.directory 属性自定义此位置。

最后，Jetty 的访问日志也可以配置如下：

    server.jetty.accesslog.enabled=true
    server.jetty.accesslog.filename=/var/log/jetty-access.log

默认情况下，日志被重定向到 System.err。有关更多详细信息，请参见 [Jetty 文档](https://www.eclipse.org/jetty/documentation/9.4.x/configuring-jetty-request-logs.html)。

## 78.12、在前端代理服务器后面运行

你的应用程序可能需要发送 302 重定向或渲染带有绝对链接的内容。当在代理后面运行时，调用者想要一个指向代理的链接，而不是指向托管你的应用程序的机器的物理地址。通常，这种情况是通过与代理的协定来处理的，该协定添加了头，以告诉后端如何构造指向自身的链接。

如果代理添加了常规的 X-Forwarded-For 和 X-Forwarded-Proto 头（大多数代理服务器都这样做），则应正确呈现绝对链接，前提是在 application.properties 中将 server.use-forward-headers 设置为 true。

注释：如果你的应用程序在 Cloud Foundry 或 Heroku 中运行，则 server.use-forward-headers 属性默认为 true。在所有其他实例中，它默认为 false。

### 78.12.1、自定义 Tomcat 的代理配置

如果使用 Tomcat，则可以另外配置用于携带 “forwarded” 信息的头的名称，如下面示例所示：

    server.tomcat.remote-ip-header=x-your-remote-ip-header
    server.tomcat.protocol-header=x-your-protocol-header

Tomcat 还配置了与要信任的内部代理匹配的默认正则表达式。默认情况下，10/8、192.168/16、169.254/16 和 127/8 中的 IP 地址是可信的。你可以通过将条目添加到 application.properties 来自定义阀门的配置，如下面示例所示：

    server.tomcat.internal-proxies=192\\.168\\.\\d{1,3}\\.\\d{1,3}

注释：只有在使用属性文件进行配置时，才需要使用双反斜杠。如果使用 YAML，单反斜杠就足够了，与前面示例中显示的等效的值是 192\.168\.\d{1,3}.\d{1,3}。

注释：你可以通过将 internal-proxies 设置为空来信任所有代理（但不要在生产环境中这样做）。

通过关闭自动阀门（为此，请设置 server.use-forward-headers=false）并在 TomcatServletWebServerFactory bean 中添加新的阀门实例，可以完全控制 Tomcat 的 RemoteIpValve 的配置。

## 78.13、使用 Tomcat 启用多个连接器

你可以将 org.apache.catalina.connector.Connector 添加到 TomcatServletWebServerFactory，它可以允许多个连接器，包括 HTTP 和 HTTPS 连接器，如下面示例所示：
```
@Bean
public ServletWebServerFactory servletContainer() {
    TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
    tomcat.addAdditionalTomcatConnectors(createSslConnector());
    return tomcat;
}

private Connector createSslConnector() {
    Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
    Http11NioProtocol protocol = (Http11NioProtocol) connector.getProtocolHandler();
    try {
        File keystore = new ClassPathResource("keystore").getFile();
        File truststore = new ClassPathResource("keystore").getFile();
        connector.setScheme("https");
        connector.setSecure(true);
        connector.setPort(8443);
        protocol.setSSLEnabled(true);
        protocol.setKeystoreFile(keystore.getAbsolutePath());
        protocol.setKeystorePass("changeit");
        protocol.setTruststoreFile(truststore.getAbsolutePath());
        protocol.setTruststorePass("changeit");
        protocol.setKeyAlias("apitester");
        return connector;
    }
    catch (IOException ex) {
        throw new IllegalStateException("can't access keystore: [" + "keystore"
                + "] or truststore: [" + "keystore" + "]", ex);
    }
}
```
## 78.14、使用 Tomcat 的 LegacyCookieProcessor

默认情况下，Spring Boot 使用的嵌入式 Tomcat 不支持 Cookie 格式的 “Version 0”，因此你可能会看到以下错误：

    java.lang.IllegalArgumentException: An invalid character [32] was present in the Cookie value

如果可能的话，你应该考虑更新你的代码，只存储符合后来的 Cookie 规范的值。但是，如果你无法更改 cookie 的写入方式，则可以将 Tomcat 配置为使用 LegacyCookieProcessor。要切换到 LegacyCookieProcessor，请使用添加 TomcatContextCustomizer 的 WebServerFactoryCustomizer bean，如下面示例所示：
```
@Bean
public WebServerFactoryCustomizer<TomcatServletWebServerFactory> cookieProcessorCustomizer() {
    return (factory) -> factory
            .addContextCustomizers((context) -> context.setCookieProcessor(new LegacyCookieProcessor()));
}
```
## 78.15、在 Undertow 中启用多个监听器

将 UndertowBuilderCustomizer 添加到 UndertowServletWebServerFactory，并将监听器添加到 Builder，如下面示例所示：
```
@Bean
public UndertowServletWebServerFactory servletWebServerFactory() {
    UndertowServletWebServerFactory factory = new UndertowServletWebServerFactory();
    factory.addBuilderCustomizers(new UndertowBuilderCustomizer() {

        @Override
        public void customize(Builder builder) {
            builder.addHttpListener(8080, "0.0.0.0");
        }

    });
    return factory;
}
```
## 78.16、使用 @ServerEndpoint 创建 WebSocket 端点

如果要在使用嵌入式容器的 Spring Boot 应用程序中使用 @ServerEndpoint，则必须声明单个 ServerEndpointExporter @Bean，如下面示例所示：
```
@Bean
public ServerEndpointExporter serverEndpointExporter() {
    return new ServerEndpointExporter();
}
```
上述示例中显示的 bean 向底层 WebSocket 容器注册任何 @ServerEndpoint 注解的 bean。当部署到独立的 servlet 容器时，此角色由 servlet 容器初始化器执行，并且不需要 ServerEndpointExporter bean。