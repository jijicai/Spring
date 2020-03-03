# 39、使用 JTA 的分布式事务

Spring Boot 通过使用 Atomikos 或 Bitronix 嵌入式事务管理器，支持跨多个 XA 资源的分布式 JTA 事务。部署到合适的 Java EE 应用服务器时，也支持 JTA 事务。

检测到 JTA 环境时，Spring 的 JtaTransactionManager 用于管理事务。已升级自动配置的 JMS 、数据源和 JPA bean 以支持 XA 事务。你可以使用标准的 Spring 习惯用法，如 @Transaction，来参与分布式事务。如果你在 JTA 环境中并且仍然希望使用本地事务，则可以将 spring.jta.enabled 属性设置为 false 以禁用 JTA 自动配置。

## 39.1、使用 Atomikos 事务管理器

Atomikos 是一个流行的开源事务管理器，可以嵌入到你的 Spring Boot 应用程序中。你可以使用 spring-boot-starter-jta-atomikos Starter 拉入适当的 Atomikos 库。Spring Boot 自动配置 Atomikos，并确保将适当的依赖设置应用于你的 Spring bean，以获得正确的启动和关闭顺序。

默认情况下，Atomikos 事务日志会写入应用程序主目录（应用程序 jar 文件所在的目录）中的 transaction-logs 目录。通过在 application.properties 文件中设置 spring.jta.log-dir 属性，可以自定义此目录的位置。以 spring.jta.atomikos.properties 为开始的属性也可以用于自定义 Atomikos UserTransactionServiceImp。有关完整的详细信息，请参阅 AtomikosProperties Javadoc。

注释：为了确保多个事务管理器可以安全地协调相同的资源管理器，必须使用唯一的 ID 配置每个 Atomikos 实例。默认情况下，此 ID 是运行 Atomikos 的计算机的 IP 地址。为了确保生产中的唯一性，你应该为应用程序的每个实例，将 spring.jta.transaction-manager-id 属性配置为不同的值。

## 39.2、使用 Bitronix 事务管理器

Bitronix 是一个流行的开源 JTA 事务管理器实现。你可以使用 spring-boot-starter-jta-bitronix starter 将适当的 Bitronix 依赖项添加到项目中。与 Atomikos 一样，Spring Boot 会自动配置 Bitronix 并对你的 bean 进行后处理，以确保启动和关闭顺序是正确的。

默认情况下，Bitronix 事务日志文件（part1.btm 和 part2.btm）将写入应用程序主目录中的 transaction-logs 目录。可以通过设置 spring.jta.log-dir 属性来自定义此目录的位置。以 spring.jta.bitronix.properties 为开始的属性也绑定到 bitronix.tm.Configuration bean，允许完全自定义。有关详细信息，请参阅 [Bitronix 文档](https://github.com/bitronix/btm/wiki/Transaction-manager-configuration)。

注释：为了确保多个事务管理器可以安全地协调相同的资源管理器，必须使用唯一的 ID 配置每个 Bitronix 实例。默认情况下，此 ID 是运行 Bitronix 的计算机的 IP 地址。为了确保生产中的唯一性，你应该为应用程序的每个实例，将 spring.jta.transaction-manager-id 属性配置为不同的值。

## 39.3、使用 Java EE 托管事务管理器

如果将 Spring Boot 应用程序打包为 war 或 ear 文件并将其部署到 Java EE 应用程序服务器，则可以使用应用程序服务器的内置事务管理器。Spring Boot 尝试通过查看常见的 JNDI 位置（java: comp/UserTransaction，java: comp/TransactionManager，等等）来自动配置事务管理器。如果使用应用程序服务器提供的事务服务，通常还需要确保所有资源都由服务器管理并通过 JNDI 公开。Spring Boot 尝试通过在 JNDI 路径（java:/JmsXA 或 java:/XAConnectionFactory）查找 ConnectionFactory 来自动配置 JMS，你可以使用 [spring.datasource.jndi-name 属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-sql.html#boot-features-connecting-to-a-jndi-datasource)来配置数据源。

## 39.4、混合 XA 和 Non-XA JMS 连接

使用 JTA 时，主 JMS ConnectionFactory bean 是 XA 感知的，并参与分布式事务。在某些情况下，你可能希望使用非 XA ConnectionFactory 来处理某些 JMS 消息。例如，你的 JMS 处理逻辑可能需要比 XA 超时更长的时间。

如果要使用非 XA ConnectionFactory，可以注入 nonXaJmsConnectionFactory bean 而不是 @Primary jmsConnectionFactory bean。为了保持一致性，jmsConnectionFactory bean 也通过使用 bean 别名 xaJmsConnectionFactory 来提供。

以下示例说明如何注入 ConnectionFactory 实例：
```
// Inject the primary (XA aware) ConnectionFactory
@Autowired
private ConnectionFactory defaultConnectionFactory;

// Inject the XA aware ConnectionFactory (uses the alias and injects the same as above)
@Autowired
@Qualifier("xaJmsConnectionFactory")
private ConnectionFactory xaConnectionFactory;

// Inject the non-XA aware ConnectionFactory
@Autowired
@Qualifier("nonXaJmsConnectionFactory")
private ConnectionFactory nonXaConnectionFactory;
```
## 39.5、支持其他的嵌入式事务管理器

XAConnectionFactoryWrapper 和 XADataSourceWrapper 接口可用于支持其他的嵌入式事务管理器。这些接口负责包装 XAConnectionFactory 和 XADataSource bean，并将它们作为常规 ConnectionFactory 和 DataSource bean 公开，这些 bean 透明地注册到分布式事务中。DataSource 和 JMS 自动配置使用 JTA 变体，前提是你在 ApplicationContext 中注册了 JtaTransactionManager bean 和适当的 XA 包装 bean。

BitronixXAConnectionFactoryWrapper 和 BitronixXADataSourceWrapper 提供了如何编写 XA 包装器的好例子。