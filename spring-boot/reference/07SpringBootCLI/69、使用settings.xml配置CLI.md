# 69、使用 settings.xml 配置 CLI

Spring Boot CLI 使用 Aether（Maven 的依赖项解析引擎）来解析依赖项。CLI 使用在 ~/.m2/settings.xml 中找到的 Maven 配置来配置 Aether。CLI 遵守以下配置设置：

    Offline
    Mirrors
    Servers
    Proxies
    Profiles
        Activation
        Repositories
    Active profiles

有关更多信息，请参见 [Maven 的设置文档](https://maven.apache.org/settings.html)。
