# B、配置元数据

Spring Boot jar 包含元数据文件，这些文件提供了所有支持的配置属性的详细信息。这些文件旨在让 IDE 开发者在用户使用 application.properties 或 application.yml 文件时提供上下文帮助和 “代码完成”。

大多数元数据文件是在编译时通过处理所有用 @ConfigurationProperties 注解的项自动生成的。但是，也可以[手动编写部分元数据](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/configuration-metadata.html#configuration-metadata-additional-metadata)，以满足特殊情况或更高级的用例。

## B.1、元数据格式

配置元数据文件位于 META-INF/spring-configuration-metadata.json 下的 jar 中，它们使用一种简单的 JSON 格式，将项分类在“groups”或“properties”下，并将附加值提示分类在“hints”下，如下面示例所示：
```
{"groups": [
    {
        "name": "server",
        "type": "org.springframework.boot.autoconfigure.web.ServerProperties",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
    },
    {
        "name": "spring.jpa.hibernate",
        "type": "org.springframework.boot.autoconfigure.orm.jpa.JpaProperties$Hibernate",
        "sourceType": "org.springframework.boot.autoconfigure.orm.jpa.JpaProperties",
        "sourceMethod": "getHibernate()"
    }
    ...
],"properties": [
    {
        "name": "server.port",
        "type": "java.lang.Integer",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
    },
    {
        "name": "server.address",
        "type": "java.net.InetAddress",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
    },
    {
          "name": "spring.jpa.hibernate.ddl-auto",
          "type": "java.lang.String",
          "description": "DDL mode. This is actually a shortcut for the \"hibernate.hbm2ddl.auto\" property.",
          "sourceType": "org.springframework.boot.autoconfigure.orm.jpa.JpaProperties$Hibernate"
    }
    ...
],"hints": [
    {
        "name": "spring.jpa.hibernate.ddl-auto",
        "values": [
            {
                "value": "none",
                "description": "Disable DDL handling."
            },
            {
                "value": "validate",
                "description": "Validate the schema, make no changes to the database."
            },
            {
                "value": "update",
                "description": "Update the schema if necessary."
            },
            {
                "value": "create",
                "description": "Create the schema and destroy previous data."
            },
            {
                "value": "create-drop",
                "description": "Create and then destroy the schema at the end of the session."
            }
        ]
    }
]}

```
每个“property”都是用户用给定值指定的配置项。例如，可以在 application.properties 中指定 server.port 和 server.address，如下所示：

    server.port=9090
    server.address=127.0.0.1

“groups”是更高级别的项，它们本身不指定值，而是为属性提供上下文分组。例如，server.port 和 server.address 属性是 server 组的一部分。

注释：并不要求每个 property 都有一个 groups。有些属性可能本身就存在。

最后，“hints”是用于帮助用户配置给定属性的附加信息。例如，当开发者正在配置 spring.jpa.hibernate.ddl-auto 属性时，工具可以使用提示为 none、validate、update、create 和 create-drop 值提供一些自动完成帮助。

### B.1.1、Group 属性

groups 数组中包含的 JSON 对象可以包含下表所示的属性：

|名称		|类型	|用途|
|---|---|---|
|name		|String	|group 的全名。此属性是必需的。|
|type		|String	|group 的数据类型的类名。例如，如果该 group 基于使用 @ConfigurationProperties 注解的类，则该属性将包含该类的完全限定名称。如果它基于 @Bean 方法，它将是该方法的返回类型。如果类型未知，则可以省略该属性。|
|description	|String	|可显示给用户的 group 的简短描述。如果没有描述，可以省略。建议以简短的段落描述，第一行提供简明的摘要。描述中的最后一行应以句点（.）结尾。|
|sourceType	|String	|提供该 group 的源的类名。例如，如果该 group 基于使用 @ConfigurationProperties 注解的 @Bean 方法，则此属性将包含 @Configuration 类（包含该方法）的完全限定名称。如果源类型未知，则可以省略该属性。|
|sourceMethod	|String	|提供此 group 的方法的全名（包括括号和参数类型）（例如，@ConfigurationProperties 注解的 @Bean 方法的名称）。如果源方法未知，则可以省略它。|

### B.1.2、Property 属性

properties 数组中包含的 JSON 对象可以包含下表中描述的属性：

|名称		|类型		|用途|
|---|---|---|
|name		|String		|property 的全名。名称采用小写句点分隔形式（例如，server.address）。此属性是必需的。|
|type		|String		|property 的数据类型（例如：java.lang.String）的完整签名，但也是完整的泛型类型（例如：java.util.Map<java.util.String，acme.MyEnum>）。可以使用此属性指导用户输入值的类型。为了保持一致性，基本类型通过使用其包装器对应项来指定（例如，boolean 变为 java.lang.Boolean）。请注意，该类可能是一个复杂类型，在绑定值时从 String 转换而来。如果类型未知，则可以省略。|
|description	|String		|可以向用户显示的 group 的简短描述。如果没有可用的描述，可以省略。建议描述为简短段落，第一行提供简明摘要。描述中的最后一行应以句点（.）结束。|
|sourceType	|String		|提供此属性的源的类名。例如，如果属性来自用 @ConfigurationProperties 注解的类，则此属性将包含该类的完全限定名。如果源类型未知，则可以省略它。|
|defaultValue	|Object		|默认值，在未指定 property 时使用。如果 property 的类型是数组，则它可以是值数组。如果默认值未知，则可以省略。|
|deprecation	|Deprecation	|指定 property 是否已弃用。如果该字段未弃用或该信息未知，则可以省略该字段。下表提供了有关 deprecation 属性的更多详细信息。|

每个 properties 元素的 deprecation 属性中包含的 JSON 对象可以包含以下属性：

|名称		|类型		|用途|
|---|---|---|
|level		|String		|弃用级别，可以是 warning（默认值）或 error。当属性具有 warning 弃用级别时，它仍应在环境中绑定。但是，当它具有 error 弃用级别时，该属性将不再受管理且不受绑定。|
|reason		|String		|该属性被弃用的原因的简短描述。如果没有理由，可以省略。建议描述为简短段落，第一行提供简明摘要。描述中的最后一行应以句点（.）结尾。|
|replacement	|String		|替换此已弃用属性的属性的全名。如果此属性没有替换项，则可以省略。|

注释：在 Spring Boot 1.3 之前，可以使用单个 deprecated 的布尔属性代替 deprecation 元素。这仍然以弃用的方式得到支持，不应再使用。如果没有可用的原因和替换，则应设置一个空的 deprecation 对象。

通过将 @DeprecatedConfigurationProperty 注解添加到暴露弃用属性的 getter 中，也可以在代码中以声明方式指定弃用。例如，假设 app.acme.target 属性令人困惑，并被重命名为 app.acme.name。以下示例说明如何处理这种情况：
```
@ConfigurationProperties("app.acme")
public class AcmeProperties {

    private String name;

    public String getName() { ... }

    public void setName(String name) { ... }

    @DeprecatedConfigurationProperty(replacement = "app.acme.name")
    @Deprecated
    public String getTarget() {
        return getName();
    }

    @Deprecated
    public void setTarget(String target) {
        setName(target);
    }
}
```
注释：没有办法设定 level。由于代码仍在处理属性，因此始终假定为 warning。

前面的代码确保弃用的属性仍然有效（委托给幕后的 name 属性）。一旦 getTarget 和 setTarget 方法可以从公共 API 中删除，元数据中的自动弃用提示也会消失。如果你想保留一个提示，添加带有 error 弃用级别的手动元数据可以确保用户仍然被告知该属性。当提供 replacement 时，这样做特别有用。

### B.1.3、Hint 属性

提示数组中包含的 JSON 对象可以包含下表所示的属性：

|名称		|类型		|用途|
|---|---|---|
|name		|String		|此提示所引用的属性的全名。名称采用小写的句点分隔形式（例如：spring.mvc.servlet.path）。如果属性引用映射（如 system.contexts），则提示要么应用于映射的键（system.context.keys），要么应用于映射的值（system.context.values）。此属性是必需的。|
|values		|ValueHint[]	|ValueHint 对象定义的有效值列表（在下表中描述）。每个条目都定义了值并可能有一个描述。|
|providers	|ValueProvider[]	|由 ValueProvider 对象定义的提供程序列表（在本文档后面描述）。每个条目都定义提供程序的名称及其参数（如果有的话）。|

每个 hint 元素的 values 属性中包含的 JSON 对象可以包含下表中描述的属性：

|名称		|类型		|用途|
|---|---|---|
|value		|Object		|提示引用的元素的有效值。如果属性的类型是数组，它也可以是值的数组。此属性是强制性的。|
|description	|String		|可以向用户显示的值的简短描述。如果没有可用的描述，可以省略。建议描述为简短段落，第一行提供简明摘要。描述中的最后一行应以句点（.）结束。|

每个 hint 元素的 providers 属性中包含的 JSON 对象可以包含下表中描述的属性：

|名称		|类型		|用途|
|---|---|---|
|name		|String		|用于为提示引用的元素提供附加内容帮助的提供程序的名称。|
|parameters	|JSON 对象	|提供程序支持的任何其他参数（有关详细信息，请查看提供程序的文档）。|

### B.1.4、重复的元数据项

具有相同“property”和“group”名称的对象可以在元数据文件中出现多次。例如，你可以将两个单独的类绑定到相同的前缀，每个类都有可能重叠的 property 名称。虽然多次出现在元数据中的相同名称不应该是常见的，但元数据的使用者应该注意确保他们支持它。

## B.2、提供手动提示

为了改善用户体验并进一步帮助用户配置给定属性，可以提供以下附加元数据：

    （1）描述属性的潜在值列表。
    （2）关联提供者，将定义良好的语义附加到属性，以便工具可以根据项目的上下文发现潜在值的列表。

### B.2.1、值提示

每个提示的 name 属性都引用 property 的 name。在[前面显示的初始示例](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/configuration-metadata.html#configuration-metadata-format)中，我们为 spring.jpa.hibernate.ddl-auto property 提供了五个值：none、validate、update、create 和 create-drop。每个值也可能有一个描述。

如果 property 是 Map 类型，则可以同时为键和值提供提示（但不能为映射本身提供提示）。特殊的 .keys 和 .values 后缀必须分别引用键和值。

假设 sample.contexts 将魔法 String 值映射为整数，如下面示例所示：
```
@ConfigurationProperties("sample")
public class SampleProperties {

    private Map<String,Integer> contexts;
    // getters and setters
}
```
魔法值（在本例中）是 sample1 和 sample2。要为键提供额外的内容帮助，可以将以下 JSON 添加到模块的手动元数据中：
```
{"hints": [
    {
        "name": "sample.contexts.keys",
        "values": [
            {
                "value": "sample1"
            },
            {
                "value": "sample2"
            }
        ]
    }
]}
```
提示：我们建议你对这两个值使用 Enum。如果你的 IDE 支持它，这是目前为止最有效的自动完成方法。

### B.2.2、值提供者

提供者是将语义附加到 property 的一种强大方法。在本节中，我们定义了可以用于你自己提示的官方提供者。但是，你最喜欢的 IDE 可能会实现其中的一些或没有。此外，它最终可以提供自己的。

注释：由于这是一个新特性，IDE 供应商必须跟上它的工作方式。采用时间自然会有所不同。

下表总结了支持的提供者列表：

|名称			|描述|
|---|---|
|any			|允许提供任何附加值。|
|class-reference		|自动完成项目中可用的类。通常由 target 参数指定的基类约束。|
|handle-as		|处理 property，就像它是由强制 target 参数定义的类型定义的一样。|
|logger-name		|自动完成有效的日志器名称和日志器组。通常，当前项目中可用的包和类名可以自动完成，也可以定义组。|
|spring-bean-reference	|自动完成当前项目中可用的 bean 名称。通常由 target 参数指定的基类约束。|
|spring-profile-name	|自动完成项目中可用的 Spring profile 名称。|

提示：对于给定的 property，只能有一个提供者处于活动状态，但是如果它们都可以以某种方式管理该 property，则可以指定多个提供者。确保将最强大的提供程序放在第一位，因为 IDE 必须使用它可以处理的 JSON 部分中的第一个提供者。如果不支持给定 property 的提供者，则也不提供特殊的内容帮助。

**Any**

特殊的 any 提供者值允许提供任何附加值。如果支持，则应应用基于属性类型的常规值验证。

如果你有一个值列表，并且任何额外的值仍应视为有效，则通常使用此提供者。

以下示例提供 on 和 off 作为 system.state 的自动完成值：
```
{"hints": [
    {
        "name": "system.state",
        "values": [
            {
                "value": "on"
            },
            {
                "value": "off"
            }
        ],
        "providers": [
            {
                "name": "any"
            }
        ]
    }
]}
```
请注意，在上面的示例中，也允许任何其他值。

**Class Reference**

class-reference 提供者自动完成项目中可用的类。此提供者支持以下参数：

|参数		|类型		|默认值	|描述|
|---|---|---|---|
|target		|String (Class)	|none	|应分配给所选值的类的完全限定名称。通常用于过滤掉非候选类。请注意，类型本身可以通过公开具有适当上限的类来提供此信息。|
|concrete	|boolean		|true	|指定是否仅将具体类视为有效候选。|

以下元数据片段对应于定义要使用的 JspServlet 类名的标准 server.servlet.jsp.class-name 属性：
```
{"hints": [
    {
        "name": "server.servlet.jsp.class-name",
        "providers": [
            {
                "name": "class-reference",
                "parameters": {
                    "target": "javax.servlet.http.HttpServlet"
                }
            }
        ]
    }
]}
```
**Handle As**

handle-as 提供者允许你将属性的类型替换为更高级的类型。当属性具有 java.lang.String 类型时，通常会发生这种情况，因为你不希望配置类依赖于可能不在类路径上的类。此提供者支持以下参数：

|参数	|类型		|默认值	|描述|
|---|---|---|---|
|target	|String（Class）	|无	|要为属性考虑的类型的完全限定名。此参数是必需的。|

可以使用以下类型：

    （1）任何 java.lang.Enum：列出属性的可能值。（我们建议使用枚举类型定义属性，因为 IDE 不需要进一步的提示来自动完成这些值。）
    （2）java.nio.charset.Charset：支持字符集/编码值 (如 UTF-8) 的自动完成
    （3）java.util.Locale：区域设置（如 en_US）的自动完成
    （4）org.springframework.util.MimeType：支持内容类型值（如 text/plain）的自动完成
    （5）org.springframework.core.io.Resource：支持 Spring 的资源抽象的自动完成，以引用文件系统或类路径上的文件。（如 classpath:/sample.properties）

提示：如果可以提供多个值，请使用 Collection 或 Array 类型向 IDE 介绍它。

以下元数据片段对应于定义要使用的变更日志路径的标准 spring.liquibase.change-log 属性。它实际上在内部用作 org.springframework.core.io.Resource，但不能这样公开，因为我们需要保留原始 String 值才能将其传递给 Liquibase API。
```
{"hints": [
    {
        "name": "spring.liquibase.change-log",
        "providers": [
            {
                "name": "handle-as",
                "parameters": {
                    "target": "org.springframework.core.io.Resource"
                }
            }
        ]
    }
]}
```
**Logger Name**

logger-name 提供者自动完成有效的日志器名称和[日志器组](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-groups)。通常，当前项目中可用的包和类名可以自动完成。如果组已启用（默认），并且配置中标识了自定义日志器组，则应提供该组的自动完成。特定框架可能还支持额外的魔法日志器名称。

此提供者支持以下参数：

|参数	|类型	|默认值	|描述|
|---|---|---|---|
|group	|boolean	|true	|指定是否应考虑已知组。|

由于日志器名称可以是任意名称，因此此提供者应该允许任何值，但可以突出显示有效包和项目类路径中不可用的类名称。

以下元数据片段对应于标准 logging.level 属性。键是日志器名称，值对应于标准日志级别或任何自定义级别。由于 Spring Boot 定义了一些现成的日志组，因此已经为它们添加了专用的值提示。
```
{"hints": [
    {
        "name": "logging.level.keys",
        "values": [
            {
                "value": "root",
                "description": "Root logger used to assign the default logging level."
            },
            {
                "value": "sql",
                "description": "SQL logging group including Hibernate SQL logger."
            },
            {
                "value": "web",
                "description": "Web logging group including codecs."
            }
        ],
        "providers": [
            {
                "name": "logger-name"
            }
        ]
    },
    {
        "name": "logging.level.values",
        "values": [
            {
                "value": "trace"
            },
            {
                "value": "debug"
            },
            {
                "value": "info"
            },
            {
                "value": "warn"
            },
            {
                "value": "error"
            },
            {
                "value": "fatal"
            },
            {
                "value": "off"
            }

        ],
        "providers": [
            {
                "name": "any"
            }
        ]
    }
]}
```
**Spring Bean Reference**

spring-bean-reference 提供者自动完成在当前项目的配置中定义的 bean。此提供者支持以下参数：

|参数	|类型		|默认值	|描述|
|---|---|---|---|
|target	|String（Class）	|无	|应分配给候选者的 bean 类的完全限定名称。通常用于过滤掉非候选 bean。|

以下元数据片段对应于定义要使用的 MBeanServer bean 名称的标准 spring.jmx.server 属性：
```
{"hints": [
    {
        "name": "spring.jmx.server",
        "providers": [
            {
                "name": "spring-bean-reference",
                "parameters": {
                    "target": "javax.management.MBeanServer"
                }
            }
        ]
    }
]}
```
注释：绑定器不知道元数据。如果你提供了该提示，则仍然需要将 bean 名称转换为 ApplicationContext 使用的实际的 bean 引用。

**Spring Profile Name**

spring-profile-name 提供者自动完成当前项目配置中定义的 Spring profiles。

以下元数据片段对应于定义要启用的 Spring profile(s) 名称的标准 spring.profiles.active 属性：
```
{"hints": [
    {
        "name": "spring.profiles.active",
        "providers": [
            {
                "name": "spring-profile-name"
            }
        ]
    }
]}
```
## B.3、使用注解处理器生成自己的元数据

通过使用 spring-boot-configuration-processor jar，你可以轻松地从使用 @ConfigurationProperties 注解的项生成自己的配置元数据文件。jar 包括一个 Java 注解处理器，它在编译项目时被调用。要使用处理器，请包括对 spring-boot-configuration-processor 的依赖。

对于 Maven，依赖项应该声明为可选的，如下面示例所示：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```
对于 Gradle 4.5 和更早版本，应该在 compileOnly 配置中声明依赖项，如下面示例所示：
```
dependencies {
    compileOnly "org.springframework.boot:spring-boot-configuration-processor"
}
```
对于 Gradle4.6 及更高版本，应该在 annotationProcessor 配置中声明依赖项，如下面示例所示：
```
dependencies {
    annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
}
```
如果你使用的是 additional-spring-configuration-metadata.json 文件，则应将 compileJava 任务配置为依赖于 processResources 任务，如下面示例所示：

    compileJava.dependsOn(processResources)

此依赖项确保在编译期间注解处理器运行时，附加元数据可用。

处理器获取用 @ConfigurationProperties 注解的类和方法。配置类中字段值的 Javadoc 用于填充 description 属性。

注释：你应该只使用带有 @ConfigurationProperties 字段 Javadoc 的简单文本，因为它们在被添加到 JSON 之前不会被处理。

属性是通过标准 getter 和 setter 来发现的，它们对集合类型进行了特殊处理（即使只有 getter 存在，也会检测到）。注解处理器还支持使用 @Data、@Getter 和 @Setter lombok 注解。

>注释：
>
>如果在项目中使用 AspectJ，则需要确保注解处理器仅运行一次。有几种方法可以做到这一点。使用 Maven，你可以显式配置 maven-apt-plugin，并将依赖项添加到注解处理器中。你还可以让 AspectJ 插件在 maven-compiler-plugin 配置中运行所有处理和禁用注解处理，如下所示：
>```
><plugin>
>    <groupId>org.apache.maven.plugins</groupId>
>    <artifactId>maven-compiler-plugin</artifactId>
>    <configuration>
>        <proc>none</proc>
>    </configuration>
></plugin>
>```
### B.3.1、嵌套属性

注解处理器自动将内部类视为嵌套属性。请考虑以下类别：
```
@ConfigurationProperties(prefix="server")
public class ServerProperties {

    private String name;

    private Host host;

    // ... getter and setters

    public static class Host {

        private String ip;

        private int port;

        // ... getter and setters

    }

}
```
上面的示例为 server.name、server.host.ip 和 server.host.port 属性生成元数据信息。你可以在字段上使用 @NestedConfigurationProperty 注解来指示应将常规（非内部）类视为嵌套类。

提示：这对集合和映射没有影响，因为这些类型是自动识别的，并且为它们中的每一个生成一个元数据属性。

### B.3.2、添加其他元数据

Spring Boot 的配置文件处理非常灵活，通常情况下，可能存在未绑定到 @ConfigurationProperties bean 的属性。可能还需要调整现有键的某些属性。为了支持这种情况并提供自定义的 “提示”，注解处理器会自动将 META-INF/additional-spring-configuration-metadata.json 中的项合并到主元数据文件中。·

如果引用已自动检测到的属性，则将覆盖描述、默认值和弃用信息（如果指定）。如果当前模块中未标识手动属性声明，则将其添加为新属性。

additional-spring-configuration-metadata.json 文件的格式与常规的 spring-configuration-metadata.json 完全相同。附加属性文件是可选的。如果没有任何其他属性，则不用添加该文件。
