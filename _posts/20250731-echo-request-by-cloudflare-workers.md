---
title: 利用 Cloudflare Workers 来回显请求
date: 2025-07-31 13:00
updated: 2025-07-31 13:00
tags:
  - Dev
urlname: echo-request-by-cloudflare-workers
original: true
description: 边缘计算实现在线 Mock 小工具.
---
边缘计算实现在线 Mock 小工具.
<!--more-->
无需注册登陆, 基于 Cloudflare Workers 开发的 Serverless 小工具. 原理简单, 回显 url 携带的数据. 

Workers 免费配额 10w 次/日, 可部署到自己的 workers. 

路由: 

- /edit 为编辑内容静态页

- /echo 响应数据

<img src="https://github.com/thesomeexp/echo-url/raw/main/demo.gif" width="360">

工具链接: [echo.p2gg.com](https://echo.p2gg.com)

Github: [echo-url](https://github.com/thesomeexp/echo-url)