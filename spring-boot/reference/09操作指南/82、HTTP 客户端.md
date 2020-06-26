# 82、HTTP 客户端

Spring Boot 提供了许多与 HTTP 客户端一起使用的 starters。本节回答与使用它们相关的问题。

## 82.1、配置 RestTemplate 以使用代理

如[第 35.1 节：RestTemplate 定制](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-resttemplate.html#boot-features-resttemplate-customization)中所述，你可以使用 RestTemplateCustomizer 和 RestTemplateBuilder 来构建定制的 RestTemplate。这是创建配置为使用代理的 RestTemplate 的推荐方法。

代理配置的确切细节取决于正在使用的底层客户端请求工厂。以下示例使用 HttpClient 配置 HttpComponentsClientRequestFactory，该 HttpClient 为除 192.168.0.5 之外的所有主机使用代理：
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