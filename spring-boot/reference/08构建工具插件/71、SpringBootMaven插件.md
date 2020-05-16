# 第八部分：构建工具插件

Spring Boot 为 Maven 和 Gradle 提供构建工具插件。插件提供了多种功能，包括可执行 jar 的打包。本节提供了有关这两个插件的更多详细信息，以及你需要扩展不受支持的构建系统时的一些帮助。如果你刚刚开始，你可能想先从 “第三部分：使用 Spring Boot ”中阅读第 13 章：构建系统。

# 71、Spring Boot Maven 插件

[Spring Boot Maven 插件](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin)在 Maven 中提供 Spring Boot 支持，让你打包可执行 jar 或 war 归档文件并 “就地” 运行应用程序。要使用它，你必须使用 Maven 3.2（或更高版本）。

注释：有关完整的插件文档，请参见[Spring Boot Maven 插件网站](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin) 。

## 71.1、包括插件

要使用 Spring Boot Maven Plugin，请在 pom.xml 的 plugins 部分包含适当的 XML，如下面示例所示：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- ... -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.1.6.RELEASE</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
前面的配置重新打包了在 Maven 生命周期的 package 阶段构建的 jar 或 war。以下示例展示了重新打包的 jar 以及 target 目录中的原始 jar：

    $ mvn package
    $ ls target/*.jar
    target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original

如果不包括 &lt;execution/> 配置，如前一个示例所示，则可以单独运行插件（但仅当同时使用包目标时），如下面示例所示：

    $ mvn package spring-boot:repackage
    $ ls target/*.jar
    target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original

如果使用里程碑或快照版本，还需要添加适当的 pluginRepository 元素，如以下列表所示：
```
<pluginRepositories>
    <pluginRepository>
        <id>spring-snapshots</id>
        <url>https://repo.spring.io/snapshot</url>
    </pluginRepository>
    <pluginRepository>
        <id>spring-milestones</id>
        <url>https://repo.spring.io/milestone</url>
    </pluginRepository>
</pluginRepositories>
```
## 71.2、打包可执行 Jar 和 War 文件

一旦 spring-boot-maven-plugin 包含在你的 pom.xml 中，它会自动尝试重写归档文件，通过使用 spring-boot:repackage 目标使它们可执行。你应该通过使用通常的打包元素配置项目以构建 jar 或 war（视情况而定），如下例所示：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>jar</packaging>
    <!-- ... -->
</project>
```
在 package 阶段，Spring Boot 增强了你现有的归档文件。你要启动的主类可以通过使用配置选项或以通常的方式向清单添加 Main-Class 来指定。如果你没有指定主类，该插件将搜索具有 public static void main(String[] args) 方法的类。

要生成和运行项目工件，可以键入以下内容：

    $ mvn package
    $ java -jar target/mymodule-0.0.1-SNAPSHOT.jar

要构建一个可执行且可部署到外部容器中的 war 文件，你需要将嵌入式容器依赖项标记为“provided”，如下面示例所示：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>war</packaging>
    <!-- ... -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- ... -->
    </dependencies>
</project>
```
提示：有关如何创建可部署的 war 文件的更多详细信息，请参见[第 92.1 节：创建可部署的 War 文件 ](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-traditional-deployment.html#howto-create-a-deployable-war-file)。

[插件信息页面](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin)提供了高级配置选项和示例。