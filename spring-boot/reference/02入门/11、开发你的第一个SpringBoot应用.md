# 11、开发你的第一个SpringBoot应用

本节介绍如何开发一个简单的“Hello World!”应用程序，它突出了 Spring Boot 的一些关键特性。我们使用 Maven 来构建项目，因为大多数 IDE 都只支持它。

提示：spring.io 网站包含许多使用 Spring Boot 的入门指南。如果你需要解决某个特定的问题，请首先查看它们。你可以通过转到 start.spring.io 并从依赖项搜索器中选择“Web”启动器来简化以下步骤。这样做会生成一个新的项目结构，这样你就可以立即开始编码了。请参阅 Spring Initializr 文档以获得更多详细信息。

在开始之前，请打开终端并运行以下命令，以确保安装了有效的 Java 和Maven 版本：

    $ java -version
    java version "1.8.0_102"
    Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
    
    $ mvn -v
    Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T14:33:14-04:00)
    Maven home: /usr/local/Cellar/maven/3.3.9/libexec
    Java version: 1.8.0_102, vendor: Oracle Corporation

注释：这个示例需要在它自己的文件夹中创建。后续说明假设您已经创建了一个合适的文件夹，并且它是你的当前目录。

## 11.1 创建 POM 

我们需要从创建一个 Maven pom.xml 文件开始。这个 pom.xml 文件是一个用于构建项目的配方。打开你喜欢的文本编辑器，并添加以下内容：

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <! -- Additional lines to be added here... -->

    </project>

前面的清单应该为你提供一个工作构建。你可以通过运行 mvn package 包来测试它（现在，你可以忽略警告“jar 将为空-没有内容被标记为包含！”）。

注释：此时，你可以将项目导入到 IDE 中（大多数现代 Java IDEs 包含对 Maven 的内置支持）。为了简单起见，我们在本例中继续使用纯文本编辑器。

## 11.2 添加类路径依赖项

Spring Boot 提供了许多 “Starters”，可以将 jars 添加到类路径中。我们的示例应用已经在 POM 的 parent 部分中使用了 spring-boot-starter-parent。spring-boot-starter-parent 是一个特殊的启动器，它提供了有用的 Maven 默认设置。它还提供了一个 dependency-management 部分，这样你就可以省略依赖项的版本标记。

其他“Starters”提供了当开发特定类型的应用时可能需要的依赖项。由于我们正在开发一个 web 应用，所以我们添加 spring-boot-starter-web 依赖项。在此之前，我们可以通过运行以下命令查看当前有的依赖项：
    
    $ mvn dependency:tree
    
    [INFO] com.example:myproject:jar:0.0.1-SNAPSHOT

mvn dependency:tree 命令打印树表示的项目依赖项。你可以看到 spring-boot-starter-parent 本身不提供依赖项。要添加这个必要的依赖项，请编辑pom.xml，并且立即在 parent 部分的下面添加 spring-boot-starter-web 依赖项：

    <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    </dependencies>

如果你再次运行：mvn dependency:tree，你会看到现在有许多附加的依赖项，包括 Tomcat web 服务器和 Spring boot 本身。	

## 11.3 编写代码

为了完成我们的应用程序，我们需要创建一个 Java 文件。默认情况下，Maven 编译“src/main/java”路径中的源代码，因此你需要创建该文件夹结构，然后添加一个名为"src/main/java/Example.java"文件，用于包含以下代码：

    import org.springframework.boot.*;
    import org.springframework.boot.autoconfigure.*;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @EnableAutoConfiguration
    public class Example {
    
    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }
    
    public static void main(String[] args) {
        SpringApplication.run(Example.class, args);
    }
    
    }

虽然这里没有太多的代码，但是正在进行很多工作。我们将在接下来的几节中逐步介绍重要的部分。

### 11.3.1 注解：@RestController 和 @RequestMapping

我们的 Example 类的第一个注解是 @RestController。这就是所谓的原型注解。它为阅读代码的人和 Spring 提供了类扮演特定角色的提示。在本例中，我们的类是 web @Controller，因此 Spring 在处理传入的 web 请求时会考虑它。

@RequestMapping 注解提供了“路由”信息。它告诉 Spring 任何路径为 / 的 HTTP 请求都应当被映射到 home 方法。@RestController 注解告诉 Spring 将渲染的结果字符串直接返回给调用者。 

提示：@RestController 和 @RequestMapping 注解是 Spring MVC 注解（它们不是 Spring Boot 特有的）。有关更多详情，请查看 Spring 参考文档中的 MVC 部分。

### 11.3.2 @EnableAutoConfiguration 注解

第二个类级别注解是 @EnableAutoConfiguration。这个注解告诉 Spring Boot 根据你添加的 jar 依赖项“猜测”你想要如何配置 Spring。由于 spring-boot-starter-web 添加了 Tomcat 和 Spring MVC，因此自动配置假定你正在开发一个 web 应用程序并相应地设置 Spring。

**启动器和自动配置**

自动配置被设为可以很好地与启动器一起工作，但是这两个概念并没有直接捆绑在一起。你可以在启动器之外自由地选择 jar 依赖项。Spring Boot 仍然尽力自动配置应用程序。

### 11.3.3 “main” 方法

我们应用的最后一部分是 main 方法。这只是一个遵循 Java 约定的应用程序入口点的标准方法。我们的 main 方法通过调用 run 方法来委托给 Spring Boot 的 SpringApplication 类。SpringApplication 引导我们的应用程序，启动 Spring，然后启动自动配置的 Tomcat web 服务器。我们需要将 Example.class 作为一个参数传递给 run 方法，以告诉 SpringApplication 哪个是主 Spring 组件。数组 args 也被传递来公开任何命令行参数。 

## 11.4 运行示例

此时，你的应用程序应该可以工作了。由于使用了 spring-boot-starter-parent POM，因此你有了一个有用的 run 目标，可以用来启动应用程序。在项目的根目录中，敲下命令：mvn spring-boot:run，来启动应用程序。你应该可以看到与下面相似的输出： 

    $ mvn spring-boot:run
    
    .   ____          _            __ _ _
    /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
    \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
    =========|_|==============|___/=/_/_/_/
    :: Spring Boot ::  (v2.1.6.RELEASE)
    ....... . . .
    ....... . . . (log output here)
    ....... . . .
    ........ Started Example in 2.222 seconds (JVM running for 6.514)

如果你打开浏览器访问：localhost:8080，你应该会看到以下输出：

    Hello World!

要优雅地退出应用程序，请按 ctrl-c。

## 11.5 创建一个可执行的 jar 文件

我们通过创建一个完全自包含的可执行 jar 文件来完成我们的示例，该文件可以在生成环境中运行。可执行 jar（有时候称为“胖 jar”）是包含编译类以及代码需要运行的所有 jar 依赖项的归档文件。

**可执行的 jars 和 Java**

Java 不提供加载嵌套 jar 文件（jar 文件本身包含在一个 jar 中）的标准方法。如果你希望发布一个自包含的应用程序，这可能会有问题。

为了解决这个问题，许多开发者使用“uber” jars。一个 uber jar 将应用程序的所有依赖项的所有类都打包到单个归档文件中。这种方法的问题是很难看出应用程序中有哪些库。如果在多个 jar 中使用相同的文件名（但内容不同），也可能会出现问题。

Spring Boot 采用了一种不同的方法，允许你直接嵌套 jar。

要创建一个可执行 jar，我们需要将 spring-boot-maven-plugin 添加到 pom.xml 文件中。为此，请在 dependencies 部分的下面插入以下行：

    <build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
    </build>

注释：spring-boot-starter-parent POM 包含绑定 repackage 目标的 <executions> 配置。如果你不使用 parent POM，则你需要自己声明此配置。有关详情，请参阅插件文档。（https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin/usage.html）

保存 pom.xml 并从命令行运行：mvn package，如下所示：

    $ mvn package
    
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] Building myproject 0.0.1-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO] .... ..
    [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
    [INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
    [INFO]
    [INFO] --- spring-boot-maven-plugin:2.1.6.RELEASE:repackage (default) @ myproject ---
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------

如果你查看 target 目录，你应该会看到 myproject-0.0.1-SANPSHOT.jar。这个文件应该在 10 MB 左右。如果你想查看内部，可以使用：jar tvf，如下所示：

    $ jar tvf target/myproject-0.0.1-SNAPSHOT.jar

你还应该在 target 目录中看到一个更小的文件 myproject-0.0.1-SNAPSHOT.jar.original。这是 Spring Boot 重新打包之前的Maven 创建的原始 jar 文件。

要运行该应用程序，请使用 java -jar 命令，如下所示：

    $ java -jar target/myproject-0.0.1-SNAPSHOT.jar
    
    .   ____          _            __ _ _
    /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
    \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
    =========|_|==============|___/=/_/_/_/
    :: Spring Boot ::  (v2.1.6.RELEASE)
    ....... . . .
    ....... . . . (log output here)
    ....... . . .
    ........ Started Example in 2.536 seconds (JVM running for 2.864)

和之前一样，要退出应用程序，请按 ctrl-c。
