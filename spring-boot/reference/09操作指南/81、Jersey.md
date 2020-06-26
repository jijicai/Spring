# 81、Jersey

## 81.1、使用 Spring Security 保护 Jersey 端点

Spring Security 可用于保护基于 Jersey 的 web 应用程序，其方式与用于保护基于 Spring MVC 的 web 应用程序的方式大致相同。但是，如果要将 Spring Security 的方法级安全性与 Jersey 一起使用，则必须将 Jersey 配置为使用 setStatus(int) 而不是 sendError(int)。这防止了 Jersey 在 Spring Security 有机会向客户端报告身份验证或授权失败之前提交响应。

必须在应用程序的 ResourceConfig bean 上将 jersey.config.server.response.setStatusOverSendError 属性设置为 true，如下面示例所示：
```
@Component
public class JerseyConfig extends ResourceConfig {

    public JerseyConfig() {
        register(Endpoint.class);
        setProperties(Collections.singletonMap("jersey.config.server.response.setStatusOverSendError", true));
    }

}
```