# 79、Spring MVC

Spring Boot 有许多包括 Spring MVC 的 starters。请注意，一些 starters 包含了对 Spring MVC 的依赖，而不是直接包含它。本节回答有关 Spring MVC 和 Spring Boot 的常见问题。

## 79.1、编写 JSON REST 服务

默认情况下，只要 Jackson2 在类路径上，Spring Boot 应用程序中的任何 Spring @RestController 都应该呈现 JSON 响应，如下面示例所示：
```
@RestController
public class MyController {

    @RequestMapping("/thing")
    public MyThing thing() {
            return new MyThing();
    }

}
```
只要 MyThing 可以被 Jackson2 序列化（对于普通的 POJO 或 Groovy 对象为 true），那么 localhost:8080/thing 默认提供它的 JSON 表示。请注意，在浏览器中，有时可能会看到 XML 响应，因为浏览器倾向于发送更喜欢 XML 的 accept 头。

## 79.2、编写 XML REST 服务

如果类路径上有 Jackson XML 扩展（jackson-dataformat-xml），则可以使用它来呈现 XML 响应。我们用于 JSON 的前一个示例可以工作。要使用 Jackson XML 渲染器，请将以下依赖项添加到项目中：
```
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```
如果 Jackson 的 XML 扩展不可用，并且 JAXB 可用，则可以使用附加要求来呈现 XML，即 MyThing 被注解为 @XmlRootElement，如下面示例所示：
```
@XmlRootElement
public class MyThing {
    private String name;
    // .. getters and setters
}
```
JAXB 只在 Java 8 中是现成的。如果你使用的是最新的 Java 版本，请将以下依赖项添加到项目中：
```
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
</dependency>
```
注释：要让服务器呈现 XML 而不是 JSON，你可能必须发送 Accept: text/xml 头（或使用浏览器）。

## 79.3、自定义 Jackson ObjectMapper

Spring MVC（客户端和服务器端）使用 HttpMessageConverters 在 HTTP 交换中协商内容转换。如果 Jackson 在类路径上，你已经获得了 Jackson2ObjectMapperBuilder 提供的默认转换器，该转换器的实例将为你自动配置。

ObjectMapper（或 Jackson XML 转换器的 XmlMapper）实例（默认创建）具有以下自定义属性：

    （1）禁用 MapperFeature.DEFAULT_VIEW_INCLUSION
    （2）禁用 DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES
    （3）禁用 SerializationFeature.WRITE_DATES_AS_TIMESTAMPS

Spring Boot 还具有一些功能，可以更轻松地自定义此行为。

可以使用环境配置 ObjectMapper 和 XmlMapper 实例。Jackson 提供了一系列简单的开/关功能，可用于配置其处理的各个方面。在映射到环境中的属性的六个枚举 (在 Jackson 中) 中描述了这些功能：

|枚举								|属性						|值|
|---|---|---|
|com.fasterxml.jackson.databind.DeserializationFeature		|spring.jackson.deserialization.<feature_name>	|true, false|
|com.fasterxml.jackson.core.JsonGenerator.Feature		|spring.jackson.generator.<feature_name>		|true, false|
|com.fasterxml.jackson.databind.MapperFeature			|spring.jackson.mapper.<feature_name>		|true, false|
|com.fasterxml.jackson.core.JsonParser.Feature			|spring.jackson.parser.<feature_name>		|true, false|
|com.fasterxml.jackson.databind.SerializationFeature		|spring.jackson.serialization.<feature_name>	|true, false|
|com.fasterxml.jackson.annotation.JsonInclude.Include		|spring.jackson.default-property-inclusion	|always, non_null, non_absent, non_default, non_empty|	

例如，要启用漂亮的打印，请设置 spring.jackson.serialization.indent_output=true。注意，由于使用了[松散绑定](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-relaxed-binding)，indent_output 的情况不必与相应的枚举常量的情况相匹配，即 INDENT_OUTPUT。

此基于环境的配置应用于自动配置的 Jackson2ObjectMapperBuilder bean，并应用于使用构建器创建的任何映射器，包括自动配置的 ObjectMapper bean。

上下文的 Jackson2ObjectMapperBuilder 可以由一个或多个 Jackson2ObjectMapperBuilderCustomizer bean 自定义。可以排序这样的定制器 bean（Boot 自己的定制器的排序为 0），允许在 Boot 定制之前和之后应用额外的定制。

com.fasterxml.jackson.databind.Module 类型的任何 bean 都会自动注册到自动配置的 Jackson2ObjectMapperBuilder，并应用于它创建的任何 ObjectMapper 实例。这为向应用程序添加新功能时贡献自定义模块提供了全局机制。

如果要完全替换默认 ObjectMapper，请定义该类型的 @Bean 并将其标记为 @Primary，或者，如果你更喜欢基于构建器的方法，则定义 Jackson2ObjectMapperBuilder @Bean。请注意，在任何一种情况下，这样做都会禁用 ObjectMapper 的所有自动配置。

如果你提供任何 MappingJackson2HttpMessageConverter 类型的 @Beans，它们将替换 MVC 配置中的默认值。此外，还提供了一个 HttpMessageConverters 类型的便利 bean（如果使用默认的 MVC 配置，则始终可用）。它有一些有用的方法来访问默认和用户增强的消息转换器。

有关更多详细信息，请参见第 79.4 节：自定义 @ResponseBody 渲染部分和 WebMvcAutoConfiguration 源代码。

## 79.4、自定义 @ResponseBody 呈现

Spring 使用 HttpMessageConverters 呈现 @ResponseBody（或来自 @RestController 的响应）。你可以通过在 Spring Boot 上下文中添加适当类型的 bean 来贡献其他转换器。如果你添加的 bean 属于默认情况下会包含的类型（例如 JSON 转换的 MappingJackson2HttpMessageConverter），它将替换默认值。提供了一个 HttpMessageConverters 类型的便利 bean，如果你使用默认的 MVC 配置，它总是可用的。它有一些有用的方法来访问默认和用户增强的消息转换器（例如，如果你想将它们手动注入自定义 RestTemplate，它可能会很有用）。

与普通的 MVC 使用一样，你提供的任何 WebMvcConfigurer bean 也可以通过覆盖 configureMessageConverters 方法来贡献转换器。但是，与普通 MVC 不同，你只能提供所需的其他转换器（因为 Spring Boot 使用相同的机制来提供其默认值）。最后，如果你通过提供自己的 @EnableWebMvc 配置来选择退出 Spring Boot 默认 MVC 配置，则可以完全控制并通过使用 WebMvcConfigurationSupport 中的 getMessageConverters 手动执行所有操作。

有关更多详细信息，请参见 [WebMvcAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/servlet/WebMvcAutoConfiguration.java) 源代码。

## 79.5、处理 Multipart File 上传

Spring Boot 包含 Servlet 3 javax.servlet.http.Part API 来支持上传文件。默认情况下，Spring Boot 配置 Spring MVC，在单个请求中每个文件的最大大小为 1MB，文件数据的最大大小为 10MB。通过使用 MultipartProperties 类中公开的属性，你可以重写这些值、存储中间数据的位置（例如，到 /tmp 目录）和刷新到磁盘的数据的阈值。例如，如果要指定文件无限制，请将 spring.servlet.multipart.max-file-size 属性设置为 -1。

当你希望在 Spring MVC 控制器处理程序方法中将多部分编码的文件数据作为 MultipartFile 类型的 @RequestParam 注解参数接收时，多部分支持非常有用。

有关更多详细信息，请参见 [MultipartAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/servlet/MultipartAutoConfiguration.java) 源。

注释：建议使用容器内置的支持进行多部分上传，而不是引入额外的依赖项，如 Apache Commons 文件上传。

## 79.6、关闭 Spring MVC DispatcherServlet

默认情况下，所有内容都是从应用程序的根目录（/）提供的。如果希望映射到其他路径，可以按如下方式配置一个路径：

    spring.mvc.servlet.path=/acme

如果你有额外的 servlets，你可以为每一个声明一个 Servlet 或 ServletRegistrationBean 类型的 @Bean，Spring Boot 会透明地将它们注册到容器中。因为 servlets 是以这种方式注册的，所以它们可以映射到 DispatcherServlet 的子上下文，而无需调用它。

自己配置 DispatcherServlet 是不寻常的，但是如果你真的需要这样做，必须提供一个 DispatcherServletPath 类型的 @Bean 来提供自定义 DispatcherServlet 的路径。

## 79.7、关闭默认 MVC 配置

完全控制 MVC 配置的最简单方法是使用 @EnableWebMvc 注解提供你自己的 @Configuration。这样做会使所有 MVC 配置都掌握在你的手中。

## 79.8、定制 ViewResolvers

ViewResolver 是 Spring MVC 的核心组件，将 @Controller 中的视图名称转换为实际的 View 实现。请注意，ViewResolvers 主要用于 UI 应用程序，而不是 REST 风格的服务（View 不用于呈现 @ResponseBody）。ViewResolver 有许多实现可供选择，Spring 本身并不认为应该使用哪些实现。另一方面，Spring Boot 为你安装一个或两个，这取决于它在类路径和应用程序上下文中找到的内容。DispatcherServlet 使用它在应用程序上下文中找到的所有解析器，依次尝试每个解析器，直到得到结果，因此，如果你添加自己的解析器，你必须知道顺序以及你的解析器被添加到哪个位置。

WebMvcAutoConfiguration 将以下 ViewResolver 添加到上下文中：

（1）名为 defaultViewResolver 的 InternalResourceViewResolver。它定位可以使用 DefaultServlet 呈现的物理资源（包括静态资源和 JSP 页面，如果你使用它们的话）。它对视图名称应用前缀和后缀，然后在 servlet 上下文中查找具有该路径的物理资源（默认值均为空，但可通过 spring.mvc.view.prefix 和 spring.mvc.view.suffix 访问外部配置）。你可以通过提供相同类型的 bean 来重写它。

（2）名为 beanNameViewResolver 的 BeanNameViewResolver。这是视图解析器链的一个有用成员，它可以提取与正在解析的 View 同名的所有 bean。没有必要覆盖或替换它。

（3）只有当实际存在类型为 View 的 bean 时，才会添加名为 viewResolver 的 ContentNegotiatingViewResolver。这是一个主解析器，委托给所有其他解析器，并试图找到与客户端发送的 “Accept” HTTP 头的匹配。有一个关于 ContentNegotiatingViewResolver 的有用博客，你可能想学习它以了解更多信息，你也可以查看源代码以获取详细信息。你可以通过定义一个名为 viewResolver 的 bean 来关闭自动配置的 ContentNegotiatingViewResolver。

（4）如果使用 Thymeleaf，则还有一个名为 thymeleafViewResolver 的 ThymeleafViewResolver。它通过在视图名称周围加上前缀和后缀来查找资源。前缀是 spring.thymeleaf.prefix，后缀是 spring.thymeleaf.suffix。前缀和后缀的值分别默认为 classpath:/templates/ 和 .html。你可以通过提供同名的 bean 来重写 ThymeleafViewResolver。

（5）如果使用 FreeMarker，则还有一个名为 freeMarkerViewResolver 的 FreeMarkerViewResolver。它在加载器路径（外部化为spring.freemarker.templateLoaderPath，默认值为 classpath:/templates/）中查找资源，方法是用前缀和后缀包围视图名称。前缀外部化为 spring.freemarker.prefix，后缀外部化为 spring.freemarker.suffix。前缀和后缀的默认值分别为空和 .ftl。通过提供同名的bean，可以重写FreeMarkerViewResolver。

（6）如果你使用 Groovy 模板（实际上，如果 groovy-templates 在你的类路径上），那么你还有一个名为 groovyMarkupViewResolver 的 GroovyMarkupViewResolver。它通过在视图名周围加上前缀和后缀（外部化为 spring.groovy.template.prefix 和 spring.groovy.template.suffix）来查找加载器路径中的资源。前缀和后缀的默认值分别为 classpath:/templates/ 和 .tpl。你可以通过提供同名的 bean 来重写 GroovyMarkupViewResolver。

有关详细信息，请参见以下部分：

（1）[WebMvcAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/servlet/WebMvcAutoConfiguration.java)

（2）[ThymeleafAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/thymeleaf/ThymeleafAutoConfiguration.java)

（3）[FreeMarkerAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/freemarker/FreeMarkerAutoConfiguration.java)

（4）[GroovyTemplateAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/groovy/template/GroovyTemplateAutoConfiguration.java)
