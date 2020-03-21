# 48、Web 服务

Spring Boot 提供 Web 服务自动配置，因此你只需定义端点即可。

使用 spring-boot-starter-webservices 模块可以轻松访问 [Spring Web 服务功能](https://docs.spring.io/spring-ws/docs/3.0.7.RELEASE/reference/)。

可以分别为你的 WSDLs 和 XSDs 自动创建 SimpleWsdl11Definition 和 SimpleXsdSchema bean。为此，配置它们的位置，如下面示例所示：

    spring.webservices.wsdl-locations=classpath:/wsdl

## 48.1、使用 WebServiceTemplate 调用 Web 服务

如果需要从应用程序调用远程 Web 服务，可以使用 [WebServiceTemplate](https://docs.spring.io/spring-ws/docs/3.0.7.RELEASE/reference/#client-web-service-template) 类。由于 WebServiceTemplate 实例通常需要在使用之前进行自定义，因此 Spring Boot 不提供任何单个自动配置的 WebServiceTemplate bean。然而，它确实自动配置了一个 WebServiceTemplateBuilder，它可以在需要时用于创建 WebServiceTemplate 实例。

以下代码展示了一个典型示例：
```
@Service
public class MyService {

    private final WebServiceTemplate webServiceTemplate;

    public MyService(WebServiceTemplateBuilder webServiceTemplateBuilder) {
        this.webServiceTemplate = webServiceTemplateBuilder.build();
    }

    public DetailsResp someWsCall(DetailsReq detailsReq) {
         return (DetailsResp) this.webServiceTemplate.marshalSendAndReceive(detailsReq, new SoapActionCallback(ACTION));

    }

}
```
默认情况下，WebServiceTemplateBuilder 使用类路径上可用的 HTTP 客户端库来检测合适的基于 HTTP 的 WebServiceMessageSender。你还可以自定义读取和连接超时，如下所示：
```
@Bean
public WebServiceTemplate webServiceTemplate(WebServiceTemplateBuilder builder) {
    return builder.messageSenders(new HttpWebServiceMessageSenderBuilder()
            .setConnectTimeout(5000).setReadTimeout(2000).build()).build();
}
```