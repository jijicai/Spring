# 64、安装 Spring Boot 应用程序

除了使用 java -jar 运行 Spring Boot 应用程序外，还可以为 Unix 系统生成完全可执行的应用程序。一个完全可执行的 jar 可以像任何其他可执行二进制文件一样执行，也可以[用 init.d 或 systemd 注册](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/deployment-install.html#deployment-service)。这使得在常见的生产环境中安装和管理 Spring Boot 应用程序变得非常容易。

警告：完全可执行的 jar 通过在文件前面嵌入额外的脚本来工作。目前，一些工具不接受这种格式，因此你可能并不总是能够使用这种技术。例如，jar -xf 可能会默默地无法提取已完全可执行的 jar 或 war。建议仅当你打算直接执行 jar 或 war 时，才使其完全可执行，而不是使用 java -jar 运行它或将其部署到 servlet 容器中。

要使用 Maven 创建 “完全可执行” 的 jar，请使用以下插件配置：
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <executable>true</executable>
    </configuration>
</plugin>
```
下面的示例展示了等效的 Gradle 配置：
```
bootJar {
    launchScript()
}
```
然后可以通过键入 ./my-application.jar（其中 my-application 是工件的名称）来运行应用程序。包含 jar 的目录用作应用程序的工作目录。

## 64.1、支持的操作系统

默认脚本支持大多数 Linux 发行版，并在 CentOS 和 Ubuntu 上进行了测试。其他平台，如 OS X 和 FreeBSD，需要使用自定义的 embeddedLaunchScript。

## 64.2、Unix/Linux 服务

通过使用 init.d 或 systemd，Spring Boot 应用程序可以很容易地作为 Unix/Linux 服务启动。

### 64.2.1、作为 init.d 服务安装（系统 V）

如果已配置 Spring Boot 的 Maven 或 Gradle 插件来生成完全可执行的 jar，并且不使用自定义的 embeddedLaunchScript，那么你的应用程序可以用作 init.d 服务。为此，将 jar 符号连接到 init.d，以支持标准的 start、stop、restart 和 status 命令。

脚本支持以下功能：

    （1）以拥有 jar 文件的用户身份启动服务
    （2）使用 /var/run/<appname>/<appname>.PID 跟踪应用程序的 PID
    （3）将控制台日志写入 /var/log/<appname>.log

假设你在 /var/myapp 中安装了 Spring Boot 应用程序，要将 Spring Boot 应用程序安装为 init.d 服务，请创建符号连接，如下所示：

    $ sudo ln -s /var/myapp/myapp.jar /etc/init.d/myapp

安装后，你可以以通常的方式启动和停止服务。例如，在基于 Debian 的系统上，你可以使用以下命令启动它：

    $ service myapp start

提示：如果应用程序无法启动，请检查写入 /var/log/<appname>.log 的日志文件中的错误。

你还可以使用标准操作系统工具将应用程序标记为自动启动。例如，在 Debian 上，你可以使用以下命令：

    $ update-rc.d myapp defaults <priority>

**保护 init.d 服务**

注释：以下是一组关于如何保护作为 init.d 服务运行的 Spring Boot 应用程序的指南。它并不是一个详尽的列表，列出了为了强化应用程序及其运行环境而应该做的所有事情。

当以 root 身份执行时，就像使用 root 启动 init.d 服务时一样，默认可执行脚本以拥有 jar 文件的用户身份运行应用程序。你永远不应该以 root 用户身份运行 Spring Boot 应用程序，因此你的应用程序的 jar 文件永远不应该由 root 用户拥有。相反，创建一个特定的用户来运行你的应用程序，并使用 chown 使其成为 jar 文件的所有者，如下面示例所示：

    $ chown bootapp:bootapp your-app.jar

在这种情况下，默认可执行脚本以 bootapp 用户的身份运行应用程序。

提示：为了减少应用程序的用户帐户被泄露的可能性，你应该考虑阻止它使用登录外壳。例如，你可以将帐户的 shell 设置为 /usr/sbin/nologin。

你还应该采取措施防止修改应用程序的 jar 文件。首先，配置其权限，使其无法写入，只能由其所有者读取或执行，如下面示例所示：

    $ chmod 500 your-app.jar

第二，如果你的应用程序或运行它的帐户受到损害，你还应该采取措施限制损害。如果攻击者确实获得了访问权限，他们可以使 jar 文件可写并更改其内容。防止这种情况的一种方法是使用 chattr 使其不可变，如下例所示：

    sudo chattr +i your-app.jar

这将阻止任何用户（包括 root 用户）修改 jar。

如果使用 root 来控制应用程序的服务，并且使用 .conf 文件自定义其启动，则 root 用户将读取并计算 .conf 文件。应相应地加以保护。使用 chmod 使文件只能由所有者读取，并使用 chown 使 root 成为所有者，如下例所示：

    $ chmod 400 your-app.conf
    $ sudo chown root:root your-app.conf

### 64.2.2、作为 systemd 服务安装

systemd 是 System V init 系统的后继者，现在被许多现代 Linux 发行版使用。尽管可以继续在 systemd 中使用 init.d 脚本，但也可以通过使用 systemd “服务” 脚本启动 Spring Boot 应用程序。

假设你在 /var/myapp 中安装了一个 Spring Boot 应用程序，要将 Spring Boot 应用程序作为 systemd 服务安装，请创建一个名为 myapp.service 的脚本并将其放置在 /etc/systemd/system 目录中。以下脚本提供了一个示例：
```
[Unit]
Description=myapp
After=syslog.target

[Service]
User=myapp
ExecStart=/var/myapp/myapp.jar
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```
重要的：请记住更改应用程序的 Description、User 和 ExecStart 字段。

注释：ExecStart 字段不声明脚本操作命令，这意味着默认情况下使用 run 命令。

注意，与作为 init.d 服务运行不同，运行应用程序的用户、PID 文件和控制台日志文件由 systemd 本身管理，因此必须使用“service”脚本中的适当字段进行配置。有关更多详细信息，请参阅[服务单元配置手册页](https://www.freedesktop.org/software/systemd/man/systemd.service.html)。

要将应用程序标记为在系统启动时自动启动，请使用以下命令：

    $ systemctl enable myapp.service

有关更多详细信息，请参阅 man systemctl。

### 64.2.3、自定义启动脚本

Maven 或 Gradle 插件编写的默认嵌入式启动脚本可以通过多种方式进行自定义。对于大多数人来说，使用默认脚本以及一些自定义通常就足够了。如果你发现你不能定制你需要的东西，使用 embeddedLaunchScript 选项来完全编写你自己的文件。

**编写启动脚本时自定义它**

当开始脚本被写入 jar 文件时，定制它的元素通常是有意义的。例如，init.d 脚本可以提供 “描述”。因为你知道前面的描述（并且它不需要改变），所以你最好在生成 jar 时提供它。

要自定义编写的元素，请使用 Spring Boot Maven 插件的 embeddedLaunchScriptProperties 选项或 [Spring Boot Gradle 插件的 launchScript 的 properties 属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/gradle-plugin/reference/html/#packaging-executable-configuring-launch-script)。

默认脚本支持以下属性替换：

|名称				|描述					|Gradle 默认			|Maven 默认|
|---|---|---|---|
|mode				|脚本模式。				|auto				|auto|
|initInfoProvides		|“INIT INFO” 的 Provides 部分。		|${task.baseName}		|${project.artifactId}|
|initInfoRequiredStart		|“INIT INFO” 的 Required-Start 部分。	|$remote_fs $syslog $network	|$remote_fs $syslog $network|
|initInfoRequiredStop		|“INIT INFO” 的 Required-Stop 部分。	|$remote_fs $syslog $network	|$remote_fs $syslog $network|
|initInfoDefaultStart		|“INIT INFO” 的 Default-Start 部分。	|2 3 4 5				|2 3 4 5|
|initInfoDefaultStop		|“INIT INFO” 的 Default-Stop 部分。	|0 1 6				|0 1 6|
|initInfoShortDescription	|“INIT INFO” 的 Short-Description 部分。	|${project.description} 单行版本（返回到 ${task.baseName}）		|${project.name}|
|initInfoDescription		|“INIT INFO” 的 Description 部分。	|${project.description}（返回到 ${task.baseName}）		|${project.description}（返回到 ${project.name}）|
|initInfoChkconfig		|“INIT INFO” 的 chkconfig 部分		|2345 99 01			|2345 99 01|
|confFolder			|CONF_FOLDER 的默认值			|包含 jar 的文件夹		|包含 jar 的文件夹|
|inlinedConfScript		|对应该内联在默认启动脚本中的文件脚本的引用。这可以用于在加载任何外部配置文件之前设置环境变量，例如 JAVA_OPTS。|||
|logFolder			|LOG_FOLDER 的默认值。仅对 init.d |服务有效 	| |
|logFilename			|LOG_FILENAME 的默认值。仅对 init.d |服务有效 	| |
|pidFolder			|PID_FOLDER 的默认值。仅对 init.d |服务有效 	| |
|pidFilename			|PID_FOLDER 中 PID 文件名称的默认值。仅对 init.d |服务有效 	| |
|useStartStopDaemon		|是否应该使用 start-stop-daemon 命令（当它可用时）来控制进程	|true	|true|
|stopWaitTime			|STOP_WAIT_TIME 的默认值（秒）。仅对 init.d 服务有效		|60	|60|

**在运行时自定义脚本**

对于编写 jar 后需要自定义的脚本项，可以使用环境变量或配置文件。

默认脚本支持以下环境属性：

|变量			|描述|
|---|---|
|MODE			|操作的“模式”。默认值取决于 jar 的构建方式，但通常是 auto 的（这意味着它试图通过检查它是否是名为 init.d 的目录中的符号连接来猜测它是否是 init 脚本）。你可以显式地将其设置为 service，以便 stop | start | status | restart 命令可以工作，或者希望在前台运行脚本时，则使用 run。|
|USE_START_STOP_DAEMON	|是否应使用 start-stop-daemon 命令来控制进程（如果可用）。默认为 true。|
|PID_FOLDER		|pid 文件夹的根名称（默认情况下为 /var/run）。|
|LOG_FOLDER		|放置日志文件的文件夹的名称（默认情况下为 /var/log）。|
|CONF_FOLDER		|从中读取 .conf 文件的文件夹的名称（默认情况下与 jar-file 同一个文件夹）。|
|LOG_FILENAME		|LOG_FOLDER 中日志文件的名称（默认为 <appname>.log）。|
|APP_NAME		|应用程序的名称。如果 jar 是从符号连接运行的，脚本将猜测应用程序名称。如果它不是符号连接，或者你想显式设置应用程序名称，这可能很有用。|
|RUN_ARGS		|要传递给程序（Spring Boot 应用程序）的参数。|
|JAVA_HOME		|java 可执行文件的位置在默认情况下是通过使用 PATH 来发现的，但是如果在 $JAVA_HOME/bin/java 处有一个可执行文件，你可以显式地设置它。|
|JAVA_OPTS		|启动 JVM 时传递给其的选项。|
|JARFILE			|jar 文件的显式位置，以防脚本用于启动一个实际上没有嵌入的 jar。|
|DEBUG			|如果不为空，则在 shell 进程上设置 -x 标志，以便于查看脚本中的逻辑。|
|STOP_WAIT_TIME		|在强制关闭之前停止应用程序时等待的时间（以秒为单位，默认为 60）。|

注释：PID_FOLDER、LOG_FOLDER 和 LOG_FILENAME 变量仅对 init.d 服务有效。对于 systemd，使用 “服务” 脚本进行等效的自定义。有关详细信息，请参阅[服务单元配置手册页](https://www.freedesktop.org/software/systemd/man/systemd.service.html)。

除了 JARFILE 和 APP_NAME 之外，可以使用 .conf 文件配置上一节中列出的设置。该文件应该在 jar 文件旁边，并且具有相同的名称，但后缀是 .conf 而不是 .jar。例如，名为 /var/myapp/myapp.jar 的 jar 使用名为 /var/myapp/myapp.conf 的配置文件，如下例所示：
```
myapp.conf

JAVA_OPTS=-Xmx1024M
LOG_FOLDER=/custom/log/folder
```
提示：如果你不喜欢将配置文件放在 jar 文件旁边，可以设置 CONF_FOLDER 环境变量来自定义配置文件的位置。

要了解如何适当地保护此文件，请参阅[保护 init.d 服务的指南](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/deployment-install.html#deployment-initd-service-securing)。

## 64.3、Microsoft Windows 服务

使用 [winsw](https://github.com/kohsuke/winsw) 可以将 Spring Boot 应用程序作为 Windows 服务启动。

[单独维护的示例](https://github.com/snicoll-scratches/spring-boot-daemon)，逐步地描述了如何为 Spring Boot 应用程序创建 Windows 服务。
