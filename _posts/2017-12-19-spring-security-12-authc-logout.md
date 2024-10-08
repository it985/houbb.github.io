---
layout: post
title:  Spring Security-12-Authentication logout 登出特性
date:  2017-12-19 22:29:09 +0800
categories: [Spring]
tags: [spring, spring-security, web-safe]
published: true
---

# Spring Security 系列

[Spring Security-01-Hello World](https://houbb.github.io/2017/12/19/spring-security-01-hello-world)

[Spring Security-02-springboot 入门使用实战](https://houbb.github.io/2017/12/19/spring-security-02-springboot)

[Spring Security-03-maven 整合使用](https://houbb.github.io/2017/12/19/spring-security-03-maven)

[Spring Security-04-密码加密详解及源码分析](https://houbb.github.io/2017/12/19/spring-security-04-passwordEncoder)

[Spring Security-05-CSRF 跨域攻击](https://houbb.github.io/2017/12/19/spring-security-05-csrf)

[Spring Security-06-安全响应头配置详解](https://houbb.github.io/2017/12/19/spring-security-06-security-response-headers)

[Spring Security-07-整体架构概览](https://houbb.github.io/2017/12/19/spring-security-07-big-picture)

[Spring Security-08-Authentication 认证详解](https://houbb.github.io/2017/12/19/spring-security-08-authc)

[Spring Security-09-Authentication session 管理](https://houbb.github.io/2017/12/19/spring-security-09-authc-session-management)

[Spring Security-10-Authentication 记住我特性实现](https://houbb.github.io/2017/12/19/spring-security-10-authc-remember-me)

[Spring Security-11-Authentication 匿名登录特性 & RunAS 以 xx 身份](https://houbb.github.io/2017/12/19/spring-security-11-authc-annoy)

[Spring Security-12-Authentication logout 登出特性](https://houbb.github.io/2017/12/19/spring-security-12-authc-logout)

[Spring Security-13-Authorization 授权](https://houbb.github.io/2017/12/19/spring-security-13-autha-overview)

[Spring Security-14-Authorization 使用FilterSecurityInterceptor授权HttpServletRequest](https://houbb.github.io/2017/12/19/spring-security-14-autha-servlet)

[Spring Security-15-Authorization 基于表达式的访问控制](https://houbb.github.io/2017/12/19/spring-security-15-expression)

[Spring Security-16-Authorization 安全对象实施](https://houbb.github.io/2017/12/19/spring-security-16-security-object)

[Spring Security-17-Authorization 方法安全](https://houbb.github.io/2017/12/19/spring-security-17-method-security)

[Spring Security-18-Authorization Domain Object Security (ACLs)](https://houbb.github.io/2017/12/19/spring-security-18-domain-object)

# 序言

这一节我们来学习一下 spring security 的整体架构设计。

# 处理注销

## 注销 Java 配置

使用WebSecurityConfigurerAdapter时，将自动应用注销功能。

默认是访问 URL/logout 将通过以下方式注销用户：

使HTTP会话无效

清理所有已配置的RememberMe身份验证

清除SecurityContextHolder

重定向到 `/login?logout`

但是，与配置登录功能相似，您还可以使用各种选项来进一步自定义注销要求：

- 配置

```java
protected void configure(HttpSecurity http) throws Exception {
    http
        .logout(logout -> logout                                                
            .logoutUrl("/my/logout")                                            
            .logoutSuccessUrl("/my/index")                                      
            .logoutSuccessHandler(logoutSuccessHandler)                         
            .invalidateHttpSession(true)                                        
            .addLogoutHandler(logoutHandler)                                    
            .deleteCookies(cookieNamesToClear)                                  
        )
        ...
}
```

# 注销XML配置

logout元素通过导航到特定URL来添加对注销的支持。 

默认注销URL是 `/logout`，但是您可以使用logout-url属性将其设置为其他名称。 

## 注销处理程序

通常，LogoutHandler实现指示能够参与注销处理的类。 预计将调用它们以执行必要的清理。 

因此，它们不应引发异常。 

提供了各种实现：

PersistentTokenBasedRememberMeServices

TokenBasedRememberMeServices

CookieClearingLogoutHandler

CsrfLogoutHandler

SecurityContextLogoutHandler

HeaderWriterLogoutHandler

有关详细信息，请参见“记住我的界面和实现”。

除了直接提供LogoutHandler实现之外，fluent API还提供了快捷方式，这些快捷方式在幕后提供了各自的LogoutHandler实现。 

例如。 deleteCookies（）允许指定在注销成功后要删除的一个或多个cookie的名称。 

与添加CookieClearingLogoutHandler相比，这是一种快捷方式。

## 注销成功处理程序

LogoutFilter成功注销后，将调用LogoutSuccessHandler来处理例如重定向或转发到适当的目的地。

请注意，该接口与LogoutHandler几乎相同，但可能会引发异常。

提供以下实现：

SimpleUrlLogoutSuccessHandler

HttpStatusReturningLogoutSuccessHandler

如上所述，您无需直接指定SimpleUrlLogoutSuccessHandler。相反，流利的API通过设置logoutSuccessUrl（）提供了快捷方式。这将在幕后设置SimpleUrlLogoutSuccessHandler。

提供的URL将在注销后重定向到，默认值为 `/login?logout`。

在REST API类型的场景中，HttpStatusReturningLogoutSuccessHandler可能很有趣。通过LogoutSuccessHandler，您不必提供在成功注销后重定向到URL的方法，而是可以提供要返回的纯HTTP状态代码。如果未配置，默认情况下将返回状态码200。

## 其他注销相关参考

注销处理

测试登出

HttpServletRequest.logout（）

记住我的接口和实现

在CSRF警告部分注销

单节注销（CAS协议）

Spring Security XML命名空间部分中注销元素的文档

# 小结

希望本文对你有所帮助，如果喜欢，欢迎点赞收藏转发一波。

我是老马，期待与你的下次相遇。

# 参考资料

[https://docs.spring.io/spring-security/site/docs/5.4.2/reference/html5/#features](https://docs.spring.io/spring-security/site/docs/5.4.2/reference/html5/#features)

* any list
{:toc}