# 68、使用 Groovy Beans DSL 开发应用程序

Spring Framework 4.0 对 beans{} “DSL”（从 Grails 借用）具有本机支持，并且你可以使用相同的格式在 Groovy 应用程序脚本中嵌入 bean 定义。这有时是包含中间件声明等外部功能的好方法，如下面示例所示：
```
@Configuration
class Application implements CommandLineRunner {

    @Autowired
    SharedService service

    @Override
    void run(String... args) {
        println service.message
    }

}

import my.company.SharedService

beans {
    service(SharedService) {
        message = "Hello World"
    }
}
```
你可以将类声明与 beans{} 混合在同一个文件中，只要它们保持在顶层，或者，如果你愿意，可以将 beans DSL 放在一个单独的文件中。
