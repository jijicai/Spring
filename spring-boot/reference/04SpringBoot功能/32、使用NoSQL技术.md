# 32、使用 NoSQL 技术

Spring Data 提供了额外的项目，帮助你访问各种 NoSQL 技术，包括：MongoDB、Neo4j、Elasticsearch、Solr、Redis、Gemfire、Cassandra、Couchbase 和 LDAP。Spring Boot 为它们提供了自动配置：Redis、MongoDB、Neo4j、Elasticsearch、Solr、Cassandra、Couchbase 和 LDAP。你可以使用其他项目，但必须自己配置它们。请参阅 projects.spring.io/spring-data 中的相应参考文档。

## 32.1、Redis

Redis 是一个缓存、消息代理和功能丰富的键值存储。Spring Boot 为 Lettuce 和 Jedis 客户端库以及Spring Data Redis 提供的抽象库提供了基本的自动配置。

有一个 spring-boot-starter-data-redis “Starter”，可以方便地收集依赖项。默认情况下，它使用 Lettuce。这个启动器同时处理传统的和反应式应用程序。

提示：我们还提供了一个 spring-boot-starter-data-redis-reactive “Starter”，以便与具有反应式支持的其他存储保持一致。

### 32.1.1、连接到 Redis

你可以注入一个自动配置的 RedisConnectionFactory、StringRedisTemplate 或 vanilla（香草味的）RedisTemplate 实例，就像注入任何其他 Spring Bean 一样。默认情况下，该实例尝试连接到 localhost:6379 上的 Redis 服务器。下面的清单显示了这样一个 bean 的示例：
```
@Component
public class MyBean {

    private StringRedisTemplate template;

    @Autowired
    public MyBean(StringRedisTemplate template) {
        this.template = template;
    }

    // ...

}
```
提示：你还可以注册任意数量的 bean，实现 LettuceClientConfigurationBuilderCustomizer 以进行更高级的自定义。如果使用 Jedis，也可以使用 JedisClientConfigurationBuilderCustomizer。

如果你添加任何自动配置类型的自己的 @Bean，则它将取代默认的 @Bean（除了 RedisTemplate，当排除基于 bean 名称 redisTemplate 而不是其类型时）。默认情况下，如果 commons-pool2 位于类路径上，则会得到一个池化的连接工厂。

## 32.2、MongoDB

MongoDB 是一个开源的 NoSQL 文档数据库，它使用类似 JSON 的模式，而不是传统的基于表的关系数据。Spring Boot 为使用 MongoDB 提供了一些便利，包括 spring-boot-starter-data-mongodb 和 spring-boot-starter-data-mongodb-reactive “Starters”。

### 32.2.1、连接到 MongoDB 数据库

要访问 Mongo 数据库，可以插入一个自动配置的 org.springframework.data.mongodb.MongoDbFactory。默认情况下，实例尝试连接到 mongodb://localhost/test 上的 MongoDB 服务器。下面的示例展示了如何连接到 MongoDB 数据库：
```
import org.springframework.data.mongodb.MongoDbFactory;
import com.mongodb.DB;

@Component
public class MyBean {

    private final MongoDbFactory mongo;

    @Autowired
    public MyBean(MongoDbFactory mongo) {
        this.mongo = mongo;
    }

    // ...

    public void example() {
        DB db = mongo.getDb();
        // ...
    }

}
```
你可以设置 spring.data.mongodb.uri 属性来更改 URL 并配置其他设置，例如副本集，如下面示例所示：

    spring.data.mongodb.uri=mongodb://user:secret@mongo1.example.com:12345,mongo2.example.com:23456/test

或者，只要使用 Mongo 2.x，就可以指定主机/端口。例如，你可以在 application.properties 中声明以下设置：

    spring.data.mongodb.host=mongoserver
    spring.data.mongodb.port=27017

如果你已经定义了自己的 MongoClient，它将用于自动配置合适的 MongoDbFactory。同时支持com.mongodb.MongoClient 和 com.mongodb.client.MongoClient。

注释：如果使用 Mongo3.0 Java 驱动程序，则不支持 spring.data.mongodb.host 和 spring.data.mongodb.port。在这种情况下，应该使用 spring.data.mongodb.uri 来提供所有配置。

提示：如果未指定 spring.data.mongodb.port，则使用默认值 27017。你可以从前面显示的示例中删除这一行。

提示：如果不使用 Spring Data Mongo，可以注入 com.mongodb.MongoClient bean，而不是使用 MongoDbFactory。如果想完全控制建立 MongoDB 连接，还可以声明自己的 MongoDbFactory 或 MongoClientBean。

注释：如果你使用的是反应式驱动程序，那么 SSL 需要 Netty。如果 Netty 可用且要使用的工厂尚未自定义，则自动配置将自动配置此工厂。

### 32.2.2、MongoTemplate

Spring Data MongoDB 提供了一个 MongoTemplate 类，其设计与 Spring 的 JdbcTemplate 非常相似。与 JdbcTemplate 一样，Spring Boot 会自动配置一个 bean，供你注入模板，如下所示：
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.stereotype.Component;

@Component
public class MyBean {

    private final MongoTemplate mongoTemplate;

    @Autowired
    public MyBean(MongoTemplate mongoTemplate) {
        this.mongoTemplate = mongoTemplate;
    }

    // ...

}
```
有关完整的详细信息，请参阅 [MongoOperations Javadoc](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/MongoOperations.html)。

### 32.2.3、Spring Data MongDB 存储库

Spring Data 包括对 MongoDB 的存储库支持。与前面讨论的 JPA 存储库一样，基本原则是根据方法名自动构造查询。

实际上，Spring Data JPA 和 Spring Data MongoDB 共享相同的公共基础设施。你可以从前面的 JPA 示例开始，假设 City 现在是 Mongo 数据类而不是 JPA @Entity，那么它的工作方式是相同的，如下面示例所示：
```
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends Repository<City, Long> {

    Page<City> findAll(Pageable pageable);

    City findByNameAndStateAllIgnoringCase(String name, String state);

}
```
提示：你可以使用 @EntityScan 注解自定义文档扫描位置。

提示：有关 Spring Data MongoDB 的完整详细信息，包括其丰富的对象映射技术，请参阅其[参考文档](https://projects.spring.io/spring-data-mongodb/)。

### 32.2.4、嵌入式 Mongo

Spring Boot 为嵌入式 Mongo 提供了自动配置。要在 Spring  Boot 应用程序中使用它，请添加对de.flapdoodle.embed:de.flapdoodle.embed.mongo 的依赖项。

Mongo 监听的端口可以通过设置 spring.data.mongodb.port 属性进行配置。要使用随机分配的空闲端口，请使用值 0。MongoAutoConfiguration 创建的 MongoClient 会自动配置为使用随机分配的端口。

注释：如果不配置自定义端口，默认情况下，嵌入式支持使用随机端口（而不是27017）。

如果类路径上有 SLF4J，Mongo 生成的输出将自动路由到名为 org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongo 的记录器。

你可以声明自己的 IMongodConfig 和 IRuntimeConfig bean 来控制 Mongo 实例的配置和日志路由。

## 32.3、Neo4j

Neo4j 是一个开源的 NoSQL 图形数据库，它使用了一个由一级关系连接的节点的丰富数据类型，比传统的 RDBMS 方法更适合连接大数据。Spring Boot 为使用 Neo4j 提供了一些便利，包括：spring-boot-starter-data-neo4j “Starter”。

### 32.3.1、连接到 Neo4j 数据库

要访问 Neo4j 服务器，可以插入自动配置的 org.Neo4j.ogm.session.Session。默认情况下，该实例尝试使用 Bolt 协议连接到 localhost:7687 上的 Neo4j 服务器。下面的示例展示如何注入 Neo4j Session：
```
@Component
public class MyBean {

    private final Session session;

    @Autowired
    public MyBean(Session session) {
        this.session = session;
    }

    // ...

}
```
你可以通过设置 spring.data.neo4j.* 属性来配置要使用的 uri 和凭据，如下面示例所示：

    spring.data.neo4j.uri=bolt://my-server:7687
    spring.data.neo4j.username=neo4j
    spring.data.neo4j.password=secret

通过添加 org.neo4j.ogm.config.Configuration @Bean，你可以完全控制会话的创建。另外，添加 SessionFactory 类型的 @Bean 将禁用自动配置并提供完全控制。

### 32.3.2、使用嵌入式模式

如果将 org.neo4j:neo4j-ogm-embedded-driver 添加到应用程序的依赖项中，Spring Boot 会自动配置一个进程内的 Neo4j 嵌入式实例，该实例在应用程序关闭时不会保留任何数据。

注释：由于嵌入式 Neo4j OGM 驱动程序本身不提供 Neo4j 内核，因此必须声明 org.neo4j:neo4j 为依赖项。有关兼容版本的列表，请参阅 [Neo4j OGM 文档](https://neo4j.com/docs/ogm-manual/current/reference/#reference:getting-started)。

当类路径上有多个驱动程序时，嵌入式驱动程序优先于其他驱动程序。可以通过设置spring.data.neo4j.embedded.enabled=false 显式禁用嵌入模式。

如果嵌入式驱动程序和 Neo4j 内核位于上述类路径上，则 Data Neo4j Tests 自动使用嵌入式 Neo4j 实例。

注释：通过在配置中提供数据库文件的路径（例如：spring.data.neo4j.uri=file ://var/tmp/graph.db），可以为嵌入式模式启用持久性。

### 32.3.3、Neo4jSession

默认情况下，如果你运行的是 web 应用程序，则会话将绑定到整个请求处理的线程（即，它使用“在视图中打开会话”模式）。如果不需要此行为，请将以下行添加到 application.properties 文件中：

    spring.data.neo4j.open-in-view=false

### 32.3.4、Spring Data Neo4j 存储库

Spring Data 包括对 Neo4j 的存储库支持。

与许多其他 Spring Data 模块一样，Spring Data Neo4j 与 Spring Data JPA 共享公共基础设施。你可以使用前面的 JPA 示例，并将 City 定义为 Neo4j OGM @NodeEntity 而不是 JPA @Entity，并且存储库抽象也以相同的方法工作，如下面示例所示：
```
package com.example.myapp.domain;

import java.util.Optional;

import org.springframework.data.neo4j.repository.*;

public interface CityRepository extends Neo4jRepository<City, Long> {

    Optional<City> findOneByNameAndState(String name, String state);

}
```
spring-boot-starter-data-neo4j “Starter” 支持存储库和事务管理。通过在 @Configuration bean 上分别使用 @EnableNeo4jRepositories 和 @EntityScan，可以自定义查找存储库和实体的位置。

提示：有关 Spring Data Neo4j 的完整细节，包括其对象映射技术，请参阅[参考文档](https://projects.spring.io/spring-data-neo4j/)。

## 32.4、Gemfire

[Spring Data Gemfire](https://github.com/spring-projects/spring-data-gemfire) 为访问 [Pivotal Gemfire](https://pivotal.io/big-data/pivotal-gemfire#details) 数据管理平台提供了方便的 Spring 友好工具。有一个 spring-boot-starter-data-gemfire “Starter”，用于以方便的方式收集依赖项。目前还没有对 Gemfire 的自动配置支持，但是你可以使用[单个注解 @EnableGemfireRepositories](https://github.com/spring-projects/spring-data-gemfire/blob/master/src/main/java/org/springframework/data/gemfire/repository/config/EnableGemfireRepositories.java) 来启用 Spring Data Repositories。

## 32.5、Solr

Apache Solr 是一个搜索引擎。Spring Boot 为 Solr 5 客户端库提供了基本的自动配置，以及 Spring Data Solr 提供的其之上的抽象。有一个 spring-boot-starter-data-solr “Starter”，用于以方便的方式收集依赖项。

### 32.5.1、连接到 Solr

你可以像注入任何其他 Spring bean 一样注入一个自动配置的 SolrClient 实例。默认情况下，该实例尝试连接到 localhost:8983/solr 上的服务器。下面的示例展示了如何注入 Solr bean：
```
@Component
public class MyBean {

    private SolrClient solr;

    @Autowired
    public MyBean(SolrClient solr) {
        this.solr = solr;
    }

    // ...

}
```
如果你添加自己的 SolrClient 类型的 @Bean，它将替换默认值。

### 32.5.2、Spring Data Solr 存储库

Spring Data 包括对 Apache Solr 的存储库支持。与前面讨论的 JPA 存储库一样，基本原则是根据方法名为你自动构造查询。

事实上，Spring Data JPA 和 Spring Data Solr 共享相同的公共基础设施。你可以从前面的 JPA 示例开始，假设 City 现在是一个 @SolrDocument 类，而不是一个 JPA @Entity，那么它的工作方式是相同的。

提示：有关 Spring Data Solr 的完整详细信息，请参阅[参考文档](https://projects.spring.io/spring-data-solr/)。

## 32.6、Elasticsearch

Elasticsearch 是一个开源、分布式、RESTful 的搜索和分析引擎。Spring Boot 为 Elasticsearch 提供了基本的自动配置。

Spring Boot 支持几个 HTTP 客户端：

（1）官方的 Java “Low Level” 和 “High Level” REST 客户端

（2）[Jest](https://github.com/searchbox-io/Jest)

Spring Data Elasticsearch 仍在使用传输客户端，你可以开始使用 spring-boot-starter-data-elasticsearch “Starter”。

### 32.6.1、通过 REST 客户端连接到 Elasticsearch

Elasticsearch 提供了两个不同的 REST 客户端，你可以使用它们来查询集群：“Low Level” 客户端和“High Level” 客户端。

如果类路径上有 org.elasticsearch.client:elasticsearch-rest-client 依赖项，Spring Boot 将自动配置并注册一个 RestClient bean，默认目标是 localhost:9200。你可以进一步调整 RestClient 的配置方式，如下面示例所示：

    spring.elasticsearch.rest.uris=https://search.example.com:9200
    spring.elasticsearch.rest.username=user
    spring.elasticsearch.rest.password=secret

你还可以注册任意数量的 bean，以实现 RestClientBuilderCustomizer 来进行更高级的自定义。要完全控制注册，定义一个 RestClient bean。

如果在类路径上有 org.elasticsearch.client:elasticsearch-rest-high-level-client 依赖项，Spring Boot 将自动配置 RestHighLevelClient ，它封装任何现有的 RestClient bean，重用其 HTTP 配置。

### 32.6.2、使用 Jest 连接到 Elasticsearch

如果在类路径上有 Jest，则可以插入一个自动配置的 JestClient，默认情况下该 JestClient 以 localhost:9200 为目标。你可以进一步调整客户端的配置方式，如下面示例所示：

    spring.elasticsearch.jest.uris=https://search.example.com:9200
    spring.elasticsearch.jest.read-timeout=10000
    spring.elasticsearch.jest.username=user
    spring.elasticsearch.jest.password=secret

你还可以注册任意数量的 bean，这些 bean 实现 HttpClientConfigBuilderCustomizer 以进行更高级的自定义。以下示例调整其他 HTTP 设置：
```
static class HttpSettingsCustomizer implements HttpClientConfigBuilderCustomizer {

    @Override
    public void customize(HttpClientConfig.Builder builder) {
        builder.maxTotalConnection(100).defaultMaxTotalConnectionPerRoute(5);
    }

}
```
要完全控制注册，请定义一个 JestClient bean。

### 32.6.3、使用 Spring Data 连接到 Elasticsearch

要连接到 Elasticsearch，必须提供一个或多个群集节点的地址。可以通过将 spring.data.elasticsearch.cluster-nodes 属性设置为逗号分隔的 host:port 列表来指定地址。有了这个配置，ElasticsearchTemplate 或 TransportClient 可以像任何其他 Spring bean 一样被注入，如下面示例所示：

    spring.data.elasticsearch.cluster-nodes=localhost:9300
```
@Component
public class MyBean {

    private final ElasticsearchTemplate template;

    public MyBean(ElasticsearchTemplate template) {
        this.template = template;
    }

    // ...

}
```
如果添加自己的 ElasticsearchTemplate 或 TransportClient @Bean，它将替换默认值。

### 32.6.4、Spring Data Elasticsearch 存储库

Spring Data 包括对 Elasticsearch 的存储库支持。与前面讨论的 JPA 存储库一样，基本原则是根据方法名为你自动构造查询。
事实上，Spring Data JPA 和 Spring Data Elasticsearch 共享相同的公共基础设施。你可以从前面的 JPA 示例开始，假设 City 现在是一个 Elasticsearch @Document 类，而不是一个 JPA @Entity，那么它的工作方式是相同的。

提示：有关 Spring Data Elasticsearch 的完整详细信息，请参阅参考文档。

## 32.7、Cassandra

Cassandra 是一个开放源代码的分布式数据库管理系统，旨在跨许多商业服务器处理大量数据。Spring Boot 为 Cassandra 提供了自动配置，并在此基础上提供了 Spring Data Cassandra 提供的抽象。有一个 spring-boot-starter-data-cassandra “Starter”，用于以方便的方式收集依赖项。

### 32.7.1、连接到 Cassandra

你可以注入一个自动配置的 CassandraTemplate 或一个 Cassandra Session 实例，就像注入任何其他 Spring Bean一样。spring.data.cassandra.* 属性可用于自定义连接。通常，你会提供 keyspace-name 和 contact-points 属性，如下面示例所示：

    spring.data.cassandra.keyspace-name=mykeyspace
    spring.data.cassandra.contact-points=cassandrahost1,cassandrahost2

你还可以注册任意数量的 bean，以实现 ClusterBuilderCustomizer 来进行更高级的自定义。

下面的代码列表展示了如何注入 Cassandra bean：
```
@Component
public class MyBean {

    private CassandraTemplate template;

    @Autowired
    public MyBean(CassandraTemplate template) {
        this.template = template;
    }

    // ...

}
```
如果添加 CassandraTemplate 类型的 @Bean，它将替换默认值。

### 32.7.2、Spring Data Cassandra 存储库

Spring Data 包括对 Cassandra 的基本存储库支持。目前，这比前面讨论的 JPA 存储库更受限制，需要使用 @Query 注解 finder 方法。

提示：有关 Spring Data Cassandra 的完整详细信息，请参阅[参考文档](https://docs.spring.io/spring-data/cassandra/docs/)。

## 32.8、Couchbase

Couchbase 是一个开源、分布式、多模型的面向 NoSQL 文档的数据库，为交互式应用程序优化。Spring Boot 提供Couchbase 的自动配置以及 Spring Data Couchbase 在其上提供的抽象。有 spring-boot-starter-data-couchbase 和 spring-boot-starter-data-couchbase-reactive “Starters”，可以方便地收集依赖项。

### 32.8.1、连接到 Couchbase

你可以通过添加 Couchbase SDK 和一些配置来获得 Bucket 和 Cluster。spring.couchbase.* 属性可用于自定义连接。通常，你提供 bootstrap hosts、bucket name 和 password，如下面示例所示：

    spring.couchbase.bootstrap-hosts=my-host-1,192.168.1.123
    spring.couchbase.bucket.name=my-bucket
    spring.couchbase.bucket.password=secret

提示：你至少需要提供 bootstrap host(s)，在这种情况下，bucket name 是默认的，password 是空字符串。或者，你可以定义自己的 org.springframework.data.couchbase.config.CouchbaseConfigurer @Bean 来控制整个配置。

还可以自定义一些 CouchbaseEnvironment 设置。例如，以下配置更改了用于打开新 Bucket 的超时，启用了 SSL 支持：

    spring.couchbase.env.timeouts.connect=3000
    spring.couchbase.env.ssl.key-store=/location/of/keystore.jks
    spring.couchbase.env.ssl.key-store-password=secret

有关详细信息，请查看 spring.couchbase.env.* 属性。

### 32.8.2、Spring Data Couchbase 存储库

Spring Data 包括对 Couchbase 的存储库支持。有关 Spring Data Couchbase 的完整详细信息，请参阅[参考文档](https://docs.spring.io/spring-data/couchbase/docs/current/reference/html/)。

你可以像任何其他 Spring Bean 一样注入一个自动配置的 CouchbaseTemplate 实例，只要有一个可用的缺省 CouchbaseConfigurer（在启用 Couchbase 支持时发生，如前所述）。

下面的示例展示了如何注入 Couchbase bean：
```
@Component
public class MyBean {

    private final CouchbaseTemplate template;

    @Autowired
    public MyBean(CouchbaseTemplate template) {
        this.template = template;
    }

    // ...

}
```
你可以在自己的配置中定义几个 bean 来覆盖自动配置提供的 bean：

    （1）名称为 couchbaseTemplate 的 CouchbaseTemplate @Bean
    （2）名称为 couchbaseIndexManager 的 IndexManager @Bean
    （3）名称为 couchbaseCustomConversions 的 CustomConversions @Bean

为了避免在你自己的配置中硬编码这些名称，你可以重用 Spring Data Couchbase 提供的 BeanNames。例如，可以自定义要使用的转换器，如下所示：
```
@Configuration
public class SomeConfiguration {

    @Bean(BeanNames.COUCHBASE_CUSTOM_CONVERSIONS)
    public CustomConversions myCustomConversions() {
        return new CustomConversions(...);
    }

    // ...

}
```
提示：如果你想完全绕过 Spring Data Couchbase 的自动配置，请提供你自己的 org.springframework.data.couchbase.config.AbstractCouchbaseDataConfiguration 实现。

## 32.9、LDAP

LDAP（轻量级目录访问协议）是一种开放的、与供应商无关的、行业标准的应用程序协议，用于通过 IP 网络访问和维护分布式目录信息服务。Spring Boot 为任何兼容的 LDAP 服务器提供自动配置，并支持 UnboundID 中的嵌入式内存 LDAP 服务器。
LDAP 抽象由 Spring Data LDAP 提供。有一个 spring-boot-starter-data-ldap “Starter”，用于以一种方便的方式收集依赖项。

### 32.9.1、连接到 LDAP 服务器

要连接到 LDAP 服务器，请确保声明对 spring-boot-starter-data-ldap “Starter” 或 spring-ldap-core 的依赖，然后在 application.properties 中声明服务器的 URL，如下面示例所示：

    spring.ldap.urls=ldap://myserver:1235
    spring.ldap.username=admin
    spring.ldap.password=secret

如果需要自定义连接设置，可以使用 spring.ldap.base 和 spring.ldap.base-environment 属性。

LdapContextSource 是基于这些设置自动配置的。如果需要对其进行自定义，例如要使用 PooledContextSource，仍然可以注入自动配置的 LdapContextSource。确保将自定义 ContextSource 标记为 @Primary，以便自动配置的 LdapTemplate 使用它。

### 32.9.2、Spring Data LDAP 存储库

Spring Data 包括对 LDAP 的存储库支持。有关 Spring Data LDAP 的完整详细信息，请参阅参考文档。

你还可以像对任何其他 Spring Bean 一样，注入一个自动配置的 LdapTemplate 实例，如下面示例所示：
```
@Component
public class MyBean {

    private final LdapTemplate template;

    @Autowired
    public MyBean(LdapTemplate template) {
        this.template = template;
    }

    // ...

}
```
### 32.9.3、嵌入式内存 LDAP 服务器

出于测试目的，Spring Boot 支持从 UnboundID 自动配置内存中的 LDAP 服务器。要配置服务器，请向 com.unboundid:unboundid-ldapsdk 添加依赖项并声明 base-dn 属性，如下所示：

    spring.ldap.embedded.base-dn=dc=spring,dc=io

注释：

但是，可以定义多个 base-dn 值，因为可分辨名称通常包含逗号，所以必须使用正确的表示法定义它们。

在 yaml 文件中，可以使用 yaml 列表表示法：

    spring.ldap.embedded.base-dn:
      - dc=spring,dc=io
      - dc=pivotal,dc=io

在属性文件中，必须将索引包含在属性名中：

    spring.ldap.embedded.base-dn[0]=dc=spring,dc=io
    spring.ldap.embedded.base-dn[1]=dc=pivotal,dc=io

默认情况下，服务器在随机端口上启动并触发常规 LDAP 支持。不需要指定 spring.ldap.url 属性。

如果类路径上有 schema.ldif 文件，则它用于初始化服务器。如果要从其他资源加载初始化脚本，还可以使用 spring.ldap.embedded.ldif 属性。

默认情况下，标准模式用于验证 LDIF 文件。通过设置 spring.ldap.embedded.validation.enabled 属性，可以完全关闭验证。如果有自定义属性，则可以使用 spring.ldap.embedded.validation.schema 定义自定义属性类型或对象类。

## 32.10、InfluxDB

InfluxDB 是一个开源的时间序列数据库，针对操作监控、应用程序度量、物联网（Internet-of-Things）传感器数据和实时分析等领域的时间序列数据的快速、高可用性存储和检索进行了优化。

### 32.10.1、连接到 InfluxDB

Spring Boot 自动配置 InfluxDB 实例，前提是 influxdb-java 客户端位于类路径上，并且设置了数据库的 URL，如下面示例所示：

    spring.influx.url=https://172.0.0.1:8086

如果到 InfluxDB 的连接需要用户和密码，则可以相应地设置 spring.influx.user 和 spring.influx.password 属性。

InfluxDB 依赖于 OKHttp。如果需要优化 InfluxDB 在后台使用的 http 客户端，可以注册 InfluxDbOkHttpClientBuilderProvider bean。
