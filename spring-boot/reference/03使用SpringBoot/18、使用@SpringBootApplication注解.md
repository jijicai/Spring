# 18、使用 @SpringBootApplication 注解

许多 Spring Boot 开发者希望他们的应用使用自动配置、组件扫描，并且能够在“应用程序类”中定义额外的配置。可以使用单个 @SpringBootApplication 注解来启用这三个特性，它们是：

    （1）@EnableAutoConfiguration：启动 Spring Boot 自动配置机制。
    （2）@ComponentScan：在应用程序所在的包上启用 @Component 扫描。
    （3）@Configuration：允许在上下文中注册额外的 beans 或者导入 其他配置类。

@SpringBootApplication 注解等价于使用具有默认属性的 @Configuration、@EnableAutoConfiguration 和 @ComponentScan，如下面的示例所示：

    package com.example.myapplication;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
    public class Application {
    
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    
    } 

注释：@SpringBootApplication 还提供了别名来定制 @EnableAutoConfiguration 和 @ComponentScan 的属性。

注释：这些功能都不是强制的，你可以选择用它启用的任何功能来替换单个注解。例如，你也许不想在应用中使用组件扫描：

    package com.example.myapplication;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.context.annotation.ComponentScan
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.Import;
    
    @Configuration
    @EnableAutoConfiguration
    @Import({ MyConfig.class, MyAnotherConfig.class })
    public class Application {
    
        public static void main(String[] args) {
                SpringApplication.run(Application.class, args);
        }
    
    }

注解：在本例子中，Application 与其他任何 Spring Boot 应用一样，只是不会自动检测 @Component 的类，并且显示地导入了用户定义的 bean（参见 @Import）。










