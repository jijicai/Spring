# E、可执行 Jar 格式

spring-boot-loader 模块允许 Spring Boot 支持可执行 jar 和 war 文件。如果你使用 Maven 插件或 Gradle 插件，可执行的 jar 会自动生成，你通常不需要知道它们是如何工作的细节。

如果你需要从不同的构建系统创建可执行 jar，或者如果你只是对底层技术感到好奇，本节提供了一些背景。

## E.1、嵌套 JARs

Java 没有提供任何标准的方式来加载嵌套的 jar 文件（即，本身包含在 jar 中的 jar 文件）。如果你需要分发一个独立的应用程序，而无需解包即可从命令行运行，这可能是有问题的。

为了解决这个问题，许多开发者使用“shaded” jars。shaded  jar 将所有 jars 的所有类打包成一个“uber jar”。shaded jar 的问题是很难看到哪些库实际上在你的应用程序中。如果在多个 jar 中使用相同的文件名（但内容不同），也可能会有问题。Spring Boot 采用了一种不同的方法，可以让你直接嵌套 jars。

### E.1.1、可执行 Jar 文件结构

与 Spring Boot Loader 兼容的 jar 文件应按以下方式构建：

    example.jar
     |
     +-META-INF
     |  +-MANIFEST.MF
     +-org
     |  +-springframework
     |     +-boot
     |        +-loader
     |           +-<spring boot loader classes>
     +-BOOT-INF
        +-classes
        |  +-mycompany
        |     +-project
        |        +-YourClasses.class
        +-lib
           +-dependency1.jar
           +-dependency2.jar

应用程序类应该放在嵌套的 BOOT-INF/classes 目录中。依赖项应该放在嵌套的 BOOT-INF/lib 目录中。

### E.1.2、可执行 War 文件结构

与 Spring Boot Loader 兼容的 war 文件应按以下方式构建：

    example.war
     |
     +-META-INF
     |  +-MANIFEST.MF
     +-org
     |  +-springframework
     |     +-boot
     |        +-loader
     |           +-<spring boot loader classes>
     +-WEB-INF
        +-classes
        |  +-com
        |     +-mycompany
        |        +-project
        |           +-YourClasses.class
        +-lib
        |  +-dependency1.jar
        |  +-dependency2.jar
        +-lib-provided
           +-servlet-api.jar
           +-dependency3.jar

依赖项应放在嵌套的 WEB-INF/lib 目录中。运行嵌入式时需要但部署到传统 web 容器时不需要的任何依赖项都应该放在 WEB-INF/lib-provided 中。

## E.2、Spring Boot 的 JarFile 类

用于支持加载嵌套 jar 的核心类是 org.springframework.boot.loader.jar.JarFile。它允许你从标准 jar 文件或嵌套的子 jar 数据加载 jar 内容。首次加载时，每个 JarEntry 的位置将映射到外部 jar 的物理文件偏移量，如下面示例所示：

    myapp.jar
    +-------------------+-------------------------+
    | /BOOT-INF/classes | /BOOT-INF/lib/mylib.jar |
    |+-----------------+||+-----------+----------+|
    ||     A.class      |||  B.class  |  C.class ||
    |+-----------------+||+-----------+----------+|
    +-------------------+-------------------------+
     ^                    ^           ^
     0063                 3452        3980

上面的示例展示了如何在 myapp.jar 中的 /BOOT-INF/classes 中的 0063 位置找到 A.class。嵌套 jar 中的 B.class 实际上可以在 myapp.jar 中的 3452 位置找到，而 C.class 位于 3980 位置。

有了这些信息，我们可以通过寻找外部 jar 的适当部分来加载特定的嵌套条目。我们不需要解压缩归档文件，也不需要将所有输入数据读入内存。

### E.2.1、与标准 Java JarFile 的兼容性

Spring Boot Loader 努力保持与现有代码和库兼容。org.springframework.boot.loader.jar.JarFile 继承自 java.util.jar.JarFile，应该作为一个替换项。getURL() 方法返回一个 URL，该 URL 打开一个与 java.net.JarURLConnection 兼容的连接，并且可以与 Java 的 URLClassLoader 一起使用。

## E.3、启动可执行 Jars

org.springframework.boot.loader.Launcher 类是一个特殊的引导类，用作可执行 jar 的主要入口点。它是 jar 文件中的实际 Main-Class，用于设置适当的 URLClassLoader 并最终调用 main() 方法。

有三个启动器子类（JarLauncher、WarLauncher 和 PropertiesLauncher）。它们的目的是从目录（与类路径上的显式文件相反）中的嵌套 jar 文件或 war 文件加载资源 （.class 文件等）。在 JarLauncher 和 WarLauncher 的情况下，嵌套路径是固定的。JarLauncher 在 BOOT-INF/lib/ 中查找，而 WarLauncher 在 WEB-INF/lib/ 和 WEB-INF/lib-provided/ 中查找。如果需要更多，可以在这些位置添加额外的 jars。默认情况下，PropertiesLauncher 在应用程序归档文件的 BOOT-INF/lib/ 中查找，但你可以通过在 loader.properties 中设置名为 LOADER_PATH 或 loader.path 的环境变量（这是归档文件中目录、归档文件或目录的逗号分隔列表）来添加其他位置。

### E.3.1、启动器清单

你需要指定适当的 Launcher 作为 META-INF/MANIFEST.MF 的 Main-Class 属性。要启动的实际类（即包含 main 方法的类）应在 Start-Class  属性中指定。

以下示例展示可执行 jar 文件的典型 MANIFEST.MF：

    Main-Class: org.springframework.boot.loader.JarLauncher
    Start-Class: com.mycompany.project.MyApplication

对于 war 文件，如下所示：

    Main-Class: org.springframework.boot.loader.WarLauncher
    Start-Class: com.mycompany.project.MyApplication

注释：不需要在清单文件中指定 Class-Path 项。类路径是从嵌套的 jar 中推导出来的。

### E.3.2、Exploded 归档文件

某些 PaaS 实现可能会选择在运行之前解压缩归档文件。例如，Cloud Foundry 以这种方式运行。你可以通过启动相应的启动器来运行解压缩的归档文件，如下所示：

    $ unzip -q myapp.jar
    $ java org.springframework.boot.loader.JarLauncher

## E.4、PropertiesLauncher 的功能

PropertiesLauncher 有一些特殊功能，可以使用外部属性（系统属性、环境变量、清单条目或 loader.properties）启用。下表描述了这些属性：

|键			|用途|
|---|---|
|loader.path		|逗号分隔的类路径，例如：lib、${HOME}/app/lib。前面的条目优先，就像 javac 命令行上的常规 -classpath 一样。|
|loader.home		|用于解析 loader.path 中的相对路径。例如，给定 loader.path=lib，则 ${loader.home}/lib 是一个类路径位置（以及该目录中的所有 jar 文件）。此属性也用于定位 loader.properties 文件，如以下示例 /opt/app 中，它默认为 ${user.dir}。|
|loader.args		|主方法的默认参数（空格分隔）。|
|loader.main		|要启动的主类的名称（例如：com.app.Application）。|
|loader.config.name	|属性文件的名称（例如，launcher）它默认为 loader。|
|loader.config.location	|属性文件的路径（例如，classpath:loader.properties）。默认为 loader.properties。|
|loader.system		|布尔标志表示所有属性都应该添加到系统属性中，默认为 false。|

当指定为环境变量或清单项时，应使用以下名称：

|键			|清单项			|环境变量|
|---|---|---|
|loader.path		|Loader-Path		|LOADER_PATH|
|loader.home		|Loader-Home		|LOADER_HOME|
|loader.args		|Loader-Args		|LOADER_ARGS|
|loader.main		|Start-Class		|LOADER_MAIN|
|loader.config.location	|Loader-Config-Location	|LOADER_CONFIG_LOCATION|
|loader.system		|Loader-System		|LOADER_SYSTEM|

提示：构建插件会在构建 fat jar 时自动将 Main-Class 属性移动到 Start-Class。如果使用它，请使用 Main-class 属性并省略 Start-Class 来指定要启动的类的名称。

以下规则适用于和 PropertiesLauncher 一起使用：

    （1）在 loader.home 中搜索 loader.properties，然后在类路径的根目录中搜索，然后在类路径 /BOOT-INF/classes 中搜索。使用具有该名称的文件存在的第一个位置。
    （2）仅当未指定 loader.config.location 时，loader.home 才是其他属性文件（覆盖默认值）的目录位置。
    （3）loader.path 可以包含目录（递归扫描 jar 和 zip 文件）、归档文件路径、存档中扫描 jar 文件的目录（例如：dependencies.jar!/lib）或通配符模式（用于默认的 JVM 行为）。归档文件路径可以相对于 loader.home 或文件系统中带有 jar:file: 前缀的任何位置。
    （4）loader.path（如果为空）默认为 BOOT-INF/lib（表示本地目录或嵌套目录（如果从归档文件运行））。因此，当没有提供额外的配置时，PropertiesLauncher 的行为与 JarLauncher 相同。
    （5）无法使用 loader.path 配置 loader.properties 的位置（启动 PropertiesLauncher 时用于搜索后者的类路径是 JVM 类路径）。
    （6）占位符替换是在使用前从系统和环境变量加上属性文件本身对所有值进行的。
    （7）属性的搜索顺序（在多个地方查找是有意义的）是环境变量、系统属性、loader.properties、分解的归档文件清单和归档文件清单。

## E.5、可执行 Jar 的限制

使用 Spring Boot Loader 打包的应用程序时，需要考虑以下限制：

（1）Zip 条目压缩：嵌套 jar 的 ZipEntry 必须使用 ZipEntry.STORED 方法保存。这是必需的，以便我们可以直接查找嵌套 jar 中的单个内容。嵌套 jar 文件本身的内容仍然可以压缩，就像外部 jar 中的任何其他条目也可以压缩一样。

（2）系统类加载器: 启动的应用程序在加载类时应该使用 Thread.getContextClassLoader()（大多数库和框架默认都这样做）。尝试用 ClassLoader.getSystemClassLoader() 加载嵌套的 jar 类失败。java.util.Logging 始终使用系统类加载器。因此，你应该考虑不同的日志实现。

## E.6、备选的单个 Jar 解决方案

如果上述限制意味着你不能使用 Spring Boot Loader，请考虑以下替代方案：

（1）[Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/)

（2）[JarClassLoader](http://www.jdotsoft.com/JarClassLoader.php)

（3）[OneJar](https://sourceforge.net/projects/one-jar/)

（4）[Gradle Shadow Plugin](https://imperceptiblethoughts.com/shadow/)
