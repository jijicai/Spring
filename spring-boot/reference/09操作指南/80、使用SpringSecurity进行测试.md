# 80、使用 Spring Security 进行测试

Spring Security 为作为特定用户运行测试提供支持。例如，下面代码段中的测试将与具有 ADMIN 角色的经过身份验证的用户一起运行。
```
@Test
@WithMockUser(roles="ADMIN")
public void requestProtectedUrlWithUser() throws Exception {
    mvc
        .perform(get("/"))
        ...
}
```
Spring Security 提供了与 Spring MVC Test 的全面集成，这也可以在使用 @WebMvcTest 切片和 MockMvc 测试控制器时使用。

有关 Spring Security 测试支持的其他详细信息，请参阅 Spring Security 的[参考文档](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test)。