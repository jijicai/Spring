# 41、Quartz 调度器

Spring Boot 为使用 Quartz 调度程序提供了多种便利，包括 spring-boot-starter-quartz “Starter”。如果 Quartz 可用，则会自动配置 Scheduler (通过 SchedulerFactoryBean 抽象)。

以下类型的 bean 将自动获取并与 Scheduler 关联：

    （1）JobDetail：定义特定的 Job。可以使用 JobBuilder API 构建 JobDetail 实例。
    （2）Calendar
    （3）Trigger：定义何时触发特定 job。

默认情况下，使用内存中的 JobStore。但是，如果应用程序中有可用的 DataSource bean，并且相应地配置了 spring.quartz.job-store-type 属性，则可以配置基于 JDBC 的存储，如下面示例所示：

    spring.quartz.job-store-type=jdbc

使用 JDBC 存储时，可以在启动时初始化模式（schema），如下面示例所示：

    spring.quartz.jdbc.initialize-schema=always

警告：默认情况下，通过使用 Quartz 库提供的标准脚本检测和初始化数据库。这些脚本删除现有表，并在每次重新启动时删除所有触发器。还可以通过设置 spring.quartz.jdbc.schema 属性来提供自定义脚本。

要让 Quartz 使用应用程序主 DataSource 以外的 DataSource，请声明一个 DataSource  bean，用 @QuartzDataSource 注解其 @Bean 方法。这样做可以确保 SchedulerFactoryBean 和模式初始化都使用 Quartz 特定的 DataSource。

默认情况下，由配置创建的 job 不会覆盖已从持久 job 存储读取的已注册 job。要启用覆盖现有 job 定义，请设置 spring.quartz.overwrite-existing-jobs 属性。

Quartz 调度器配置可以使用 spring.quartz 属性和 SchedulerFactoryBeanCustomizer bean 进行自定义，后者允许编程方式的 SchedulerFactoryBean 自定义。高级 Quartz 配置属性可以使用 spring.quartz.properties.* 自定义。

注释：特别是，Executor bean 不与调度器相关联，因为 Quartz 提供了一种通过 spring.quartz.properties 配置调度器的方法。如果你需要自定义任务执行器，请考虑实现 SchedulerFactoryBeanCustomizer。

job 可以定义 setter 来注入数据映射属性。常规 bean 也可以以类似的方式注入，如下面示例所示：
```
public class SampleJob extends QuartzJobBean {

    private MyService myService;

    private String name;

    // Inject "MyService" bean
    public void setMyService(MyService myService) { ... }

    // Inject the "name" job data property
    public void setName(String name) { ... }

    @Override
    protected void executeInternal(JobExecutionContext context)
            throws JobExecutionException {
        ...
    }

}
```