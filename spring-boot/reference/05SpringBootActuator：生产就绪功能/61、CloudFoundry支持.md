# 61、Cloud Foundry 支持

Spring Boot 的 actuator 模块包括在部署到兼容的 Cloud Foundry 实例时激活的其他支持。/cloudfoundryapplication 路径提供了通往所有 @Endpoint bean 的另一种安全路由。

扩展支持使 Cloud Foundry 管理 UI（例如你可以用来查看已部署的应用程序的 web 应用程序）能够使用 Spring Boot actuator 信息进行增强。例如，应用程序状态页面可能包含完整的运行状况信息，而不是典型的 “正在运行” 或 “已停止” 状态。

注释：普通用户无法直接访问 /cloudfundryapplication 路径。为了使用端点，必须与请求一起传递有效的 UAA token。

## 61.1、禁用扩展的 Cloud Foundry Actuator 支持

如果要完全禁用 /cloudfoundryapplication 端点，可以将以下设置添加到 application.properties 文件中：

    application.properties
    
    management.cloudfoundry.enabled=false

## 61.2、Cloud Foundry 自签名证书

默认情况下，/cloudfoundryapplication 端点的安全验证对各种 Cloud Foundry 服务进行 SSL 调用。如果你的 Cloud Foundry UAA 或 Cloud Controller 服务使用自签名证书，则需要设置以下属性：

    application.properties
    
    management.cloudfoundry.skip-ssl-validation=true

## 61.3、自定义上下文路径

如果服务器的上下文路径已配置为 / 以外的任何内容，则 Cloud Foundry 端点在应用程序的根目录将不可用。例如，如果 server.servlet.context-path=/app，则 Cloud Foundry 端点将在/app/cloudfoundry dryapplication/* 上可用。

如果你希望 Cloud Foundry 端点始终在/cloudfoundryapplication/* 上可用，无论服务器的上下文路径如何，则你需要在应用程序中明确配置它。配置将根据使用中的 web 服务器而有所不同。对于 Tomcat，可以添加以下配置：
```java
@Bean
public TomcatServletWebServerFactory servletWebServerFactory() {
    return new TomcatServletWebServerFactory() {

        @Override
        protected void prepareContext(Host host, ServletContextInitializer[] initializers) {
            super.prepareContext(host, initializers);
            StandardContext child = new StandardContext();
            child.addLifecycleListener(new Tomcat.FixContextListener());
            child.setPath("/cloudfoundryapplication");
            ServletContainerInitializer initializer = getServletContextInitializer(getContextPath());
            child.addServletContainerInitializer(initializer, Collections.emptySet());
            child.setCrossContext(true);
            host.addChild(child);
        }

    };
}

private ServletContainerInitializer getServletContextInitializer(String contextPath) {
    return (c, context) -> {
        Servlet servlet = new GenericServlet() {

            @Override
            public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
                ServletContext context = req.getServletContext().getContext(contextPath);
                context.getRequestDispatcher("/cloudfoundryapplication").forward(req, res);
            }

        };
        context.addServlet("cloudfoundry", servlet).addMapping("/*");
    };
}
```