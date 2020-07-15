# A、常见应用程序属性

可以在 application.properties 文件中、application.yml 文件中或作为命令行开关指定各种属性。本附录提供了一个常见的 Spring Boot 属性列表以及对使用它们的底层类的引用。

提示：Spring Boot 提供了各种具有高级值格式的转换机制，请务必回顾[属性转换部分](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-conversion)。

注释：属性贡献可能来自类路径上的其他 jar 文件，所以你不应该认为这是一个详尽的列表。此外，你可以定义自己的属性。

警告：此示例文件仅作为指南。不要将整个内容复制并粘贴到你的应用程序中。相反，只选择你需要的属性。
    
    # ===================================================================
    # 常见 SPRING BOOT 属性
    #
    # 不要将其全部复制到你自己的应用程序中。    ^^^
    # 
    # ===================================================================
    
    
    # ----------------------------------------
    # 核心属性
    # ----------------------------------------
    debug=false # 启用调试日志。
    trace=false # 启用跟踪日志。
    
    # 日志记录
    logging.config= # 日志配置文件的位置。例如，Logback 的 classpath:logback.xml。
    logging.exception-conversion-word=%wEx # 日志记录异常时使用的转换字。
    logging.file= # 日志文件名（例如：myapp.Log）。名称可以是确切的位置或相对于当前目录。
    logging.file.max-history=0 # 要保留的存档日志文件的最大值。仅支持默认 logback 设置。
    logging.file.max-size=10MB # 最大日志文件大小。仅支持默认 logback 设置。
    logging.group.*= # 日志组以同时快速更改多个日志器。例如，logging.level.db=org.hibernate,org.springframework.jdbc。
    logging.level.*= # 日志级别严重程度映射。例如，logging.level.org.springframework=DEBUG。
    logging.path= # 日志文件的位置。例如，/var/log。
    logging.pattern.console= # 输出到控制台的 Appender 模式。仅支持默认 Logback 设置。
    logging.pattern.dateformat=yyyy-MM-dd HH:mm:ss.SSS # 日志日期格式的 Appender 模式。仅支持默认的 Logback 设置。
    logging.pattern.file= # 输出到文件的 Appender 模式。仅支持默认 Logback 设置。
    logging.pattern.level=%5p # 日志级别的 Appender 模式。仅支持默认 Logback 设置。
    logging.register-shutdown-hook=false # 初始化日志系统时，为其注册一个关机钩子。
    
    # AOP
    spring.aop.auto=true # 添加 @EnableAspectJAutoProxy.
    spring.aop.proxy-target-class=true # 是否要创建基于子类（CGLIB）的代理（true），而不是标准的基于 Java 接口的代理（false）。
    
    # IDENTITY (ContextIdApplicationContextInitializer)
    spring.application.name= # 应用程序名称。
    
    # ADMIN (SpringApplicationAdminJmxAutoConfiguration)
    spring.application.admin.enabled=false # 是否为应用程序启用管理功能。
    spring.application.admin.jmx-name=org.springframework.boot:type=Admin,name=SpringApplication # JMX 应用程序管理 MBean 的名称。
    
    # AUTO-CONFIGURATION
    spring.autoconfigure.exclude= # 要排除的自动配置类。
    
    # BANNER
    spring.banner.charset=UTF-8 # banner 文件编码。
    spring.banner.location=classpath:banner.txt # banner 文本资源位置。
    spring.banner.image.location=classpath:banner.gif # banner 图像文件位置 (也可以使用 jpg 或 png)。
    spring.banner.image.width=76 # 以字符表示的 banner 图像宽度。
    spring.banner.image.height= # 以字符表示的 banner 图像高度（默认值基于图像高度）。
    spring.banner.image.margin=2 # 以字符表示的左侧图像边距。
    spring.banner.image.invert=false # 是否应为深色终端主题反转图像。
    
    # SPRING CORE
    spring.beaninfo.ignore=true # 是否跳过 BeanInfo 类的搜索。
    
    # SPRING CACHE (CacheProperties)
    spring.cache.cache-names= # 要创建的缓存名称的逗号分隔列表（如果底层缓存管理器支持）。
    spring.cache.caffeine.spec= # 用于创建缓存的规范。有关规范格式的详细信息，请参见 CaffeineSpec。
    spring.cache.couchbase.expiration= # 条目到期。默认情况下，条目永远不会过期。请注意，此值最终会转换为秒。
    spring.cache.ehcache.config= # 用于初始化 EhCache 的配置文件的位置。
    spring.cache.infinispan.config= # 用于初始化 Infinispan 的配置文件的位置。
    spring.cache.jcache.config= # 用于初始化缓存管理器的配置文件的位置。
    spring.cache.jcache.provider= # 用于检索符合 JSR-107 的缓存管理器的 CachingProvider 实现的完全限定名称。仅当类路径上有多个 JSR-107 实现时才需要。
    spring.cache.redis.cache-null-values=true # 允许缓存 null 值。
    spring.cache.redis.key-prefix= # 键前缀。
    spring.cache.redis.time-to-live= # 条目过期。默认情况下，条目永远不会过期。
    spring.cache.redis.use-key-prefix=true # 写入 Redis 时是否使用 key 前缀。
    spring.cache.type= # 缓存类型。默认情况下，根据环境自动检测。
    
    # SPRING CONFIG - 仅使用环境属性 (ConfigFileApplicationListener)
    spring.config.additional-location= # 默认值之外使用的配置文件的位置。
    spring.config.location= # 替换默认的配置文件位置。
    spring.config.name=application # 配置文件名。
    
    # HAZELCAST (HazelcastProperties)
    spring.hazelcast.config= # 用于初始化 Hazelcast 的配置文件的位置。
    
    # PROJECT INFORMATION (ProjectInfoProperties)
    spring.info.build.encoding=UTF-8 # 文件编码。
    spring.info.build.location=classpath:META-INF/build-info.properties # 生成的 build-info.properties 文件的位置。
    spring.info.git.encoding=UTF-8 # 文件编码。
    spring.info.git.location=classpath:git.properties # 生成的 git.properties 文件的位置。
    
    # JMX
    spring.jmx.default-domain= # JMX 域名。
    spring.jmx.enabled=true # 向 JMX 域公开管理 bean。
    spring.jmx.server=mbeanServer # MBeanServer bean 名称。
    spring.jmx.unique-names=false # 是否应确保运行时对象名的唯一性。
    
    # Email (MailProperties)
    spring.mail.default-encoding=UTF-8 # 默认的 MimeMessage 编码。
    spring.mail.host= # SMTP 服务器主机。例如，“smtp.example.com”。
    spring.mail.jndi-name= # 会话 JNDI 名称。设置后，优先于其他会话设置。
    spring.mail.password= # SMTP 服务器的登录密码。
    spring.mail.port= # SMTP 服务器端口。
    spring.mail.properties.*= # 其他的 JavaMail 会话属性。
    spring.mail.protocol=smtp # SMTP 服务器使用的协议。
    spring.mail.test-connection=false # 是否测试邮件服务器在启动时是否可用。
    spring.mail.username= # SMTP 服务器的登录用户。
    
    # APPLICATION SETTINGS (SpringApplication)
    spring.main.allow-bean-definition-overriding=false # 通过注册与现有定义同名的定义，是否允许 bean 定义重写。
    spring.main.banner-mode=console # 用于在应用程序运行时显示横幅的模式。
    spring.main.sources= # 要包含在 ApplicationContext 中的源（类名、包名或 XML资源位置）。
    spring.main.web-application-type= # 显式地请求特定类型的 web 应用程序的标志（flag）。如果未设置，则基于类路径自动检测。
    
    # FILE ENCODING (FileEncodingApplicationListener)
    spring.mandatory-file-encoding= # 应用程序必须使用的字符编码。
    
    # INTERNATIONALIZATION (MessageSourceProperties)
    spring.messages.always-use-message-format=false # 是否始终应用 MessageFormat 规则，甚至解析没有参数的消息。
    spring.messages.basename=messages # 逗号分隔的基名列表（本质上是一个完全限定的类路径位置），每个基名都遵循 ResourceBundle 约定，并对基于斜杠的位置提供宽松的支持。
    spring.messages.cache-duration= # 加载的资源包文件缓存持续时间。未设置时，将永远缓存捆绑包。如果未指定持续时间后缀，则将使用秒数。
    spring.messages.encoding=UTF-8 # 消息包编码。
    spring.messages.fallback-to-system-locale=true # 如果找不到特定区域设置的文件，是否返回到系统区域设置。
    spring.messages.use-code-as-default-message=false # 是否使用消息代码作为默认消息，而不是抛出“NoSuchMessageException”。仅建议在开发期间使用。
    
    # OUTPUT
    spring.output.ansi.enabled=detect # 配置 ANSI 输出。
    
    # PID FILE (ApplicationPidFileWriter)
    spring.pid.fail-on-write-error= # 如果使用 ApplicationPidFileWriter，但它不能写入 PID 文件，则会失败。
    spring.pid.file= # 要写入的 PID 文件的位置 (如果使用 ApplicationPidFileWriter)。
    
    # PROFILES
    spring.profiles.active= # 以逗号分隔的活动 profiles 列表。可由命令行开关重写。
    spring.profiles.include= # 无条件激活指定的以逗号分隔的 profiles 列表（如果使用 YAML，则为 profiles 列表）。
    
    # QUARTZ SCHEDULER (QuartzProperties)
    spring.quartz.auto-startup=true # 初始化后是否自动启动调度器。
    spring.quartz.jdbc.comment-prefix=-- # SQL 初始化脚本中单行注释的前缀。
    spring.quartz.jdbc.initialize-schema=embedded # 数据库 schema 初始化模式。
    spring.quartz.jdbc.schema=classpath:org/quartz/impl/jdbcjobstore/tables_@@platform@@.sql # 用于初始化数据库 schema 的 SQL 文件路径。
    spring.quartz.job-store-type=memory # Quartz 作业存储类型。
    spring.quartz.overwrite-existing-jobs=false # 配置的作业是否应覆盖现有作业定义。
    spring.quartz.properties.*= # 其他 Quartz 调度器属性。
    spring.quartz.scheduler-name=quartzScheduler # 调度器的名称。
    spring.quartz.startup-delay=0s # 初始化完成后启动调度器的延迟。
    spring.quartz.wait-for-jobs-to-complete-on-shutdown=false # 是否等待正在运行的作业在关机时完成。
    
    # REACTOR (ReactorCoreProperties)
    spring.reactor.stacktrace-mode.enabled=false # Reactor 是否应在运行时收集 stacktrace 信息。
    
    # SENDGRID (SendGridAutoConfiguration)
    spring.sendgrid.api-key= # SendGrid API 键。
    spring.sendgrid.proxy.host= # SendGrid 代理主机。
    spring.sendgrid.proxy.port= # SendGrid 代理端口。
    
    # TASK EXECUTION (TaskExecutionProperties)
    spring.task.execution.pool.allow-core-thread-timeout=true # 是否允许核心线程超时。这允许池的动态增长和收缩。
    spring.task.execution.pool.core-size=8 # 核心线程数。
    spring.task.execution.pool.keep-alive=60s # 线程在终止前可能保持空闲的时间限制。
    spring.task.execution.pool.max-size= # 允许的最大线程数。如果任务正在填满队列，则池可以扩展到该大小以适应负载。如果队列是无限的，则忽略。
    spring.task.execution.pool.queue-capacity= # 队列容量。无限容量不会增加池，因此会忽略 max-size 属性。
    spring.task.execution.thread-name-prefix=task- # 用于新创建线程的名称的前缀。
    
    # TASK SCHEDULING (TaskSchedulingProperties)
    spring.task.scheduling.pool.size=1 # 允许的最大线程数。
    spring.task.scheduling.thread-name-prefix=scheduling- # 用于新创建线程的名称的前缀。
    
    # ----------------------------------------
    # WEB PROPERTIES
    # ----------------------------------------
    
    # EMBEDDED SERVER CONFIGURATION (ServerProperties)
    server.address= # 服务器应绑定到的网络地址。
    server.compression.enabled=false # 是否启用响应压缩。
    server.compression.excluded-user-agents= # 不应压缩响应的用户代理的逗号分隔列表。
    server.compression.mime-types=text/html,text/xml,text/plain,text/css,text/javascript,application/javascript,application/json,application/xml # 应压缩的 MIME 类型的逗号分隔列表。
    server.compression.min-response-size=2KB # 执行压缩所需的最小“Content-Length”值。
    server.connection-timeout= # 连接器在关闭连接之前等待另一个 HTTP 请求的时间。未设置时，将使用连接器的容器特定默认值。使用 -1 值表示没有（即无限）超时。
    server.error.include-exception=false # 包含“exception”属性。
    server.error.include-stacktrace=never # 何时包含 “stacktrace” 属性。
    server.error.path=/error # error 控制器的路径。
    server.error.whitelabel.enabled=true # 是否在出现服务器错误时启用浏览器中显示的默认错误页面。
    server.http2.enabled=false # 如果当前环境支持，是否启用 HTTP/2 支持。
    server.jetty.acceptors=-1 # 要使用的 acceptor 线程数。当值为-1（默认值）时，acceptor 的数量是从操作环境派生的。
    server.jetty.accesslog.append=false # 追加到日志。
    server.jetty.accesslog.date-format=dd/MMM/yyyy:HH:mm:ss Z # 请求日志的时间戳格式。
    server.jetty.accesslog.enabled=false # 启用访问日志。
    server.jetty.accesslog.extended-format=false # 启用扩展的 NCSA 格式。
    server.jetty.accesslog.file-date-format= # 要放入日志文件名中的日期格式。
    server.jetty.accesslog.filename= # 日志文件名。如果未指定，则日志重定向到“System.err”。
    server.jetty.accesslog.locale= # 请求日志的区域设置。
    server.jetty.accesslog.log-cookies=false # 启用请求 cookie 的日志记录。
    server.jetty.accesslog.log-latency=false # 启用请求处理时间的日志记录。
    server.jetty.accesslog.log-server=false # 启用请求主机名的日志记录。
    server.jetty.accesslog.retention-period=31 # 删除旋转日志文件之前的天数。
    server.jetty.accesslog.time-zone=GMT # 请求日志的时区。
    server.jetty.max-http-post-size=200000B # HTTP post 或 put 内容的最大大小。
    server.jetty.selectors=-1 # 要使用的选择器线程数。当值为-1（默认值）时，选择器的数量来自操作环境。
    server.max-http-header-size=8KB # HTTP 消息标头的最大大小。
    server.port=8080 # 服务器 HTTP 端口。
    server.server-header= # 用于服务器响应头的值 (如果为空，则不发送头)。
    server.use-forward-headers= # 是否应将 X-Forwarded-* 头应用于 HttpRequest。
    server.servlet.context-parameters.*= # Servlet 上下文初始化参数。
    server.servlet.context-path= # 应用程序的上下文路径。
    server.servlet.application-display-name=application # 应用程序的显示名称。
    server.servlet.jsp.class-name=org.apache.jasper.servlet.JspServlet # 用于 JSPs 的 servlet 的类名。
    server.servlet.jsp.init-parameters.*= # 用于配置 JSP servlet 的初始化参数。
    server.servlet.jsp.registered=true # 是否注册 JSP servlet。
    server.servlet.session.cookie.comment= # 会话 cookie 的注释。
    server.servlet.session.cookie.domain= # 会话 cookie 的域。
    server.servlet.session.cookie.http-only= # 是否对会话 cookie 使用 “HttpOnly” cookie。
    server.servlet.session.cookie.max-age= # 会话 cookie 的最大期限。如果未指定持续时间后缀，将使用秒。
    server.servlet.session.cookie.name= # 会话 cookie 名称。
    server.servlet.session.cookie.path= # 会话 cookie 的路径。
    server.servlet.session.cookie.secure= # 是否始终将会话 cookie 标记为安全。
    server.servlet.session.persistent=false # 是否在重新启动之间保留会话数据。
    server.servlet.session.store-dir= # 用于存储会话数据的目录。
    server.servlet.session.timeout=30m # 会话超时。如果未指定持续时间后缀，将使用秒。
    server.servlet.session.tracking-modes= # 会话跟踪模式。
    server.ssl.ciphers= # 支持的 SSL 密码。
    server.ssl.client-auth= # 客户端身份验证模式。
    server.ssl.enabled=true # 是否启用 SSL 支持。
    server.ssl.enabled-protocols= # 启用的 SSL 协议。
    server.ssl.key-alias= # 标识密钥存储中密钥的别名。
    server.ssl.key-password= # 用于访问密钥存储区中的密钥的密码。
    server.ssl.key-store= # 保存 SSL 证书 (通常是 jks 文件) 的密钥存储的路径。
    server.ssl.key-store-password= # 用于访问密钥存储的密码。
    server.ssl.key-store-provider= # 密钥存储的提供者。
    server.ssl.key-store-type= # 密钥存储的类型。
    server.ssl.protocol=TLS # 要使用的 SSL 协议。
    server.ssl.trust-store= # 持有 SSL 证书的信任存储。
    server.ssl.trust-store-password= # 用于访问信任存储的密码。
    server.ssl.trust-store-provider= # 信任存储的提供者。
    server.ssl.trust-store-type= # 信任存储的类型。
    server.tomcat.accept-count=100 # 当所有可能的请求处理线程都在使用时，传入连接请求的最大队列长度。
    server.tomcat.accesslog.buffered=true # 是否缓冲输出，以便仅定期刷新。
    server.tomcat.accesslog.directory=logs # 在其中创建日志文件的目录。可以是绝对的，也可以是相对于 Tomcat 基本目录的。
    server.tomcat.accesslog.enabled=false # 启用访问日志。
    server.tomcat.accesslog.file-date-format=.yyyy-MM-dd # 要放入日志文件名中的日期格式。
    server.tomcat.accesslog.pattern=common # 访问日志的通用格式模式。
    server.tomcat.accesslog.prefix=access_log # 日志文件名前缀。
    server.tomcat.accesslog.rename-on-rotate=false # 是否推迟在文件名中包含日期戳，直到旋转时间。
    server.tomcat.accesslog.request-attributes-enabled=false # 设置用于请求的IP地址、主机名、协议和端口的请求属性。
    server.tomcat.accesslog.rotate=true # 是否启用访问日志轮换。
    server.tomcat.accesslog.suffix=.log # 日志文件名后缀。
    server.tomcat.additional-tld-skip-patterns= # 逗号分隔的附加模式列表，这些模式与要忽略的 jar 相匹配，用于 TLD 扫描。
    server.tomcat.background-processor-delay=10s # backgroundProcess 方法调用之间的延迟。如果未指定持续时间后缀，则将使用秒数。
    server.tomcat.basedir= # Tomcat 基本目录。如果未指定，则使用临时目录。
    server.tomcat.internal-proxies=10\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}|\\
        192\\.168\\.\\d{1,3}\\.\\d{1,3}|\\
        169\\.254\\.\\d{1,3}\\.\\d{1,3}|\\
        127\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}|\\
        172\\.1[6-9]{1}\\.\\d{1,3}\\.\\d{1,3}|\\
        172\\.2[0-9]{1}\\.\\d{1,3}\\.\\d{1,3}|\\
        172\\.3[0-1]{1}\\.\\d{1,3}\\.\\d{1,3}\\
        0:0:0:0:0:0:0:1\\
        ::1 # 与要信任的代理匹配的正则表达式。
    server.tomcat.max-connections=10000 # 服务器在任何给定时间接受和处理的最大连接数。
    server.tomcat.max-http-post-size=2MB # HTTP post 内容的最大大小。
    server.tomcat.max-swallow-size=2MB # 要吞咽的请求主体的最大数量。
    server.tomcat.max-threads=200 # 最大工作线程量。
    server.tomcat.min-spare-threads=10 # 最小工作线程量。
    server.tomcat.port-header=X-Forwarded-Port # 用于覆盖原始端口值的 HTTP 头的名称。
    server.tomcat.protocol-header= # 保存传入协议的头，通常命名为“X-Forwarded-Proto”。
    server.tomcat.protocol-header-https-value=https # 表示传入请求是否使用 SSL 的协议头的值。
    server.tomcat.redirect-context-root=true # 是否应该通过向路径追加 / 来重定向对上下文根的请求。
    server.tomcat.remote-ip-header= # 从中提取远程 ip 的 HTTP 头的名称。例如，“X-FORWARDED-For”。
    server.tomcat.resource.allow-caching=true # 此 web 应用程序是否允许静态资源缓存。
    server.tomcat.resource.cache-ttl= # 静态资源缓存的生存时间。
    server.tomcat.uri-encoding=UTF-8 # 用于解码 URI 的字符编码。
    server.tomcat.use-relative-redirects= # 调用 sendRedirect 所生成的 HTTP 1.1 和更高版本的位置头信息是使用相对重定向还是绝对重定向。
    server.undertow.accesslog.dir= # Undertow 访问日志目录。
    server.undertow.accesslog.enabled=false # 是否启用访问日志。
    server.undertow.accesslog.pattern=common # 访问日志的通用格式模式。
    server.undertow.accesslog.prefix=access_log. # 日志文件名前缀。
    server.undertow.accesslog.rotate=true # 是否启用访问日志旋转。
    server.undertow.accesslog.suffix=log # 日志文件名后缀。
    server.undertow.buffer-size= # 每个缓冲区的大小。
    server.undertow.direct-buffers= # 是否在 Java 堆外分配缓冲区。默认值来自 JVM 可用的最大内存量。
    server.undertow.eager-filter-init=true # servlet 过滤器是否应该启动时初始化。
    server.undertow.io-threads= # 要为工作线程创建的 I/O 线程数。默认值来自可用处理器的数量。
    server.undertow.max-http-post-size=-1B # HTTP post 内容的最大大小。默认值为 -1 时，大小不受限制。
    server.undertow.worker-threads= # 工作线程数。默认值是 I/O 线程数的 8 倍。
    
    # FREEMARKER (FreeMarkerProperties)
    spring.freemarker.allow-request-override=false # 是否允许 HttpServletRequest 属性覆盖（隐藏）控制器生成的同名模型属性。
    spring.freemarker.allow-session-override=false # 是否允许 HttpSession 属性重写（隐藏）控制器生成的同名模型属性。
    spring.freemarker.cache=false # 是否启用模板缓存。
    spring.freemarker.charset=UTF-8 # 模板编码。
    spring.freemarker.check-template-location=true # 是否检查模板位置是否存在。
    spring.freemarker.content-type=text/html # Content-Type 值.
    spring.freemarker.enabled=true # 是否为此技术启用 MVC 视图解析。
    spring.freemarker.expose-request-attributes=false # 是否应在与模板合并之前将所有请求属性添加到模型中。
    spring.freemarker.expose-session-attributes=false # 是否应在与模板合并之前将所有 HttpSession 属性添加到模型中。
    spring.freemarker.expose-spring-macro-helpers=true # 是否以“springMacroRequestContext”的名称公开 Spring 宏库使用的 RequestContext。
    spring.freemarker.prefer-file-system-access=true # 是否更喜欢文件系统访问来加载模板。文件系统访问允许对模板更改进行热检测。
    spring.freemarker.prefix= # 在构建 URL 时被预先设置为视图名称的前缀。
    spring.freemarker.request-context-attribute= # 所有视图的 RequestContext 属性的名称。
    spring.freemarker.settings.*= # 传递给 Freemarker 配置的众所周知的 Freemarker 键。
    spring.freemarker.suffix=.ftl # 在构建 URL 时被预先设置为视图名称的后缀。
    spring.freemarker.template-loader-path=classpath:/templates/ # 逗号分隔的模板路径列表。
    spring.freemarker.view-names= # 可以解析的视图名称的白名单。
    
    # GROOVY TEMPLATES (GroovyTemplateProperties)
    spring.groovy.template.allow-request-override=false # 是否允许 HttpServletRequest 属性覆盖（隐藏）控制器生成的同名模型属性。
    spring.groovy.template.allow-session-override=false # 是否允许 HttpSession 属性覆盖（隐藏）控制器生成的同名模型属性。
    spring.groovy.template.cache=false # 是否启用模板缓存。
    spring.groovy.template.charset=UTF-8 # 模板编码。
    spring.groovy.template.check-template-location=true # 是否检查模板位置是否存在。
    spring.groovy.template.configuration.*= # 请参阅 GroovyMarkupConfigurer（https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/view/groovy/GroovyMarkupConfigurer.html ）
    spring.groovy.template.content-type=text/html # Content-Type 值.
    spring.groovy.template.enabled=true # 是否为此技术启用 MVC 视图解析。
    spring.groovy.template.expose-request-attributes=false # 是否应在与模板合并之前将所有请求属性添加到模型中。
    spring.groovy.template.expose-session-attributes=false # 是否应在与模板合并之前将所有 HttpSession 属性添加到模型中。
    spring.groovy.template.expose-spring-macro-helpers=true # 是否以“springMacroRequestContext”的名称公开 RequestContext 供 Spring 的宏库使用。
    spring.groovy.template.prefix= # 在构建 URL 时被预先设置为视图名称的前缀。
    spring.groovy.template.request-context-attribute= # 所有视图的 RequestContext 属性的名称。
    spring.groovy.template.resource-loader-path=classpath:/templates/ # 模板路径。
    spring.groovy.template.suffix=.tpl # 在构建 URL 时被预先设置为视图名称的后缀。
    spring.groovy.template.view-names= # 可以解析的视图名称的白名单。
    
    # SPRING HATEOAS (HateoasProperties)
    spring.hateoas.use-hal-as-default-json-media-type=true # 是否应将 application/hal+json 响应发送到接受 application/json 的请求。
    
    # HTTP (HttpProperties)
    spring.http.converters.preferred-json-mapper= # 用于 HTTP 消息转换的首选 JSON 映射器。默认情况下，根据环境自动检测。
    spring.http.encoding.charset=UTF-8 # HTTP 请求和响应的字符集。如果未显式设置，则添加到“Content-Type”头。
    spring.http.encoding.enabled=true # 是否启用http编码支持。
    spring.http.encoding.force= # 是否在 HTTP 请求和响应上强制将编码设置为已配置的字符集。
    spring.http.encoding.force-request= # 是否在 HTTP 请求上强制将编码设置为已配置的字符集。未指定 “force” 时，默认为 true。
    spring.http.encoding.force-response= # 是否在 HTTP 响应上强制将编码设置为已配置的字符集。
    spring.http.encoding.mapping= # 要在其中编码映射的区域设置。
    spring.http.log-request-details=false # 是否允许在调试和跟踪级别记录（可能敏感的）请求详细信息。
    
    # MULTIPART (MultipartProperties)
    spring.servlet.multipart.enabled=true # 是否启用支持 multipart 上传。
    spring.servlet.multipart.file-size-threshold=0B # 将文件写入磁盘的阈值。
    spring.servlet.multipart.location= # 上传文件的中间位置。
    spring.servlet.multipart.max-file-size=1MB # 最大文件大小。
    spring.servlet.multipart.max-request-size=10MB # 最大请求大小。
    spring.servlet.multipart.resolve-lazily=false # 是否在文件或参数访问时延迟解析 multipart 请求。
    
    # JACKSON (JacksonProperties)
    spring.jackson.date-format= # 日期格式字符串或完全限定的日期格式类名。例如，“yyyy-MM-dd HH:MM:ss”。
    spring.jackson.default-property-inclusion= # 控制序列化期间属性的包含。使用 Jackson 的 JsonInclude.Include 枚举中的一个值配置。
    spring.jackson.deserialization.*= # 影响 Java 对象反序列化方式的 Jackson 开/关功能。
    spring.jackson.generator.*= # 生成器的 Jackson 开/关功能。
    spring.jackson.joda-date-time-format= # Joda 日期时间格式字符串。如果未配置，则使用“date-format”作为回退（如果使用格式字符串配置）。
    spring.jackson.locale= # 用于格式化的区域设置。
    spring.jackson.mapper.*= # Jackson 通用开/关功能。
    spring.jackson.parser.*= # 用于解析器的 Jackson 开/关功能。
    spring.jackson.property-naming-strategy= # Jackson 的 PropertyNamingStrategy 上的常数之一。也可以是 PropertyNamingStrategy 子类的完全限定类名。
    spring.jackson.serialization.*= # 影响 Java 对象序列化方式的 Jackson 开/关功能。
    spring.jackson.time-zone= # 格式化日期时使用的时区。例如，“America/Los_Angeles”或“GMT+10”。
    spring.jackson.visibility.*= # 可用于限制自动检测哪些方法（和字段）的 Jackson 可见性阈值。
    
    # GSON (GsonProperties)
    spring.gson.date-format= # 序列化日期对象时使用的格式。
    spring.gson.disable-html-escaping= # 是否禁用对诸如“<”、“>”等 HTML 字符的转义。
    spring.gson.disable-inner-class-serialization= # 在序列化期间是否排除内部类。
    spring.gson.enable-complex-map-key-serialization= # 是否启用复杂映射键（即非基本数据类型）的序列化。
    spring.gson.exclude-fields-without-expose-annotation= # 是否从序列化或反序列化的考虑中排除所有没有“Expose”注解的字段。
    spring.gson.field-naming-policy= # 在序列化和反序列化期间应应用于对象字段的命名策略。
    spring.gson.generate-non-executable-json= # 是否通过在输出前面加上一些特殊文本来生成不可执行 JSON。
    spring.gson.lenient= # 是否对解析不符合 RFC 4627 的 JSON 宽容。
    spring.gson.long-serialization-policy= # Long 和 long 类型的序列化策略。
    spring.gson.pretty-printing= # 是否输出适合漂亮打印页面的序列化 JSON。
    spring.gson.serialize-nulls= # 是否序列化 null 字段。
    
    # JERSEY (JerseyProperties)
    spring.jersey.application-path= # 用作应用程序基本 URI 的路径。如果指定，则覆盖“@ApplicationPath”的值。
    spring.jersey.filter.order=0 # Jersey 过滤器链顺序。
    spring.jersey.init.*= # 要通过 servlet 或过滤器传递到 Jersey 的初始化参数。
    spring.jersey.servlet.load-on-startup=-1 # Jersey servlet 的启动时加载优先级。
    spring.jersey.type=servlet # Jersey 集成类型。
    
    # SPRING LDAP (LdapProperties)
    spring.ldap.anonymous-read-only=false # 只读操作是否应该使用匿名环境。
    spring.ldap.base= # 所有操作都应从其发起的基后缀。
    spring.ldap.base-environment.*= # LDAP 规范设置。
    spring.ldap.password= # 服务器的登录密码。
    spring.ldap.urls= # 服务器的 LDAP URL。
    spring.ldap.username= # 服务器的登录用户名。
    
    # EMBEDDED LDAP (EmbeddedLdapProperties)
    spring.ldap.embedded.base-dn= # 基本 DNs 列表。
    spring.ldap.embedded.credential.username= # 嵌入式 LDAP 用户名。
    spring.ldap.embedded.credential.password= # 嵌入式 LDAP 密码。
    spring.ldap.embedded.ldif=classpath:schema.ldif # schema（ldif）脚本资源引用。
    spring.ldap.embedded.port=0 # 嵌入式 LDAP 端口。
    spring.ldap.embedded.validation.enabled=true # 是否启用 LDAP schema 验证。
    spring.ldap.embedded.validation.schema= # 自定义 schema 的路径。
    
    # MUSTACHE TEMPLATES (MustacheAutoConfiguration)
    spring.mustache.allow-request-override=false # 是否允许 HttpServletRequest 属性覆盖（隐藏）控制器生成的同名模型属性。
    spring.mustache.allow-session-override=false # 是否允许 HttpSession 属性覆盖（隐藏）控制器生成的同名模型属性。
    spring.mustache.cache=false # 是否启用模板缓存。
    spring.mustache.charset=UTF-8 # 模板编码。
    spring.mustache.check-template-location=true # 是否检查模板位置是否存在。
    spring.mustache.content-type=text/html # Content-Type 值。
    spring.mustache.enabled=true # 是否为此技术启用 MVC 视图解析。
    spring.mustache.expose-request-attributes=false # 是否应在与模板合并之前将所有请求属性添加到模型中。
    spring.mustache.expose-session-attributes=false # 是否应在与模板合并之前将所有 HttpSession 属性添加到模型中。
    spring.mustache.expose-spring-macro-helpers=true # 是否以“springMacroRequestContext”的名称公开 RequestContext 供 Spring 的宏库使用。
    spring.mustache.prefix=classpath:/templates/ # 要应用于模板名称的前缀。
    spring.mustache.request-context-attribute= # 所有视图的 RequestContext 属性的名称。
    spring.mustache.suffix=.mustache # 要应用于模板名称的后缀。
    spring.mustache.view-names= # 可以解析的视图名称的白名单。
    
    # SPRING MVC (WebMvcProperties)
    spring.mvc.async.request-timeout= # 异步请求处理超时之前的时间量。
    spring.mvc.contentnegotiation.favor-parameter=false # 是否应使用请求参数（默认为“format”）来确定请求的媒体类型。
    spring.mvc.contentnegotiation.favor-path-extension=false # 是否应该使用 URL 路径中的路径扩展来确定请求的媒体类型。
    spring.mvc.contentnegotiation.media-types.*= # 将文件扩展名映射到用于内容协商的媒体类型。例如，yml 到 text/yaml。
    spring.mvc.contentnegotiation.parameter-name= # 启用“favor-parameter”时要使用的查询参数名称。
    spring.mvc.date-format= # 要使用的日期格式。例如，“dd/MM/yyyy”。
    spring.mvc.dispatch-trace-request=false # 是否将 TRACE 请求分派到 FrameworkServlet doService 方法。
    spring.mvc.dispatch-options-request=true # 是否将 OPTIONS 请求分派到 FrameworkServlet doService 方法。
    spring.mvc.favicon.enabled=true # 是否启用 favicon.ico 的解析。
    spring.mvc.formcontent.filter.enabled=true # 是否启用 Spring 的 FormContentFilter。
    spring.mvc.hiddenmethod.filter.enabled=true # 是否启用 Spring 的 HiddenHttpMethodFilter。
    spring.mvc.ignore-default-model-on-redirect=true # 在重定向场景中是否应该忽略“default”模型的内容。
    spring.mvc.locale= # 要使用的区域设置。默认情况下，此区域设置由“Accept-Language”头覆盖。
    spring.mvc.locale-resolver=accept-header # 定义如何解析区域设置。
    spring.mvc.log-resolved-exception=false # 是否启用由“HandlerExceptionResolver”解析的异常的警告日志记录，但“DefaultHandlerExceptionResolver”除外。
    spring.mvc.message-codes-resolver-format= # 消息代码的格式化策略。例如：PREFIX_ERROR_CODE。
    spring.mvc.pathmatch.use-registered-suffix-pattern=false # 后缀模式匹配是否应仅适用于使用 spring.mvc.contentnegotiation.media-types.* 注册的扩展。
    spring.mvc.pathmatch.use-suffix-pattern=false # 在将模式与请求匹配时是否使用后缀模式匹配（“.*”）。
    spring.mvc.servlet.load-on-startup=-1 # 调度器 servlet 的启动时加载优先级。
    spring.mvc.servlet.path=/ # 调度器 servlet 的路径。
    spring.mvc.static-path-pattern=/** # 用于静态资源的路径模式。
    spring.mvc.throw-exception-if-no-handler-found=false # 如果没有找到 Handler 来处理请求，是否应该抛出“NoHandlerFoundException”。
    spring.mvc.view.prefix= # Spring MVC 视图前缀。
    spring.mvc.view.suffix= # Spring MVC 视图后缀。
    
    # SPRING RESOURCES HANDLING (ResourceProperties)
    spring.resources.add-mappings=true # 是否启用默认资源处理。
    spring.resources.cache.cachecontrol.cache-private= # 表示响应消息是针对单个用户的，不能由共享缓存存储。
    spring.resources.cache.cachecontrol.cache-public= # 表示任何缓存都可以存储响应。
    spring.resources.cache.cachecontrol.max-age= # 响应应该被缓存的最大时间，如果没有指定持续时间后缀，以秒为单位。
    spring.resources.cache.cachecontrol.must-revalidate= # 表示一旦过期，缓存在没有与服务器重新验证的情况下不得使用响应。
    spring.resources.cache.cachecontrol.no-cache= # 表示缓存的响应只有在与服务器重新验证时才能重复使用。
    spring.resources.cache.cachecontrol.no-store= # 表示在任何情况下都不缓存响应。
    spring.resources.cache.cachecontrol.no-transform= # 表示中介（缓存和其他）不应转换响应内容。
    spring.resources.cache.cachecontrol.proxy-revalidate= # 与“必须重新验证”指令的含义相同，只是它不适用于私有缓存。
    spring.resources.cache.cachecontrol.s-max-age= # 共享缓存应缓存响应的最大时间，如果未指定持续时间后缀，则以秒为单位。
    spring.resources.cache.cachecontrol.stale-if-error= # 遇到错误时可以使用响应的最大时间，如果没有指定持续时间后缀，则以秒为单位。
    spring.resources.cache.cachecontrol.stale-while-revalidate= # 响应变得过时后可以被提供的最大时间，如果没有指定持续时间后缀，则以秒为单位。
    spring.resources.cache.period= # 资源处理器提供的资源的缓存周期。如果未指定持续时间后缀，则将使用秒数。
    spring.resources.chain.cache=true # 是否在资源链中启用缓存。
    spring.resources.chain.compressed=false # 是否启用已压缩资源（gzip、brotli）的解析。
    spring.resources.chain.enabled= # 是否启用 Spring 资源处理链。默认情况下，禁用，除非至少启用了一个策略。
    spring.resources.chain.html-application-cache=false # 是否启用 HTML5 应用程序缓存清单重写。
    spring.resources.chain.strategy.content.enabled=false # 是否启用内容版本策略。
    spring.resources.chain.strategy.content.paths=/** # 应用到内容版本策略的逗号分隔模式列表。
    spring.resources.chain.strategy.fixed.enabled=false # 是否启用固定版本策略。
    spring.resources.chain.strategy.fixed.paths=/** # 应用到固定版本策略的逗号分隔模式列表。
    spring.resources.chain.strategy.fixed.version= # 用于固定版本策略的版本字符串。
    spring.resources.static-locations=classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/ # 静态资源的位置。
    
    # SPRING SESSION (SessionProperties)
    spring.session.store-type= # 会话存储类型。
    spring.session.timeout= # 会话超时。如果未指定持续时间后缀，则将使用秒数。
    spring.session.servlet.filter-order=-2147483598 # 会话存储库过滤器顺序。
    spring.session.servlet.filter-dispatcher-types=async,error,request # 会话存储库过滤调度器类型。
    
    # SPRING SESSION HAZELCAST (HazelcastSessionProperties)
    spring.session.hazelcast.flush-mode=on-save # 会话刷新模式。
    spring.session.hazelcast.map-name=spring:session:sessions # 用于存储会话的映射的名称。
    
    # SPRING SESSION JDBC (JdbcSessionProperties)
    spring.session.jdbc.cleanup-cron=0 * * * * * # 过期会话清理作业的 cron 表达式。
    spring.session.jdbc.initialize-schema=embedded # 数据库 schema 初始化模式。
    spring.session.jdbc.schema=classpath:org/springframework/session/jdbc/schema-@@platform@@.sql # 用于初始化数据库 schema 的 SQL 文件的路径。
    spring.session.jdbc.table-name=SPRING_SESSION # 用于存储会话的数据库表的名称。
    
    # SPRING SESSION MONGODB (MongoSessionProperties)
    spring.session.mongodb.collection-name=sessions # 用于存储会话的集合名称。
    
    # SPRING SESSION REDIS (RedisSessionProperties)
    spring.session.redis.cleanup-cron=0 * * * * * # 过期会话清理作业的 cron 表达式。
    spring.session.redis.flush-mode=on-save # 会话刷新模式。
    spring.session.redis.namespace=spring:session # 用于存储会话的键的命名空间。
    
    # THYMELEAF (ThymeleafAutoConfiguration)
    spring.thymeleaf.cache=true # 是否启用模板缓存。
    spring.thymeleaf.check-template=true # 在呈现模板之前是否检查它是否存在。
    spring.thymeleaf.check-template-location=true # 是否检查模板位置是否存在。
    spring.thymeleaf.enabled=true # 是否为 Web 框架启用 Thymeleaf 视图解析。
    spring.thymeleaf.enable-spring-el-compiler=false # 在 SpringEL 表达式中启用 SpringEL 编译器。
    spring.thymeleaf.encoding=UTF-8 # 模板文件编码。
    spring.thymeleaf.excluded-view-names= # 应该从解析中排除的视图名称（模式允许的）的逗号分隔列表。
    spring.thymeleaf.mode=HTML # 要应用于模板的模板模式。另请参见 Thymeleaf 的 TemplateMode 枚举。
    spring.thymeleaf.prefix=classpath:/templates/ # 在构建 URL 时被预先设置为视图名称的前缀。
    spring.thymeleaf.reactive.chunked-mode-view-names= # 逗号分隔的视图名称列表（模式允许的），当设置最大块大小时，该视图应是在 CHUNKED 模式下唯一被执行的。
    spring.thymeleaf.reactive.full-mode-view-names= # 逗号分隔的视图名称列表（模式允许的），即使设置了最大块大小，也应在 FULL 模式下执行。
    spring.thymeleaf.reactive.max-chunk-size=0B # 用于写入响应的数据缓冲区的最大大小。
    spring.thymeleaf.reactive.media-types= # 视图技术支持的媒体类型。
    spring.thymeleaf.render-hidden-markers-before-checkboxes=false # 作为复选框标记的隐藏表单输入是否应该在复选框元素本身之前呈现。
    spring.thymeleaf.servlet.content-type=text/html # 写入 HTTP 响应的 Content-Type 值。
    spring.thymeleaf.servlet.produce-partial-output-while-processing=true # Thymeleaf 是应该尽快开始写入部分输出还是缓冲直到模板处理完成。
    spring.thymeleaf.suffix=.html # 在构建 URL 时附加到视图名称的后缀。
    spring.thymeleaf.template-resolver-order= # 链中模板解析器的顺序。
    spring.thymeleaf.view-names= # 可以解析的视图名称（模式允许的）的逗号分隔列表。
    
    # SPRING WEBFLUX (WebFluxProperties)
    spring.webflux.date-format= # 要使用的日期格式。例如，“dd/MM/yyyy”。
    spring.webflux.hiddenmethod.filter.enabled=true # 是否启用 Spring 的 HiddenHttpMethodFilter。
    spring.webflux.static-path-pattern=/** # 用于静态资源的路径模式。
    
    # SPRING WEB SERVICES (WebServicesProperties)
    spring.webservices.path=/services # 用于静态资源的路径模式。
    spring.webservices.servlet.init= # 要传递给 Spring Web 服务的 servlet 初始化参数。
    spring.webservices.servlet.load-on-startup=-1 # Spring Web 服务 servlet 的启动时加载优先级。
    spring.webservices.wsdl-locations= # 作为 bean 公开的 WSDLs 和随附的 XSDs 的逗号分隔的位置列表。
    
    # ----------------------------------------
    # SECURITY PROPERTIES
    # ----------------------------------------
    # SECURITY (SecurityProperties)
    spring.security.filter.order=-100 # 安全过滤器链顺序。
    spring.security.filter.dispatcher-types=async,error,request # 安全过滤器链调度器类型。
    spring.security.user.name=user # 默认用户名。
    spring.security.user.password= # 默认用户名的密码。
    spring.security.user.roles= # 为默认用户名授予角色。
    
    # SECURITY OAUTH2 CLIENT (OAuth2ClientProperties)
    spring.security.oauth2.client.provider.*= # OAuth 提供者详细信息。
    spring.security.oauth2.client.registration.*= # OAuth 客户端注册。
    
    # SECURITY OAUTH2 RESOURCE SERVER (OAuth2ResourceServerProperties)
    spring.security.oauth2.resourceserver.jwt.jwk-set-uri= # 用于验证 JWT 令牌的 JSON Web Key URI。
    spring.security.oauth2.resourceserver.jwt.issuer-uri= # OpenID Connect Provider 断言为其 Issuer Identifier 的 URI。
    
    # ----------------------------------------
    # DATA PROPERTIES
    # ----------------------------------------
    
    # FLYWAY (FlywayProperties)
    spring.flyway.baseline-description=<< Flyway Baseline >> # 应用基线时用以标记现有 schema 的描述。
    spring.flyway.baseline-on-migrate=false # 迁移非空 schema 时是否自动调用基线。
    spring.flyway.baseline-version=1 # 执行基线时用以标记现有 schema 的版本。
    spring.flyway.check-location=true # 是否检查迁移脚本位置是否存在。
    spring.flyway.clean-disabled=false # 是否禁用数据库清理。
    spring.flyway.clean-on-validation-error=false # 发生验证错误时是否自动调用清理。
    spring.flyway.connect-retries=0 # 尝试连接到数据库时的最大重试次数。
    spring.flyway.enabled=true # 是否启用 flyway。
    spring.flyway.encoding=UTF-8 # SQL 迁移的编码。
    spring.flyway.group=false # 是否在应用所有挂起迁移时将它们分组到同一事务中。
    spring.flyway.ignore-future-migrations=true # 读取 schema 历史表时是否忽略未来迁移。
    spring.flyway.ignore-ignored-migrations=false # 读取 schema 历史表时是否忽略已忽略的迁移。
    spring.flyway.ignore-missing-migrations=false # 读取 schema 历史表时是否忽略丢失的迁移。
    spring.flyway.ignore-pending-migrations=false # 读取 schema 历史表时是否忽略挂起的迁移。
    spring.flyway.init-sqls= # 获取后立即执行以初始化连接的 SQL 语句。
    spring.flyway.installed-by= # 已应用迁移的 schema 历史表中记录的用户名。
    spring.flyway.locations=classpath:db/migration # 迁移脚本的位置。可以包含特殊的“{vendor}”占位符以使用特定于供应商的位置。
    spring.flyway.mixed=false # 是否允许在同一迁移中混合事务和非事务语句。
    spring.flyway.out-of-order=false # 是否允许迁移无序运行。
    spring.flyway.password= # 要迁移的数据库的登录密码。
    spring.flyway.placeholder-prefix=${ # 迁移脚本中占位符的前缀。
    spring.flyway.placeholder-replacement=true # 在迁移脚本中执行占位符替换。
    spring.flyway.placeholder-suffix=} # 迁移脚本中占位符的后缀。
    spring.flyway.placeholders= # 要应用于 sql 迁移脚本的占位符及其替换。
    spring.flyway.repeatable-sql-migration-prefix=R # 可重复 SQL 迁移的文件名前缀。
    spring.flyway.schemas= # 由 Flyway 管理的方案名称（区分大小写）。
    spring.flyway.skip-default-callbacks=false # 是否跳过默认回调。如果为 true，则仅使用自定义回调。
    spring.flyway.skip-default-resolvers=false # 是否跳过默认解析器。如果为 true，则仅使用自定义解析器。
    spring.flyway.sql-migration-prefix=V # SQL 迁移的文件名前缀。
    spring.flyway.sql-migration-separator=__ # SQL 迁移的文件名分隔符。
    spring.flyway.sql-migration-suffixes=.sql # SQL 迁移的文件名后缀。
    spring.flyway.table=flyway_schema_history # Flyway 将使用的 schema 历史表的名称。
    spring.flyway.target= # 应考虑迁移的目标版本。
    spring.flyway.url= # 要迁移的数据库的 JDBC url。如果未设置，则使用主配置的数据源。
    spring.flyway.user= # 要迁移的数据库的登录用户。
    spring.flyway.validate-on-migrate=true # 执行迁移时是否自动调用验证。
    
    # LIQUIBASE (LiquibaseProperties)
    spring.liquibase.change-log=classpath:/db/changelog/db.changelog-master.yaml # 更改日志配置路径。
    spring.liquibase.check-change-log-location=true # 是否检查更改日志位置是否存在。
    spring.liquibase.contexts= # 要使用的运行时上下文的逗号分隔列表。
    spring.liquibase.database-change-log-lock-table=DATABASECHANGELOGLOCK # 用于跟踪并发 Liquibase 使用情况的表的名称。
    spring.liquibase.database-change-log-table=DATABASECHANGELOG # 用于跟踪更改历史记录的表的名称。
    spring.liquibase.default-schema= # 默认数据库 schema。
    spring.liquibase.drop-first=false # 是否首先删除数据库 schema。
    spring.liquibase.enabled=true # 是否启用 Liquibase 支持。
    spring.liquibase.labels= # 要使用的运行时标签的逗号分隔列表。
    spring.liquibase.liquibase-schema= # 用于 Liquibase 对象的 schema。
    spring.liquibase.liquibase-tablespace= # 要用于 Liquibase 对象的表空间。
    spring.liquibase.parameters.*= # 更改日志参数。
    spring.liquibase.password= # 要迁移的数据库的登录密码。
    spring.liquibase.rollback-file= # 执行更新时将回滚 SQL 写入的文件。
    spring.liquibase.test-rollback-on-update=false # 是否应在执行更新之前测试回滚。
    spring.liquibase.url= # 要迁移的数据库的 JDBC URL。如果未设置，则使用主配置的数据源。
    spring.liquibase.user= # 要迁移的数据库的登录用户。
    
    # COUCHBASE (CouchbaseProperties)
    spring.couchbase.bootstrap-hosts= # 要从中引导的 Couchbase 节点（主机或 IP 地址）。
    spring.couchbase.bucket.name=default # 要连接的 bucket 的默认名称。
    spring.couchbase.bucket.password=  # bucket 的密码。
    spring.couchbase.env.endpoints.key-value=1 # 针对键/值服务的每个节点的套接字数。
    spring.couchbase.env.endpoints.queryservice.min-endpoints=1 # 每个节点的最小套接字数。
    spring.couchbase.env.endpoints.queryservice.max-endpoints=1 # 每个节点的最大套接字数。
    spring.couchbase.env.endpoints.viewservice.min-endpoints=1 # 每个节点的最小套接字数。
    spring.couchbase.env.endpoints.viewservice.max-endpoints=1 # 每个节点的最大套接字数。
    spring.couchbase.env.ssl.enabled= # 是否启用 SSL 支持。如果提供了 “keyStore”，则自动启用，除非另有说明。
    spring.couchbase.env.ssl.key-store= # 保存证书的 JVM 密钥存储的路径。
    spring.couchbase.env.ssl.key-store-password= # 用于访问密钥存储的密码。
    spring.couchbase.env.timeouts.connect=5000ms # bucket 连接超时。
    spring.couchbase.env.timeouts.key-value=2500ms # 对特定密钥超时执行的阻塞操作。
    spring.couchbase.env.timeouts.query=7500ms # N1QL 查询操作超时。
    spring.couchbase.env.timeouts.socket-connect=1000ms # 套接字连接超时。
    spring.couchbase.env.timeouts.view=7500ms # 常规和地理空间视图操作超时。
    
    # DAO (PersistenceExceptionTranslationAutoConfiguration)
    spring.dao.exceptiontranslation.enabled=true # 是否启用 PersistenceExceptionTranslationPostProcessor。
    
    # CASSANDRA (CassandraProperties)
    spring.data.cassandra.cluster-name= # Cassandra 集群的名称。
    spring.data.cassandra.compression=none # Cassandra 二进制协议支持的压缩。
    spring.data.cassandra.connect-timeout= # Socket 选项：连接超时。
    spring.data.cassandra.consistency-level= # 查询一致性级别。
    spring.data.cassandra.contact-points=localhost # 集群节点地址。
    spring.data.cassandra.fetch-size= # 查询默认获取大小。
    spring.data.cassandra.jmx-enabled=false # 是否启用 JMX 报告。
    spring.data.cassandra.keyspace-name= # 要使用的 keyspace 名称。
    spring.data.cassandra.port= # Cassandra 服务器的端口。
    spring.data.cassandra.password= # 服务器的登录密码。
    spring.data.cassandra.pool.heartbeat-interval=30s # 在空闲连接上发送消息以确保其仍处于活动状态的心跳间隔。如果未指定持续时间后缀，则将使用秒数。
    spring.data.cassandra.pool.idle-timeout=120s # 删除空闲连接之前的空闲超时。如果未指定持续时间后缀，则将使用秒数。
    spring.data.cassandra.pool.max-queue-size=256 # 如果没有连接，则排队的最大请求数。
    spring.data.cassandra.pool.pool-timeout=5000ms # 试图从主机池获取连接时的池超时。
    spring.data.cassandra.read-timeout= # 套接字选项：读取超时。
    spring.data.cassandra.repositories.type=auto # 要启用的 Cassandra 存储库的类型。
    spring.data.cassandra.serial-consistency-level= # 查询序列一致性级别。
    spring.data.cassandra.schema-action=none # 启动时要执行的 schema 操作。
    spring.data.cassandra.ssl=false # 启用 SSL 支持。
    spring.data.cassandra.username= # 服务器的登录用户。
    
    # DATA COUCHBASE (CouchbaseDataProperties)
    spring.data.couchbase.auto-index=false # 自动创建视图和索引。
    spring.data.couchbase.consistency=read-your-own-writes # 在生成的查询上默认应用的一致性。
    spring.data.couchbase.repositories.type=auto # 要启用的 Couchbase 存储库的类型。
    
    # ELASTICSEARCH (ElasticsearchProperties)
    spring.data.elasticsearch.cluster-name=elasticsearch # Elasticsearch 集群名称。
    spring.data.elasticsearch.cluster-nodes= # 以逗号分隔的群集节点地址列表。
    spring.data.elasticsearch.properties.*= # 用于配置客户端的其他属性。
    spring.data.elasticsearch.repositories.enabled=true # 是否启用 Elasticsearch 存储库。
    
    # DATA JDBC
    spring.data.jdbc.repositories.enabled=true # 是否启用 JDBC 存储库。
    
    # DATA LDAP
    spring.data.ldap.repositories.enabled=true # 是否启用 LDAP 存储库。
    
    # MONGODB (MongoProperties)
    spring.data.mongodb.authentication-database= # 身份验证数据库名称。
    spring.data.mongodb.database= # 数据库名称。
    spring.data.mongodb.field-naming-strategy= # 要使用的 FieldNamingStrategy 的完全限定名。
    spring.data.mongodb.grid-fs-database= # GridFS 数据库名称。
    spring.data.mongodb.host= # Mongo 服务器主机。不能用 URI 设置。
    spring.data.mongodb.password= # mongo 服务器的登录密码。不能用 URI 设置。
    spring.data.mongodb.port= # Mongo 服务器端口。不能用 URI 设置。
    spring.data.mongodb.repositories.type=auto # 要启用的 Mongo 存储库的类型。
    spring.data.mongodb.uri=mongodb://localhost/test # Mongo 数据库 URI。不能使用主机、端口和凭据进行设置。
    spring.data.mongodb.username= # mongo 服务器的登录用户。不能用 URI 设置。
    
    # DATA REDIS
    spring.data.redis.repositories.enabled=true # 是否启用 Redis 存储库。
    
    # NEO4J (Neo4jProperties)
    spring.data.neo4j.auto-index=none # 自动索引模式。
    spring.data.neo4j.embedded.enabled=true # 如果嵌入式驱动程序可用，是否启用嵌入式模式。
    spring.data.neo4j.open-in-view=true # 注册 OpenSessionInViewInterceptor。将一个 Neo4j 会话绑定到线程，用于整个请求处理。
    spring.data.neo4j.password= # 服务器的登录密码。
    spring.data.neo4j.repositories.enabled=true # 是否启用 Neo4j 存储库。
    spring.data.neo4j.uri= # 驱动程序使用的 URI。默认情况下自动检测。
    spring.data.neo4j.username= # 服务器的登录用户。
    
    # DATA REST (RepositoryRestProperties)
    spring.data.rest.base-path= # Spring Data REST 用于公开存储库资源的基本路径。
    spring.data.rest.default-media-type= # 未指定时用作默认值的内容类型。
    spring.data.rest.default-page-size= # 页面的默认大小。
    spring.data.rest.detection-strategy=default # 用于确定哪些存储库被公开的默认策略。
    spring.data.rest.enable-enum-translation= # 是否通过 Spring Data REST 默认资源包启用枚举值转换。
    spring.data.rest.limit-param-name= # URL 查询字符串参数的名称，该参数指示一次返回多少结果。
    spring.data.rest.max-page-size= # 页面的最大大小。
    spring.data.rest.page-param-name= # 指示要返回哪个页面的 URL 查询字符串参数的名称。
    spring.data.rest.return-body-on-create= # 创建实体后是否返回响应体。
    spring.data.rest.return-body-on-update= # 更新实体后是否返回响应体。
    spring.data.rest.sort-param-name= # URL 查询字符串参数的名称，该参数指示对结果进行排序的方向。
    
    # SOLR (SolrProperties)
    spring.data.solr.host=http://127.0.0.1:8983/solr # Solr 主机。如果设置了“zk-host”，则忽略。
    spring.data.solr.repositories.enabled=true # 是否启用 Solr 存储库。
    spring.data.solr.zk-host= # 格式为 HOST:PORT 的 ZooKeeper 主机地址。
    
    # DATA WEB (SpringDataWebProperties)
    spring.data.web.pageable.default-page-size=20 # 默认页面大小。
    spring.data.web.pageable.max-page-size=2000 # 接受的最大页面大小。
    spring.data.web.pageable.one-indexed-parameters=false # 是否公开并假设基于 1 的页码索引。
    spring.data.web.pageable.page-parameter=page # 页面索引参数名称。
    spring.data.web.pageable.prefix= # 页码和页面大小参数前面的通用前缀。
    spring.data.web.pageable.qualifier-delimiter=_ # 限定符与实际页码和大小属性之间使用的分隔符。
    spring.data.web.pageable.size-parameter=size # 页面大小参数名称。
    spring.data.web.sort.sort-parameter=sort # 排序参数名称。
    
    # DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties)
    spring.datasource.continue-on-error=false # 如果初始化数据库时发生错误，是否停止。
    spring.datasource.data= # 数据（DML）脚本资源引用。
    spring.datasource.data-username= # 要执行 DML 脚本的数据库的用户名（如果不同）。
    spring.datasource.data-password= # 要执行DML脚本的数据库的密码（如果不同）。
    spring.datasource.dbcp2.*= # Commons DBCP2 特定设置。
    spring.datasource.driver-class-name= # JDBC 驱动程序的完全限定名。默认情况下根据 URL 自动检测。
    spring.datasource.generate-unique-name=false # 是否生成随机数据源名称。
    spring.datasource.hikari.*= # Hikari 特定设置
    spring.datasource.initialization-mode=embedded # 使用可用的 DDL 和 DML 脚本初始化数据源。
    spring.datasource.jmx-enabled=false # 是否启用 JMX 支持（如果由底层池提供）。
    spring.datasource.jndi-name= # JNDI 数据源的位置。设置时，class、 url 、username 和 password 将被忽略。
    spring.datasource.name= # 数据源的名称。使用嵌入式数据库时，默认为 “testdb”。
    spring.datasource.password= # 数据库的登录密码。
    spring.datasource.platform=all # DDL 或 DML 脚本（例如 schema-${platform}.sql 或 data-${platform}.sql）中使用的平台。
    spring.datasource.schema= # schema（DDL）脚本资源引用。
    spring.datasource.schema-username= # 要执行 DDL 脚本的数据库的用户名（如果不同）。
    spring.datasource.schema-password= # 要执行 DDL 脚本的数据库的密码（如果不同）。
    spring.datasource.separator=; # SQL 初始化脚本中的语句分隔符。
    spring.datasource.sql-script-encoding= # SQL 脚本编码。
    spring.datasource.tomcat.*= # Tomcat 数据源特定设置
    spring.datasource.type= # 要使用的连接池实现的完全限定名称。默认情况下，它是从类路径自动检测到的。
    spring.datasource.url= # 数据库的 JDBC URL。
    spring.datasource.username= # 数据库的登录用户名。
    spring.datasource.xa.data-source-class-name= # XA 数据源完全限定名称。
    spring.datasource.xa.properties= # 要传递到 XA 数据源的属性。
    
    # JEST (Elasticsearch HTTP client) (JestProperties)
    spring.elasticsearch.jest.connection-timeout=3s # 连接超时。
    spring.elasticsearch.jest.multi-threaded=true # 是否启用来自多个执行线程的连接请求。
    spring.elasticsearch.jest.password= # 登录密码。
    spring.elasticsearch.jest.proxy.host= # HTTP 客户端应使用的代理主机。
    spring.elasticsearch.jest.proxy.port= # HTTP 客户端应使用的代理端口。
    spring.elasticsearch.jest.read-timeout=3s # 读取超时。
    spring.elasticsearch.jest.uris=http://localhost:9200 # 要使用的 Elasticsearch 实例的逗号分隔列表。
    spring.elasticsearch.jest.username= # 登录用户名。
    
    # Elasticsearch REST clients (RestClientProperties)
    spring.elasticsearch.rest.password= # 凭据密码。
    spring.elasticsearch.rest.uris=http://localhost:9200 # 要使用的 Elasticsearch 实例的逗号分隔列表。
    spring.elasticsearch.rest.username= # 凭据用户名。
    
    # H2 Web Console (H2ConsoleProperties)
    spring.h2.console.enabled=false # 是否启用控制台。
    spring.h2.console.path=/h2-console # 控制台可用的路径。
    spring.h2.console.settings.trace=false # 是否启用跟踪输出。
    spring.h2.console.settings.web-allow-others=false # 是否启用远程访问。
    
    # InfluxDB (InfluxDbProperties)
    spring.influx.password= # 登录密码。
    spring.influx.url= # 要连接到的 InfluxDB 实例的 URL。
    spring.influx.user= # 登录用户。
    
    # JOOQ (JooqProperties)
    spring.jooq.sql-dialect= # 要使用的 SQL 方言。默认情况下自动检测。
    
    # JDBC (JdbcProperties)
    spring.jdbc.template.fetch-size=-1 # 当需要更多行时，应从数据库中提取的行数。
    spring.jdbc.template.max-rows=-1 # 最大行数。
    spring.jdbc.template.query-timeout= # 查询超时。默认是使用 JDBC 驱动程序的默认配置。如果未指定持续时间后缀，将使用秒。
    
    # JPA (JpaBaseConfiguration, HibernateJpaAutoConfiguration)
    spring.data.jpa.repositories.bootstrap-mode=default # JPA 存储库的引导模式。
    spring.data.jpa.repositories.enabled=true # 是否启用 JPA 存储库。
    spring.jpa.database= # 要操作的目标数据库，默认情况下自动检测。也可以使用“databasePlatform”属性进行设置。
    spring.jpa.database-platform= # 要操作的目标数据库的名称，默认情况下自动检测。也可以使用“Database”枚举设置。
    spring.jpa.generate-ddl=false # 是否在启动时初始化 schema。
    spring.jpa.hibernate.ddl-auto= # DDL 模式。这实际上是“hibernate.hbm2ddl.auto”属性的快捷方式。在使用嵌入式数据库且未检测到 schema 管理器时，默认为“create-drop”。否则，默认为“none”。
    spring.jpa.hibernate.naming.implicit-strategy= # 隐式命名策略的完全限定名。
    spring.jpa.hibernate.naming.physical-strategy= # 物理命名策略的完全限定名。
    spring.jpa.hibernate.use-new-id-generator-mappings= # 是否将 Hibernate 的较新 IdentifierGenerator 用于 AUTO、TABLE 和 SEQUENCE。
    spring.jpa.mapping-resources= # 映射资源（相当于 persistence.xml 中的“mapping file”条目）。
    spring.jpa.open-in-view=true # 注册 OpenEntityManagerInViewInterceptor。将 JPA EntityManager 绑定到线程，用于整个请求处理。
    spring.jpa.properties.*= # 要在 JPA 提供者上设置的其他本机属性。
    spring.jpa.show-sql=false # 是否启用 SQL 语句日志记录。
    
    # JTA (JtaAutoConfiguration)
    spring.jta.enabled=true # 是否启用 JTA 支持。
    spring.jta.log-dir= # 事务日志目录。
    spring.jta.transaction-manager-id= # 事务管理器唯一标识符。
    
    # ATOMIKOS (AtomikosProperties)
    spring.jta.atomikos.connectionfactory.borrow-connection-timeout=30 # 从池中借用连接的超时（秒）。
    spring.jta.atomikos.connectionfactory.ignore-session-transacted-flag=true # 创建会话时是否忽略事务标志。
    spring.jta.atomikos.connectionfactory.local-transaction-mode=false # 是否需要本地事务。
    spring.jta.atomikos.connectionfactory.maintenance-interval=60 # 池维护线程运行之间的时间（秒）。
    spring.jta.atomikos.connectionfactory.max-idle-time=60 # 从池中清除连接之前的时间（秒）。
    spring.jta.atomikos.connectionfactory.max-lifetime=0 # 连接在被破坏之前可以池化的时间（秒）。0 表示无限制。
    spring.jta.atomikos.connectionfactory.max-pool-size=1 # 池的最大大小。
    spring.jta.atomikos.connectionfactory.min-pool-size=1 # 池的最小大小。
    spring.jta.atomikos.connectionfactory.reap-timeout=0 # 用于借用的连接的获取超时（秒）。0 表示无限制。
    spring.jta.atomikos.connectionfactory.unique-resource-name=jmsConnectionFactory # 用于在恢复期间标识资源的唯一名称。
    spring.jta.atomikos.connectionfactory.xa-connection-factory-class-name= # 特定于供应商的 XAConnectionFactory 实现。
    spring.jta.atomikos.connectionfactory.xa-properties= # 供应商特定的 XA 属性。
    spring.jta.atomikos.datasource.borrow-connection-timeout=30 # 从池中借用连接的超时（秒）。
    spring.jta.atomikos.datasource.concurrent-connection-validation=true # 是否使用并发连接验证。
    spring.jta.atomikos.datasource.default-isolation-level= # 池提供的连接的默认隔离级别。
    spring.jta.atomikos.datasource.login-timeout=0 # 建立数据库连接的超时（秒）。
    spring.jta.atomikos.datasource.maintenance-interval=60 # 池维护线程运行之间的时间（秒）。
    spring.jta.atomikos.datasource.max-idle-time=60 # 从池中清除连接之前的时间（秒）。
    spring.jta.atomikos.datasource.max-lifetime=0 # 连接在被破坏之前可以池化的时间（秒）。0 表示无限制。
    spring.jta.atomikos.datasource.max-pool-size=1 # 池的最大大小。
    spring.jta.atomikos.datasource.min-pool-size=1 # 池的最小大小。
    spring.jta.atomikos.datasource.reap-timeout=0 # 用于借用的连接的获取超时（秒）。0 表示无限制。
    spring.jta.atomikos.datasource.test-query= # 用于在返回连接之前验证它的 SQL 查询或语句。
    spring.jta.atomikos.datasource.unique-resource-name=dataSource # 用于在恢复期间标识资源的唯一名称。
    spring.jta.atomikos.datasource.xa-data-source-class-name= # 特定于供应商的 XAConnectionFactory 实现。
    spring.jta.atomikos.datasource.xa-properties= # 供应商特定的 XA 属性。
    spring.jta.atomikos.properties.allow-sub-transactions=true # 指定是否允许子事务。
    spring.jta.atomikos.properties.checkpoint-interval=500 # 检查点之间的间隔，表示为两个检查点之间的日志写入数。
    spring.jta.atomikos.properties.default-jta-timeout=10000ms # JTA 事务的默认超时。
    spring.jta.atomikos.properties.default-max-wait-time-on-shutdown=9223372036854775807 # 正常关机（无强制）应等待事务完成多长时间。
    spring.jta.atomikos.properties.enable-logging=true # 是否启用磁盘日志记录。
    spring.jta.atomikos.properties.force-shutdown-on-vm-exit=false # VM（虚拟机）关闭是否应触发事务核心的强制关闭。
    spring.jta.atomikos.properties.log-base-dir= # 应将日志文件存储在其中的目录。
    spring.jta.atomikos.properties.log-base-name=tmlog # 事务日志文件基本名称。
    spring.jta.atomikos.properties.max-actives=50 # 最大活动事务数。
    spring.jta.atomikos.properties.max-timeout=300000ms # 事务允许的最大超时。
    spring.jta.atomikos.properties.recovery.delay=10000ms # 两次恢复扫描之间的延迟。
    spring.jta.atomikos.properties.recovery.forget-orphaned-log-entries-delay=86400000ms # 在其后恢复可以清除挂起的（“孤立”）日志项的延迟。
    spring.jta.atomikos.properties.recovery.max-retries=5 # 在抛出异常之前提交事务的重试次数。
    spring.jta.atomikos.properties.recovery.retry-interval=10000ms # 重试间隔。
    spring.jta.atomikos.properties.serial-jta-transactions=true # 如果可能，是否应加入子事务。
    spring.jta.atomikos.properties.service= # 应该启动的事务管理器实现。
    spring.jta.atomikos.properties.threaded-two-phase-commit=false # 是否在参与资源上使用不同（并发）线程进行两阶段提交。
    spring.jta.atomikos.properties.transaction-manager-unique-name= # 事务管理器的唯一名称。
    
    # BITRONIX
    spring.jta.bitronix.connectionfactory.acquire-increment=1 # 增长池时要创建的连接数。
    spring.jta.bitronix.connectionfactory.acquisition-interval=1 # 在获取无效连接后再次尝试获取连接之前等待的时间（秒）。
    spring.jta.bitronix.connectionfactory.acquisition-timeout=30 # 从池中获取连接的超时（秒）。
    spring.jta.bitronix.connectionfactory.allow-local-transactions=false # 事务管理器是否应允许混合 XA 和非 XA 事务。
    spring.jta.bitronix.connectionfactory.apply-transaction-timeout=false # 事务超时是否应在登记时在 XAResource 上设置。
    spring.jta.bitronix.connectionfactory.automatic-enlisting-enabled=true # 是否应自动登记和删除资源。
    spring.jta.bitronix.connectionfactory.cache-producers-consumers=true # 是否应缓存生产者和消费者。
    spring.jta.bitronix.connectionfactory.class-name= # XA 资源的底层实现类名。
    spring.jta.bitronix.connectionfactory.defer-connection-release=true # 提供者是否可以在同一连接上运行多个事务并支持事务交错。
    spring.jta.bitronix.connectionfactory.disabled=false # 此资源是否已禁用，这意味着暂时禁止从其池获取连接。
    spring.jta.bitronix.connectionfactory.driver-properties= # 应该在底层实现上设置的属性。
    spring.jta.bitronix.connectionfactory.ignore-recovery-failures=false # 是否应忽略恢复失败。
    spring.jta.bitronix.connectionfactory.max-idle-time=60 # 从池中清除连接之前的时间（秒）。
    spring.jta.bitronix.connectionfactory.max-pool-size=0 # 池的最大大小。0 表示无限制。
    spring.jta.bitronix.connectionfactory.min-pool-size=0 # 池的最小大小。
    spring.jta.bitronix.connectionfactory.password= # 用于连接到 JMS 提供者的密码。
    spring.jta.bitronix.connectionfactory.share-transaction-connections=false # 是否可以在事务上下文中共享处于 ACCESSIBLE 状态的连接。
    spring.jta.bitronix.connectionfactory.test-connections=false # 从池中获取连接时是否应测试连接。
    spring.jta.bitronix.connectionfactory.two-pc-ordering-position=1 # 此资源在两阶段提交期间应采取的位置（第一个总是 Integer.MIN_VALUE，最后一个总是 Integer.MAX_VALUE）。
    spring.jta.bitronix.connectionfactory.unique-name=jmsConnectionFactory # 用于在恢复期间标识资源的唯一名称。
    spring.jta.bitronix.connectionfactory.use-tm-join=true # 启动 XAResources 时是否应使用 TMJOIN。
    spring.jta.bitronix.connectionfactory.user= # 用于连接到 JMS 提供者的用户。
    spring.jta.bitronix.datasource.acquire-increment=1 # 增长池时要创建的连接数。
    spring.jta.bitronix.datasource.acquisition-interval=1 # 在获取无效连接后再次尝试获取连接之前等待的时间（秒）。
    spring.jta.bitronix.datasource.acquisition-timeout=30 # 从池中获取连接的超时（秒）。
    spring.jta.bitronix.datasource.allow-local-transactions=false # 事务管理器是否应该允许混合 XA 和非 XA 事务。
    spring.jta.bitronix.datasource.apply-transaction-timeout=false # 事务超时是否应在登记时在 XAResource 上设置。
    spring.jta.bitronix.datasource.automatic-enlisting-enabled=true # 是否应自动登记和删除资源。
    spring.jta.bitronix.datasource.class-name= # XA 资源的底层实现类名称。
    spring.jta.bitronix.datasource.cursor-holdability= # 连接的默认游标保持能力。
    spring.jta.bitronix.datasource.defer-connection-release=true # 数据库是否可以在同一个连接上运行多个事务并支持事务交错。
    spring.jta.bitronix.datasource.disabled=false # 此资源是否被禁用，这意味着暂时禁止从其池中获取连接。
    spring.jta.bitronix.datasource.driver-properties= # 应该在底层实现上设置的属性。
    spring.jta.bitronix.datasource.enable-jdbc4-connection-test=false # 从池中获取连接时是否调用 Connection.isValid()。
    spring.jta.bitronix.datasource.ignore-recovery-failures=false # 是否应忽略恢复失败。
    spring.jta.bitronix.datasource.isolation-level= # 连接的默认隔离级别。
    spring.jta.bitronix.datasource.local-auto-commit= # 本地事务的默认自动提交模式。
    spring.jta.bitronix.datasource.login-timeout= # 用于建立数据库连接的超时（秒）。
    spring.jta.bitronix.datasource.max-idle-time=60 # 从池中清除连接之前的时间（秒）。
    spring.jta.bitronix.datasource.max-pool-size=0 # 池的最大大小。0 表示无限制。
    spring.jta.bitronix.datasource.min-pool-size=0 # 池的最小大小。
    spring.jta.bitronix.datasource.prepared-statement-cache-size=0 # 准备好的语句缓存的目标大小。0 禁用缓存。
    spring.jta.bitronix.datasource.share-transaction-connections=false # 是否可以在事务上下文中共享处于 ACCESSIBLE 状态的连接。
    spring.jta.bitronix.datasource.test-query= # 用于在返回连接之前验证它的 SQL 查询或语句。
    spring.jta.bitronix.datasource.two-pc-ordering-position=1 # 此资源在两阶段提交期间应采取的位置（第一个总是 Integer.MIN_VALUE，最后一个总是 Integer.MAX_VALUE）。
    spring.jta.bitronix.datasource.unique-name=dataSource # 用于在恢复期间标识资源的唯一名称。
    spring.jta.bitronix.datasource.use-tm-join=true # 启动 XAResources 时是否应该使用 TMJOIN。
    spring.jta.bitronix.properties.allow-multiple-lrc=false # 是否允许将多个 LRC 资源登记到同一事务中。
    spring.jta.bitronix.properties.asynchronous2-pc=false # 是否启用两阶段提交的异步执行。
    spring.jta.bitronix.properties.background-recovery-interval-seconds=60 # 在后台运行恢复过程的时间间隔（秒）。
    spring.jta.bitronix.properties.current-node-only-recovery=true # 是否仅恢复当前节点。
    spring.jta.bitronix.properties.debug-zero-resource-transaction=false # 是否记录在没有单一登记资源的情况下执行的事务的创建和提交调用堆栈。
    spring.jta.bitronix.properties.default-transaction-timeout=60 # 默认事务超时，以秒为单位。
    spring.jta.bitronix.properties.disable-jmx=false # 是否启用 JMX 支持。
    spring.jta.bitronix.properties.exception-analyzer= # 设置要使用的异常分析器实现的完全限定名称。
    spring.jta.bitronix.properties.filter-log-status=false # 是否启用日志过滤以便只写入强制日志。
    spring.jta.bitronix.properties.force-batching-enabled=true # 是否批处理磁盘强制。
    spring.jta.bitronix.properties.forced-write-enabled=true # 是否将日志强制到磁盘。
    spring.jta.bitronix.properties.graceful-shutdown-interval=60 # 在关闭时中止事务之前，TM 等待事务完成的最大秒数。
    spring.jta.bitronix.properties.jndi-transaction-synchronization-registry-name= # TransactionSynchronizationRegistry 的 JNDI 名称。
    spring.jta.bitronix.properties.jndi-user-transaction-name= # UserTransaction 的 JNDI 名称。
    spring.jta.bitronix.properties.journal=disk # 日志的名称。可以是“disk”、“null”或类名。
    spring.jta.bitronix.properties.log-part1-filename=btm1.tlog # 日志的第一个片段的名称。
    spring.jta.bitronix.properties.log-part2-filename=btm2.tlog # 日志的第二个片段的名称。
    spring.jta.bitronix.properties.max-log-size-in-mb=2 # 日志片段的最大大小（MB）。
    spring.jta.bitronix.properties.resource-configuration-filename= # ResourceLoader 配置文件名。
    spring.jta.bitronix.properties.server-id= # 必须唯一标识此 TM 实例的 ASCII ID。默认为计算机的 IP 地址。
    spring.jta.bitronix.properties.skip-corrupted-logs=false # 跳过损坏的事务日志项。
    spring.jta.bitronix.properties.warn-about-zero-resource-transaction=true # 是否记录在没有单一登记资源的情况下执行的事务的警告。
    
    # EMBEDDED MONGODB (EmbeddedMongoProperties)
    spring.mongodb.embedded.features=sync_delay # 要启用的功能的逗号分隔列表。
    spring.mongodb.embedded.storage.database-dir= # 用于数据存储的目录。
    spring.mongodb.embedded.storage.oplog-size= # oplog 的最大大小。
    spring.mongodb.embedded.storage.repl-set-name= # 副本集的名称。
    spring.mongodb.embedded.version=3.5.5 # 要使用的 Mongo 版本。
    
    # REDIS (RedisProperties)
    spring.redis.cluster.max-redirects= # 跨群集执行命令时要遵循的最大重定向数。
    spring.redis.cluster.nodes= # 要引导的“host:port”对的逗号分隔列表。
    spring.redis.database=0 # 连接工厂使用的数据库索引。
    spring.redis.url= # 连接 URL。覆盖主机、端口和密码。忽略用户。示例：redis://user:password@example.com:6379
    spring.redis.host=localhost # Redis 服务器主机。
    spring.redis.jedis.pool.max-active=8 # 池在给定时间可以分配的最大连接数。使用负值表示无限制。
    spring.redis.jedis.pool.max-idle=8 # 池中“idle”连接的最大数目。使用负值表示空闲连接的数量不受限制。
    spring.redis.jedis.pool.max-wait=-1ms # 在池耗尽时抛出异常之前，连接分配应阻塞的最大时间量。使用负值无限期地阻塞。
    spring.redis.jedis.pool.min-idle=0 # 池中要维护的最小空闲连接数的目标。此设置只有在为正数时才有效果。
    spring.redis.lettuce.pool.max-active=8 # 池在给定时间可以分配的最大连接数。使用负值表示无限制。
    spring.redis.lettuce.pool.max-idle=8 # 池中“idle”连接的最大数目。使用负值表示空闲连接的数量不受限制。
    spring.redis.lettuce.pool.max-wait=-1ms # 在池耗尽时抛出异常之前，连接分配应阻塞的最大时间量。使用负值无限期地阻塞。
    spring.redis.lettuce.pool.min-idle=0 # 池中要维护的最小空闲连接数的目标。此设置只有在为正数时才有效果。
    spring.redis.lettuce.shutdown-timeout=100ms # 关机超时。
    spring.redis.password= # Redis 服务器的登录密码。
    spring.redis.port=6379 # Redis 服务器端口。
    spring.redis.sentinel.master= # Redis 服务器的名称。
    spring.redis.sentinel.nodes= # 逗号分隔的 “host:port” 对列表。
    spring.redis.ssl=false # 是否启用 SSL 支持。
    spring.redis.timeout= # 连接超时。
    
    # TRANSACTION (TransactionProperties)
    spring.transaction.default-timeout= # 默认事务超时。如果未指定持续时间后缀，则将使用秒数。
    spring.transaction.rollback-on-commit-failure= # 是否在提交失败时回滚。
    
    # ----------------------------------------
    # INTEGRATION PROPERTIES
    # ----------------------------------------
    
    # ACTIVEMQ (ActiveMQProperties)
    spring.activemq.broker-url= # ActiveMQ 代理的 URL。默认情况下自动生成。
    spring.activemq.close-timeout=15s # 考虑关闭完成之前的等待时间。
    spring.activemq.in-memory=true # 默认代理 URL 是否应该在内存中。如果已指定显式代理，则忽略。
    spring.activemq.non-blocking-redelivery=false # 是否在从回滚事务重新传递消息之前停止消息传递。这意味着启用此选项时不会保留消息顺序。
    spring.activemq.password= # 代理的登录密码。
    spring.activemq.send-timeout=0ms # 等待消息发送响应的时间。设置为 0 将永远等待。
    spring.activemq.user= # 代理的登录用户。
    spring.activemq.packages.trust-all= # 是否信任所有包。
    spring.activemq.packages.trusted= # 要信任的特定包的逗号分隔列表（不信任所有包时）。
    spring.activemq.pool.block-if-full=true # 请求连接且池已满时是否阻塞。设置为 false 则抛出“JMSException”。
    spring.activemq.pool.block-if-full-timeout=-1ms # 池仍然满的情况下抛出异常之前的阻塞周期。
    spring.activemq.pool.enabled=false # 是否应创建 JmsPoolConnectionFactory，而不是常规的 ConnectionFactory。
    spring.activemq.pool.idle-timeout=30s # 连接空闲超时。
    spring.activemq.pool.max-connections=1 # 最大池连接数。
    spring.activemq.pool.max-sessions-per-connection=500 # 池中每个连接的最大池会话数。
    spring.activemq.pool.time-between-expiration-check=-1ms # 空闲连接回收线程运行之间的休眠时间。如果为负，则不运行空闲连接收回线程。
    spring.activemq.pool.use-anonymous-producers=true # 是否只使用一个匿名的“MessageProducer”实例。将其设置为 false 可在每次需要时创建一个“MessageProducer”。
    
    # ARTEMIS (ArtemisProperties)
    spring.artemis.embedded.cluster-password= # 群集密码。默认情况下在启动时随机生成。
    spring.artemis.embedded.data-directory= # 日志文件目录。如果关闭持久性，则不需要。
    spring.artemis.embedded.enabled=true # 如果 Artemis 服务器 APIs 可用，是否启用嵌入式模式。
    spring.artemis.embedded.persistent=false # 是否启用持久化存储。
    spring.artemis.embedded.queues= # 启动时要创建的队列的逗号分隔列表。
    spring.artemis.embedded.server-id= # 服务器 ID。默认情况下，使用自动递增计数器。
    spring.artemis.embedded.topics= # 启动时要创建的主题的逗号分隔列表。
    spring.artemis.host=localhost # Artemis 代理主机。
    spring.artemis.mode= # Artemis 部署模式，默认情况下自动检测。
    spring.artemis.password= # 代理的登录密码。
    spring.artemis.pool.block-if-full=true # 请求连接且池已满时是否阻塞。设置为 false 则抛出“JMSException”。
    spring.artemis.pool.block-if-full-timeout=-1ms # 池仍然满的情况下抛出异常之前的阻塞周期。
    spring.artemis.pool.enabled=false # 是否应创建 JmsPoolConnectionFactory，而不是常规的 ConnectionFactory。
    spring.artemis.pool.idle-timeout=30s # 连接空闲超时。
    spring.artemis.pool.max-connections=1 # 最大池连接数。
    spring.artemis.pool.max-sessions-per-connection=500 # 池中每个连接的最大池会话数。
    spring.artemis.pool.time-between-expiration-check=-1ms # 空闲连接回收线程运行之间的休眠时间。如果为负，则不运行空闲连接收回线程。
    spring.artemis.pool.use-anonymous-producers=true # 是否只使用一个匿名的“MessageProducer”实例。将其设置为 false 可在每次需要时创建一个“MessageProducer”。
    spring.artemis.port=61616 # Artemis 代理端口。
    spring.artemis.user= # 代理的登录用户。
    
    # SPRING BATCH (BatchProperties)
    spring.batch.initialize-schema=embedded # 数据库 schema 初始化模式。
    spring.batch.job.enabled=true # 启动时在上下文中执行所有 Spring Batch 作业。
    spring.batch.job.names= # 启动时要执行的以逗号分隔的作业名称列表（例如，“job1，job2”）。默认情况下，将执行在上下文中找到的所有作业。
    spring.batch.schema=classpath:org/springframework/batch/core/schema-@@platform@@.sql # 用于初始化数据库 schema 的 SQL 文件的路径。
    spring.batch.table-prefix= # 所有批处理元数据表的表前缀。
    
    # SPRING INTEGRATION (IntegrationProperties)
    spring.integration.jdbc.initialize-schema=embedded # 数据库 schema 初始化模式。
    spring.integration.jdbc.schema=classpath:org/springframework/integration/jdbc/schema-@@platform@@.sql # 用于初始化数据库 schema 的 SQL 文件的路径。
    
    # JMS (JmsProperties)
    spring.jms.cache.consumers=false # 是否缓存消息消费者。
    spring.jms.cache.enabled=true # 是否缓存会话。
    spring.jms.cache.producers=true # 是否缓存消息生产者。
    spring.jms.cache.session-cache-size=1 # 会话缓存大小（每个 JMS 会话类型）。
    spring.jms.jndi-name= # 连接工厂 JNDI 名称。设置后，优先于其他连接工厂自动配置。
    spring.jms.listener.acknowledge-mode= # 容器的确认模式。默认情况下，监听器通过自动确认进行处理。
    spring.jms.listener.auto-startup=true # 启动时自动启动容器。
    spring.jms.listener.concurrency= # 最小并发消费者数。
    spring.jms.listener.max-concurrency= # 最大并发消费者数。
    spring.jms.pub-sub-domain=false # 默认目标类型是否为 topic。
    spring.jms.template.default-destination= # 在没有目标参数的发送和接收操作中使用的默认目标。
    spring.jms.template.delivery-delay= # 用于发送调用的传递延迟。
    spring.jms.template.delivery-mode= # 传递模式。设置时启用 QoS（服务质量）。
    spring.jms.template.priority= # 发送消息时的优先级。设置时启用 QoS（服务质量）。
    spring.jms.template.qos-enabled= # 发送消息时是否启用显式 QoS（服务质量）。
    spring.jms.template.receive-timeout= # 用于接收调用的超时。
    spring.jms.template.time-to-live= # 发送时消息的生存时间。设置时启用 QoS（服务质量）。
    
    # APACHE KAFKA (KafkaProperties)
    spring.kafka.admin.client-id= # 在发出请求时传递给服务器的 ID。用于服务器端日志记录。
    spring.kafka.admin.fail-fast=false # 如果启动时代理不可用，是否快速失败。
    spring.kafka.admin.properties.*= # 用于配置客户端的其他特定于管理员的属性。
    spring.kafka.admin.ssl.key-password= # 密钥存储文件中私钥的密码。
    spring.kafka.admin.ssl.key-store-location= # 密钥存储文件的位置。
    spring.kafka.admin.ssl.key-store-password= # 密钥存储文件的存储密码。
    spring.kafka.admin.ssl.key-store-type= # 密钥存储的类型。
    spring.kafka.admin.ssl.protocol= # 要使用的 SSL 协议。
    spring.kafka.admin.ssl.trust-store-location= # 信任存储文件的位置。
    spring.kafka.admin.ssl.trust-store-password= # 信任存储文件的存储密码。
    spring.kafka.admin.ssl.trust-store-type= # 信任存储的类型。
    spring.kafka.bootstrap-servers= # 逗号分隔的 host:port 对列表，用于建立到 Kafka 集群的初始连接。应用于所有组件，除非重写。
    spring.kafka.client-id= # 在发出请求时传递给服务器的 ID。用于服务器端日志记录。
    spring.kafka.consumer.auto-commit-interval= # 如果“enable.auto.commit”设置为 true，则消费者偏移自动提交到 Kafka 的频率。
    spring.kafka.consumer.auto-offset-reset= # 当 Kafka 中没有初始偏移或当前偏移在服务器上不再存在时，该怎么办。
    spring.kafka.consumer.bootstrap-servers= # 逗号分隔的 host:port 对列表，用于建立到 Kafka 集群的初始连接。覆盖消费者的全局属性。
    spring.kafka.consumer.client-id= # 在发出请求时传递给服务器的 ID。
    spring.kafka.consumer.enable-auto-commit= # 消费者的偏移量是否定期在后台提交。
    spring.kafka.consumer.fetch-max-wait= # 如果没有足够的数据立即满足“fetch min size”给定的要求，则服务器在回答获取请求之前阻塞的最大时间量。
    spring.kafka.consumer.fetch-min-size= # 服务器应为获取请求返回的最小数据量。
    spring.kafka.consumer.group-id= # 标识此消费者所属的消费者组的唯一字符串。
    spring.kafka.consumer.heartbeat-interval= # 消费者协调器心跳之间的预期时间。
    spring.kafka.consumer.key-deserializer= # 键的反序列化器类。
    spring.kafka.consumer.max-poll-records= # 在一次 poll() 调用中返回的最大记录数。
    spring.kafka.consumer.properties.*= # 用于配置客户端的其他特定于消费者的属性。
    spring.kafka.consumer.ssl.key-password= # 密钥存储文件中私钥的密码。
    spring.kafka.consumer.ssl.key-store-location= # 密钥存储文件的位置。
    spring.kafka.consumer.ssl.key-store-password= # 密钥存储文件的存储密码。
    spring.kafka.consumer.ssl.key-store-type= # 密钥存储的类型。
    spring.kafka.consumer.ssl.protocol= # 要使用的 SSL 协议。
    spring.kafka.consumer.ssl.trust-store-location= # 信任存储文件的位置。
    spring.kafka.consumer.ssl.trust-store-password= # 信任存储文件的存储密码。
    spring.kafka.consumer.ssl.trust-store-type= # 信任存储的类型。
    spring.kafka.consumer.value-deserializer= # 值的反序列化器类。
    spring.kafka.jaas.control-flag=required # 登录配置的控制标志。
    spring.kafka.jaas.enabled=false # 是否启用 JAAS 配置。
    spring.kafka.jaas.login-module=com.sun.security.auth.module.Krb5LoginModule # 登录模块。
    spring.kafka.jaas.options= # 其他 JAAS 选项。
    spring.kafka.listener.ack-count= # 当 ackMode 为“COUNT”或“COUNT_TIME”时，偏移提交之间的记录数。
    spring.kafka.listener.ack-mode= # 监听器 AckMode。参见 spring-kafka 文档。
    spring.kafka.listener.ack-time= # 当 ackMode 为“TIME”或“COUNT_time”时，偏移提交之间的时间。
    spring.kafka.listener.client-id= # 监听器的消费者 client.id 属性的前缀。
    spring.kafka.listener.concurrency= # 要在监听器容器中运行的线程数。
    spring.kafka.listener.idle-event-interval= # 发布空闲消费者事件之间的时间（未接收到数据）。
    spring.kafka.listener.log-container-config= # 是否在初始化期间记录容器配置（INFO 级别）。
    spring.kafka.listener.monitor-interval= # 检查无响应消费者之间的时间。如果未指定持续时间后缀，将使用秒。
    spring.kafka.listener.no-poll-threshold= # Multiplier 应用于 “pollTimeout” 以确定消费者是否无响应。
    spring.kafka.listener.poll-timeout= # 轮询消费者时使用的超时。
    spring.kafka.listener.type=single # 监听器类型。
    spring.kafka.producer.acks= # 生产者要求 leader 在考虑请求完成之前收到的确认数。
    spring.kafka.producer.batch-size= # 默认批量大小。
    spring.kafka.producer.bootstrap-servers= # 逗号分隔的 host:port 对列表，用于建立到 Kafka 集群的初始连接。覆盖生产者的全局属性。
    spring.kafka.producer.buffer-memory= # 生产者可用于缓冲等待发送到服务器的记录的总内存大小。
    spring.kafka.producer.client-id= # 在发出请求时传递给服务器的 ID。用于服务器端日志记录。
    spring.kafka.producer.compression-type= # 生产者生成的所有数据的压缩类型。
    spring.kafka.producer.key-serializer= # 键的序列化器类。
    spring.kafka.producer.properties.*= # 用于配置客户端的其他特定于生产者的属性。
    spring.kafka.producer.retries= # 大于零时，启用失败发送的重试。
    spring.kafka.producer.ssl.key-password= # 密钥存储文件中私钥的密码。
    spring.kafka.producer.ssl.key-store-location= # 密钥存储文件的位置。
    spring.kafka.producer.ssl.key-store-password= # 密钥存储文件的存储密码。
    spring.kafka.producer.ssl.key-store-type= # 密钥存储的类型。
    spring.kafka.producer.ssl.protocol= # 要使用的 SSL 协议。
    spring.kafka.producer.ssl.trust-store-location= # 信任存储文件的位置。
    spring.kafka.producer.ssl.trust-store-password= # 信任存储文件的存储密码。
    spring.kafka.producer.ssl.trust-store-type= # 信任存储的类型。
    spring.kafka.producer.transaction-id-prefix= # 非空时，启用对生产者的事务支持。
    spring.kafka.producer.value-serializer= # 值的序列化器类。
    spring.kafka.properties.*= # 用于配置客户端的生产者和消费者共有的其他属性。
    spring.kafka.ssl.key-password= # 密钥存储文件中私钥的密码。
    spring.kafka.ssl.key-store-location= # 密钥存储文件的位置。
    spring.kafka.ssl.key-store-password= # 密钥存储文件的存储密码。
    spring.kafka.ssl.key-store-type= # 密钥存储的类型。
    spring.kafka.ssl.protocol= # 要使用的 SSL 协议。
    spring.kafka.ssl.trust-store-location= # 信任存储文件的位置。
    spring.kafka.ssl.trust-store-password= # 信任存储文件的存储密码。
    spring.kafka.ssl.trust-store-type= # 信任存储的类型。
    spring.kafka.streams.application-id= # Kafka 流的 application.id 属性；默认为 spring.application.name 的值。
    spring.kafka.streams.auto-startup=true # 是否自动启动流工厂 bean。
    spring.kafka.streams.bootstrap-servers= # 逗号分隔的 host:port 对列表，用于建立到 Kafka 集群的初始连接。覆盖流的全局属性。
    spring.kafka.streams.cache-max-size-buffering= # 用于跨所有线程缓冲的最大内存大小。
    spring.kafka.streams.client-id= # 在发出请求时传递给服务器的 ID。用于服务器端日志记录。
    spring.kafka.streams.properties.*= # 用于配置流的其他 Kafka 属性。
    spring.kafka.streams.replication-factor= # 流处理应用程序创建的更改日志主题和重新分区主题的复制因子。
    spring.kafka.streams.ssl.key-password= # 密钥存储文件中私钥的密码。
    spring.kafka.streams.ssl.key-store-location= # 密钥存储文件的位置。
    spring.kafka.streams.ssl.key-store-password= # 密钥存储文件的存储密码。
    spring.kafka.streams.ssl.key-store-type= # 密钥存储的类型。
    spring.kafka.streams.ssl.protocol= # 要使用的 SSL 协议。
    spring.kafka.streams.ssl.trust-store-location= # 信任存储文件的位置。
    spring.kafka.streams.ssl.trust-store-password= # 信任存储文件的存储密码。
    spring.kafka.streams.ssl.trust-store-type= # 信任存储的类型。
    spring.kafka.streams.state-dir= # 状态存储的目录位置。
    spring.kafka.template.default-topic= # 消息发送到的默认主题。
    
    # RABBIT (RabbitProperties)
    spring.rabbitmq.addresses= # 以逗号分隔的客户端应连接到的地址列表。
    spring.rabbitmq.cache.channel.checkout-timeout= # 达到缓存大小后等待获取通道的持续时间。
    spring.rabbitmq.cache.channel.size= # 要在缓存中保留的通道数。
    spring.rabbitmq.cache.connection.mode=channel # 连接工厂缓存模式。
    spring.rabbitmq.cache.connection.size= # 要缓存的连接数。
    spring.rabbitmq.connection-timeout= # 连接超时。将其设置为零，以便永远等待。
    spring.rabbitmq.dynamic=true # 是否创建 AmqpAdmin bean。
    spring.rabbitmq.host=localhost # RabbitMQ 主机。
    spring.rabbitmq.listener.direct.acknowledge-mode= # 容器的确认模式。
    spring.rabbitmq.listener.direct.auto-startup=true # 是否在启动时自动启动容器。
    spring.rabbitmq.listener.direct.consumers-per-queue= # 每个队列的消费者数量。
    spring.rabbitmq.listener.direct.default-requeue-rejected= # 默认情况下，被拒绝的传递是否重新排队。
    spring.rabbitmq.listener.direct.idle-event-interval= # 应发布空闲容器事件的频率。
    spring.rabbitmq.listener.direct.missing-queues-fatal=false # 当容器声明的队列在代理上不可用时，是否失败。
    spring.rabbitmq.listener.direct.prefetch= # 每个消费者可能未完成的未确认消息的最大数量。
    spring.rabbitmq.listener.direct.retry.enabled=false # 是否启用发布重试。
    spring.rabbitmq.listener.direct.retry.initial-interval=1000ms # 第一次和第二次尝试传递消息之间的持续时间。
    spring.rabbitmq.listener.direct.retry.max-attempts=3 # 传递消息的最大尝试次数。
    spring.rabbitmq.listener.direct.retry.max-interval=10000ms # 两次尝试之间的最大持续时间。
    spring.rabbitmq.listener.direct.retry.multiplier=1 # 要应用于上一个重试间隔的乘数。
    spring.rabbitmq.listener.direct.retry.stateless=true # 重试是无状态的还是有状态的。
    spring.rabbitmq.listener.simple.acknowledge-mode= # 容器的确认模式。
    spring.rabbitmq.listener.simple.auto-startup=true # 是否在启动时自动启动容器。
    spring.rabbitmq.listener.simple.concurrency= # 监听器调用程序线程的最小数目。
    spring.rabbitmq.listener.simple.default-requeue-rejected= # 默认情况下，拒绝的传递是否重新排队。
    spring.rabbitmq.listener.simple.idle-event-interval= # 应发布空闲容器事件的频率。
    spring.rabbitmq.listener.simple.max-concurrency= # 监听器调用程序线程的最大数目。
    spring.rabbitmq.listener.simple.missing-queues-fatal=true # 当容器声明的队列在代理上不可用时，是否失败，和/或在运行时删除一个或多个队列时，是否停止容器。
    spring.rabbitmq.listener.simple.prefetch= # 每个消费者可能未完成的未确认消息的最大数量。
    spring.rabbitmq.listener.simple.retry.enabled=false # 是否启用发布重试。
    spring.rabbitmq.listener.simple.retry.initial-interval=1000ms # 第一次和第二次尝试传递消息之间的持续时间。
    spring.rabbitmq.listener.simple.retry.max-attempts=3 # 传递消息的最大尝试次数。
    spring.rabbitmq.listener.simple.retry.max-interval=10000ms # 两次尝试之间的最大持续时间。
    spring.rabbitmq.listener.simple.retry.multiplier=1 # 要应用于上一个重试间隔的乘数。
    spring.rabbitmq.listener.simple.retry.stateless=true # 重试是无状态的还是有状态的。
    spring.rabbitmq.listener.simple.transaction-size= # 确认模式为 AUTO 时，ack 之间要处理的消息数。如果大于预取，则预取将增加到此值。
    spring.rabbitmq.listener.type=simple # 监听器容器类型。
    spring.rabbitmq.password=guest # 登录以对代理进行身份验证。
    spring.rabbitmq.port=5672 # RabbitMQ 端口。
    spring.rabbitmq.publisher-confirms=false # 是否启用发布者确认。
    spring.rabbitmq.publisher-returns=false # 是否启用发布者返回。
    spring.rabbitmq.requested-heartbeat= # 请求的心跳超时；零表示无。如果未指定持续时间后缀，则将使用秒。
    spring.rabbitmq.ssl.algorithm= # 要使用的 SSL 算法。默认情况下，由 Rabbit 客户端库配置。
    spring.rabbitmq.ssl.enabled=false # 是否启用 SSL 支持。
    spring.rabbitmq.ssl.key-store= # 持有 SSL 证书的密钥存储的路径。
    spring.rabbitmq.ssl.key-store-password= # 用于访问密钥存储的密码。
    spring.rabbitmq.ssl.key-store-type=PKCS12 # 密钥存储类型。
    spring.rabbitmq.ssl.trust-store= # 保存 SSL 证书的信任存储。
    spring.rabbitmq.ssl.trust-store-password= # 用于访问信任存储的密码。
    spring.rabbitmq.ssl.trust-store-type=JKS # 信任存储类型。
    spring.rabbitmq.ssl.validate-server-certificate=true # 是否启用服务器端证书验证。
    spring.rabbitmq.ssl.verify-hostname=true # 是否启用主机名验证。
    spring.rabbitmq.template.default-receive-queue= # 当没有明确指定时，要从中接收消息的默认队列的名称。
    spring.rabbitmq.template.exchange= # 用于发送操作的默认 exchange 的名称。
    spring.rabbitmq.template.mandatory= # 是否启用强制消息。
    spring.rabbitmq.template.receive-timeout= # receive() 操作的超时。
    spring.rabbitmq.template.reply-timeout= # sendAndReceive() 操作的超时。
    spring.rabbitmq.template.retry.enabled=false # 是否启用发布重试。
    spring.rabbitmq.template.retry.initial-interval=1000ms # 第一次和第二次尝试传递消息之间的持续时间。
    spring.rabbitmq.template.retry.max-attempts=3 # 传递消息的最大尝试次数。
    spring.rabbitmq.template.retry.max-interval=10000ms # 两次尝试之间的最大持续时间。
    spring.rabbitmq.template.retry.multiplier=1 # 要应用于上一个重试间隔的乘数。
    spring.rabbitmq.template.routing-key= # 用于发送操作的默认路由键的值。
    spring.rabbitmq.username=guest # 向代理进行身份验证的登录用户。
    spring.rabbitmq.virtual-host= # 连接到代理时要使用的虚拟主机。
    
    # ----------------------------------------
    # ACTUATOR PROPERTIES
    # ----------------------------------------
    
    # MANAGEMENT HTTP SERVER (ManagementServerProperties)
    management.server.add-application-context-header=false # 在每个响应中添加“X-application-context”HTTP 头。
    management.server.address= # 管理端点应绑定到的网络地址。需要自定义 management.server.port。
    management.server.port= # 管理端点 HTTP 端口（默认情况下使用与应用程序相同的端口）。配置其他端口以使用特定于管理的 SSL。
    management.server.servlet.context-path= # 管理端点 context-path（例如，/management）。需要自定义 management.server.port。
    management.server.ssl.ciphers= # 支持的 SSL 密码。
    management.server.ssl.client-auth= # 客户端身份验证模式。
    management.server.ssl.enabled=true # 是否启用 SSL 支持。
    management.server.ssl.enabled-protocols= # 启用 SSL 协议。
    management.server.ssl.key-alias= # 标识密钥存储中密钥的别名。
    management.server.ssl.key-password= # 用于访问密钥存储区中的密钥的密码。
    management.server.ssl.key-store= # 保存 SSL 证书的密钥存储的路径（通常是 jks 文件）。
    management.server.ssl.key-store-password= # 用于访问密钥存储的密码。
    management.server.ssl.key-store-provider= # 密钥存储的提供者。
    management.server.ssl.key-store-type= # 密钥存储的类型。
    management.server.ssl.protocol=TLS # 要使用的 SSL 协议。
    management.server.ssl.trust-store= # 持有 SSL 证书的信任存储。
    management.server.ssl.trust-store-password= # 用于访问信任存储的密码。
    management.server.ssl.trust-store-provider= # 信任存储的提供者。
    management.server.ssl.trust-store-type= # 信任存储的类型。
    
    # CLOUDFOUNDRY
    management.cloudfoundry.enabled=true # 是否启用扩展的 Cloud Foundry actuator 端点。
    management.cloudfoundry.skip-ssl-validation=false # 是否跳过 Cloud Foundry actuator 端点安全调用的 SSL 验证。
    
    # ENDPOINTS GENERAL CONFIGURATION
    management.endpoints.enabled-by-default= # 是否在默认情况下启用或禁用所有端点。
    
    # ENDPOINTS JMX CONFIGURATION (JmxEndpointProperties)
    management.endpoints.jmx.domain=org.springframework.boot # 端点 JMX 域名。如果未设置，回退到  spring.jmx.default-domain。
    management.endpoints.jmx.exposure.include=* # 应包含的端点 ID， 或 * 为所有。
    management.endpoints.jmx.exposure.exclude= # 应排除的端点 ID， 或 * 为所有。
    management.endpoints.jmx.static-names= # 附加到表示端点的 MBeans 的所有 ObjectName 的其他静态属性。
    
    # ENDPOINTS WEB CONFIGURATION (WebEndpointProperties)
    management.endpoints.web.exposure.include=health,info # 应包含的端点 ID， 或 * 为所有。
    management.endpoints.web.exposure.exclude= # 应排除的端点 ID， 或 * 为所有。
    management.endpoints.web.base-path=/actuator # Web 端点的基本路径。相对于 server.servlet.context-path，或如果 management.server.port 已配置，则相对于 management.server.servlet.context-path。
    management.endpoints.web.path-mapping= # 端点 ID 与应公开它们的路径之间的映射。
    
    # ENDPOINTS CORS CONFIGURATION (CorsEndpointProperties)
    management.endpoints.web.cors.allow-credentials= # 是否支持凭据。未设置时，不支持凭据。
    management.endpoints.web.cors.allowed-headers= # 请求中允许的以逗号分隔的头列表。* 允许所有头。
    management.endpoints.web.cors.allowed-methods= # 允许的逗号分隔的方法列表。* 允许所有方法。未设置时，默认为 GET。
    management.endpoints.web.cors.allowed-origins= # 允许的以逗号分隔的源列表。* 允许所有来源。未设置时，禁用 CORS 支持。
    management.endpoints.web.cors.exposed-headers= # 要包含在响应中的以逗号分隔的头列表。
    management.endpoints.web.cors.max-age=1800s # 客户端可以缓存来自飞行前请求的响应的时间。如果未指定持续时间后缀，则将使用秒。
    
    # AUDIT EVENTS ENDPOINT (AuditEventsEndpoint)
    management.endpoint.auditevents.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.auditevents.enabled=true # 是否启用 auditevents 端点。
    
    # BEANS ENDPOINT (BeansEndpoint)
    management.endpoint.beans.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.beans.enabled=true # 是否启用 bean 端点。
    
    # CACHES ENDPOINT (CachesEndpoint)
    management.endpoint.caches.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.caches.enabled=true # 是否启用缓存端点。
    
    # CONDITIONS REPORT ENDPOINT (ConditionsReportEndpoint)
    management.endpoint.conditions.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.conditions.enabled=true # 是否启用条件端点。
    
    # CONFIGURATION PROPERTIES REPORT ENDPOINT (ConfigurationPropertiesReportEndpoint, ConfigurationPropertiesReportEndpointProperties)
    management.endpoint.configprops.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.configprops.enabled=true # 是否启用 configprops 终结点。
    management.endpoint.configprops.keys-to-sanitize=password,secret,key,token,.*credentials.*,vcap_services,sun.java.command # 应该净化的键。键可以是属性结尾的简单字符串或正则表达式。
    
    # ENVIRONMENT ENDPOINT (EnvironmentEndpoint, EnvironmentEndpointProperties)
    management.endpoint.env.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.env.enabled=true # 是否启用 env 端点。
    management.endpoint.env.keys-to-sanitize=password,secret,key,token,.*credentials.*,vcap_services,sun.java.command # 应该净化的键。键可以是属性结尾的简单字符串或正则表达式。
    
    # FLYWAY ENDPOINT (FlywayEndpoint)
    management.endpoint.flyway.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.flyway.enabled=true # 是否启用 flyway 端点。
    
    # HEALTH ENDPOINT (HealthEndpoint, HealthEndpointProperties)
    management.endpoint.health.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.health.enabled=true # 是否启用 health 端点。
    management.endpoint.health.roles= # 用于确定用户是否被授权显示详细信息的角色。当为空时，所有经过身份验证的用户都将被授权。
    management.endpoint.health.show-details=never # 何时显示完整的健康详细信息。
    
    # HEAP DUMP ENDPOINT (HeapDumpWebEndpoint)
    management.endpoint.heapdump.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.heapdump.enabled=true # 是否启用 heapdump 端点。
    
    # HTTP TRACE ENDPOINT (HttpTraceEndpoint)
    management.endpoint.httptrace.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.httptrace.enabled=true # 是否启用 httptrace 端点。
    
    # INFO ENDPOINT (InfoEndpoint)
    info= # Arbitrary properties to add to the info endpoint.
    management.endpoint.info.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.info.enabled=true # 是否启用 info 端点。
    
    # INTEGRATION GRAPH ENDPOINT (IntegrationGraphEndpoint)
    management.endpoint.integrationgraph.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.integrationgraph.enabled=true # 是否启用 integrationgraph 端点。
    
    # JOLOKIA ENDPOINT (JolokiaProperties)
    management.endpoint.jolokia.config.*= # Jolokia 设置。有关更多详细信息，请参阅 Jolokia 的文档。
    management.endpoint.jolokia.enabled=true # 是否启用 jolokia 端点。
    
    # LIQUIBASE ENDPOINT (LiquibaseEndpoint)
    management.endpoint.liquibase.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.liquibase.enabled=true # 是否启用 liquibase 端点。
    
    # LOG FILE ENDPOINT (LogFileWebEndpoint, LogFileWebEndpointProperties)
    management.endpoint.logfile.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.logfile.enabled=true # 是否启用 logfile 端点。
    management.endpoint.logfile.external-file= # 要访问的外部的 Logfile。如果日志文件是通过输出重定向而不是日志系统本身写入的，则可以使用。
    
    # LOGGERS ENDPOINT (LoggersEndpoint)
    management.endpoint.loggers.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.loggers.enabled=true # 是否启用 loggers 端点。
    
    # REQUEST MAPPING ENDPOINT (MappingsEndpoint)
    management.endpoint.mappings.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.mappings.enabled=true # 是否启用 mappings 端点。
    
    # METRICS ENDPOINT (MetricsEndpoint)
    management.endpoint.metrics.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.metrics.enabled=true # 是否启用 metrics 端点。
    
    # PROMETHEUS ENDPOINT (PrometheusScrapeEndpoint)
    management.endpoint.prometheus.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.prometheus.enabled=true # 是否启用 prometheus 端点。
    
    # SCHEDULED TASKS ENDPOINT (ScheduledTasksEndpoint)
    management.endpoint.scheduledtasks.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.scheduledtasks.enabled=true # 是否启用 scheduledtasks 端点。
    
    # SESSIONS ENDPOINT (SessionsEndpoint)
    management.endpoint.sessions.enabled=true # 是否启用 sessions 端点。
    
    # SHUTDOWN ENDPOINT (ShutdownEndpoint)
    management.endpoint.shutdown.enabled=false # 是否启用 shutdown 端点。
    
    # THREAD DUMP ENDPOINT (ThreadDumpEndpoint)
    management.endpoint.threaddump.cache.time-to-live=0ms # 可缓存响应的最长时间。
    management.endpoint.threaddump.enabled=true # 是否启用 threaddump 端点。
    
    # HEALTH INDICATORS
    management.health.db.enabled=true # 是否启用数据库运行状况检查。
    management.health.cassandra.enabled=true # 是否启用 Cassandra 运行状况检查。
    management.health.couchbase.enabled=true # 是否启用 Couchbase 运行状况检查。
    management.health.defaults.enabled=true # 是否启用默认运行状况指示器。
    management.health.diskspace.enabled=true # 是否启用磁盘空间运行状况检查。
    management.health.diskspace.path= # 用于计算可用磁盘空间的路径。
    management.health.diskspace.threshold=10MB # 应该可用的最小磁盘空间。
    management.health.elasticsearch.enabled=true # 是否启用 Elasticsearch 运行状况检查。
    management.health.elasticsearch.indices= # 逗号分隔的索引名列表。
    management.health.elasticsearch.response-timeout=100ms # 等待群集响应的时间。
    management.health.influxdb.enabled=true # 是否启用 InfluxDB 运行状况检查。
    management.health.jms.enabled=true # 是否启用 JMS 运行状况检查。
    management.health.ldap.enabled=true # 是否启用 LDAP 运行状况检查。
    management.health.mail.enabled=true # 是否启用 Mail 运行状况检查。
    management.health.mongo.enabled=true # 是否启用 MongoDB 运行状况检查。
    management.health.neo4j.enabled=true # 是否启用 Neo4j 运行状况检查。
    management.health.rabbit.enabled=true # 是否启用 RabbitMQ 运行状况检查。
    management.health.redis.enabled=true # 是否启用 Redis 运行状况检查。
    management.health.solr.enabled=true # 是否启用 Solr 运行状况检查。
    management.health.status.http-mapping= # 运行状况到 HTTP 状态代码的映射。默认情况下，已注册的运行状况状态映射到合理的默认值（例如，UP 映射到 200）。
    management.health.status.order=DOWN,OUT_OF_SERVICE,UP,UNKNOWN # 按严重程度排列的以逗号分隔的运行状况列表。
    
    # HTTP TRACING (HttpTraceProperties)
    management.trace.http.enabled=true # 是否启用 HTTP 请求-响应跟踪。
    management.trace.http.include=request-headers,response-headers,cookies,errors # 要包含在跟踪中的项。
    
    # INFO CONTRIBUTORS (InfoContributorProperties)
    management.info.build.enabled=true # 是否启用构建信息。
    management.info.defaults.enabled=true # 是否启用默认信息贡献者。
    management.info.env.enabled=true # 是否启用环境信息。
    management.info.git.enabled=true # 是否启用 git 信息。
    management.info.git.mode=simple # 用于公开 git 信息的模式。
    
    # METRICS
    management.metrics.distribution.maximum-expected-value.*= # 期望观察到的以指定名称开头的度量 ID 的最大值。
    management.metrics.distribution.minimum-expected-value.*= # 期望观察到的以指定名称开头的度量 ID 的最小值。
    management.metrics.distribution.percentiles.*= # 要发送到以指定名称开头的度量 ID 后端的特定计算的不可聚合百分位数。
    management.metrics.distribution.percentiles-histogram.*= # 以指定名称开头的度量 ID 是否应发布百分比直方图。
    management.metrics.distribution.sla.*= # 以指定名称开头的度量 ID 的特定 SLA 边界。最长匹配获胜。
    management.metrics.enable.*= # 是否应启用以指定名称开头的度量 ID。最长匹配获胜，键“all”也可用于配置所有度量。
    management.metrics.export.appoptics.api-token= # AppOptics API 令牌。
    management.metrics.export.appoptics.batch-size=500 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.appoptics.connect-timeout=5s # 此后端请求的连接超时。
    management.metrics.export.appoptics.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.appoptics.host-tag=instance # 将度量发送到 AppOptics 时映射到“@host”的标签。
    management.metrics.export.appoptics.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.appoptics.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.appoptics.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.appoptics.uri=https://api.appoptics.com/v1/measurements # 要将度量发送到的 URI。
    management.metrics.export.atlas.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.atlas.config-refresh-frequency=10s # 从 LWC 服务刷新配置设置的频率。
    management.metrics.export.atlas.config-time-to-live=150s # LWC 服务订阅的生存时间。
    management.metrics.export.atlas.config-uri=http://localhost:7101/lwc/api/v1/expressions/local-dev # 用于 Atlas LWC 端点检索当前订阅的 URI。
    management.metrics.export.atlas.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.atlas.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.atlas.eval-uri=http://localhost:7101/lwc/api/v1/evaluate # 用于 Atlas LWC 端点评估订阅数据的 URI。
    management.metrics.export.atlas.lwc-enabled=false # 是否启用到 Atlas LWC 的流。
    management.metrics.export.atlas.meter-time-to-live=15m # 没有任何活动的仪表的生存时间。在此期间之后，仪表将被视为过期，不会被报告。
    management.metrics.export.atlas.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.atlas.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.atlas.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.atlas.uri=http://localhost:7101/api/v1/publish # Atlas 服务器的 URI。
    management.metrics.export.datadog.api-key= # Datadog API 秘钥。
    management.metrics.export.datadog.application-key= # Datadog 应用程序密钥。不严格要求，但通过向 Datadog 发送仪表说明、类型和基本单位来改进 Datadog 体验。
    management.metrics.export.datadog.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.datadog.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.datadog.descriptions=true # 是否将描述元数据发布到 Datadog。关闭此选项可最小化发送的元数据量。
    management.metrics.export.datadog.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.datadog.host-tag=instance # 将度量发送到 Datadog 时映射到“@host”的标签。
    management.metrics.export.datadog.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.datadog.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.datadog.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.datadog.uri=https://app.datadoghq.com # 要将度量发送到的 URI。如果你需要将度量发布到路由到 Datadog 的内部代理，你可以使用此项定义代理的位置。
    management.metrics.export.dynatrace.api-token= # Dynatrace 身份验证令牌。
    management.metrics.export.dynatrace.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.dynatrace.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.dynatrace.device-id= # 将度量导出到 Dynatrace 的自定义设备的 ID。
    management.metrics.export.dynatrace.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.dynatrace.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.dynatrace.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.dynatrace.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.dynatrace.technology-type=java # 导出度量的技术类型。用于在 Dynatrace UI 中使用逻辑技术名称对度量进行分组。
    management.metrics.export.dynatrace.uri= # 要将度量发送到的 URI。应用于 SaaS、自管理实例或通过内部代理进行路由。
    management.metrics.export.elastic.auto-create-index=true # 如果索引不存在，是否自动创建它。
    management.metrics.export.elastic.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.elastic.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.elastic.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.elastic.host=http://localhost:9200 # 要将度量导出到的主机。
    management.metrics.export.elastic.index=metrics # 要将度量导出到的度量的索引。
    management.metrics.export.elastic.index-date-format=yyyy-MM # 用于滚动索引的索引日期格式。附加到索引名称，前面有一个 “-”。
    management.metrics.export.elastic.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.elastic.password= # Elastic 服务器的登录密码。
    management.metrics.export.elastic.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.elastic.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.elastic.timestamp-field-name=@timestamp # timestamp 字段的名称。
    management.metrics.export.elastic.user-name= # Elastic 服务器的登录用户。
    management.metrics.export.ganglia.addressing-mode=multicast # UDP 寻址模式，单播或多播。
    management.metrics.export.ganglia.duration-units=milliseconds # 用于报告持续时间的基本时间单位。
    management.metrics.export.ganglia.enabled=true # 是否启用将度量导出到 Ganglia。
    management.metrics.export.ganglia.host=localhost # 要接收导出度量的 Ganglia 服务器的主机。
    management.metrics.export.ganglia.port=8649 # 要接收导出度量的 Ganglia 服务器的端口。
    management.metrics.export.ganglia.protocol-version=3.1 # Ganglia 协议版本。必须是 3.1 或 3.0。
    management.metrics.export.ganglia.rate-units=seconds # 用于报告速率的基本时间单位。
    management.metrics.export.ganglia.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.ganglia.time-to-live=1 # Ganglia 上度量的生存时间。将多播 Time-To-Live 设置为大于主机之间的跳数（路由器）。
    management.metrics.export.graphite.duration-units=milliseconds # 用于报告持续时间的基本时间单位。
    management.metrics.export.graphite.enabled=true # 是否启用将度量导出到 Graphite。
    management.metrics.export.graphite.host=localhost # 要接收导出度量的 Graphite 服务器的主机。
    management.metrics.export.graphite.port=2004 # 要接收导出度量的 Graphite 服务器的端口。
    management.metrics.export.graphite.protocol=pickled # 将数据传送到 Graphite 时要使用的协议。
    management.metrics.export.graphite.rate-units=seconds # 用于报告速率的基本时间单位。
    management.metrics.export.graphite.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.graphite.tags-as-prefix= # 对于默认命名约定，将指定的标记键转换为度量前缀的一部分。
    management.metrics.export.humio.api-token= # Humio API 令牌。
    management.metrics.export.humio.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.humio.connect-timeout=5s # 此后端请求的连接超时。
    management.metrics.export.humio.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.humio.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.humio.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.humio.repository=sandbox # 要将度量发布到的存储库的名称。
    management.metrics.export.humio.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.humio.tags.*= # 描述将存储度量的数据源的 Humio 标签。Humio 标签是一个不同于 Micrometer 标签的概念。Micrometer 标签用于沿尺寸边界划分度量。
    management.metrics.export.humio.uri=https://cloud.humio.com # 要将度量发送到的 URI。如果你需要将度量发布到路由到 Humio 的内部代理，你可以使用此项定义代理的位置。
    management.metrics.export.influx.auto-create-db=true # 在尝试向其发布度量之前，如果 Influx 数据库不存在，是否创建该数据库。
    management.metrics.export.influx.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.influx.compressed=true # 是否启用发布到 Influx 的度量批的 GZIP 压缩。
    management.metrics.export.influx.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.influx.consistency=one # 写下每一点的一致性。
    management.metrics.export.influx.db=mydb # 将度量发送到 Influx 时映射到“@host”的标签。
    management.metrics.export.influx.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.influx.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.influx.password= # Influx 服务器的登录密码。
    management.metrics.export.influx.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.influx.retention-duration= # Influx 应在当前数据库中保留数据的时间段。
    management.metrics.export.influx.retention-shard-duration= # 分片组覆盖的时间范围。
    management.metrics.export.influx.retention-policy= # 要使用的保留策略（如果未指定，则 Influx 写入 DEFAULT 保留策略）。
    management.metrics.export.influx.retention-replication-factor= # 集群中存储了多少数据副本。
    management.metrics.export.influx.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.influx.uri=http://localhost:8086 # Influx 服务器的 URI。
    management.metrics.export.influx.user-name= # Influx 服务器的登录用户。
    management.metrics.export.jmx.domain=metrics # 度量 JMX 域名。
    management.metrics.export.jmx.enabled=true # 是否启用将度量导出到 JMX。
    management.metrics.export.jmx.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.kairos.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.kairos.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.kairos.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.kairos.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.kairos.password= # KairosDB 服务器的登录密码。
    management.metrics.export.kairos.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.kairos.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.kairos.uri= localhost:8080/api/v1/datapoints # KairosDB 服务器的 URI。
    management.metrics.export.kairos.user-name= # KairosDB 服务器的登录用户。
    management.metrics.export.newrelic.account-id= # New Relic 账户 ID。
    management.metrics.export.newrelic.api-key= # New Relic API 秘钥。
    management.metrics.export.newrelic.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.newrelic.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.newrelic.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.newrelic.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.newrelic.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.newrelic.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.newrelic.uri=https://insights-collector.newrelic.com # 要将度量发送到的 URI。
    management.metrics.export.prometheus.descriptions=true # 是否启用将描述作为 scrape 有效负载的一部分发布到 Prometheus。关闭此选项可最小化每次 scrape 上发送的元数据量。
    management.metrics.export.prometheus.enabled=true # 是否启用将度量导出到 Prometheus。
    management.metrics.export.prometheus.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.prometheus.pushgateway.base-url=localhost:9091 # Pushgateway 的基本 URL。
    management.metrics.export.prometheus.pushgateway.enabled=false # 通过 Prometheus Pushgateway 启用发布。
    management.metrics.export.prometheus.pushgateway.grouping-key= # 推送的度量的分组键。
    management.metrics.export.prometheus.pushgateway.job= # 此应用程序实例的作业标识符。
    management.metrics.export.prometheus.pushgateway.push-rate=1m # 推送度量的频率。
    management.metrics.export.prometheus.pushgateway.shutdown-operation= # 应在关机时执行的操作。
    management.metrics.export.signalfx.access-token= # SignalFX 访问令牌。
    management.metrics.export.signalfx.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.signalfx.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.signalfx.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.signalfx.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.signalfx.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.signalfx.source= # 唯一标识将度量发布到 SignalFx 的应用实例。默认为本地主机名。
    management.metrics.export.signalfx.step=10s # 要使用的步长（即报告频率）。
    management.metrics.export.signalfx.uri=https://ingest.signalfx.com # 要将度量发送到的 URI。
    management.metrics.export.simple.enabled=true # 在没有任何其他导出程序的情况下，是否启用将度量导出到内存后端。
    management.metrics.export.simple.mode=cumulative # 计数模式。
    management.metrics.export.simple.step=1m # 要使用的步长（即报告频率）。
    management.metrics.export.statsd.enabled=true # 是否启用将度量导出到 StatsD。
    management.metrics.export.statsd.flavor=datadog # 要使用的 StatsD 行协议。
    management.metrics.export.statsd.host=localhost # 要接收导出度量的 StatsD 服务器的主机。
    management.metrics.export.statsd.max-packet-length=1400 # 单个负载的总长度应保持在网络的 MTU 内。
    management.metrics.export.statsd.polling-frequency=10s # 仪表的轮询频率。轮询仪表时，将重新计算其值，如果该值已更改（或 publishUnchangedMeters 为 true），则将其发送到 StatsD 服务器。
    management.metrics.export.statsd.port=8125 # 要接收导出度量的 StatsD 服务器的端口。
    management.metrics.export.statsd.publish-unchanged-meters=true # 是否将未更改的仪表发送到 StatsD 服务器。
    management.metrics.export.wavefront.api-token= # 直接向 Wavefront API 主机发布度量时使用的 API 令牌。
    management.metrics.export.wavefront.batch-size=10000 # 用于此后端的每个请求的测量数。如果发现更多测量值，则将发出多个请求。
    management.metrics.export.wavefront.connect-timeout=1s # 此后端请求的连接超时。
    management.metrics.export.wavefront.enabled=true # 是否启用将度量导出到此后端。
    management.metrics.export.wavefront.global-prefix= # 在 Wavefront UI 中查看时，使用全局前缀将来自本应用程序白盒仪器的度量与来自其他 Wavefront 集成的度量分开。
    management.metrics.export.wavefront.num-threads=2 # 要与度量发布调度器一起使用的线程数。
    management.metrics.export.wavefront.read-timeout=10s # 此后端请求的读超时。
    management.metrics.export.wavefront.source= # 要发布到 Wavefront 的度量源的应用实例的唯一标识符。默认为本地主机名。
    management.metrics.export.wavefront.step=10s # 要使用的步长（即报告频率）。
    management.metrics.export.wavefront.uri=https://longboard.wavefront.com # 要将度量发送到的 URI。
    management.metrics.use-global-registry=true # 是否应将自动配置的 MeterRegistry 实现绑定到 Metrics 上的全局静态注册表。
    management.metrics.tags.*= # 应用于每个仪表的公用标签。
    management.metrics.web.client.max-uri-tags=100 # 允许的唯一 URI 标签值的最大数量。达到标签值的最大数量后，过滤器将拒绝具有附加标签值的度量。
    management.metrics.web.client.requests-metric-name=http.client.requests # 发送请求的度量名称。
    management.metrics.web.server.auto-time-requests=true # Spring MVC 、WebFlux 或 Jersey 处理的请求是否应该自动计时。
    management.metrics.web.server.max-uri-tags=100 # 允许的唯一 URI 标签值的最大数量。达到标签值的最大数量后，过滤器将拒绝具有附加标签值的度量。
    management.metrics.web.server.requests-metric-name=http.server.requests # 接收请求的度量的名称。
    
    # ----------------------------------------
    # DEVTOOLS PROPERTIES
    # ----------------------------------------
    
    # DEVTOOLS (DevToolsProperties)
    spring.devtools.add-properties=true # 是否启用开发属性默认值。
    spring.devtools.livereload.enabled=true # 是否启用与 livereload.com 兼容的服务器。
    spring.devtools.livereload.port=35729 # 服务器端口。
    spring.devtools.restart.additional-exclude= # 应该从触发完全重启中排除的附加模式。
    spring.devtools.restart.additional-paths= # 要监视更改的其他路径。
    spring.devtools.restart.enabled=true # 是否启用自动重启。
    spring.devtools.restart.exclude=META-INF/maven/**,META-INF/resources/**,resources/**,static/**,public/**,templates/**,**/*Test.class,**/*Tests.class,git.properties,META-INF/build-info.properties # 应该从触发完全重新启动中排除的模式。
    spring.devtools.restart.log-condition-evaluation-delta=true # 是否在重新启动时记录条件评估增量。
    spring.devtools.restart.poll-interval=1s # 在轮询类路径更改之间等待的时间量。
    spring.devtools.restart.quiet-period=400ms # 在重启被触发之前没有任何类路径更改所需的安静时间量。
    spring.devtools.restart.trigger-file= # 更改后触发重新启动检查的特定文件的名称。如果未指定，则任何类路径文件更改都会触发重新启动。
    
    # REMOTE DEVTOOLS (RemoteDevToolsProperties)
    spring.devtools.remote.context-path=/.~~spring-boot!~ # 用于处理远程连接的上下文路径。
    spring.devtools.remote.proxy.host= # 用于连接到远程应用程序的代理的主机。
    spring.devtools.remote.proxy.port= # 用于连接到远程应用程序的代理的端口。
    spring.devtools.remote.restart.enabled=true # 是否启用远程重启。
    spring.devtools.remote.secret= # 建立连接所需的共享密钥（启用远程支持所需的）。
    spring.devtools.remote.secret-header-name=X-AUTH-TOKEN # 用于传输共享密钥的 HTTP 头。
    
    
    # ----------------------------------------
    # TESTING PROPERTIES
    # ----------------------------------------
    
    spring.test.database.replace=any # 要替换的现有 DataSource 的类型。
    spring.test.mockmvc.print=default # MVC 打印选项。