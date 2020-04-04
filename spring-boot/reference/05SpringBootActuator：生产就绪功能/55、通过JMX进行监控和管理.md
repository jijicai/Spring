# 55、通过 JMX 进行监控和管理

Java 管理扩展（JMX）提供了一种标准机制来监控和管理应用程序。默认情况下，Spring Boot 将管理端点公开为 org.springframework.boot 域下的 JMX MBeans。

## 55.1、自定义 MBean 名称

MBean 的名称通常由端点的 id 生成。例如，health 端点公开为 org.springframework.boot:type=Endpoint,name=Health。

如果应用程序包含多个 Spring ApplicationContext，则可能会发现名称冲突。要解决此问题，可以将 spring.jmx.unique-names 属性设置为 true，以便 MBean 名称始终是唯一的。

你还可以自定义端点在其下公开的 JMX 域。以下设置显示了在 application.properties 中执行此操作的示例：

    spring.jmx.unique-names=true
    management.endpoints.jmx.domain=com.example.myapp

## 55.2、禁用 JMX 端点

如果不想通过 JMX 公开端点，可以将 management.endpoints.jmx.exposure.exclude 属性设置为 *，如下面示例所示：

    management.endpoints.jmx.exposure.exclude=*

## 55.3、通过 HTTP 将 Jolokia 用于 JMX

Jolokia 是一个 JMX-HTTP 桥，它提供了一种访问 JMX bean 的替代方法。要使用 Jolokia，请包含对 org.jolokia:jolokia-core 的依赖。例如，使用 Maven，你可以添加以下依赖项：
```xml
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
```
然后，可以通过将 Jolokia 或 * 添加到 management.endpoints.web.exposure.include 属性中来公开 Jolokia 端点。然后可以在管理 HTTP 服务器上使用 /actuator/jolokia 访问它。

### 55.3.1、自定义 Jolokia

Jolokia 有许多设置，传统上你可以通过设置 servlet 参数来配置这些设置。使用 Spring Boot，你可以使用 application.properties 文件。为此，请使用 management.endpoint.jolokia.config. 作为参数的前缀，如下面示例所示：

    management.endpoint.jolokia.config.debug=true

### 55.3.2、禁用 Jolokia

如果使用 Jolokia 但不希望 Spring Boot 对其进行配置，请将 management.endpoint.Jolokia.enabled 属性设置为 false，如下所示：

    management.endpoint.jolokia.enabled=false


