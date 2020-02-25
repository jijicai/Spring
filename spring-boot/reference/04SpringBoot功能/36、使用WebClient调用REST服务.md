# 36、使用 WebClient 调用 REST 服务

如果你的类路径上有 Spring WebFlux，你也可以选择使用 WebClient 调用远程 REST 服务。与 RestTemplate 相比，该客户端具有更多函数式感觉，并且完全是反应式的。你可以在 [Spring Framework 文档的专用部分](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web-reactive.html#webflux-client)了解有关 WebClient 的更多信息。

Spring Boot 为你创建并预配置 WebClient.Builder; 强烈建议将其注入组件并使用它创建 WebClient 实例。Spring Boot 正在配置该构建器以共享 HTTP 资源，以与服务器相同的方式表示编解码器设置 (请参见 [WebFlux HTTP 编解码器自动配置](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-webflux-httpcodecs))，等等。

以下代码展示了一个典型示例:
```
@Service
    public class MyService {

    private final WebClient webClient;

    public MyService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder.baseUrl("https://example.org").build();
    }

    public Mono<Details> someRestCall(String name) {
        return this.webClient.get().uri("/{name}/details", name)
                        .retrieve().bodyToMono(Details.class);
    }

}
```
## 36.1、WebClient 运行时

根据应用程序类路径上可用的库，Spring Boot 将自动检测用于驱动 WebClient 的 ClientHttpConnector。目前，支持 Reactor Netty 和 Jetty RS 客户端。

spring-boot-starter-webflux 默认依赖于 io.projectreactor.netty:reactor-netty，它同时提供服务器和客户端实现。如果你选择使用 Jetty 作为一个反应式服务器，那么你应该添加对 Jetty Reactive HTTP 客户端库 org.eclipse.jetty:jetty-reactive-httpclient 的依赖。对服务器和客户端使用相同的技术有其优点，因为它将在客户端和服务器之间自动共享 HTTP 资源。

开发者可以通过提供自定义 JettyResourceFactory 或 ReactorResourceFactory bean 来覆盖 Jetty 或 Reactor Netty 的资源配置-这将应用于客户端和服务器。

如果你希望为客户端覆盖该选择，则可以定义自己的 ClientHttpConnector bean 并完全控制客户端配置。
你可以在 [Spring Framework 参考文档中了解有关 WebClient 配置选项](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web-reactive.html#webflux-client-builder)的更多信息。

## 36.2、WebClient 定制

WebClient 定制有三种主要方法，具体情况基本上取决于你希望如何应用定制。

要尽可能缩小任何自定义的范围，请注入自动配置的 WebClient.Builder，然后按需调用其方法。WebClient.Builder 实例是有状态的: 构建器上的任何更改都会反映在随后使用它创建的所有客户端中。如果要使用同一构建器创建多个客户端，还可以考虑使用 WebClient.builder other=builder.clone(); 克隆该构建器。

要对所有 WebClient.Builder 实例进行应用程序范围的附加自定义，可以声明 WebClientCustomizer bean 并在注入点本地更改 WebClient.Builder。

最后，你可以返回到原始 API 并使用 WebClient.create()。在这种情况下，不会应用自动配置或 WebClientCustomizer。
