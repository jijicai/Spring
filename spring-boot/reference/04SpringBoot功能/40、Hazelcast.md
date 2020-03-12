# 40、Hazelcast

如果 Hazelcast 位于类路径上，并且找到了合适的配置，则 Spring Boot 会自动配置可以在应用程序中注入的 HazelcastInstance。

如果定义 com.hazelcast.config.Config bean，Spring Boot 会使用它。如果你的配置定义了实例名称，则 Spring Boot 会尝试查找现有实例，而不是创建新实例。

你还可以通过配置指定要使用的 hazelcast.xml 配置文件，如下面示例所示：

    spring.hazelcast.config=classpath:config/my-hazelcast.xml

否则，Spring Boot 会尝试从工作目录或类路径的根目录中的默认位置: hazelcast.xml 中查找 Hazelcast 配置。我们还检查是否设置了 hazelcast.config 系统属性。有关更多详细信息，请参见 [Hazelcast 文档](https://docs.hazelcast.org/docs/latest/manual/html-single/)。

如果类路径上存在 hazelcast-client，则 Spring Boot 首先通过检查以下配置选项来尝试创建客户端：

    （1）com.hazelcast.client.config.ClientConfig bean 的存在。
    （2）由 spring.hazelcast.config 属性定义的配置文件。
    （3）hazelcast.client.config 系统属性的存在。
    （4）工作目录或类路径根目录中的 hazelcast-client.xml。

Spring Boot 还具有对 Hazelcast 的显式缓存支持。如果启用缓存，HazelcastInstance 将自动包装在 CacheManager 实现中。
