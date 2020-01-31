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

### 29.1.6、欢迎页

Spring Boot 同时支持静态和模板化欢迎页面。它首先在配置的静态内容位置中查找 index.html 文件。如果没有找到，则查找 index 模板。如果找到其中之一，它将自动用作应用程序的欢迎页面。

### 29.1.7、自定义 favicon

Spring Boot 在配置的静态内容位置和类路径的根目录中查找 favicon.ico（按这个顺序）。如果存在这样的文件，它将自动用作应用程序的 favicon。

### 29.1.8、路径匹配与内容协商（Content Negotiation）

Spring MVC 可以通过查看请求路径并将其与应用程序中定义的映射（例如，Controller 方法上的 @GetMapping 注解）匹配，将传入的 HTTP 请求映射到处理程序。

Spring Boot 默认选择禁用后缀模式匹配，这意味着“GET/projects/spring-boot.json”之类的请求将不会与 @GetMapping("/projects/spring-boot") 映射匹配。这被认为是 Spring MVC 应用程序的最佳实践。这个特性在过去对于没有发送正确的“Accept”请求头的 HTTP 客户端非常有用；我们需要确保向客户端发送正确的 Content Type。如今，内容协商更加可靠。

还有其他方法可以处理不一致地发送正确的“Accept”请求头的 HTTP 客户端。不用后缀匹配，我们可以使用查询参数来确保像“GET/projects/spring-boot?format=json”这样的请求会被映射到 @GetMapping("/projects/spring-boot")：

    spring.mvc.contentnegotiation.favor-parameter=true
    
    # We can change the parameter name, which is "format" by default:
    # spring.mvc.contentnegotiation.parameter-name=myparam
    
    # We can also register additional file extensions/media types with:
    spring.mvc.contentnegotiation.media-types.markdown=text/markdown

如果你理解这些注意事项，并且仍然希望应用程序使用后缀模式匹配，则需要以下配置：

    spring.mvc.contentnegotiation.favor-path-extension=true
    spring.mvc.pathmatch.use-suffix-pattern=true

或者，与其打开所有后缀模式，不如只支持注册的后缀模式更安全。

    spring.mvc.contentnegotiation.favor-path-extension=true
    spring.mvc.pathmatch.use-registered-suffix-pattern=true
    
    # You can also register additional file extensions/media types with:
    # spring.mvc.contentnegotiation.media-types.adoc=text/asciidoc

### 29.1.9、ConfigurableWebBindingInitializer

Spring MVC 使用 WebBindingInitializer 为特定请求初始化 WebDataBinder。如果你创建自己的 ConfigurableWebBindingInitializer @Bean，则 Spring Boot 会自动配置 Spring MVC 来使用它。

### 29.1.10、模板引擎

除了 REST web 服务，你还可以使用 Spring MVC 来提供动态 HTML 内容。Spring MVC 支持多种模板技术，包括：Thymeleaf、FreeMarker 和 JSPs。另外，许多其他的模板引擎还包括它们自己的 Spring MVC 集成。

Spring Boot 包括对以下模板引擎的自动配置支持：

（1）[FreeMarker](https://freemarker.apache.org/docs/)

（2）[Groovy](http://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_the_markuptemplateengine)

（3）[Thymeleaf](https://www.thymeleaf.org/)

（4）[Mustache](https://mustache.github.io/)

提示：如果可能，应避免使用 JSPs。在将它们与嵌入式 servlet 容器一起使用时，有几个已知的限制。

当你使用这些具有默认配置的模板引擎之一时，你的模板将自动从 src/main/resources/templates 中获取。

提示：根据你运行应用程序的方式，IntelliJ IDEA 对类路径的排序是不同的。在 IDE 中从主方法运行应用程序的顺序与使用 Maven 或 Gradle 或从其打包的 jar 运行应用程序的顺序不同。这可能会导致 Spring Boot 无法在类路径上找到模板。如果遇到此问题，可以在 IDE 中重新排序类路径，以将模块的类和资源放在第一位。或者，你可以配置模板前缀来搜索类路径上的每个模板目录，如下所示：classpath*:/templates/。

### 29.1.11、错误处理

默认情况下，Spring Boot 提供了一个 /error 映射，它以合理的方式处理所有错误，并在 servlet 容器中将其注册为“global”错误页。对于机器客户端，它生成一个 JSON 响应，其中包含错误、HTTP状态和异常消息的详细信息。对于浏览器客户端，有一个“whitelabel”错误视图，它以 HTML 格式呈现相同的数据（要对其进行自定义，请添加一个解析为 error 的 View）。若要完全替换默认行为，则可以实现 ErrorController 并注册该类型的 bean 定义，或者添加 ErrorAttributes 类型的 bean 来使用现有机制，但替换内容。

提示：BasicErrorController 可以用作自定义 ErrorController 的基类。如果要为新内容类型添加处理程序（默认情况下是专门处理 text/html，并为其他所有内容提供回退），这一点特别有用。为此，扩展 BasicErrorController，添加一个带有 products 属性的 @RequestMapping 的公共方法，并创建一个新类型的 bean。

你还可以定义一个用 @ControllerAdvice 注解的类来定制 JSON 文档，以返回特定的控制器和/或异常类型，如下面示例所示：
```
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

    @ExceptionHandler(YourException.class)
    @ResponseBody
    ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
        HttpStatus status = getStatus(request);
        return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
    }

    private HttpStatus getStatus(HttpServletRequest request) {
        Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
        if (statusCode == null) {
            return HttpStatus.INTERNAL_SERVER_ERROR;
        }
        return HttpStatus.valueOf(statusCode);
    }

}
```
在前面的示例中，如果 YourException 是由与 AcmeController 在同一个包中定义的控制器抛出的，则使用 CustomErrorType POJO 的 JSON 表示，而不是 ErrorAttributes 表示。

**自定义错误页**

如果要显示给定状态码的自定义 HTML 错误页，则可以将文件添加到 /error 文件夹。错误页可以是静态 HTML（即添加到任何静态资源文件夹下）或使用模板生成。文件名应该是确切的状态码或序列掩码。

例如，要将 404 映射到静态 HTML 文件，文件夹结构如下：

    src/
     +- main/
         +- java/
         |   + <source code>
         +- resources/
             +- public/
                 +- error/
                 |   +- 404.html
                 +- <other public assets>

要使用 FreeMarker 模板映射所有 5xx 错误，文件夹结构如下：

    src/
     +- main/
         +- java/
         |   + <source code>
         +- resources/
             +- templates/
                 +- error/
                 |   +- 5xx.ftl
                 +- <other templates>

对于更复杂的映射，还可以添加实现 ErrorViewResolver 接口的 bean，如下面所示：
```
public class MyErrorViewResolver implements ErrorViewResolver {

    @Override
    public ModelAndView resolveErrorView(HttpServletRequest request,
            HttpStatus status, Map<String, Object> model) {
        // Use the request or status to optionally return a ModelAndView
        return ...
    }

}
```
你还可以使用常规的 Spring MVC 特性，比如 @ExceptionHandler 方法和 @ControllerAdvice。然后，ErrorController 将拾取任何未处理的异常。

**在 Spring MVC 之外映射错误页**

对于不使用 Spring MVC 的应用程序，可以使用 ErrorPageRegistrar 接口直接注册 ErrorPages。这种抽象直接与底层的嵌入式 servlet 容器一起工作，即使没有 Spring MVC DispatcherServlet。
```
@Bean
public ErrorPageRegistrar errorPageRegistrar(){
    return new MyErrorPageRegistrar();
}

// ...

private static class MyErrorPageRegistrar implements ErrorPageRegistrar {

    @Override
    public void registerErrorPages(ErrorPageRegistry registry) {
        registry.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
    }

}
```
如果使用最终由 Filter 处理的路径注册 ErrorPage（这在某些非 Spring web 框架中很常见，如 Jersey 和 Wicket），则必须将 Filter 显示注册为 ERROR 调度器，如下面示例所示：
```
@Bean
public FilterRegistrationBean myFilter() {
    FilterRegistrationBean registration = new FilterRegistrationBean();
    registration.setFilter(new MyFilter());
    ...
    registration.setDispatcherTypes(EnumSet.allOf(DispatcherType.class));
    return registration;
}
```
注意，默认的 FilterRegistrationBean 不包括错误调度器类型。

注意：当部署到 servlet 容器时，Spring Boot 使用其错误页过滤器将带有错误状态的请求转发到相应的错误页。如果尚未提交响应，则只能将请求转发到正确的错误页面。默认情况下，WebSphere Application Server 8.0 及更高版本在成功完成 servlet 服务方法后提交响应。你应该通过将 com.ibm.ws.webcontainer.invokeFlushAfterService 设置为 false 来禁用此行为。

### 29.1.12、Spring HATEOAS

如果你开发了一个使用超媒体的 RESTful API，那么 Spring Boot 为 Spring HATEOAS 提供了自动配置，它可以很好地与大多数应用程序一起工作。自动配置取代了使用 @EnableHypermediaSupport 的需要，并注册了许多 bean 来简化基于超媒体的应用程序的构建，包括一个 LinkDiscoverers（用于客户端支持）和一个 ObjectMapper（配置为正确地将响应封入所需的表示）。ObjectMapper 是通过设置不同的 spring.jackson.* 属性来定制的，或者如果存在的话，可以使用 Jackson2ObjectMapperBuilder 来定制。

你可以使用 @EnableHypermediaSupport 来控制 Spring HATEOAS 的配置。注意，这样做会禁用前面描述的 ObjectMapper 定制。

### 29.1.13、CORS 支持

跨源资源共享（CORS）是由大多数浏览器实现的 W3C 规范，它允许你灵活地指定什么样的跨域请求被授权，而不是使用一些不太安全和不太强大的方法，如 IFRAME 或 JSONP。

从版本 4.2 开始，Spring MVC 支持 CORS。在 Spring Boot 应用程序中使用带有 @CrossOrigin 注解的控制器方法 CORS 配置不需要任何特定的配置。全局 CORS 配置可以通过使用自定义的 addCorsMappings(CorsRegistry) 方法注册 WebMvcConfigurer bean 来定义，如下面示例所示：
```
@Configuration
public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**");
            }
        };
    }
}
```
## 29.2 Spring WebFlux 框架

Spring WebFlux 是 Spring Framework 5.0 中引入的新的反应式 web 框架。与 Spring MVC 不同，它不需要 Servlet API ，完全异步且非阻塞，并通过 Reactor 项目实现反应式流规范。

Spring WebFlux 有两种风格：函数式和基于注解的。基于注解的模型非常接近 Spring MVC 模型，如下面示例所示：
```
@RestController
@RequestMapping("/users")
public class MyRestController {

    @GetMapping("/{user}")
    public Mono<User> getUser(@PathVariable Long user) {
        // ...
    }

    @GetMapping("/{user}/customers")
    public Flux<Customer> getUserCustomers(@PathVariable Long user) {
        // ...
    }

    @DeleteMapping("/{user}")
    public Mono<User> deleteUser(@PathVariable Long user) {
        // ...
    }

}
```
函数变体“WebFlux.fn”将路由配置与请求的实际处理分离，如下面示例所示：
```
@Configuration
public class RoutingConfiguration {

    @Bean
    public RouterFunction<ServerResponse> monoRouterFunction(UserHandler userHandler) {
        return route(GET("/{user}").and(accept(APPLICATION_JSON)), userHandler::getUser)
                .andRoute(GET("/{user}/customers").and(accept(APPLICATION_JSON)), userHandler::getUserCustomers)
                .andRoute(DELETE("/{user}").and(accept(APPLICATION_JSON)), userHandler::deleteUser);
    }

}

@Component
public class UserHandler {

    public Mono<ServerResponse> getUser(ServerRequest request) {
        // ...
    }

    public Mono<ServerResponse> getUserCustomers(ServerRequest request) {
        // ...
    }

    public Mono<ServerResponse> deleteUser(ServerRequest request) {
        // ...
    }
}
```
WebFlux 是 Spring 框架的一部分，其[参考文档](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web-reactive.html#webflux-fn)中提供了详细信息。

提示：你可以定义任意多个 RouterFunction bean 来模块化路由器的定义。如果需要应用优先级，可以对 beans 进行排序。

要开始，请将 spring-boot-starter-webflux 模块添加到应用程序中。

注释：在应用程序中同时添加 spring-boot-starter-web 和 spring-boot-starter-webflux 模块会导致 Spring Boot 自动配置 Spring MVC，而不是 WebFlux。之所以选择这种行为，是因为许多 Spring 开发者将 spring-boot-starter-webflux 添加到他们的 Spring MVC 应用程序中，以使用反应式 WebClient。你仍然可以通过将所选应用程序类型设置为 SpringApplication.setWebApplicationType(WebApplicationType.REACTIVE) 来强制你的选择。

### 29.2.1、Spring WebFlux 自动配置

Spring Boot 为 Spring WebFlux 提供了自动配置，它可以很好地与大多数应用程序一起工作。

自动配置在 Spring 默认设置的基础上添加了以下特性：

    （1）为 HttpMessageReader 和 HttpMessageWriter 实例配置编解码器（本文档稍后介绍）。
    （2）支持为静态资源提供服务，包括对 Webjar 的支持（稍后将在本文中描述）。

如果你想保留 Spring Boot WebFlux 的特性，并且想添加额外的 WebFlux 配置，那么你可以添加自己的类型为 WebFluxConfigurer 类，但是不需要 @EnableWebFlux。

如果您想完全控制 Spring WebFlux，可以添加自己的 @Configuration，并用 @EnableWebFlux 注解。

### 29.2.2、带有 HttpMessageReaders 和 HttpMessageWriters 的 HTTP 编解码器

Spring WebFlux 使用 HttpMessageReader 和 HttpMessageWriter 接口来转换 HTTP 请求和响应。它们是用 CodecConfigurer 配置的，通过查看类路径中可用的库来获得合理的默认值。

Spring Boot 通过使用 CodecCustomizer 实例应用进一步的定制。例如，spring.jackson.* 配置键应用于 Jackson 编解码器。

如果需要添加或自定义编解码器，则可以创建自定义 CodecCustomizer 组件，如下面示例所示：
```
import org.springframework.boot.web.codec.CodecCustomizer;

@Configuration
public class MyConfiguration {

    @Bean
    public CodecCustomizer myCodecCustomizer() {
        return codecConfigurer -> {
            // ...
        }
    }

}
```
你还可以利用 [Boot 的自定义 JSON 序列化器和反序列化器](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-json-components)。

### 29.2.3、静态内容

默认情况下，Spring Boot 从类路径中名为 /static（或 /public 或 /resources 或 /META-INF/resources）的目录提供静态内容。它使用 Spring WebFlux 中的 ResourceWebHandler，这样你就可以通过添加自己的 WebFluxConfigurer 并重写 addResourceHandlers 方法来修改该行为。 

默认情况下，资源被映射到 /\*\*，但是你可以通过设置 spring.webflux.static-path-pattern 属性来优化它。例如，可以将所有资源重新定位到 /resources/** 中，如下所示：

    spring.webflux.static-path-pattern=/resources/**

还可以使用 spring.resources.static-locations 自定义静态资源位置。这样做会将默认值替换为目录位置列表。如果这样做，默认的欢迎页面检测将切换到你的自定义位置。因此，如果在启动时在你的任何位置有 index.html，它就是应用程序的主页。

除了前面的列出的“标准”静态资源位置之外，Webjars 内容还有一个特殊情况。如果以 Webjars 格式打包，那么路径为 /webjars/** 的任何资源都将从 jar 文件中获得。

提示：Spring WebFlux 应用程序并不严格依赖于 Servlet API，因此它们不能作为 war 文件部署，也不能使用 src/main/webapp 目录。

### 29.2.4、模板引擎

除了 REST web 服务，你还可以使用 Spring WebFlux 来提供动态 HTML 内容。Spring WebFlux 支持多种模板技术，包括 Thymeleaf、FreeMarker 和 Mustache。

Spring Boot 包括对以下模板引擎的自动配置支持：

（1）[FreeMarker](https://freemarker.apache.org/docs/)

（2）[Thymeleaf](https://www.thymeleaf.org/)

（3）[Mustache](https://mustache.github.io/)

当你使用这些具有默认配置的模板引擎之一时，你的模板将自动从 src/main/resources/templates 中获取。

### 29.2.5、错误处理

Spring Boot 提供了一个 WebExceptionHandler，它以合理的方式处理所有错误。它在处理顺序中的位置紧接在 WebFlux 提供的处理器之前，后者被认为是最后一个处理器。对于机器客户端，它生成一个 JSON 响应，其中包含错误、HTTP 状态和异常消息的详细信息。对于浏览器客户端，有一个“whitelabel”错误处理器以 HTML 格式呈现相同的数据。你还可以提供自己的 HTML 模板来显示错误（请参阅下一节）。

定制此功能的第一步通常涉及使用现有的机制，但替换或扩充错误内容。为此，可以添加 ErrorAttributes 类型的 bean。

要更改错误处理行为，可以实现 ErrorWebExceptionHandler 并注册该类型的 bean 定义。由于 WebExceptionHandler 是非常低级的，Spring Boot 还提供了一个方便的 AbstractErrorWebExceptionHandler，使你能够以 WebFlux 函数的方式处理错误，如下面示例所示：
```
public class CustomErrorWebExceptionHandler extends AbstractErrorWebExceptionHandler {

    // Define constructor here

    @Override
    protected RouterFunction<ServerResponse> getRoutingFunction(ErrorAttributes errorAttributes) {

        return RouterFunctions
                .route(aPredicate, aHandler)
                .andRoute(anotherPredicate, anotherHandler);
    }

}
```
为了获得更完整的图景，还可以直接将 DefaultErrorWebExceptionHandler 子类化并重写特定的方法。

**自定义错误页**

如果要显示给定状态码的自定义 HTML 错误页，则可以将文件添加到 /error 文件夹。错误页可以是静态 HTML（即添加到任何静态资源文件夹下）或使用模板生成。文件名应为确切的状态码或序列掩码。

例如，要将 404 映射到静态 HTML 文件，文件夹结构如下：

    src/
     +- main/
         +- java/
         |   + <source code>
         +- resources/
             +- public/
                 +- error/
                 |   +- 404.html
                 +- <other public assets>

要使用 Mustache 模板映射所有 5xx 错误，文件夹结构如下：

    src/
     +- main/
         +- java/
         |   + <source code>
         +- resources/
             +- templates/
                 +- error/
                 |   +- 5xx.mustache
                 +- <other templates>

### 29.2.6、Web 过滤器

Spring WebFlux 提供了一个 WebFilter 接口，可以实现该接口来过滤 HTTP 请求-响应交换。在应用程序上下文中找到的 WebFilter bean 将自动用于过滤每个 exchange。

如果过滤器的顺序很重要，那么它们可以实现 Ordered 接口或用 @Order 注解。Spring Boot 自动配置可以为你配置 web 过滤器。执行此操作时，将使用下表中显示的顺序：

|Web Filter				|Order|
|----|----|
|MetricsWebFilter			|Ordered.HIGHEST_PRECEDENCE + 1|
|WebFilterChainProxy(Spring Security)|	-100|
|HttpTraceWebFilter			|Ordered.LOWEST_PRECEDENCE - 10|

## 29.3、JAX-RS 和 Jersey

如果你喜欢 REST 端点的 JAX-RS 编程模型，则你可以使用其中一个可用的实现来代替 Spring MVC。Jersey 和 Apache CXF 的开箱即用效果非常好。CXF 要求你在应用程序上下文中将其 Servlet 或过滤器注册为 @Bean。Jersey 有一些本地的 Spring 支持，因此我们也在 Spring Boot 中为它提供自动配置支持，同时还提供了一个启动器（starter）。

要开始使用 Jersey，需要将 spring-boot-starter-jersey 作为依赖项，然后需要一个 ResourceConfig 类型的 @Bean，在其中注册所有端点，如下面示例所示：
```
@Component
public class JerseyConfig extends ResourceConfig {

    public JerseyConfig() {
        register(Endpoint.class);
    }

}
```
警告：Jersey 对扫描可执行文件（archives）的支持相当有限。例如，当运行可执行 war 文件时，它无法扫描在完全可执行 jar 文件或 WEB-INF/classes 中找到的包中的端点。为了避免这种限制，不应该使用 packages 方法，而应该使用 register 方法单独注册端点，如前一个示例所示。

对于更高级的定制，您还可以注册实现 ResourceConfigCustomizer 的任意数量的 bean。

所有注册的端点都应该是带有 HTTP 资源注解（@GET 和其他）的 @Components，如下面示例所示：
```
@Component
@Path("/hello")
public class Endpoint {

    @GET
    public String message() {
        return "Hello";
    }

}
```
由于 Endpoint 是 Spring @Component，其生命周期由 Spring 管理，你可以使用 @Autowired 注解注入依赖项，并使用 @Value 注解注入外部配置。默认情况下，Jersey servlet 已注册并映射到 /*。你可以通过向 ResourceConfig 添加 @ApplicationPath 来更改映射。

默认情况下，Jersey 在一个名为 jerseyServletRegistration 的 ServletRegistrationBean 类型的 @Bean 中设置为一个 Servlet。默认情况下，servlet 是惰性初始化的，但是你可以通过设置  spring.jersey.servlet.load-on-startup 自定义该行为。你可以通过使用相同的名称创建自己的 bean 来禁用或重写该 bean。还可以通过设置 spring.jersey.type=filter 来使用过滤器而不是 servlet（在这种情况下，要替换或重写的 @Bean 是 jerseyFilterRegistration）。过滤器有一个 @Order，可以用 spring.jersey.filter.order 设置。通过使用 spring.jersey.init.* 指定属性映射，可以为 servlet 和过滤器注册提供 init 参数。

这里有一个 [Jersey 示例](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-samples/spring-boot-sample-jersey)，你可以看看如何设置。

## 29.4、嵌入式 Servlet 容器支持

Spring Boot 包括对嵌入式 Tomcat、Jetty 和 Undertow 服务器的支持。大多数开发人员使用适当的“Starter”来获得完全配置的实例。默认情况下，嵌入式服务器监听端口 8080 上的 HTTP 请求。

### 29.4.1、Servlet、过滤器和监听器

使用嵌入式 servlet 容器时，可以使用 Spring beans 或通过扫描 Servlet 组件从 Servlet 规范注册 servlet、过滤器和所有监听器（例如：HttpSessionListener）。

**将 Servlets、Filters 和 Listeners 注册为 Spring Beans**

任何作为 Spring bean 的 Servlet、Filter 或 servlet* 监听器实例都会注册到嵌入式容器中。如果要在配置过程中引用 application.properties 中的值，这会特别方便。

默认情况下，如果上下文只包含一个 Servlet，则将其映射到 / 。在多个 servlet bean 的情况下，bean 名称用作路径前缀。过滤器映射到 /* 。

如果基于约定的映射不够灵活，则你可以使用 ServletRegistrationBean、FilterRegistrationBean 和 ServletListenerRegistrationBean 来完全控制。

Spring Boot 附带了许多可以定义 Filter bean 的自动配置。下面是一些 Filters 的示例及其各自的顺序（低阶值意味着更高的优先级）：

|Servlet Filter				|Order|
|----|----|
|OrderedCharacterEncodingFilter		|Ordered.HIGHEST_PRECEDENCE|
|WebMvcMetricsFilter			|Ordered.HIGHEST_PRECEDENCE + 1|
|ErrorPageFilter				|Ordered.HIGHEST_PRECEDENCE + 1|
|HttpTraceFilter				|Ordered.LOWEST_PRECEDENCE - 10|

通常情况下，不为 Filter beans 排序是安全的。

如果需要特定的顺序，则应避免配置以 Ordered.HIGHEST_PRECEDENCE 读取请求正文的 Filter，因为它可能违反应用程序的字符编码配置。如果 Servlet 过滤器包装请求，则应该使用小于或等于 OrderedFilter.REQUEST_WRAPPER_FILTER_MAX_ORDER 的顺序对其进行配置。

### 29.4.2、Servlet 上下文初始化

嵌入式 servlet 容器不会直接执行 Servlet 3.0+ 的 javax.servlet.ServletContainerInitializer 接口或 Spring 的 org.springframework.web.WebApplicationInitializer 接口。这是一个有意的设计决策，旨在降低设计用于在 war 中运行的第三方库可能会破坏 Spring Boot 应用程序的风险。

如果需要在 Spring Boot 应用程序中执行 servlet 上下文初始化，则应注册实现 org.springframework.boot.web.servlet.ServletContext 接口的 bean。单个 onStartup 方法提供对 ServletContext 的访问，如果需要，可以很容易地将其用作现有 WebApplicationInitializer 的适配器。

**扫描 Servlets、Filters 和 监听器**

在使用嵌入式容器时，可以通过使用 @ServletComponentScan 启用 @WebServlet、@WebFilter 和 @WebListener 注解的类的自动注册。

提示：@ServletComponentScan 在一个独立的容器中不起作用，它使用容器的内置发现机制。

### 29.4.3、ServletWebServerApplicationContext

在底层，Spring Boot 使用不同类型的 ApplicationContext 来支持嵌入式 servlet 容器。ServletWebServerApplicationContext 是一种特殊类型的 WebApplicationContext，它通过搜索单个 ServletWebServerFactory bean 来引导自己。通常，TomcatServletWebServerFactory、JettyServletWebServerFactory 或 UndertowServletWebServerFactory 已自动配置。

注释：你通常不需要知道这些实现类。大多数应用程序是自动配置的，适当的 ApplicationContext 和 ServletWebServerFactory 是代表你创建的。

### 29.4.4、自定义嵌入式 Servlet 容器

可以使用 Spring Environment 属性配置常用的 servlet 容器设置。通常，你会在 application.properties 文件中定义属性。

**常用服务器设置包括：**

（1）网络设置：监听传入 HTTP 请求的端口（server.port），绑定到 server.address 的接口地址，等等。

（2）会话设置：会话是否持久（server.servlet.session.persistence）、会话超时（server.servlet.session.timeout）、会话数据位置（server.servlet.session.store-dir）、和 session-cookie 配置（server.servlet.session.cookie.*）。

（3）错误管理：错误页的位置（server.error.path）等。

（4）[SSL](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-embedded-web-servers.html#howto-configure-ssl)

（5）[HTTP 压缩](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-embedded-web-servers.html#how-to-enable-http-response-compression)

Spring Boot 尽可能多地尝试公开公共设置，但这并不总是可能的。对于这些情况，专用名称空间提供特定于服务器的定制（请参阅 server.tomcat 和 server.undertow）。例如，可以使用嵌入式 servlet 容器的特定功能配置[访问日志](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-embedded-web-servers.html#howto-configure-accesslogs)。

提示：有关完整列表，请参见 [ServerProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/ServerProperties.java) 类。

**程序化定制**

如果需要以编程方式配置嵌入式 servlet 容器，可以注册实现 WebServerFactoryCustomizer 接口的 Spring bean。WebServerFactoryCustomizer 提供对 ConfigurableServletWebServerFactory 的访问，其中包括许多自定义 setter 方法。以下示例显示了以编程方式设置端口：
```
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.boot.web.servlet.server.ConfigurableServletWebServerFactory;
import org.springframework.stereotype.Component;

@Component
public class CustomizationBean implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Override
    public void customize(ConfigurableServletWebServerFactory server) {
        server.setPort(9000);
    }

}
```
注释：TomcatServletWebServerFactory、JettyServletWebServerFactory 和 UndertowServletWebServerFactory 是ConfigurableServletWebServerFactory 的专用变体，它们分别为 Tomcat、Jetty 和 Undertow 提供了额外的定制 setter 方法。

**直接自定义 ConfigurableServletWebServerFactory**

如果前面的定制技术太有限，你可以自己注册 TomcatServletWebServerFactory、JettyServletWebServerFactory 或 UndertowServletWebServerFactory bean。
```
@Bean
public ConfigurableServletWebServerFactory webServerFactory() {
    TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
    factory.setPort(9000);
    factory.setSessionTimeout(10, TimeUnit.MINUTES);
    factory.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/notfound.html"));
    return factory;
}
```
为许多配置选项提供了 setter。如果你需要做一些更奇特的事情，还提供了几个受保护的方法钩子。有关详细信息，请参阅[源码文档](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/web/servlet/server/ConfigurableServletWebServerFactory.html)。

### 29.4.5、JSP 限制

当运行使用嵌入式 servlet 容器（并打包为可执行归档文件）的 Spring Boot 应用程序时，JSP 支持有一些限制。

（1）对于 Jetty 和 Tomcat，如果使用 war 包装，它应该可以工作。一个可执行的 war 在用 java-jar 启动时可以工作，并且可以部署到任何标准容器。使用可执行 jar 时不支持 JSP。

（2）Undertow 不支持 JSP。

（3）创建自定义 error.jsp 页面不会覆盖默认的错误处理视图。应该使用自定义错误页面。

这里有一个 [JSP 示例](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-samples/spring-boot-sample-web-jsp)，以便你了解如何进行设置。

## 29.5、嵌入式反应式 Server 支持

Spring Boot 包括对以下嵌入式反应式 web 服务器的支持：Reactor Netty、Tomcat、Jetty和Undertow。大多数开发人员使用适当的“Starter”来获得完全配置的实例。默认情况下，嵌入式服务器监听端口 8080 上的 HTTP 请求。

## 29.6、反应式 Server 资源配置

当自动配置 Reactor Netty 或 Jetty 服务器时，Spring Boot 将创建特定的 bean，为服务器实例：ReactorResourceFactory 或 JettyResourceFactory ，提供 HTTP 资源。

默认情况下，这些资源还将与 Reactor Netty 和 Jetty 客户端共享，以获得最佳性能，前提是：

（1）服务器和客户端使用相同的技术。

（2）客户端实例是使用由 Spring Boot 自动配置的 WebClient.Builder bean 构建的。

开发者可以通过提供自定义的 ReactorResourceFactory 或 JettyResourceFactory bean 来覆盖Jetty 和 Reactor Netty 的资源配置-这将应用于客户端和服务器。

你可以在 [WebClient Runtime 章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-webclient.html#boot-features-webclient-runtime)中了解有关客户端资源配置的更多信息。

