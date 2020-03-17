# 44、Spring 会话

Spring Boot 为各种数据存储提供 Spring Session 自动配置。在构建 Servlet web 应用程序时，可以自动配置以下存储：

    （1）JDBC
    （2）Redis
    （3）Hazelcast
    （4）MongoDB

构建反应式 web 应用程序时，可以自动配置以下存储：

    （1）Redis
    （2）MongoDB

如果类路径上存在单个 Spring Session 模块，则 Spring Boot 会自动使用该存储实现。如果你有多个实现，则必须选择要用于存储会话的 StoreType。例如，要将 JDBC 用作后端存储，你可以按如下方式配置应用程序：

    spring.session.store-type=jdbc

提示：通过将 store-type 设置为 none，可以禁用 Spring Session。

每个存储的都有特定的附加设置。例如，可以为 JDBC 存储定制表的名称，如下面示例所示：

    spring.session.jdbc.table-name=SESSIONS

要设置会话的超时，可以使用 spring.session.timeout 属性。如果未设置该属性，则自动配置会返回到 server.servlet.session.timeout 的值。
