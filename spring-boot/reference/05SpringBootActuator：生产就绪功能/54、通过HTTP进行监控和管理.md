# 54、通过 HTTP 进行监控和管理

如果你正在开发 web 应用程序，Spring Boot Actuator 会自动配置所有启用的端点，以便通过 HTTP 公开。默认约定是使用前缀 /actuator 和端点 id 一起作为 URL 路径。例如，health 被公开为 /actuator/health。提示：Spring MVC、Spring WebFlux 和 Jersey 原生支持 Actuator。

## 54.1、自定义管理端点路径

有时，为管理端点自定义前缀是很有用的。例如，你的应用程序可能已经将 /actuator 用于其他目的。你可以使用 management.endpoints.web.base-path 属性更改管理端点的前缀，如下面示例所示：

    management.endpoints.web.base-path=/manage

前面的 application.properties 示例将端点从 /actuator/{id} 更改为 /manage/{id}（例如 /manage/info）。

注释：如果管理端口未配置为使用其他 HTTP 端口公开端点，则 management.endpoints.web.base-path 相对于 server.servlet.context-path。如果配置了 management.server.port，则 management.endpoints.web.base-path 相对于 management.server.servlet.context-path。

如果要将端点映射到不同的路径，可以使用 management.endpoints.web.path-mapping 属性。

以下示例将 /actuator/health 重新映射到 /healthcheck：

**application.properties**

    management.endpoints.web.base-path=/
    management.endpoints.web.path-mapping.health=healthcheck

## 54.2、自定义管理服务器端口

使用默认 HTTP 端口公开管理端点对于基于云的部署是明智的选择。但是，如果你的应用程序在你自己的数据中心内运行，你可能更喜欢使用不同的 HTTP 端口来公开端点。

你可以设置 management.server.port 属性来更改 HTTP 端口，如下面示例所示：

    management.server.port=8081

注释：在 Cloud Foundry 上，默认情况下，应用程序仅在端口 8080 上接收 HTTP 和 TCP 路由的请求。如果你想在 Cloud Foundry 上使用自定义管理端口，则需要明确设置应用程序的路由，以便将流量转发到自定义端口。

## 54.3、配置特定于管理的 SSL

当配置为使用自定义端口时，也可以使用各种 management.server.ssl.* 属性来配置带有 SSL 的管理服务器。例如，这样做可以使管理服务器通过 HTTP 可用，而主应用程序使用 HTTPS，如下面的属性设置所示：

    server.port=8443
    server.ssl.enabled=true
    server.ssl.key-store=classpath:store.jks
    server.ssl.key-password=secret
    management.server.port=8080
    management.server.ssl.enabled=false

或者，主服务器和管理服务器都可以使用 SSL，但使用不同的密钥存储，如下所示：

    server.port=8443
    server.ssl.enabled=true
    server.ssl.key-store=classpath:main.jks
    server.ssl.key-password=secret
    management.server.port=8080
    management.server.ssl.enabled=true
    management.server.ssl.key-store=classpath:management.jks
    management.server.ssl.key-password=secret

## 54.4、自定义管理服务器地址

通过设置 management.server.address 属性，可以自定义管理端点可用的地址。如果你只想在内部或面向 ops 的网络上监听，或者只想监听来自 localhost 的连接，则这样做非常有用。

注释：只有当端口与主服务器端口不同时，才能监听其他地址。

以下示例 application.properties 不允许远程管理连接：

    management.server.port=8081
    management.server.address=127.0.0.1

## 54.5、禁用 HTTP 端点

如果不想通过 HTTP 公开端点，可以将管理端口设置为 -1，如下面示例所示：

    management.server.port=-1

这也可以使用 management.endpoints.web.exposure.exclude 属性来实现，如下面示例所示：

    management.endpoints.web.exposure.exclude=*


