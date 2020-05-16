# 67、使用 CLI

安装 CLI 后，你可以通过键入 spring 并在命令行按 Enter 键来运行它。如果运行 spring 时没有任何参数，将显示一个简单的帮助屏幕，如下所示：

    $ spring
    usage: spring [--help] [--version]
           <command> [<args>]
    
    Available commands are:
    
      run [options] <files> [--] [args]
        Run a spring groovy script
    
      ... more command help is shown here

你可以键入 spring help 以获取有关任何受支持命令的更多详细信息，如下面示例所示：

    $ spring help run
    spring run - Run a spring groovy script
    
    usage: spring run [options] <files> [--] [args]
    
    Option                     Description
    ------                     -----------
    --autoconfigure [Boolean]  Add autoconfigure compiler
                                 transformations (default: true)
    --classpath, -cp           Additional classpath entries
    -e, --edit                 Open the file with the default system
                                 editor
    --no-guess-dependencies    Do not attempt to guess dependencies
    --no-guess-imports         Do not attempt to guess imports
    -q, --quiet                Quiet logging
    -v, --verbose              Verbose logging of dependency
                                 resolution
    --watch                    Watch the specified file for changes

version 命令提供了一种快速方法来检查你使用的是哪个版本的 Spring Boot，如下所示：

    $ spring version
    Spring CLI v2.1.6.RELEASE

## 67.1、使用 CLI 运行应用程序

你可以使用 run 命令编译和运行 Groovy 源代码。Spring Boot CLI 是完全独立的，因此你不需要任何外部 Groovy 安装。

以下示例显示了一个用 Groovy 编写的“hello world” web 应用程序：

**hello.groovy**
```
@RestController
class WebApplication {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```
要编译并运行应用程序，请键入以下命令：

    $ spring run hello.groovy

要将命令行参数传递给应用程序，请使用 -- 将命令与“spring”命令参数分开，如下面示例所示：

    $ spring run hello.groovy -- --server.port=9000

要设置 JVM 命令行参数，可以使用 JAVA_OPTS 环境变量，如下面示例所示：

    $ JAVA_OPTS=-Xmx1024m spring run hello.groovy

注释：在 Microsoft Windows 上设置 JAVA_OPTS 时，请确保引用整个指令，例如设置 “JAVA_OPTS =-Xms256m-Xmx2048m”。这样做可以确保值被正确地传递给进程。

### 67.1.1、推断的“抓取”依赖关系

标准 Groovy 包括一个 @Grab 注解，它允许你声明对第三方库的依赖。这种有用的技术允许 Groovy 以与 Maven 或 Gradle 相同的方式下载 jar，但不需要你使用构建工具。

Spring Boot 进一步扩展了这种技术，并试图根据你的代码推断出要 “抓取” 哪些库。例如，由于前面显示的 WebApplication 代码使用 @RestController 注解，Spring Boot 抓取 “Tomcat” 和 “Spring MVC”。

以下项用作“抓取提示”：

|项目							|抓取|
|---|---|
|JdbcTemplate, NamedParameterJdbcTemplate, DataSource	|JDBC 应用程序。|
|@EnableJms						|JMS 应用程序。|
|@EnableCaching						|缓存抽象。|
|@Test							|JUnit|
|@EnableRabbit						|RabbitMQ|
|extends Specification					|Spock 测试。|
|@EnableBatchProcessing					|Spring Batch|
|@MessageEndpoint @EnableIntegration			|Spring Integration|
|@Controller @RestController @EnableWebMvc		|Spring MVC + Embedded Tomcat|
|@EnableWebSecurity					|Spring Security|
|@EnableTransactionManagement				|Spring Transaction Management|

提示：请参阅 Spring Boot CLI 源代码中 [CompilerAutoConfiguration](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-cli/src/main/java/org/springframework/boot/cli/compiler/CompilerAutoConfiguration.java) 的子类，以确切了解如何应用自定义。

### 67.1.2、推导的“抓取”坐标

Spring Boot 扩展了 Groovy 的标准 @Grab 支持，允许你指定一个不带组或版本的依赖项（例如，@Grab('freemarker')）。这样做会参考 Spring Boot 的默认依赖元数据来推断工件的组和版本。

注释：默认元数据与你使用的 CLI 版本相关联。只有当你移动到新版本的 CLI 时，它才会更改，这使你可以控制依赖项的版本何时可能更改。在[附录](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/appendix-dependency-versions.html)中可以找到显示默认元数据中包含的依赖项及其版本的表。

### 67.1.3、默认导入语句

为了帮助减少 Groovy 代码的大小，会自动包含几个 import 语句。请注意上面的示例如何引用 @Component、 @RestController 和 @RequestMapping，而不需要使用完全限定的名称或 import 语句。

提示：许多 Spring 注解无需使用 import 语句即可工作。尝试运行你的应用程序，以查看在添加导入之前失败的内容。

### 67.1.4、自动主方法

与等效的 Java 应用程序不同，Groovy 脚本不需要包含 public static void main(String[] args) 方法。系统会自动创建一个 SpringApplication，并将编译后的代码作为源代码。

### 67.1.5、自定义依赖项管理

默认情况下，CLI 在解析 @Grab 依赖项时使用 spring-boot-dependencies 中声明的依赖项管理。其他依赖项管理（覆盖默认依赖项管理）可以通过使用 @DependencyManagementBom 注解进行配置。注解的值应指定一个或多个 Maven BOMs 的坐标（groupId:artifactId:version）。

例如，考虑以下声明：

    @DependencyManagementBom("com.example.custom-bom:1.0.0")

前面的声明在 com/example/custom-versions/1.0.0/ 下的 Maven 存储库中获取 custom-bom-1.0.0.pom。

当你指定多个 BOMs 时，它们将按照你声明它们的顺序应用，如下面示例所示：

    @DependencyManagementBom(["com.example.custom-bom:1.0.0",
            "com.example.another-bom:1.0.0"])

上述示例表明，another-bom 中的依赖项管理覆盖了 custom-bom 中的依赖项管理。

你可以在任何可以使用 @Grab 的地方使用 @DependencyManagementBom。但是，为了确保依赖项管理的顺序一致，你最多可以在应用程序中使用 @DependencyManagementBom 一次。依赖项管理的一个有用的来源（它是 Spring Boot 依赖项管理的超集）是 Spring IO 平台，你可以将其包含在以下行中：

    @DependencyManagementBom('io.spring.platform:platform-bom:1.1.2.RELEASE')

## 67.2、具有多个源文件的应用程序

你可以将 “shell globbing” 与接受文件输入的所有命令一起使用。这样做可以使用单个目录中的多个文件，如下面示例所示：

    $ spring run *.groovy

## 67.3、打包应用程序

你可以使用 jar 命令将应用程序打包到一个独立的可执行 jar 文件中，如下面示例所示：

    $ spring jar my-app.jar *.groovy

生成的 jar 包含编译应用程序生成的类和应用程序的所有依赖项，以便可以使用 java -jar 运行它。jar 文件还包含来自应用程序类路径的条目。你可以通过使用 --include 和 --exclude 来添加和删除 jar 的显式路径。两者都是逗号分隔的，并且都接受“+”和“-”形式的前缀，以表示它们应该从默认值中删除。默认包括如下：

    public/**, resources/**, static/**, templates/**, META-INF/**, *

默认排除如下：

    .*, repository/**, build/**, target/**, **/*.jar, **/*.groovy

在命令行上键入 spring help jar 以获取更多信息。

## 67.4、初始化新项目

init 命令允许你使用 start.spring.io 创建新项目，而无需离开 shell，如下面示例所示：

    $ spring init --dependencies=web,data-jpa my-project
    Using service at https://start.spring.io
    Project extracted to '/Users/developer/example/my-project'

前面的示例使用基于 Maven 的项目创建了一个 my-project 目录，该项目使用 spring-boot-starter-web 和 spring-boot-starter-data-jpa。你可以使用 --list 标志列出服务的功能，如下面示例所示：

    $ spring init --list
    =======================================
    Capabilities of https://start.spring.io
    =======================================
    
    Available dependencies:
    -----------------------
    actuator - Actuator: Production ready features to help you monitor and manage your application
    ...
    web - Web: Support for full-stack web development, including Tomcat and spring-webmvc
    websocket - Websocket: Support for WebSocket development
    ws - WS: Support for Spring Web Services
    
    Available project types:
    ------------------------
    gradle-build -  Gradle Config [format:build, build:gradle]
    gradle-project -  Gradle Project [format:project, build:gradle]
    maven-build -  Maven POM [format:build, build:maven]
    maven-project -  Maven Project [format:project, build:maven] (default)
    
    ...

init 命令支持许多选项。有关更多详细信息，请参见 help 输出。例如，以下命令创建一个使用 Java 8 和 war 打包的 Gradle 项目：

    $ spring init --build=gradle --java-version=1.8 --dependencies=websocket --packaging=war sample-app.zip
    Using service at https://start.spring.io
    Content saved to 'sample-app.zip'

## 67.5、使用嵌入式 Shell

Spring Boot 包括 BASH 和 zsh shell 的命令行完成脚本。如果你不使用这些 shell 中的任何一个 (也许你是 Windows 用户)，则可以使用 shell 命令启动集成 shell，如下面示例所示：

    $ spring shell
    Spring Boot (v2.1.6.RELEASE)
    Hit TAB to complete. Type \'help' and hit RETURN for help, and \'exit' to quit.

从嵌入式 shell 内部，你可以直接运行其他命令：

    $ version
    Spring CLI v2.1.6.RELEASE

嵌入式 shell 支持 ANSI 颜色输出以及 tab 完成。如果需要运行本机命令，可以使用 ! 前缀。要退出嵌入式 shell，请按 ctrl-c。

## 67.6、向 CLI 添加扩展

你可以使用 install 命令向 CLI 添加扩展。该命令采用格式为 group:artifact:version 的一组或多组工件坐标，如下面示例所示：

    $ spring install com.example:spring-boot-cli-extension:1.0.0.RELEASE

除了安装由你提供的坐标标识的工件之外，还安装了所有工件的依赖项。

要卸载依赖项，请使用 uninstall 命令。与 install 命令一样，它采用 group:artifact:version 格式的一组或多组工件坐标，如下面示例所示：

    $ spring uninstall com.example:spring-boot-cli-extension:1.0.0.RELEASE

它卸载由你提供的坐标及其依赖项标识的工件。

要卸载所有其他依赖项，可以使用 --all 选项，如下面示例所示：

    $ spring uninstall --all