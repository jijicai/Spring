# 50、Kotlin 支持

Kotlin 是一种针对 JVM（和其他平台）的静态类型语言，它允许编写简洁而优雅的代码，同时提供与用 Java 编写的现有库的互操作性。

Spring Boot 通过利用其他 Spring 项目（如 Spring Framework、 Spring Data 和 Reactor） 中的支持来提供 Kotlin 支持。有关更多信息，请参见 [Spring Framework Kotlin 支持文档](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/languages.html#kotlin)。

从 Spring Boot 和 Kotlin 开始的最简单的方法是遵循这个全面的教程。你可以通过 start.spring.io 创建新的 Kotlin 项目。如果你需要支持，请随时加入 Kotlin Slack 的 #spring 频道，或者在 Stack Overflow 上询问 spring 和 kotlin 标签的问题。

## 50.1、要求

Spring Boot 支持 Kotlin 1.2.x。要使用 Kotlin，org.jetbrains.kotlin:kotlin-stdlib 和 org.jetbrains.kotlin:kotlin-reflect 必须出现在类路径上。也可以使用 kotlin-stdlib 变体 kotlin-stdlib-jdk7 和 kotlin-stdlib-jdk8。

由于 Kotlin 类是默认的 final 类，你可能需要配置 kotlin-spring 插件，以便自动打开 Spring  注解类，以便它们可以被代理。

在 Kotlin 中序列化/反序列化 JSON 数据需要 Jackson 的 Kotlin 模块。在类路径上找到时，它会自动注册。如果 Jackson 和 Kotlin 存在，但 Jackson Kotlin 模块不存在，则会记录一条警告消息。

提示：如果在 start.spring.io 上引导 Kotlin 项目，则默认提供这些依赖项和插件。

## 50.2、空安全

Kotlin 的一个关键特性是空安全性。它在编译时处理空值，而不是将问题推迟到运行时并遇到 NullPointerException。这有助于消除常见的错误源，而不必像 Optional 那样支付包装器的成本。Kotlin 还允许使用函数式构造，其值可以为空，如 Kotlin 中的空安全性综合指南所述。

尽管 Java 不允许在其类型系统中表达空安全性，但 Spring Framework、Spring Data 和 Reactor 现在通过工具友好的注解为其 API 提供空安全性。默认情况下，Kotlin 中使用的 Java APIs 中的类型被识别为可以放宽空检查的平台类型。Kotlin 对 JSR 305 注解的支持结合可空性注解为 Kotlin 中的相关 Spring API 提供了空安全性。

JSR 305 检查可以通过添加带有以下选项的 -Xjsr305 编译器标志来配置：-Xjsr305={strict | warn | ignore}。默认行为与 -Xjsr305=warn 相同。strict 值要求在从 Spring API 推断的 Kotlin 类型中考虑空安全性，但应在使用时知道 Spring API 可空性声明可能会在较小的版本之间演变，并且将来可能会添加更多的检查。

警告：还不支持泛型类型参数、可变参数 和数组元素的可空性。有关最新信息，请参阅 SPR-15942。还要注意，Spring Boot 自己的 API [还没有注解](https://github.com/spring-projects/spring-boot/issues/10712)。

## 50.3、Kotlin API

## 50.3.1、runApplication

Spring Boot 提供了一种惯用的方法来运行带有 runApplication<MyApplication>(*args) 的应用程序，如下面示例所示：
```
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class MyApplication

fun main(args: Array<String>) {
    runApplication<MyApplication>(*args)
}
```
这是 SpringApplication.run(MyApplication::class.java, *args) 的一个嵌入式替代品。它还允许自定义应用程序，如下面示例所示：
```
runApplication<MyApplication>(*args) {
    setBannerMode(OFF)
}
```
## 50.3.2、扩展

Kotlin 扩展提供了用附加功能扩展现有类的能力。Spring Boot Kotlin API 利用这些扩展为现有 API 添加新的 Kotlin 特有的便利性。

提供了 TestRestTemplate 扩展，类似于 Spring Framework 中用于 RestOperations 的 Spring Framework 提供的扩展。除此之外，这些扩展使得利用 Kotlin 具体化类型参数成为可能。

## 50.4、依赖管理

为了避免在类路径上混合不同版本的 Kotlin 依赖项，提供了以下 Kotlin 依赖项的依赖项管理：

    kotlin-reflect
    kotlin-runtime
    kotlin-stdlib
    kotlin-stdlib-jdk7
    kotlin-stdlib-jdk8
    kotlin-stdlib-jre7
    kotlin-stdlib-jre8

使用 Maven，可以通过 kotlin.version 属性自定义 Kotlin 版本，并为 kotlin-maven-plugin 提供插件管理。使用 Gradle，Spring Boot 插件会自动将 kotlin.version 与 Kotlin 插件的版本对齐。

## 50.5、@ConfigurationProperties

@ConfigurationProperties 当前仅适用于 lateinit 或可为空的 var 属性（建议使用前者），因为[尚不支持](https://github.com/spring-projects/spring-boot/issues/8762)由构造函数初始化的不可变类。
```
@ConfigurationProperties("example.kotlin")
class KotlinExampleProperties {

    lateinit var name: String

    lateinit var description: String

    val myService = MyService()

    class MyService {

        lateinit var apiToken: String

        lateinit var uri: URI

    }

}
```
提示：要使用注解处理器生成你自己的元数据，应使用 spring-boot-configuration-processor 依赖项配置 kapt。

## 50.6、测试

虽然可以使用 JUnit 4（spring-boot-starter-test 提供的默认值）来测试 Kotlin 代码，但建议使用 JUnit 5。JUnit 5 使测试类可以被实例化一次，并可重复用于该类的所有测试。这使得在非静态方法上使用 @BeforeAll 和 @AfterAll 注解成为可能，这对于 Kotlin 来说是一个很好的选择。

要使用 JUnit 5，请从 spring-boot-starter-test 中排除 junit:junit 依赖项，添加 JUnit 5 依赖项，并相应地配置 Maven 或 Gradle 插件。有关更多详细信息，请参见 JUnit 5 文档。你还需要将测试实例生命周期切换为 “每类”。

为了模拟 Kotlin 类，建议使用 MockK。如果你需要与 Mockito 特定的 @MockBean 和 @SpyBean 注解等效的 Mockk，则可以使用提供类似 @MockBean 和 @SpykBean 注解的 SpringMockK。

## 50.7、资源

## 50.7.1、延伸阅读

（1）[Kotlin 语言参考](https://kotlinlang.org/docs/reference/)

（2）[Kotlin Slack（配有专用 #spring 频道）](https://slack.kotlinlang.org/)

（3）[带 spring 和 kotlin 标签的 Stackoverflow](https://stackoverflow.com/questions/tagged/spring+kotlin)

（4）[在浏览器中尝试 Kotlin](https://try.kotlinlang.org/)

（5）[Kotlin 博客](https://blog.jetbrains.com/kotlin/)

（6）[棒极了的 Kotlin](https://kotlin.link/)

（7）[教程：使用 Spring Boot 和 Kotlin 构建 web 应用程序](https://spring.io/guides/tutorials/spring-boot-kotlin/)

（8）[使用 Kotlin 开发 Spring Boot 应用程序](https://spring.io/blog/2016/02/15/developing-spring-boot-applications-with-kotlin)

（9）[带有 Kotlin 、 Spring Boot 和 PostgreSQL 的地理空间信使](https://spring.io/blog/2016/03/20/a-geospatial-messenger-with-kotlin-spring-boot-and-postgresql)

（10）[在 Spring Framework 5.0 中引入 Kotlin 支持](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0)

（11）[Spring Framework 5 Kotlin APIs，函数式方式](https://spring.io/blog/2017/08/01/spring-framework-5-kotlin-apis-the-functional-way)

## 50.7.2、示例

（1）[spring-boot-kotlin-demo](https://github.com/sdeleuze/spring-boot-kotlin-demo)：常规 Spring Boot + Spring Data JPA 项目

（2）[mixit](https://github.com/mixitconf/mixit)：Spring Boot 2 + WebFlux + Reactive Spring Data MongoDB

（3）[spring-kotlin-fullstack](https://github.com/sdeleuze/spring-kotlin-fullstack)：WebFlux Kotlin 全栈示例，其中 Kotlin2js 用于前端，而不是 JavaScript 或 TypeScript

（4）[spring-petclinic-kotlin](https://github.com/spring-petclinic/spring-petclinic-kotlin)：Kotlin 版本的 Spring PetClinic 示例应用程序

（5）[spring-kotlin-deepdive](https://github.com/sdeleuze/spring-kotlin-deepdive)：Boot 1.0 + Java 到 Boot 2.0 + Kotlin 的逐步迁移