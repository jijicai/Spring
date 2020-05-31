# 73、Spring Boot AntLib 模块

Spring Boot AntLib 模块为 Apache Ant 提供基本的 Spring Boot 支持。你可以使用该模块创建可执行 jar。要使用该模块，你需要在 build.xml 中声明一个额外的 spring-boot 命名空间，如下面示例所示：
```
<project xmlns:ivy="antlib:org.apache.ivy.ant"
    xmlns:spring-boot="antlib:org.springframework.boot.ant"
    name="myapp" default="build">
    ...
</project>
```
你需要记住使用 -lib 选项启动 Ant，如以下示例所示：

    $ ant -lib <folder containing spring-boot-antlib-2.1.6.RELEASE.jar>

提示：“使用 Spring Boot” 部分包括一个更完整的[使用 Apache Ant 和 spring-boot-antlib 的示例](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-ant)。

## 73.1、Spring Boot Ant 任务

一旦声明了 spring-boot-antlib 名称空间，就可以执行以下附加任务：

（1）[第 73.1.1 节：spring-boot:exejar](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/build-tool-plugins-antlib.html#spring-boot-ant-exejar)

（2）[第 73.2 节：spring-boot:findmainclass](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/build-tool-plugins-antlib.html#spring-boot-ant-findmainclass)

### 73.1.1、spring-boot:exejar

你可以使用 exejar 任务创建一个 Spring Boot 可执行 jar。任务支持以下属性：

|属性		|描述				|必须|
|---|---|---|
|destfile	|要创建的目标 jar 文件		|Yes|
|classes		|Java 类文件的根目录		|Yes|
|start-class	|要运行的主应用程序类		|No（默认值是找到的第一个声明主方法的类）|

以下嵌套元素可与任务一起使用：

|元素		|描述|
|---|---|
|resources	|描述一组资源的一个或多个资源集合，这些资源应添加到创建的 jar 文件的内容中。|
|lib		|一个或多个资源集合，应添加到构成应用程序的运行时依赖类路径的 jar 库集中。|

### 73.1.2、例子

本节展示了 Ant 任务的两个示例。

**指定 start-class**
```
<spring-boot:exejar destfile="target/my-application.jar"
        classes="target/classes" start-class="com.example.MyApplication">
    <resources>
        <fileset dir="src/main/resources" />
    </resources>
    <lib>
        <fileset dir="lib" />
    </lib>
</spring-boot:exejar>
```
**检测 start-class**
```
<exejar destfile="target/my-application.jar" classes="target/classes">
    <lib>
        <fileset dir="lib" />
    </lib>
</exejar>
```
## 73.2、spring-boot:findmainclass

exejar 在内部使用 findmainclass 任务来定位声明 main 的类。如有必要，你也可以在构建中直接使用此任务。支持以下属性：

|属性		|描述					|必要|
|---|---|---|
|classesroot	|Java 类文件的根目录			|Yes（除非指定了 mainclass）|
|mainclass	|可用于短路（short-circuit）主类搜索	|No|
|property	|应使用结果设置的 Ant 属性		|No（如果未指定，将记录结果）|

### 73.2.1、例子

本节包含使用 findmainclass 的三个示例。

**查找和记录**

    <findmainclass classesroot="target/classes" />

**查找和设置**

    <findmainclass classesroot="target/classes" property="main-class" />

**覆盖与设置**

    <findmainclass mainclass="com.example.MainClass" property="main-class" />
