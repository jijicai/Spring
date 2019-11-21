# 第二部分：入门

如果你正在开始使用 Spring Boot，或者通常是“Spring”，请从阅读本节开始。本节回答了基本的问题：是什么？、怎么做？、为什么？。本节包括 Spring Boot 介绍，及其安装说明。然后，我们带领你构建你的第一个 Spring Boot 应用，同时讨论一些核心的原则。

# 8、Spring Boot 介绍

Spring Boot 使你可以轻松地创建独立的、生产级别基于 Spring 的可运行的应用。我们同等对待 Spring 平台和第三方库，这样你就可以轻松入门了。大多数 Spring Boot 应用只需要很少的 Spring 配置。

你可以使用 Spring Boot 创建 Java 应用程序，它们可以通过使用命令 java -jar 或更传统的 war 部署启动。我们还提供了一个运行“spring 脚本”的命令行工具。

我们的主要目标是：

    （1）为所有 Spring 开发提供一个更快、更容易获得的入门体验。
    （2）开箱即用，但要在需求开始偏离默认值时迅速离开。
    （3）提供对大型项目类（如嵌入式服务器、安全性、度量标准、健康检查和外部化配置）通用的一系列非功能特性。
    （4）完全不需要代码生成，也不需要 XML 配置。

# 9、系统需求

Spring Boot 2.1.6.RELEASE 需要 Java 8 ，并且兼容至 Java 11（包括在内）。Spring Framework 5.1.8.RELEASE 或更高版本也是必需的。

为以下构建工具提供显示地构建支持：

    构建工具（Build Tool）	版本（Version）
    Maven					3.3+
    Gradle					4.4+

## 9.1 Servlet 容器

Spring Boot 支持以下嵌入式的 servlet 容器：

    名称（Nmae）				Servlet 版本（Servlet Version）
    Tomcat 9.0				4.0
    Jetty 9.4				3.1
    Undertom 2.0				4.0

你还可以将 Spring Boot 应用部署到任何 Servlet 3.1+ 兼容的容器中。
