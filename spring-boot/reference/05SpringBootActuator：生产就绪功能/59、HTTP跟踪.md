# 59、HTTP 跟踪

对所有 HTTP 请求自动启用跟踪。你可以查看 httptrace 端点并获取有关最近 100 个请求-响应交换的基本信息。

## 59.1、自定义 HTTP 跟踪

要自定义每个跟踪中包含的项，请使用 management.trace.http.include 配置属性。对于高级定制，请考虑注册你自己的 HttpExchangeTracer 实现。

默认情况下，使用 InMemoryHttpTraceRepository 存储最近 100 个请求-响应交换的跟踪。如果需要扩展容量，可以定义自己的 InMemoryHttpTraceRepository bean 实例。你还可以创建自己的替代的 HttpTraceRepository 实现。
