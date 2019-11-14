# 17、Spring Beans 和依赖注入
	
你可以自由地使用任何标准的 Spring Framework 技术来定义 beans 及其注入的依赖项。为简单起见，我们经常发现使用 @ComponentScan（查找 beans）和 @Autowired（执行构造函数注入）工作得很好。

如果按照上面的建议构造你的代码（将应用程序类放在根包中），则可以添加 @ComponentScan 而无需任何参数。所有应用程序组件（@Component、@Service、@Repository、@Controller 等等）都自动注册为 Spring Beans。	

下面的示例展示了 @Service bean 使用构造方法注入来获取所需的 RiskAssessor bean：

    package com.example.service;
    
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    @Service
    public class DatabaseAccountService implements AccountService {
    
        private final RiskAssessor riskAssessor;
    
        @Autowired
        public DatabaseAccountService(RiskAssessor riskAssessor) {
            this.riskAssessor = riskAssessor;
        }
    
        // ...
    
    }

如果 bean 有一个构造方法，你可以省略 @Autowired，如下面的示例所示：

    @Service
    public class DatabaseAccountService implements AccountService {
    
        private final RiskAssessor riskAssessor;
    
        public DatabaseAccountService(RiskAssessor riskAssessor) {
            this.riskAssessor = riskAssessor;
        }
    
        // ...
    
    }

提示：请注意，如何使用构造方法注入将 riskAssessor 字段标记为 final，表示它随后不能更改。
