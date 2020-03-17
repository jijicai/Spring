# 45、JMX 的监控和管理

Java 管理扩展 (JMX) 提供了一种标准机制来监控和管理应用程序。Spring Boot 将最合适的 MBeanServer 公开为 ID 为 mbeanServer 的 bean。你的任何使用 Spring JMX 注解 （@ManagedResource 、 @ManagedAttribute 或 @ManagedOperation）进行注解的 bean 都将暴露给它。

如果你的平台提供了一个标准的 MBeanServer，那么 Spring Boot 将使用它，并且在必要时默认为 VM MBeanServer。如果所有这些都失败了，将创建一个新的 MBeanServer。

有关更多详细信息，请参阅 [JmxAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jmx/JmxAutoConfiguration.java) 类。
