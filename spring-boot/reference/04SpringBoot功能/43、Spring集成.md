# 43、Spring 集成

Spring Boot 为使用 Spring Integration 提供了多种便利，包括 spring-boot-starter-ntegration “Starter”。Spring Integration 提供了消息传递和其他传输 (如 HTTP 、 TCP 等) 的抽象。如果你的类路径上有 Spring Integration，则它将通过 @EnableIntegration 注解初始化。

Spring Boot 还配置了一些由其他 Spring Integration 模块触发的功能。如果 spring-integration-jmx 也在类路径上，则通过 JMX 发布消息处理统计信息。如果 spring-integration-jdbc 可用，则可以在启动时创建默认数据库模式（schema），如下面的行所示：

    spring.integration.jdbc.initialize-schema=always

有关更多详细信息，请参见 [IntegrationAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/integration/IntegrationAutoConfiguration.java) 和 [IntegrationProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/integration/IntegrationProperties.java) 类。

默认情况下，如果存在 Micrometer meterRegistry bean，则 Spring Integration 度量将由 Micrometer 管理。如果你希望使用旧的 Spring Integration 度量，请将 DefaultMetricsFactory bean 添加到应用程序上下文。

