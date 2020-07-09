# 88、Actuator

Spring Boot 包括 Spring Boot Actuator。本节回答了在使用过程中经常出现的问题。

## 88.1、更改 Actuator 端点的 HTTP 端口或地址

在独立应用程序中，Actuator HTTP 端口默认为与主 HTTP 端口相同。要使应用程序监听其他端口，请设置外部属性：management.server.port。要监听完全不同的网络地址（例如，当你有用于管理的内部网络和用于用户应用程序的外部网络时），还可以将 management.server.address 设置为服务器可以绑定到的有效 IP 地址。

有关更多详细信息，请参见 [ManagementServerProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-actuator-autoconfigure/src/main/java/org/springframework/boot/actuate/autoconfigure/web/server/ManagementServerProperties.java) 源代码和 “生产就绪功能” 部分中的[第 54.2 节：自定义管理服务器端口](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/production-ready-monitoring.html#production-ready-customizing-management-server-port)。

## 88.2、自定义“whitelabel”错误页

Spring Boot 安装一个“whitelabel”错误页面，如果遇到服务器错误，你可以在浏览器客户端中看到该页面（使用 JSON 和其他媒体类型的机器客户端应该会看到一个带有恰当的错误代码的合理响应）。

注释：设置 server.error.whitelabel.enabled=false 以关闭默认错误页。这样做可以恢复你正在使用的 servlet 容器的默认值。请注意，Spring Boot 仍然试图解析错误视图，因此你可能应该添加自己的错误页面，而不是完全禁用它。

自行覆盖错误页面取决于你使用的模板技术。例如，如果你使用 Thymeleaf，则可以添加 error.html 模板。如果使用 FreeMarker，则可以添加 error.ftl 模板。通常，你需要一个以 error 名称解析的 View，或者一个处理 /error 路径的 @Controller。如果你没有替换了一些默认配置，则你应该在 ApplicationContext 中找到一个 BeanNameViewResolver，因此，名为 error 的 @Bean 将是一种处理那的简单方法。有关更多选项，请参见 [ErrorMvcAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/servlet/error/ErrorMvcAutoConfiguration.java)。

有关如何在 servlet 容器中注册处理器的详细信息，请参见“[错误处理](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-error-handling)”一节。

## 88.3、清除敏感值

env 和 configprops 端点返回的信息可能有些敏感，因此默认情况下匹配特定模式的键会被清理（即，它们的值被 ***** 替换）。

Spring Boot 对这些键使用合理的默认值：例如，任何以“password”、“secret”、“key”或“token”结尾的键都会被清除。也可以改用正则表达式，例如 \*credentials.\* 来清理将单词 credentials 作为键的一部分的任何键。

可以分别使用 management.endpoint.env.keys-to-sanitize 和 management.endpoint.configprops.keys-to-sanitize 自定义要使用的模式。