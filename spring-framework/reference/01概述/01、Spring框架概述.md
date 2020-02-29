# Spring 框架概述

Spring 使创建 Java 企业应用程序变得容易。它提供了在企业环境中使用 Java 语言所需的一切，并支持作为 JVM 上的替代语言的 Groovy 和 Kotlin, 并具有根据应用程序需求创建多种体系结构的灵活性。从 Spring Framework 5.1 开始，Spring 需要 JDK 8 + (Java SE 8 +)，并为 JDK 11 LTS 提供开箱即用的支持。建议使用 Java SE 8 update 60 作为 Java 8 的最低补丁版本，但通常建议使用最近的补丁版本。

Spring 支持广泛的应用场景。在大型企业中，应用程序通常存在很长时间，并且必须在 JDK 和应用服务器上运行，其升级周期超出了开发者的控制范围。其他的可以作为单个 jar 运行，并嵌入服务器，可能在云环境中。然而，其他可能是不需要服务器的独立应用程序 (如批处理或集成工作负载)。

Spring 是开源的。它有一个庞大而活跃的社区，根据不同的现实世界用例提供持续的反馈。这帮助 Spring 在很长一段时间内成功进化。

# 1、我们所说的“Spring”

术语 “Spring” 在不同的上下文中意味着不同的东西。它可以用来指代 Spring Framework 项目本身，这就是它全部开始的地方。随着时间的推移，其他 Spring 项目已经建立在 Spring Framework 之上。大多数情况下，当人们说 “Spring” 时，他们指的是整个项目家族。本参考文档侧重于基础: Spring Framework 本身。

Spring Framework 分为几个模块。应用程序可以选择他们需要的模块。核心是核心容器的模块，包括配置模型和依赖注入机制。除此之外，Spring Framework 还为不同的应用程序体系结构提供基础支持，包括消息传递、事务数据和持久性以及 web。它还包括基于 Servlet 的 Spring MVC web 框架和并行的 Spring WebFlux 反应式 web 框架。

关于模块的说明：Spring 的框架 jars 允许部署到 JDK 9 的模块路径（“Jigsaw”）。为了在支持 Jigsaw 的应用程序中使用，Spring Framework 5 jars 附带了“Automatic Module Name”清单条目，这些条目定义了独立于 jar 工件名（jar 遵循与“-”而不是“.”相同的命名模式，例如“spring-core”和“spring-context”）的稳定的语言级模块名（“spring.core”、“spring.context”等）。当然，Spring 的框架 jars 在 JDK 8 和 9+ 的类路径上仍然运行良好。

# 2、Spring 和 Spring 框架的历史

Spring 作为对早期 J2EE 规范复杂性的回应，于 2003年诞生。虽然有些人认为 Java EE 和 Spring 是竞争对手，但 Spring 实际上是对 Java EE 的补充。Spring 编程模型不包含 Java EE 平台规范; 相反，它与 EE umbrella 中精心挑选的各个规范集成：

（1）[Servlet API (JSR 340)](https://jcp.org/en/jsr/detail?id=340)

（2）[WebSocket API (JSR 356)](https://www.jcp.org/en/jsr/detail?id=356)

（3）[Concurrency Utilities (JSR 236)](https://www.jcp.org/en/jsr/detail?id=236)

（4）[JSON Binding API (JSR 367)](https://jcp.org/en/jsr/detail?id=367)

（5）[Bean Validation (JSR 303)](https://jcp.org/en/jsr/detail?id=303)

（6）[JPA (JSR 338)](https://jcp.org/en/jsr/detail?id=338)

（7）[JMS (JSR 914)](https://jcp.org/en/jsr/detail?id=914)

（8）以及 JTA/JCA 事务协调设置 (如有必要)。

Spring Framework 还支持依赖注入（JSR 330）和公共注释（JSR 250）规范，应用程序开发者可以选择使用这些规范，而不是 Spring Framework 提供的特定于 Spring 的机制。

从 Spring Framework 5.0 开始，Spring 至少要求 Java EE 7 级别 (例如：Servlet 3.1+、JPA 2.1+) -- 同时在运行时提供与 Java EE 8 级别的更新 API 的开箱即用集成 (例如：Servlet 4.0、JSON 绑定 API) 在运行时遇到。这使 Spring 与例如 Tomcat 8 和 9 、 WebSphere 9 和 JBoss EAP 7 完全兼容。

随着时间的推移，Java EE 在应用程序开发中的作用已经发生了变化。在早期的 Java EE 和 Spring 中，创建应用程序是为了部署到应用服务器。今天，在 Spring Boot 的帮助下，应用程序以一种 devops 和云友好的方式创建，Servlet 容器嵌入并进行了简单的更改。从 Spring Framework 5 开始，WebFlux 应用程序甚至不直接使用 Servlet API，并且可以在不是 Servlet 容器的服务器 (如 Netty) 上运行。

Spring 继续创新和发展。除了 Spring 框架，还有其他项目，如 Spring Boot 、 Spring Security 、 Spring Data 、 Spring Cloud 、 Spring Batch 等。重要的是要记住，每个项目都有自己的源代码存储库、问题跟踪器和发布节奏。有关 Spring 项目的完整列表，请参见 spring.io/projects。

# 3、设计理念

当你学习一个框架时，不仅要知道它做了什么，还要知道它遵循了什么原则。以下是 Spring Framework 的指导原则：

（1）在每个级别提供选择。Spring 允许你尽可能晚地推迟设计决策。例如，你可以通过配置切换持久性提供程序，而无需更改代码。对于许多其他基础设施问题以及与第三方 APIs 的集成也是如此。

（2）适应不同的视角。Spring 拥抱灵活性，对事情应该如何做并不固执己见。它支持多种不同视角的应用需求。

（3）保持强大的向后兼容性。Spring 的进化已经被精心管理，以迫使版本之间很少有突破性的变化。Spring 支持精心选择的一系列 JDK 版本和第三方库，以便于维护依赖于Spring的应用程序和库。

（4）关心 API 设计。Spring 团队花了大量的精力和时间来制作直观的 API，这些 API 可以在很多版本和很多年中使用。

（5）为代码质量设定高标准。Spring 框架非常强调有意义的、当前的和精确的 javadoc。这是少数几个可以声明干净的代码结构而包之间没有循环依赖关系的项目之一。

# 4、反馈和贡献

对于如何回答（how-to）问题或诊断或调试问题，我们建议使用 StackOverflow，我们有一个问题页面，列出了建议使用的标签。如果你相当确定 Spring Framework 中存在问题或想要建议一个功能，请使用 [GitHub Issues](https://github.com/spring-projects/spring-framework/issues)。

如果你有解决方案或建议的修复方案，可以在 Github 上提交 pull 请求。但是，请记住，对于除了最琐碎的问题之外的所有问题，我们都希望在问题跟踪器中提交一张罚单，在那里进行讨论，并留下记录以供将来参考。

有关更多详细信息，请参见 [CONTRIBUTING](https://github.com/spring-projects/spring-framework/blob/master/CONTRIBUTING.md) 的顶级项目页面中的指导原则。

# 5、开始

如果你刚刚开始使用 Spring，你可能希望通过创建一个基于 [Spring Boot](https://projects.spring.io/spring-boot/) 的应用程序来开始使用 Spring Framework。Spring Boot 提供了一种快速 (自以为是) 的方法来创建基于 Spring 的生产就绪应用程序。它基于 Spring Framework，支持约定而不是配置，旨在让你尽快启动并运行。

你可以使用 start.spring.io 生成基本项目或遵循 “入门” 指南之一，例如入门构建 RESTful Web 服务。这些指南不仅更容易消化，而且非常注重任务，而且大多数都基于 Spring Boot。它们还涵盖了 Spring 产品组合中的其他项目，你在解决特定问题时可能需要考虑这些项目。
