# 28、JSON

Spring Boot 提供了与三个 JSON 映射库的集成：

    （1）Gson
    （2）Jackson
    （3）JSON-B

Jackson 是首选和默认库。

## 28.1、Jackson

提供了 Jackson 的自动配置，Jackson 是 spring-boot-starter-json 的一部分。当 Jackson 在类路径上时，会自动配置一个 ObjectMapper bean。为[自定义 ObjectMapper 的配置](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-spring-mvc.html#howto-customize-the-jackson-objectmapper)提供了几个配置属性。

## 28.2、Gson

提供 Gson 的自动配置。当 Gson 位于类路径上时，将自动配置 Gson bean。提供了几个 spring.gson.* 配置属性来定制配置。要获得更多控制，可以使用一个或多个 GsonBuilderCustomizer bean。

## 28.3、JSON-B

提供了 JSON-B 的自动配置。当 JSON-B API 和其一个实现在类路径上时，Jsonb bean 将自动配置。首选的 JSON-B 实现是 Apache Johnzon，它提供了依赖管理。

