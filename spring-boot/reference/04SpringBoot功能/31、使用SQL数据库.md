# 31、使用 SQL 数据库

Spring Framework 为使用 SQL 数据库提供了广泛的支持，从使用 JdbcTemplate 的直接 JDBC 访问到 Hibernate 等完整的对象关系映射技术。Spring Data 提供了额外的功能级别：直接从接口创建 Repository 实现，并使用约定从方法名生成查询。

## 31.1、配置数据源

Java 的 javax.sql.DataSource 接口提供了处理数据库连接的标准方法。传统上，“DataSource”使用 URL 和一些凭据来建立数据库连接。

提示：有关更高级的示例，请参阅[“如何”章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-data-access.html#howto-configure-a-datasource)，通常是为了完全控制数据源的配置。

### 31.1.1、嵌入式数据库支持

使用内存中的嵌入式数据库开发应用程序通常很方便。显然，内存数据库不提供持久存储。你需要在应用程序启动时填充数据库，并准备在应用程序结束时丢弃数据。

提示：[“如何”章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-database-initialization.html)包括一个关于如何初始化数据库的部分。

Spring Boot 可以自动配置嵌入式 H2、HSQL 和 Derby 数据库。你不需要提供任何连接 URL。只需要包含要使用的嵌入式数据库的构建依赖项。

注释：如果你在测试中使用此功能，你可能会注意到整个测试套件会重用同一个数据库，而不管你使用的应用程序上下文的数量是多少。如果要确保每个上下文都有单独的嵌入数据库，则应将 spring.datasource.generate-unique-name 设置为 true。

例如，典型的 POM 依赖关系如下：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>hsqldb</artifactId>
    <scope>runtime</scope>
</dependency>
```
注释：要自动配置嵌入式数据库，需要依赖 spring-jdbc。在本例中，它是通过 spring-boot-starter-data-jpa 以传递方式引入的。

提示：如果出于任何原因，你确实为嵌入式数据库配置了连接 URL，请注意确保数据库的自动关闭被禁用。如果使用 H2，则应使用 DB_CLOSE_ON_EXIT=FALSE 来执行此操作。如果使用 HSQLDB，则应确保不使用 shutdown=true。禁用数据库的自动关闭让 Spring Boot 可以控制何时关闭数据库，从而确保在不再需要访问数据库时关闭数据库。

### 31.1.2、连接到生产数据库

还可以使用池化 DataSource 自动配置生产数据库连接。Spring Boot 使用以下算法来选择特定的实现。

（1）我们更喜欢 HikariCP 的性能和并发性。如果 HikariCP 可用，我们总是选择它。

（2）否则，如果 Tomcat 池化 DataSource 可用，我们就使用它。

（3）如果 HikariCP 和 Tomcat 池化 DataSource 都不可用，并且 Commons DBCP2 可用，则使用它。

如果使用 spring-boot-starter-jdbc 或 spring-boot-starter-data-jpa “starters”，则自动地获得 HikariCP 的依赖项。

注释：你可以完全绕过该算法，并通过设置 spring.datasource.type 属性指定要使用的连接池。如果在 Tomcat 容器中运行应用程序，这一点尤其重要，因为 tomcat-jdbc 是默认提供的。

提示：始终可以手动配置其他连接池。如果定义自己的 DataSource bean，则不会自动配置。

数据源配置由 spring.datasource.* 中的外部配置属性控制。例如，你可以在 application.properties 中声明以下部分：

    spring.datasource.url=jdbc:mysql://localhost/test
    spring.datasource.username=dbuser
    spring.datasource.password=dbpass
    spring.datasource.driver-class-name=com.mysql.jdbc.Driver

注释：至少应该通过设置 spring.datasource.url 属性来指定 URL。否则，Spring Boot 会尝试自动配置嵌入式数据库。

提示：你通常不需要指定 driver-class-name，因为 Spring Boot 可以从 url 推断大多数数据库的该值。

注释：为了创建一个池化 DataSource，我们需要能够验证一个有效的 Driver 类是否可用，所以我们在做任何事情之前都要检查它。换句话说，如果设置 spring.datasource.driver-class-name=com.mysql.jdbc.Driver，那么该类必须是可加载的。

有关支持的选项的详细信息，请参阅 DataSourceProperties。无论实际实现如何，这些都是标准选项。还可以通过使用它们各自的前缀（spring.datasource.hikari.*
、spring.datasource.tomcat.* 和 spring.datasource.dbcp2.*）来微调特定于实现的设置。有关详细信息，请参阅正在使用的连接池实现的文档。

例如，如果使用 Tomcat 连接池，可以自定义许多其他设置，如下面示例所示：

    # Number of ms to wait before throwing an exception if no connection is available.
    spring.datasource.tomcat.max-wait=10000
    
    # Maximum number of active connections that can be allocated from this pool at the same time.
    spring.datasource.tomcat.max-active=50
    
    # Validate the connection before borrowing it from the pool.
    spring.datasource.tomcat.test-on-borrow=true

### 31.1.3、连接到 JNDI 数据源

如果将 Spring Boot 应用程序部署到 Application Server，则可能需要使用 Application Server 的内置功能配置和管理 DataSource，并使用 JNDI 访问它。

spring.datasource.jndi-name 属性可以用作 spring.datasource.url、spring.datasource.username 和 spring.datasource.password 属性的代替项，以从特定 JNDI 位置访问 DataSource。例如，application.properties 中的以下部分显示了如何访问 JBoss AS 定义的 DataSource：

    spring.datasource.jndi-name=java:jboss/datasources/customers

## 31.2、使用 JdbcTemplate

Spring 的 JdbcTemplate 和 NamedParameterJdbcTemplate 类是自动配置的，可以直接将它们 @Autowire 到自己的 bean 中，如下面示例所示：
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;

@Component
public class MyBean {

    private final JdbcTemplate jdbcTemplate;

    @Autowired
    public MyBean(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    // ...

}
```
可以使用 spring.jdbc.template.* 属性自定义模板的一些属性，如下面示例所示：

    spring.jdbc.template.max-rows=500

注释：NamedParameterJdbcTemplate 在后台重用相同的 JdbcTemplate 实例。如果定义了多个 JDBC 模板，并且没有主候选存在，则 NamedParameterJdbcTemplate 不是自动配置的。

## 31.3、JPA 和 Spring Data JPA

Java Persistence API 是一种标准技术，允许你将对象“映射”到关系数据库。spring-boot-starter-data-jpa POM 提供了快速入门的方法。它提供了以下关键依赖项：
    
    （1）Hibernate：最流行的 JPA 实现之一。
    （2）Spring Data JPA：使实现基于 JPA 的存储库变得容易。
    （3）Spring ORMs：Spring Framework 的核心 ORM 支持。

提示：我们在这里不涉及太多 JPA 或 [Spring Data](https://projects.spring.io/spring-data/) 的细节。你可以遵循 spring.io 中的“[使用 JPA 访问数据](https://spring.io/guides/gs/accessing-data-jpa/)”指南，并阅读 [Spring Data JPA](https://projects.spring.io/spring-data-jpa/) 和 [Hibernate](https://hibernate.org/orm/documentation/) 参考文档。

### 31.3.1、实体类

传统上，JPA“Entity”类是在 persistence.xml 文件中指定的。对于 Spring Boot，不需要这个文件，而是使用“Entity 扫描”。默认情况下，搜索主配置类（用 EnableAutoConfiguration 或 @SpringBootApplication 注解的包）下面的所有包。

任何用 @Entity、@Embeddable 或 @MappedSuperclass 注解的类都将被考虑。典型的实体类类似于以下示例：
```
package com.example.myapp.domain;

import java.io.Serializable;
import javax.persistence.*;

@Entity
public class City implements Serializable {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String state;

    // ... additional members, often include @OneToMany mappings

    protected City() {
        // no-args constructor required by JPA spec
        // this one is protected since it shouldn't be used directly
    }

    public City(String name, String state) {
        this.name = name;
        this.state = state;
    }

    public String getName() {
        return this.name;
    }

    public String getState() {
        return this.state;
    }

    // ... etc

}
```
提示：你可以使用 @EntityScan 注解自定义实体扫描位置。参见[第 84.4 节：将 @Entity 定义与 Spring 配置分离](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-data-access.html#howto-separate-entity-definitions-from-spring-configuration)。

### 31.3.2、Spring Data JPA 存储库

Spring Data JPA 存储库是可以定义来访问数据的接口。JPA 查询是根据方法名自动创建的。例如，CityRepository 接口可以声明 findAllByState(String state) 方法来查找给定状态下的所有城市。

对于更复杂的查询，可以使用 Spring Data 的 Query 注解来注解方法。

Spring Data 存储库通常从 Repository 或 CrudRepository 接口扩展。如果使用自动配置，则会从包含主配置类（用 @EnableAutoConfiguration 或 @SpringBootApplication 注解的配置类）的包中向下搜索存储库。

以下示例显示了典型的 Spring Data 存储库接口定义：
```
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends Repository<City, Long> {

    Page<City> findAll(Pageable pageable);

    City findByNameAndStateAllIgnoringCase(String name, String state);

}
```
Spring Data JPA 存储库支持三种不同的引导模式：default、deferred 和 lazy。要启用 deferred 或 lazy，请分别将 spring.data.jpa.repositories.bootstrap-mode 设置为 deferred 或 lazy。使用 deferred 或 lazy 时，自动配置的 EntityManagerFactoryBuilder 将使用上下文的 AsynTaskExecutor（如果有）作为引导执行器。如果存在多个，将使用名为 applicationTaskExecutor 的名称。

提示：我们仅仅触及了 Spring Data JPA 的皮毛。有关完整的详细信息，请参阅 [Spring Data JPA 参考文档](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)。

### 31.3.3、创建和删除 JPA 数据库

默认情况下，只有在使用嵌入式数据库（H2、HSQL 或 Derby）时，才会自动创建 JPA 数据库。你可以使用 spring.jpa.* 属性显示地配置 JPA 设置。例如，要创建和删除表，可以将以下行添加到 application.properties：

    spring.jpa.hibernate.ddl-auto=create-drop

注释：Hibernate 自己的内部属性名（如果你记得更好的话）是 hibernate.hbm2ddl.auto。可以使用 spring.jpa.properties.*（在将它们添加到实体管理器之前去掉该前缀）将它与其他 Hibernate 自有属属性一起设置。下一行显示了为 Hibernate 设置 JPA 属性的示例。

    spring.jpa.properties.hibernate.globally_quoted_identifiers=true

上例中的行将 hibernate.globally_quoted_identifiers 属性的值 true 传递给 Hibernate 实体管理器。

默认情况下，DDL 执行（或验证）将延迟到 ApplicationContext 启动之后。还有一个 spring.jpa.generate-ddl 标志，但如果 Hibernate 自动配置处于活动状态，则不使用它，因为 ddl-auto 设置的粒度更细。

### 31.3.4、在 View 中打开 EntityManager

如果你正在运行一个 web 应用程序，则 Spring Boot 默认情况下会注册 OpenEntityManagerInViewIntercepter 来应用“在 View 中打开 EntityManager”模式，以允许在 web 视图中延迟加载。如果不希望这种行为，则应当在 application.properties 文件中将 spring.jpa.open-in-view 设置为 false。

## 31.4、Spring Data JDBC

Spring Data 包括对 JDBC 的存储库支持，并将自动为 CrudRepository 上的方法生成 SQL。对于更高级的查询，将提供 @Query 注解。

当必要的依赖项在类路径上时，Spring Boot 将自动配置 Spring Data 的 JDBC 存储库。它们可以通过对 spring-boot-starter-data-jdbc 的单个依赖关系添加到项目中。如果需要，你可以通过向应用程序添加 @EnableJdbcRepositories 注解或 JdbcConfiguration 子类来控制 Spring Data JDBC 的配置。

提示：有关 Spring Data JDBC 的完整详细信息，请参阅[参考文档](https://projects.spring.io/spring-data-jdbc/)。

## 31.5、使用 H2 的 Web 控制台

H2 数据库提供了一个基于浏览器的控制台，Spring Boot 可以为你自动配置。当满足以下条件时，控制台将自动配置：

    （1）你正在开发一个基于 servlet 的 web 应用程序。
    （2）com.h2database:h2 在类路径上。
    （3）你正在使用 Spring Boot 的开发者工具。

提示：如果你不使用 Spring Boot 的开发者工具，但仍希望使用 H2 的控制台，则可以使用 true 值配置 spring.h2.console.enabled 属性。

注释：H2 控制台仅在开发期间使用，因此应注意确保 spring.h2.console.enabled 在生产中不设置为 true。

### 31.5.1、更改 H2 控制台的路径

默认情况下，控制台在 /h2-console 上可用。可以使用spring.h2.console.path 属性自定义控制台的路径。

## 31.6、使用 jOOQ

jOOQ 面向对象查询（jOOQ）是 Data Geekery 的一个流行产品，并允许你通过其 fluent API 构建类型安全的 SQL 查询。商业版和开源版都可以与 Spring Boot 一起使用。

### 31.6.1、代码生成

为了使用 jOOQ 类型安全查询，需要从数据库模式生成 Java 类。你可以按照 jOOQ 用户手册中的说明进行操作。如果你使用 jooq-codegen-maven 插件，并且还使用 spring-boot-starter-parent“父级 POM”，则可以安全地省略该插件的 &lt;version&gt; 标记。你还可以使用 Spring Boot 定义的版本变量（例如：h2.version）来声明插件的数据库依赖项。下面的列表显示了一个示例：
```
<plugin>
    <groupId>org.jooq</groupId>
    <artifactId>jooq-codegen-maven</artifactId>
    <executions>
        ...
    </executions>
    <dependencies>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>${h2.version}</version>
        </dependency>
    </dependencies>
    <configuration>
        <jdbc>
            <driver>org.h2.Driver</driver>
            <url>jdbc:h2:~/yourdatabase</url>
        </jdbc>
        <generator>
            ...
        </generator>
    </configuration>
</plugin>
```
### 31.6.2、使用 DSL Context

jOOQ 提供的 fluent API 是通过 org.jooq.DSLContext 接口的启动的。Spring Boot 将 DSLContext 自动配置为 Spring Bean，并将其连接到应用程序 DataSource。要使用 DSLContext，可以 @Autowire 它，如下面示例所示：
```
@Component
public class JooqExample implements CommandLineRunner {

    private final DSLContext create;

    @Autowired
    public JooqExample(DSLContext dslContext) {
        this.create = dslContext;
    }

}
```
提示：jOOQ 手册倾向于使用一个名为 create 的变量来保存 DSLContext。

然后，你可以使用 DSLContext 来构造查询，如下面示例所示：
```
public List<GregorianCalendar> authorsBornAfter1980() {
    return this.create.selectFrom(AUTHOR)
        .where(AUTHOR.DATE_OF_BIRTH.greaterThan(new GregorianCalendar(1980, 0, 1)))
        .fetch(AUTHOR.DATE_OF_BIRTH);
}
```
### 31.6.3、jOOQ-SQL 方言

如果没有配置 spring.joog.sql-dialect 属性，则 Spring Boot 将确定用于数据源的 SQL 方言。如果 Spring Boot 无法检测到该方言，则使用默认值。

注释：Spring Boot 只能自动配置 jOOQ 开源版支持的方言。

### 31.6.4、自定义 jOOQ

可以通过定义自己的 @Bean 定义来实现更高级的定制，该定义在创建 jOOQ 配置时使用。可以为以下 jOOQ 类型定义 bean：

    （1）ConnectionProvider
    （2）ExecutorProvider
    （3）TransactionProvider
    （4）RecordMapperProvider
    （5）RecordUnmapperProvider
    （6）RecordListenerProvider
    （7）ExecuteListenerProvider
    （8）VisitListenerProvider
    （9）TransactionListenerProvider

如果你想完全控制 jOOQ 配置，还可以创建自己的 org.jooq.Configuration @Bean。
