# 47、WebSockets

Spring Boot 为嵌入式 Tomcat、Jetty 和 Undertow 提供 WebSockets 自动配置。如果你将一个 war 文件部署到一个独立的容器中，Spring Boot 假设该容器负责其 WebSocket 支持的配置。

Spring Framework 为可以通过 spring-boot-starter-websocket 模块轻松访问的 MVC web 应用程序提供了[丰富的 WebSocket 支持](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web.html#websocket)。

WebSocket 支持也可用于[反应式 web 应用程序](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web-reactive.html#webflux-websocket)，需要在 spring-boot-starter-webflux 中包含 WebSocket API：
```
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
</dependency>
```