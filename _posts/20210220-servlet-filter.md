---
title: Servlet 中的 Filter
original: true
date: 2021-02-20
updated: 2022-05-30
tags: 
  - Java
urlname: servlet-filter
description: 记录一些 Tomcat 容器中常见的 Filter 和作用.
---
记录一些 Tomcat 容器中常见的 Filter 和作用.
<!--more-->
在Web容器中, Filter的作用是在访问特定资源前或后执行一些操作, 比如判断是否允许访问. 

javax.servlet.Filter:
~~~ java
interface Filter {
    /*
    用于初始化过滤器, FilterConfig接口是过滤器的配置信息和初始配置参数的封装.
    */
    void init(FilterConfig filterConfig);

    /*
    执行过滤器的代码, 参数request, response为容器传入的请求与响应. 
    FilterChain为一个特殊接口, 用于提醒容器是否继续执行, 如果过滤器内调用了
    FilterChain.doFilter(), 意思是提醒容器执行下一个过滤器, 如果没有下一个过滤器则
    执行对应的Servlet方法或者资源.
    */
    void doFilter(ServletRequest request, ServletResponse response, FilterChain chain);

    /*
    在容器卸载Filter之前调用
    */
    void destroy();
}
~~~

在web.xml注册filter:
~~~ xml
<filter>
    <!-- 过滤器名 -->
    <filter-name>testFitler</filter-name>
    <!-- 过滤器类路径 -->
    <filter-class>org.test.TestFiter</filter-class>
    <!-- 初始化参数 -->
    <init-param>
        <param-name>word_file</param-name>
        <param-value>/WEB-INF/word.txt</param-value>
    </init-param>
</filter>
~~~

在web.xml映射filter:
~~~ xml
<filter-mapping>
    <!-- 过滤器名 -->
    <filter-name>testFilter</filter-name>
    <!-- 拦截路径 -->
    <url-pattern>/index.jsp</url-pattern>
    <!-- 对不同的调用方式进行拦截, 可以配置多个dispatcher -->
    <!-- REQUEST为默认值, 对进行拦截, 也就是一次请求. -->
    <!-- INCLUDE表示处理调用的Servlet包含另一个Servlet. 如jsp多页面include. -->
    <!-- FORWARD表示处理FORWARD转发的请求 -->
    <!-- ERROR对配置的<error-page>起作用 -->
    <dispatcher>REQUEST</dispatcher>
</filter-mapping>
~~~

通常应用都会配置多个Filter, 这些Filter组合起来就称之为FilterChain, Tomcat容器会根据Filter在web.xml文件中配置的顺序来决定先调用哪个. Filter会调用FilterChain.doFilter()来表示提示容器执行下一个过滤器, 如果是最后一个过滤器执行时容器会调用目标资源.

![](/picture/2021-02-20-12-35-51.png)

javax.servlet.GenericFilter对Filter获取配置或环境信息做了一个通用的实现.
javax.servlet.http.HttpFilter继承GenericFilter对请求和响应是否是http请求做了判断.

## Tomcat容器中的Filter

而Tomcat容器默认也提供了一些过滤器:
- AddDefaultCharsetFilter: HTTP规范指出如果没有指定"text"的字符集, 那么会默认使用"ISO-8859-1"字符集. 但有的浏览器会试图自动检测字符集, 这种行为容易被XSS.
- CorsFilter: 该过滤器是W3C的CORS(跨域资源共享)规范的实现, 这是一个跨域请求机制.
- CsrfPreventionFilter: 该过滤器通过产生一个随机数并将其存储在session和URL中, 接收到下一个请求时将随机数进行比较, 如果是相同的就允许继续请求.
- ExpiresFilter: 设置浏览器缓存.
- FailedRequestFilter: 触发检查解析请求的参数, 如果有参数解析失败, 那么就返回400.
- HttpHeaderSecurityFilter: 设置该头来提醒浏览器不要做怪异行为. (比如MIME类型嗅探)
- RemoteAddrFilter: 根据客户端IP地址来过滤.
- RemoteCIDRFilter: CIDR好像是一种网络的表示方法.
- RemoteHostFilter: 根据客户端主机名是否符合指定正则表达式.
- RemoteIpFilter: 获取原始客户端IP的过滤器.
- RequestDumperFilter: 从请求中获取详细信息, 方便调试.
- RestCsrfPreventionFilter: 提供基本的对REST API的CSRF保护.
- SessionInitializerFilter: 通过HttpServletRequest来初始化HttpSession, 在一些WebSocket项目上可能需要.
- SetCharacterEncodingFilter: 字符集编码过滤器, 通常在解析请求前使用来强制指定编码.
- WebdavFixFilter: 修复与微软的WebDAV客户端连接的过滤器.




参考:
[JAVAWEB开发之Servlet Filter（过滤器）详解包括post和get编码过滤器、URL访问权限控制、自动登录。以及装饰模式的使用
](https://blog.csdn.net/u013087513/article/details/56835894)
[filter中的dispatcher标签解析
](https://blog.csdn.net/xiaokang123456kao/article/details/72885171)
[tomcat容器提供的过滤器Filters【译】](https://blog.csdn.net/weixin_34307464/article/details/92591041)
[Tomcat常用的过滤器](https://www.cnblogs.com/jian0110/p/10512188.html)
[利用 HTTP Security Headers 提升站点安全性](https://blog.csdn.net/myle69/article/details/83747018)