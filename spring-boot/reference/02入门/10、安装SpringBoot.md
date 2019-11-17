# 10、安装 Spring Boot

Spring Boot 可以与“经典”Java 开发工具一起使用，也可以作为命令行工具安装。不管怎样，你都需要 Java SDK v1.8 或更高版本。开始之前，应使用以下命令检查当前的 Java 安装：

    $ java -version

如果你是 Java 开发的新手，或如果你想体验 Spring Boot，那么你可能需要首先试一下 Spring Boot CLI（Command Line Interface）。否则，请继续阅读“经典”安装说明。

## 10.1 Java 开发者的安装说明

你可以像使用任何标准 Java 库一样使用 Spring Boot。为此，在类路径中包含适当的 spring-boot-*.jar 文件。Spring Boot 不需要任何特殊的工具集成，因此可以使用任何 IDE 或文本编辑器。此外，Spring Boot 应用没有什么特别之处，因此可以像任何其他 Java 程序一样运行、调试 Spring Boot 应用程序。

尽管你可以拷贝 Spring Boot jars，但是我们通常建议你使用一个支持依赖管理的构建工具（例如：Maven 或 Gradle）。

### 10.1.1 Maven 安装

Spring Boot 兼容 Apache Maven 3.3 或以上版本。如果尚未安装 Maven，可以按照 maven.apache.org 网站的说明进行操作。

提示：在很多操作系统上，可以用 包管理器安装 Maven。如果你使用 OSX Homebrew，则请试一下 brew install maven。Ubuntu 用户可以运行 sudo apt-get install maven。用 Chocolately 的 Windows 用户可以从提升的（管理员）提示符运行 choco install maven 。

Spring Boot 依赖项使用 org.springframework.boot groupId。通常，Maven POM 文件继承自 spring-boot-starter-parent 项目，并声明一或多个启动器的依赖项。Spring Boot 也提供可选的 Maven 插件来创建可执行的 jars。

以下清单展示了一个典型的 pom.xml 文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    
    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>
    
    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    
    </project>

提示：spring-boot-startr-parent 是使用 Spring Boot 的一个好方法，但它可能并不总是适合的。有时候，你可能需要继承不同的 parent POM，或者你可能不喜欢我们的默认设置。在这些情况下，请参看 13.2.2 节“在没有 parent POM 的情况下使用 Spring Boot”，以获取使用 import 作用域的替代解决方案。

### 10.1.2 Gradle 安装

Spring Boot 兼容 Gradle 4.4 或以上版本。如果你还没有安装 Gradle，你可以按照 gradle.org 网站的说明进行操作。

Spring Boot 依赖项可以通过使用 org.springframework.boot group 来声明。通常，项目声明一或多个启动器的依赖项。Spring Boot 提供一个有用的 Gradle 插件，它可以用于简化依赖声明和创建可执行的 jars。

Gradle 包装器
Gradle 包装器提供了当需要创建项目时一个“获取” Gradle 的好方法。它是一个小脚本和库，你可以将它与代码一起提交以引导构建过程。有关详情，请查看：docs.gradle.org/4.2.1/userguide/gradle_wrapper.html （https://docs.gradle.org/4.2.1/userguide/gradle_wrapper.html）。

有关 Spring Boot 和 Gradle 入门的更多详情，请参见 Gradle 插件参考指南的入门部分。

## 10.2 安装 Spring Boot CLI

Spring Boot CLI（Command Line Interface）是一个命令行工具，你可以使用它来快速创建 Spring 原型。它允许你运行 Groovy 脚本，这意味着你有一个熟悉的类似 Java 的语法，而没有那么多样板代码。

你不需要使用 CLI 来处理 Spring Boot，但这绝对是让 Spring 应用程序起步的最快方法。

### 10.2.1 手动安装

你可以从 Spring 软件仓库下载 Spring CLI 发行版：

（1）spring-boot-cli-2.1.6.RELEASE-bin.zip（https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.1.6.RELEASE/spring-boot-cli-2.1.6.RELEASE-bin.zip）

（2）spring-boot-cli-2.1.6.RELEASE-bin.tar.gz（https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.1.6.RELEASE/spring-boot-cli-2.1.6.RELEASE-bin.tar.gz）

还提供了最新的快照分发版。

下载后，请按照解压缩的存档文件中的 INSTALL.txt 说明进行操作。总之，在 .zip 文件的 bin/ 目录中有一个 spring 脚本（Wingows中是spring.bat ）。或者，可以将 java -jar 与 .jar 文件一起使用（该脚本帮助你确保类路径设置正确）。

### 10.2.2 用 SDKMAN! 安装

SDKMAN!（软件开发工具包管理器）可用于管理各种二进制 SDK 的多个版本，包括 Groovy 和 Spring Boot CLI。从网站 sdkman.io 得到 SDKMAN!，并且使用以下命令安装 Spring Boot：

    $ sdk install springboot
    $ spring --version
    Spring Boot v2.1.6.RELEASE

如果你开发了 CLI 的功能并希望轻松访问你构建的版本，请使用以下命令：

    $ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-2.1.6.RELEASE-bin/spring-2.1.6.RELEASE/
    $ sdk default springboot dev
    $ spring --version
    Spring CLI v2.1.6.RELEASE

前面的指令安装一个名为 dev 实例的 Spring 本地实例。它指向你的目标构建位置，因此每次重新构建 Spring Boot 时，Spring 都是最新的。

你可以通过运行以下命令来查看它：

    $ sdk ls springboot
    
    ================================================================================
    Available Springboot Versions
    ================================================================================
    > + dev
    * 2.1.6.RELEASE
    
    ================================================================================
    + - local version
    * - installed
    > - currently in use
    ================================================================================

### 10.2.3 OSX Homebrew 安装

如果你在 Mac 上并使用 Homebrew，则可以通过使用以下命令安装 Spring Boot CLI：

    $ brew tap pivotal/tap
    $ brew install springboot

Homebrew 将 spring 安装到 /usr/local/bin 目录中。

注释：如果你没有看到 formula，则brew 的安装可能已过期。在这种情况下，请运行 brew update，并重试。

### 10.2.4 MacPorts 安装

如果你在 Mac 上并使用 MacPorts，则可以使用以下命令安装 Spring Boot CLI：

    $ sudo port install spring-boot-cli

### 10.2.5 命令行自动补全

Spring Boot CLI 包括为 BASH 和 zsh shell 提供命令自动补全的脚本。你可以在任何 shell 中获取脚本（也称为 spring）,也可以将其放入你的个人或系统范围的bash 自动补全初始化中。在 Debian 系统中，系统范围的脚本位于 /shell-completion/bash 目录中，并且当新 shell 启动时，该目录中的所有脚本都被执行。例如，如果使用 SDKMAN! 安装了该脚本，则可以手动运行它，使用以下命令：

    $ . ~/.sdkman/candidates/springboot/current/shell-completion/bash/spring
    $ spring <HIT TAB HERE>
    grab  help  jar  run  test  version

注释：如果你使用 Homebrew 或 MacPorts 来安装 Spring Boot CLI，则命令行自动补全脚本会是和 shell 一起自动注册的。

### 10.2.6 Windows Scoop 安装

如果你在 Windows 上使用 Scoop，你可以使用以下命令安装 Spring Boot CLI：

    > scoop bucket add extras
    > scoop install springboot

注释：如果你没有看到 app manifest，则安装的 Scoop 可能已经过期。在这种情况下，请运行 scoop update，并重试。

### 10.2.7 快速启动 Spring CLI 示例

你可以使用下面的 web 应用程序来测试安装。首先，创建一个名为 app.groovy 的文件，如下所示：

    @RestController
    class ThisWillActuallyRun {
    
    @RequestMapping("/")
    String home() {
        "Hello World!"
    }
    
    }

然后从 shell 中运行它，如下所示：

    $ spring run app.groovy

注释：第一次运行应用程序比较慢，因为要加载依赖项。接下来的运行要快得多。

在你喜欢的 web 浏览器中打开 localhost:8080。你应该会看到以下输出：

    Hello World!

## 10.3 从早期版本的 Spring Boot 升级

如果你正在从早期版本的 Spring Boot 升级，则请查看项目 wiki 上提供详细升级说明的“迁移指南”。还可以查看发布说明，以获得每个版本的新的和值得注意的特性列表。

当升级到一个新特性版本时，一些属性可能被重命名或删除。Spring Boot 提供了一种方法，可以在启动时分析应用程环境并打印诊断信息，还可以在运行时为你临时迁移属性。要启用该特性，请将以下依赖项添加到你的项目中：

    <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-properties-migrator</artifactId>
    <scope>runtime</scope>
    </dependency>

警告：后面添加到环境中的属性不会被考虑，例如：@PropertySource。
注释：一旦你做完迁移，请确保从你的项目依赖列表中移除此模块。

要想升级已安装的 CLI，请使用合适的包管理命令（例如：brew upgrade），或者，如果你手动安装了 CLI，请遵循标准说明，记得更新 PATH 环境变量以移除任何旧的引用。















