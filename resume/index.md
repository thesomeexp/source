---
title: Java 开发 简历
noindex: true
layout: resume
---
<div class="resume-photo">
    <img src="/picture/headshot.jpg" alt="个人照片">
</div>

**基本信息**
梁志荣 男 27岁 Java经验5年
- 电话: l3527832l34 邮箱: [zeawinl@gmail.com](mailto:zeawinl@gmail.com)/[@riseup.net](mailto:zeawinl@riseup.net)
- GitHub: [thesomeexp](https://github.com/thesomeexp) 简历: [p2gg.com/resume](https://p2gg.com/resume)

**技术能力** 
- 具有扎实的 Java 基础, 对常见的集合类 (HashMap) 的实现有一定的了解.
- 熟悉 Java 并发, 对 Executor 框架, 线程池, 锁, AQS 有一定的了解.
- 熟悉 JVM 的底层原理, 对运行时的数据区域, 对象的创建过程, 垃圾回收算法有一定的了解.
- 熟悉 Spring, SpringMVC, MyBatis, SpringBoot, SpringCloud, Dubbo 等主流开发框架的使用.
- 熟悉 MySQL, 对 InnoDB, MyISAM 存储引擎, 索引, binlog, redo log 日志有一定了解.
- 熟悉 Redis 常见数据结构, 内存淘汰机制, 持久化机制.
- 熟悉使用 Tomcat容器, Docker容器 配置和部署项目, 了解 Linux 基本命令.
- 了解 Elasticsearch, RabbitMQ 常见中间件的使用.

**教育经历**
- 2017.09-2021.06 岭南师范学院(全日制本科) 软件工程专业

**工作经历**
- 广州谷斯信息科技有限公司 / Java 开发 / 2023.08 - 2025.08
主要负责面向香港学校的 SaaS 平台 [GRWTH](https://grwth.hk) 的后端开发. 
- 广州趣米网络科技有限公司 / Java 开发 / 2020.08-2022.10
主要负责定制化营销项目: 抽奖、兑换、答题、活动管理系统等及公司自研项目的开发.

**项目经历**
**项目名称**: [赛马会成长「机」地](https://jclaoh.hk)
**项目简介**: 为香港学校、家长、学生及培训机构, 创造更多机会和成长空间, 促进学生的全面发展.
- 使用主流 Spring Cloud 构建分布式架构, Nacos 做注册中心, 负责对门户、后台及 APP 后端接口的开发. 
- 使用 Dubbo 实现微服务间高效通信, 结合 Seata 管理分布式事务, 确保服务调用一致性.
- 自研 RBAC 权限模块, 实现多角色菜单与数据权限隔离, 支持细粒度访问控制.
- 优化数据库索引与 SQL 查询结构, 大幅提升用户行为统计查询性能.
- 基于 RabbitMQ 实现高并发异步消息推送机制，支持失败重试、消息去重与多通道分发.
- 使用 EasyExcel 结合压缩工具, 开发数据批量导入与异步导出功能, 支持多报表合并及文件打包下载.
- 引入 Quartz / XXL-JOB 作为任务调度框架, 拆分大任务以提升批量数据汇出处理效率.

**项目名称**: [基于众包的"共享停车服务"软件](https://github.com/thesomeexp/ParkingProject)
**项目简介**: 利用众包采集停车场数据, 构建停车拥堵模型, 提供基于地理位置的智能停车场查询服务.
- 主导项目全栈开发, 负责整体架构设计与功能实现. 
- 前端采用 Vue.js + Element UI, 集成 高德地图 API 实现停车场信息的可视化展示与交互操作. 
- 后端设计为 RESTful 风格接口, 使用 Shiro + JWT 实现分布式环境下的安全认证与权限控制. 
- 使用 GeoHash 对停车场地理位置信息进行编码存储, Redis 缓存结果提升位置查询效率. 
- 代码风格遵循《阿里巴巴开发手册规范》开发, 参照《高性能 MySQL》对 SQL 语句做了优化. 

**其它** 
- 《基于众包的"共享停车服务"软件V1.0》著作权
- 了解常用密码学技术的原理. (对称密码, 公钥密码, 单向哈希函数, 消息认证码, 数字签名)
- 了解常见网络攻击 (XSS, CSRF, 重放攻击等). 
- 不懂就学, 懂得看官方文档.