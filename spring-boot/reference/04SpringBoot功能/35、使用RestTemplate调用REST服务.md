# 35、使用 RestTemplate 调用 REST 服务

如果需要从应用程序调用远程 REST 服务，可以使用 Spring 框架的 RestTemplate 类。由于 RestTemplate 实例通常需要在使用之前进行自定义，因此 Spring Boot 不提供任何单个自动配置的 RestTemplate bean。然而，它确实自动配置了一个 RestTemplateBuilder，它可以在需要时用于创建 RestTemplate 实例。自动配置的 RestTemplateBuilder 确保合理的 HttpMessageConverters 应用于 RestTemplate 实例。

以下代码展示了一个典型示例:
```
@Service
public class MyService {

    private final RestTemplate restTemplate;

    public MyService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }

    public Details someRestCall(String name) {
        return this.restTemplate.getForObject("/{name}/details", Details.class, name);
    }

}
```
提示：RestTemplateBuilder 包括许多可用于快速配置 RestTemplate 的有用方法。例如，要添加基本身份验证支持，你可以使用 builder.basicAuthentication("user", "password").build()。

## 35.1、RestTemplate 自定义

有三种主要的 RestTemplate 自定义方法，这基本上取决于你希望如何应用自定义。

要使任何自定义的范围尽可能缩小，请注入自动配置的 RestTemplateBuilder，然后根据需要调用其方法。每个方法调用都会返回一个新的 RestTemplateBuilder 实例，因此自定义项只会影响该生成器的使用。

要进行应用范围的附加定制，请使用 RestTemplateCustomizer bean。所有此类 bean 都将自动注册到自动配置的 RestTemplateBuilder 中，并应用于使用它构建的任何模板。

以下示例显示一个自定义程序，该自定义程序为除 192.168.0.5 以外的所有主机配置代理的使用：
```
static class ProxyCustomizer implements RestTemplateCustomizer {

    @Override
    public void customize(RestTemplate restTemplate) {
        HttpHost proxy = new HttpHost("proxy.example.com");
        HttpClient httpClient = HttpClientBuilder.create().setRoutePlanner(new DefaultProxyRoutePlanner(proxy) {

            @Override
            public HttpHost determineProxy(HttpHost target, HttpRequest request, HttpContext context)
                    throws HttpException {
                if (target.getHostName().equals("192.168.0.5")) {
                    return null;
                }
                return super.determineProxy(target, request, context);
            }

        }).build();
        restTemplate.setRequestFactory(new HttpComponentsClientHttpRequestFactory(httpClient));
    }

}
```
最后，最极端的 (很少使用的) 选项是创建你自己的 RestTemplateBuilder bean。这样做可以关闭 RestTemplateBuilder 的自动配置，并防止使用任何 RestTemplateCustomizer bean。