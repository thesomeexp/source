---
title: Cookie 和 Session
original: true
date: 2021-03-16
updated: 2022-05-30
tags: 
  - Network
urlname: cookie-and-session
description: Cookie, Session, Token, SessionId 到底是什么?
---
Cookie, Session, Token, SessionId 到底是什么?
<!--more-->
# Cookie

由于HTTP协议是无状态的, 也就是两次HTTP请求之间是没有任何关联的, 服务器并不知道它上一次处理的请求, 和它下一次处理的请求是否是同一个用户所为, 可能是同一个用户发来的两次HTTP请求, 也可能是两个用户分别发来的HTTP请求.
人们为了服务器能分清HTTP请求是否是同一个用户发来的, 使用了不同的手段, 其中比较常用的就是使用Cookie.
以Tomcat服务器为例, 当用户1发送请求到服务器时, 服务器判断请求是否允许使用Cookie, 如果允许使用, 那么将会在请求头中返回"Set-Cookie:"+"key"+"value", 来提示浏览器将对应的"key"+"value"保存到浏览器的Cookie中, 保存成功后, 今后用户1的每一次对这个网站发送请求, 浏览器都会自动携带上保存的Cookie, 这时服务器根据Cookie中的内容, 就能知道这次HTTP请求是哪个用户了. (在Tomcat中, 默认的Cookie的key名是叫"JSESSIONID")

# SessionId

对于上面的例子, 我们把Cookie中key为"JSESSIONID"对应的值叫做SessionId. 不同的服务器给用户生成的SessionId不一样, 对应的key名也可能不一样.

# Session

Session也就是一次会话, 也就是用户从第一次进入网站, 到关闭了这个页面这段时间就算一次Session. 当然这是抽象的Session. 实际上服务器也有一个叫Session的, 服务器的Session是一块可以保存因会话产生的内容的地方, 它只存在服务器, 不会返回给客户端. 通常SessionId和Session是绑定的关系, 服务器根据SessionId来区分不同用户产生的存在Session中的信息.

# Token

Token通常是服务器颁发给用户端的凭证, 是一个更加抽象名词.

# Q&A

## 服务器颁发给客户端的SessionId是否会重复或冲突?

答: 是有可能生成重复的SessionId的, Tomcat8.5.23源代码中org.apache.catalina.session.ManagerBase的generateSessionId方法中, 使用了一个生成SessionId的while循环来确保生成的SessionId没有重复:

~~~ java
    /**
     * Generate and return a new session identifier.
     * @return a new session id
     */
    protected String generateSessionId() {

        String result = null;

        do {
            if (result != null) {
                // Not thread-safe but if one of multiple increments is lost
                // that is not a big deal since the fact that there was any
                // duplicate is a much bigger issue.
                duplicates++;
            }

            result = sessionIdGenerator.generateSessionId();

        } while (sessions.containsKey(result));

        return result;
    }
~~~

## 为什么可以把X-CSRF-Token放在Cookie来防止CSRF攻击?

答: 假如站点B想要攻击站点A, 虽然跨站攻击触发了站点A相关的请求, 请求中也携带了Cookie的所有内容(浏览器会自动携带), 但是站点B无法读取站点A中的Cookie的内容(浏览器的跨域防御策略). 所以站点A在防止CSRF攻击时, 对一些能够修改服务器资源的请求, 从Cookie中取出对应的X-CSRF-Token放在表单上, 服务器验证表单中的Token和Cookie中的Token是否相同, 如果不同则不是用户主动触发的请求, 如果相同才是合法的请求.