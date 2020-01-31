# 29、开发 Web 应用程序

Spring Boot 非常适合 web 应用程序开发。可以使用嵌入式 Tomcat、Jetty、Undertow 或 Netty 创建自包含的 HTTP 服务器。大多数 web 应用程序使用 spring-boot-starter-web 模块快速启动和运行。还可以选择通过使用 spring-boot-starter-webflux 模块来构建反应式 web 应用程序。

如果你还没有开发一个 Spring Boot web 应用程序，则可以遵循入门章节的“Hello World!”示例。

## 29.1、Spring Web MVC 框架

Spring Web MVC 框架（通常简称为“Spring MVC”）是一个丰富的“模型-视图-控制器”Web 框架。Spring MVC 允许你创建特殊的 @Controller 或 RestController bean 来处理传入的 HTTP 请求。控制器中的方法通过使用 @RequestMapping 注解映射到 HTTP。

下面的代码显示了一个典型的 @RestController，它提供 JSON 数据：
```
@RestController
@RequestMapping(value="/users")
public class MyRestController {

    @RequestMapping(value="/{user}", method=RequestMethod.GET)
    public User getUser(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}/customers", method=RequestMethod.GET)
    List<Customer> getUserCustomers(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}", method=RequestMethod.DELETE)
    public User deleteUser(@PathVariable Long user) {
        // ...
    }

}
```
Spring MVC 是核心 Spring 框架的一部分，详细信息可以在参考文档中找到。在 spring.io/guides 中也有一些关于 Spring MVC 的指南。（https://spring.io/guides ）

### 29.1.1、Spring MVC 自动配置

Spring Boot 为 Spring MVC 提供了自动配置，它可以很好地与大多数应用程序一起工作。

自动配置在 Spring 默认设置的基础上添加以下特性：

    （1）包含 ContentNegotiatingViewResolver 和 BeanNameViewResolver bean
    （2）支持为静态资源提供服务，包括对 WebJars 的支持（本文档后面将介绍）。
    （3）Converter、GenericConverter 和 Formatter beans 的自动注册。
    （4）支持 HttpMessageConverters（本文档稍后将介绍）。
    （5）MessageCodesResolver 的自动注册（本文档稍后将介绍）。
    （6）支持静态 index.html
    （7）支持自定义 Favicon（本文档稍后将介绍）
    （8）ConfigurableWebBindingInitializer bean 的自动使用（本文档稍后将介绍）

如果你希望保留 Spring Boot MVC 功能，并且希望添加其他 MVC 配置（拦截器、格式化器、视图控制器和其他功能），则可以添加自己的 WebMvcConfigurer 类型的 @Configuration 类，但是不需要 @EnableWebMvc。如果希望提供 RequestMappingHandlerMapping、RequestMappingHandlerAdapter 或 ExceptionHandlerExceptionResolver 的自定义实例，则可以声明 WebMvcRegistrationsAdapter 实例来提供此类组件。

如果你想完全控制 Spring MVC，则可以添加自己的 @Configuration，并用 @EnableWebMvc 注解。

### 29.1.2、HttpMessageConverters

Spring MVC 使用 HttpMessageConverter 接口来转换 HTTP 请求和响应。合理的默认值是开箱即用的。例如，对象可以自动转换为 JSON（通过使用 Jackson 库）或 XML（通过使用 Jackson XML 扩展（如果可用），或者通过使用 JAXB（如果 Jackson XML 扩展不可用））。默认情况下，字符串是用 UTF-8 编码的。

如果需要添加或自定义转换器，可以使用 Spring Boot 的 HttpMessageConverters 类，如下所示：
```
import org.springframework.boot.autoconfigure.http.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration
public class MyConfiguration {

    @Bean
    public HttpMessageConverters customConverters() {
        HttpMessageConverter<?> additional = ...
        HttpMessageConverter<?> another = ...
        return new HttpMessageConverters(additional, another);
    }

}
```
上下文中存在的任何 HttpMessageConverter bean 都将添加到转换器列表中。你也可以用同样的方法覆盖默认转换器。

### 29.1.3、自定义 JSON Serializers 和 Deserializers

如果使用 Jackson 序列化和反序列化 JSON 数据，则可能需要编写自己的 JsonSerializer 和 JsonDeserializer 类。自定义序列化器通常通过模块在 Jackson 中注册，但是 Spring Boot 提供了一个替代的 @JsonComponent 注解，使得直接注册 Spring bean 更加容易。

你可以直接在 JsonSerializer 或 JsonDeserializer 实现上使用 @JsonComponent 注解。你还可以将它用于包含序列化器/反序列化器作为内部类的类，如下面示例所示：
```
import java.io.*;
import com.fasterxml.jackson.core.*;
import com.fasterxml.jackson.databind.*;
import org.springframework.boot.jackson.*;

@JsonComponent
public class Example {

    public static class Serializer extends JsonSerializer<SomeObject> {
        // ...
    }

    public static class Deserializer extends JsonDeserializer<SomeObject> {
        // ...
    }

}
```
ApplicationContext 中的所有 @JsonComponent bean 都会自动注册到 Jackson。因为 @JsonComponent 是用 @Component 进行元注解的，所以通常的组件扫描规则是适用的。

Spring Boot 还提供了 JsonObjectSerializer 和 JsonObjectDeserializer 基类，它们在序列化对象时提供了标准 Jackson 版本的有用替代品。有关详细信息，请参阅 Javadoc 中的 JsonObjectSerializer 和 JsonObjectDeserializer。

### 29.1.4、MessageCodesResolver

Spring MVC 有一个生成错误代码的策略：MessageCodesResolver，用于呈现来自绑定错误的错误消息。如果设置 spring.mvc.message-codes-resolver.format 属性为 PREFIX_ERROR_CODE 或 POSTFIX_ERROR_CODE，则Spring Boot 会为你创建一个（请参阅 [DefaultMessageCodesResolver.Format](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/validation/DefaultMessageCodesResolver.Format.html) 中的例子）。

### 29.1.5、静态内容

默认情况下，Spring Boot 从类路径中名为 /static（或 /public、/resources、/META-INF/resources）的目录或 ServletContext 的根目录提供静态内容。它使用来自 Spring MVC 的 ResourceHttpRequestHandler，这样你就可以通过添加自己的 WebMvcConfigurer 并重写 addResourceHandlers 方法来修改该行为。

在独立的 web 应用程序中，还启用了容器中的缺省 servlet，并充当备用机制，如果 Spring 决定不处理该 servlet，则从 ServletContext 的根目录提供内容。在大多数情况下，这种情况不会发生（如果没有修改默认的 MVC 配置），因为 Spring 总是可以通过 DispatcherServlet 处理请求。

默认情况下，资源映射在 /\*\*，但可以使用 spring.mvc.static-path-pattern 属性对其进行优化。例如，可以将所有资源重新定位到 /resources/** 中，如下所示：

    spring.mvc.static-path-pattern=/resources/**

还可以使用 spring.resources.static-locations 属性（用目录位置列表替换默认值）自定义静态资源位置。根 Servlet 上下文路径“/”也会自动添加为一个位置。

除了前面提到的“标准”静态资源位置之外，[Webjars content](https://www.webjars.org) 还有一个特殊情况。如果路径为 /webjars/** 的任何资源是以 Webjars 格式打包的，那么这些资源都是从 jar 文件中提供的。

提示：如果应用程序打包为 jar，请不要使用 src/main/webapp 目录。尽管这个目录是一个通用的标准，但它只适用于 war 打包，而且如果生成 jar，大多数构建工具都会忽略它。

Spring Boot 还支持 Spring MVC 提供的高级资源处理特性，允许使用诸如缓存破坏（cache-busting）静态资源或对 Webjars 使用与版本无关的 URLs 等用例。

要对 Webjars 使用版本无关的 URLs，请添加 webjars-locator-core 依赖项。然后声明你的 Webjar。以 jQuery 为例，添加“/webjars/jquery/jquery.min.js”会导致“/webjars/jquery/x.y.z/jquery.min.js”。其中 x.y.z 是 Webjar 版本。	

注释：如果使用 JBoss，则需要声明 webjars-locator-jboss-vfs 依赖项，而不是 webjars-locator-core。否则，所有 Webjars 都将解析为 404。

为了使用缓存破坏，下面的配置为所有静态资源配置了缓存破坏解决方案，有效地在 URLs 中添加了内容散列，如 &lt;link href="/css/spring-2a2d595e6ed9a0b24f027f2b63b134d6.css"/&lt;：

    spring.resources.chain.strategy.content.enabled=true
    spring.resources.chain.strategy.content.paths=/**

注释：到资源的链接在运行时在模板中重写，这要感谢为 Thymeleaf 和 FreeMarker 自动配置的 ResourceUrlEncodingFilter。使用 JSPs 时应该手动声明此过滤器。其他模板引擎目前不被自动支持，但是可以使用自定义模板宏/帮助程序和 ResourceUrlProvider。

例如，当使用 JavaScript 模块加载器动态加载资源时，重命名文件不是一个选项。这就是为什么其他策略也得到支持并可以结合起来。“fixed”策略在 URL 中添加静态版本字符串，而不更改文件名，如下面示例所示：

    spring.resources.chain.strategy.content.enabled=true
    spring.resources.chain.strategy.content.paths=/**
    spring.resources.chain.strategy.fixed.enabled=true
    spring.resources.chain.strategy.fixed.paths=/js/lib/
    spring.resources.chain.strategy.fixed.version=v12

通过这种配置，“/js/lib/”下的 JavaScript 模块使用一种固定的版本控制策略（"/v12/js/lib/mymodule.js"），而其他资源仍然使用内容策略（&lt;link href="/css/spring-2a2d595e6ed9a0b24f027f2b63b134d6.css"/&gt;）。

有关更多支持的选项，请参阅 [ResourceProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/ResourceProperties.java)。

提示：这个特性已经在一篇专门的博客文章和 Spring 框架的参考文档中详细描述过了。

29.1.6、欢迎页




29.1.7、自定义 favicon




29.1.8、路径匹配与内容协商




29.1.9、ConfigurableWebBindingInitializer




29.1.10、模板引擎




29.1.11、错误处理




29.1.12、Spring HATEOAS




29.1.13、CORS 支持




29.2 Spring WebFlux 框架




29.2.1、Spring WebFlux 自动配置




29.2.2、带有 HttpMessageReaders 和 HttpMessageWriters 的 HTTP 编解码器




29.2.3、静态内容




29.2.4、模板引擎




29.2.5、错误处理




29.2.6、Web 过滤器




29.3、JAX-RS 和 Jersey




29.4、嵌入式 Servlet 容器支持




29.4.1、Servlet、过滤器和监听器




29.4.2、Servlet 上下文初始化




29.4.3、ServletWebServerApplicationContext




29.4.4、自定义嵌入式 Servlet 容器




29.4.5、JSP 限制




29.5、嵌入式反应式 Server 支持




29.6、反应式 Server 资源配置











