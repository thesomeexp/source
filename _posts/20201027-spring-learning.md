---
title: Spring 的学习
original: false
date: 2020-10-27
updated: 2022-05-30
tags: 
  - Spring
  - Java
  - Note
urlname: spring-learning
description: 记录 Spring 的学习笔记
---
记录 Spring 的学习笔记
<!--more-->

# 1. Spring整体设计理念和整体架构

# 2. Spring Ioc

## 2.1 IOC是啥

IOC是(Inversion Of Control)的简写, 翻译过来叫控制反转, 是一种思想. 简单来说本来一些东西是你自己控制的, 而IOC控制反转的意思是控制权交给别人了, 不需要自己控制. 

# 3. Spring AOP

## 3.1 为什么会有AOP? 

如果你写过基于Spring或者Spring Boot相关的项目, 那你可能会遇到以下的问题. 
比如说: 

- 对于某些方法需要记录日志
- 对某些方法的执行前需要对用户进行权限判定

以上的这些功能, 如果直接写在业务代码中, 那么每个方法都会调用一次这些公用的方法. 

![各method都会调用log()方法](/picture/2020-10-29-11-39-46.png)

很明显这样显示调用的代码难以管理, 一旦我的log方法一变动, 或者说我有些方法不需要调用这个log()了, 删减method中的代码是一件麻烦事. 
这时候就可以考虑使用AOP了, AOP和OOP一样是一种观念, 全称为Aspect-oriented programming, 翻译过来叫面向切面编程. 它可以实现对这些重复代码单独抽取出来进行管理维护, 在需要时统一调用, 以及对于如何灵活使用这些公共代码提供了支持. 为了更好的使用AOP技术, 技术专家们是专门成立了一个AOP联盟来探讨AOP标准化, 因此你可以在AOP联盟文档中找到相关介绍. 

## 3.2 Spring AOP中的一些概念

这里直接拿[Spring文档中对AOP的介绍](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop)来讲一下. 

- Aspect 切面: Aspect是一种模块化的观念, 这种观念将横切经过多个类, 所以翻译过来就是切面. 在企业Java应用中, 事务管理是横切观念很好的例子. 在Spring AOP中, 切面可以通过基于配置文件(配置xml的方式), 或者基于@Aspect注解的类. (这里讲的非常抽象, 把它想成管理切面相关的的配置就好了)

- Join Point 连接点: 在程序执行过程中的一个点, 例如方法的执行或者异常的处理. 在Spring AOP中这个连接点总是代表方法执行. (可以理解成就是类中的方法就是连接点)

- Advice 通知: 特定切面在特定的连接点执行的操作. 通知有不同的类型, 包括"前置通知", "后置通知", "环绕通知"等. (其实就是在什么时候执行, 前置就是在目标方法执行前执行, 后置就是目标方法后执行...) 许多AOP框架, 包括Spring, 将通知建模成为一个拦截器, 并且在连接点周围维护一条由拦截器组成的拦截链. (这就是Spring对Advice的实现方式了. ) 

- Pointcut 切入点: 切入点是表达连接点的谓词. (就是连接点的集合) 通知是和这个切入点表达式相关联的, 它会在任何匹配切入点的连接点上执行. (例如, 在特定方法上执行通知). 这种根据切点表达式匹配连接点的观念是AOP的核心, 而Spring默认使用AspectJ切入点表达式)

- Introduction 引入: Declaring additional methods or fields on behalf of a type. Spring AOP lets you introduce new interfaces (and a corresponding implementation) to any advised object. For example, you could use an introduction to make a bean implement an IsModified interface, to simplify caching. (An introduction is known as an inter-type declaration in the AspectJ community.) (TODO: 我不懂求大佬带带)

- Target Object 目标对象: 一个即将被一个或者多个切面增强的对象. (增强也就是被通知影响) 也成为"通知对象". 由于Spring AOP是根据运行时代理实现的, 所以这个对象总是一个代理对象. 

- AOP proxy AOP代理: 一个为了实现切面契约的AOP框架创建的对象. 在Spring框架中, 它可能是JDK动态代理或者是CGLIB代理. 

- Weaving 编织: 将切面和其他应用程序类型或者对象连接, 来创建一个增强了的对象. 这可能在编译时完成, (例如使用AspectJ编译器) 加载时, 或者运行时. 而Spring AOP和其他纯Java AOP框架一样, 是在运行时完成织入的. 

## 3.3 Java中AOP的实现

### 3.3.1 静态AOP

- 在编译器, 切面直接以字节码形式编译到目标字节码文件中. 

### 3.3.2 动态AOP 

- 在运行时, 目标类加载后, 动态生成代理类, 将切面植入到代理类中. 

#### 3.3.2.1 基于JDK动态代理

基于**Java反射**机制实现, 必须要**实现了接口**的业务类才能用这种办法生成代理对象. 

#### 3.3.2.2 基于CGLIB代理

基于**ASM**机制实现，通过**生成业务类的子类**作为代理类。

### 3.3.3 小结

![](/picture/2020-10-29-16-00-37.png)

不同的AOP实现只是在不同的时刻用不同的工具来实现的, 下面就只讲Spring的AOP. 

## 3.4 Spring AOP的实现

下面就拿Spring 5.2.9的源代码来看咯. 

### 3.4.1 Advice 通知

Advice是AOP联盟定义的一个接口, 可以在org.aopalliance.aop.Advice中找到它. 
而Spring提供了更具体的通知类型, BeforeAdvice, AfterAdvice, ThrowsAdvice等. 

- BeforeAdvice接口: 前置通知的标记接口. 
- AfterAdvice接口: 后置通知的标记接口. 
- ThrowsAdvice接口: 一般抛出异常就由这个接口处理. 

Spring中比BeforeAdvice接口更具体一点的实现是MethodBeforeAdvice接口: 

~~~ java
public interface MethodBeforeAdvice extends BeforeAdvice {
	void before(Method method, Object[] args, @Nullable Object target) throws Throwable;
}
~~~

它提供了一个before方法, 它会在方法被调用前被调用. 这个通知不能阻止方法调用的过程, 除非它抛出Throwable. 如果方法签名允许, 它抛出的任何异常都会返回给调用方. 否则异常会被包装为运行时异常. 而它的method参数是目标方法的反射对象; args对象数组中包含目标方法的输入参数; target为目标对象. 

下面是AfterAdvice更具体的AfterReturningAdvice接口: 

~~~ java
public interface AfterReturningAdvice extends AfterAdvice {
	void afterReturning(@Nullable Object returnValue, Method method, Object[] args, @Nullable Object target) throws Throwable;

}
~~~

它提供了afterReturning方法, 顾名思义就是在目标对象方法成功返回值了之后执行. (前提当然目标方法也没抛异常) 它可以获得返回值但是无法改变返回值. 参数returnValue为返回值; 

然后是ThrowsAdvice, 它是继承AfterAdvice: 

~~~ java
public interface ThrowsAdvice extends AfterAdvice {

}
~~~

但是这个接口并没有规定的方法, 如果需要你需要实现这种形式的方法: 

~~~ java
void afterThrowing([Method, args, target], ThrowableSubclass);
~~~

其中方括号内的参数是可选的. 如果该异常通知抛出新的异常, 将会覆盖原来的异常. 

### 3.4.2 Pointcut 切点

Spring AOP提供了具体的切点, 比如JdkRegexpMethodPointcut的matches方法是通过JDK来实现正则表达式的匹配的. NameMatchMethodPointcut的matches方法就是使用方法名相同或者方法名相匹配. 

### 3.4.3 Advisor 通知器

通过Advisor可以将Advice和Pointcut结合起来. 

### 后面太难了, 夭折了...

Spring通过jdk和cglib两个精彩的实现了AOP, 虽然我们用起来很简单, 但是Spring实现AOP的过程是非常艰苦的, (是我太菜了看不懂了. ) 

# 4. Spring MVC

## 4.1 网络基础知识

在此之前先学习一下[计算机网络基础知识](/2020/basic-computer-network). 

在BS结构中使用到了DNS协议, 而且在HTTP上层还有相关规范, 如Java Web开发中使用的是Servlet标准. 标准就是规范, 都是不能干活的, 能干活的就是实现了Servlet标准的Tomcat这种Servlet容器. 

### 4.1.1 Java中的Socket

Java中的Socket可以分为**普通Socket**和**NioSocket**两种. 

#### 普通Socket

Java中的网络通信是通过Socket实现的, java.net包里的Socket主要分为**Socket**和**ServerSocket**两类. 
- java.net.ServerSocket: 用于服务端, 可以通过accept方法监听请求, 监听到Socket请求后用该Socket完成数据传输. 
- java.net.Socket: 用于客户端发起请求并传输数据. 

#### new IO模式Socket

// TODO 以后再说

这两个例子书中的代码我都放在[这里](https://github.com/thesomeexp/JavaExample/tree/master/KanTouSpringMVC)啦. 


### 4.1.2 Servlet详解

Servlet是Server+Applet的缩写, 表示一个服务器应用. 它是一套规范, 按照这套规范写的代码就可以直接在Java的服务器上运行. 

![](/picture/2020-11-05-11-18-18.png)

Servlet一些方法: 
- init(): 在容器启动时被容器调用. (当load-on-startup设置为负数或者不设置时会在Servlet第一次用到时才被调用)
- getServletConfig(): 用于获取ServletConfig. 
- getServletInfo(): 获取Servlet相关信息, 如作者, 版权等, 默认空. 
- service(): 具体处理请求的方法
- destroy(): Servlet销毁(关闭服务器)时释放一些资源, 调用一次. 

ServletConfig一些方法: 
- getServletName(): 获取Servlet名字, web.xml中定义的servlet-name. 
- getInitParameter(): 获取init-param配置参数. 
- getInitParameterNames(): 获取配置所有init-param的名字的集合. 
- getServletContext(): 这个非常重要, 它的返回值ServletContext就是这个应用本身. 所以这里面的参数就可以被当前应用的所有Servlet共享. 比如我们项目中参数可以保存在Session中, 也可以在Application中, 而这个Application多数就是保存在ServletContext. 

简单来说ServletConfig能获取一些web.xml配置好的servlet信息, 然后还能获得整个Servlet上下文. Servlet容器通过把这个ServletConfig作为参数传入Servlet的init()方法中, 便于Servlet的配置. 

GenericServlet作为一个协议无关的Servlet的默认实现, 主要做了三件事: 
- 1. 实现ServletConfig接口, 使得可以直接调用里面的方法. 
- 2. 提供无参的init()模板方法. (带参的init方法做了config的配置, 所以子类只需要覆盖无参init()专注于完成逻辑就好了)
- 3. 提供log()方法. 一般我们都有自己的日志处理, 这个用得不多. 

HttpServlet做的比较重要的事情: 
- 1. 覆盖service()方法, 主要将ServletRequest和ServletResponse转换为HttpServletRequest和HttpServletResponse. 然后调用自己的service()方法. 
- 2. 自身的service()方法, 根据HttpServletRequest的请求方法, 用不同的doXXX结构的处理方法处理. 

注意到doGet方法处理前它判断了if-modified-since, 如果缓存没过期就返回304使用缓存. 
而doHead调用了doGet请求, 然后返回空body的Response. 
而doOptions和doTrace正常不需要使用, 主要用来做调试, 它会对header原封不动的返回, 这种做法很容易被黑客利用, 存在安全漏洞, 如果不是必须使用最好禁用. 

简单来说HttpServlet把不同请求方式路由到了不同的处理方法. 

## 4.2 Tomcat

### 4.2.1 Tomcat的顶层结构

Tomcat中最顶层的容器叫Server. 
- Server: 这是Tomcat中最顶层的容器, 它代表整个服务器. 它包含至少一个Service, 用于提供服务. 
- Service: 它包含一个Container和一个或多个Connector(一个服务可以有多个连接, 如同时提供http与https, 也可以提供同协议不同端口的连接). 
- Connector: 它用于处理连接相关的事情, 并提供Socket与request, response的转换. 
- Container: 用于封装和管理Servlet, 具体处理request请求. 

![](/picture/2020-11-05-14-54-04.png)

而Tomcat的Server是由org.apache.catalina.startup.Catalina来管理的, Catalina是整个Tomcat的管理类. 它的方法: 
- 1. load(): 用于根据conf/server.xml文件创建Server并调用Server的init()方法进行初始化. 
- 2. start(): 用于启动服务器. 在内部调用Server的start()方法. 
- 3. stop(): 用于停止服务器. 在内部调用Server的stop()方法. 
- await(): 该方法调用Server的await()方法, 作用是进入一个循环让主线程不会退出. 

上面的三个方法会按照容器的结构逐层调用相应的方法. 比如Server的start()会调用Service的start(), 而Service的start()会调用包含的Connector和Container的start(). 

而Tomcat的入口方法并不在Catalina类里面, 而是在org.apache.catalina.startup.Bootstrap里面. 这个Bootstrap类似一个CatalinaAdaptor, 具体过程还是Catalina来完成的, 这样的好处是把启动入口和具体的管理类分开, 可以方便的创造出不同的启动方式, 每种启动方式只需要写相应的CatalinaAdaptor就好了. 

### 4.2.2 Bootstrap的启动过程

- 1. main(): 新建一个Bootstrap对象, 执行init()方法, 然后处理main方法传入的args参数命令, 如果为空默认执行setAwait() ---> load(args) ---> start(). 
- 2. init(): 初始化ClassLoader创建Catalina实例赋值给catalinaDaemon变量, 后面对命令操作都是用catalinaDaemon. 
- 3. setAwait(): 通过反射调用对应Catalina的setAwait(). 
- 4. load(args): 通过反射调用对应Catalina的load(args). 
- 5. start(): 通过反射调用对应的Catalina的start(). 

### 4.2.3 Catalina的启动过程

- 1. setAwait(): 用于设置Server启动后是否进入等待的await标志. 可以看到start()后会用await标志判断是否执行await(). 
- 2. load(args): 根据conf/server.xml加载配置文件创建并初始化Server对象, 然后赋值给server属性(具体操作是通过开源项目Digester完成的), 然后调用server的init(). 
- 3. start(): 调用server的start(), 然后根据await属性判断是否让程序进入等待状态. (server的await()方法内部会执行一个while循环, 当await()方法退出时会执行stop()从而关闭服务器)

### 4.2.4 Server的启动过程

Server接口提供addService(), removeService来添加和删除Service. 而Server的init()和start()方法分别循环调用每个Service的init()和start()方法来启动所有Service. 

Server的默认实现是org.apache.catalina.core.StandardServer, 而StandardServer继承自LifecycleMBeanBase, LifecycleMBeanBase又继承自LifecycleBase: 

![](/picture/2020-11-05-16-04-08.png)

init()和start()方法就定义在LifecycleBase中, LifecycleBase里的init()和start()方法又调用initInternal()和startInternal()这两个模板方法, 所以调用StandardServer的init()和start()方法时会执行StandardServer自己的initInternal()和startInternal()这两个方法, 这就是Tomcat生命周期的管理方式. initInternal()和startInternal()这两个方法分别循环调用了每一个Service的start()和init()方法. 

StandardServer还实现了**await()**方法, Catalina就是调用它让服务器进入等待状态的. 但是啥时候退出呢? 这就是await()方法要干的活了. 它根据Server设置的端口号来关闭(这个端口号不是服务的端口号, 是专门用来监听停止容器命令的端口, 默认是8005). 根据这个端口号的值, 它有三种处理方法: 
- port为-2, 则直接退出, 不进入循环. 
- port为-1, 进入一个循环, 只有调用了stopAwait方法才会退出循环. (方法关闭)
- port为其他值, 会在port所在端口启动一个ServerSocket来监听关闭命令(默认为"SHUTDOWN"), 默认为8005, 如果接收到了则跳出循环. (网络命令关闭)

### 4.2.5 Service的启动过程

Service接口的默认实现是org.apache.catalina.core.StandardService, StandardService也继承LifecycleMBeanBase类, 所以init()和start()方法最终也会调用initInternal()和startInternal()方法. 

![](/picture/2020-11-05-16-21-49.png)

我们看StandardService中的initInternal()和startInternal()方法, 它其实是调用了container, executors, mapperListener, connectors的init()和start()方法. container和connectors前已经介绍过, mapperListener是Mapper的监听器, 它可以监听container容器的变化, executors是用在connectors中管理线程的线程池, 在server.xml配置文件中有参考用法, 不过默认是注释起来的: 

~~~ xml
    <!--The connectors can use a shared executor, you can define one or more named thread pools-->
    <!--
    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="150" minSpareThreads="4"/>
    -->
~~~

把上面的注释放开, Connector就会配置一个叫tomcatThreadPool的线程池, 最多可以同时启动150个线程, 最少要有4个可用线程. 

整个Tomcat服务器启动流程就是: 

![](/picture/2020-11-05-17-23-55.jpg)

### 4.2.6 Tomcat的生命周期管理

下面的内容需要学习**观察者模式**才能看懂哦. 
Tomcat通过org.apache.catalina.Lifecycle接口统一管理生命周期, 所有有生命周期的组件都要实现Lifecycle接口. 
而这个生命周期管理的Lifecycle接口是拿来干嘛的呢? 由Lifecycle接口上面的注释我们可以了解到, 实现了这个接口的组件, 他们就会有以下的这些合法的状态值, 以及状态之间的转换, 在状态转换之间触发事件, 然后监听器们通过判断事件的类型来做相应处理. 下面这个是组件的状态机图(这个图看着难受, 过一眼就行了): 

![](/picture/2020-11-06-10-10-10.png)

这个接口主要有这些内容: 
- 1. 定义了13个String类型常量, 这是LifecycleEvent事件中的**事件类型**. 用于LifecycleEvent事件中的type属性, 作用是区分组件发出的LifecycleEvent事件时的状态(如初始化前, 启动前, 启动中等). 这种设计方式可以让多种状态都发送同一种类型的事件, 然后用其中的一个属性来区分状态而不用定义多种事件. 书本作者大佬说我们要学习借鉴这种方式. 
- 2. 定义了三个管理监听器的方法: addLifecycleListener(), findLifecycleListeners(), removeLifecycleListener(), 分别用来添加, 查找, 删除LifecycleListener类型的监听器. 
- 3. 定义了4个生命周期的方法: init(), start(), stop(), destroy(), 用于执行生命周期的各个阶段的操作. 
- 4. 定义了获取当前状态的两个方法, getState()和getStateName()分别用来获取当前的状态. getState()返回值LifecycleState是枚举类型, 里面列举了**生命周期的各个节点**, getStateName()方法返回String类型的状态名字, 主要用于JMX中. 


### 4.2.7 Container分析

Container是Tomcat中容器的接口, 因为作为Tomcat的容器组件所有有生命周期继承Lifecycle接口. 
它有4个子接口(子容器)和一个默认实现ContainerBase, 而且子容器都继承默认实现ContainerBase: 

![Container结构图](/picture/2020-11-06-14-54-35.png)

Container的子容器Engine, Host, Context, Wrapper是逐层包含的关系, 其中Engine是最顶层, 每个Service最多只能有一个Engine, 每个Engine里面可以有多个Host, 每个Host下可以有多个Context, 每个Context下可以有多个Wrapper. 

![](/picture/2020-11-06-14-59-25.jpg)

- Engine: 引擎, 用来管理多个站点, 一个Service最多只能有一个Engine. (也就是很久之前结构图的Container)
- Host: 代表一个站点, 也可以叫虚拟主机, 通过配置Host就可以添加站点. 
- Context: 代表一个应用程序, 对应着平时开发的一套程序, 或者一个WEB-INF目录以及下面的web.xml文件. 
- Wrapper: 每个Wrapper封装着一个Servlet. 

上面写得我觉得比较抽象, 还是直接看Tomcat目录吧: 
**Host**: Tomcat的默认Host(主机名)为localhost, 在conf/server.xml也能看到相关配置(这里简单的抽取一些配置): 

~~~ xml
<Server port="8005" shutdown="SHUTDOWN">
  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
    <Engine name="Catalina" defaultHost="localhost">
      <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
~~~
Tomcat启动时默认监听所有IP地址, 如果仅仅本地测试可以在Service下的Connector中配置address="127.0.0.1"来设置仅监听本地. 

![](/picture/2020-11-06-15-04-52.png)

**Context**: 代表应用, 而ROOT目录里的应用就是主应用, 直接使用主机名/应用名就能访问对应的应用, 比如我们启动Tomcat后访问127.0.0.1/docs就能访问docs这个应用了. 

![](/picture/2020-11-06-15-07-58.png)
![](/picture/2020-11-06-15-48-24.jpg)

4种容器的配置方法: 
作者简化了默认配置来供我们学习哦: 

~~~ xml
<?xml version='1.0' encoding='utf-8'?>
<Server port='8005' shutdown='SHUTDOWN'>
    <Service name='Catalina'>
        <Connector port='8080' protocol='HTTP/1.1' connectionTimeout='20000' redirectPort='8443' />
        <Connector port='8009' protocol='AJP/1.3' redirectPort='8443' />
        <Engine name='Catalina' defaultHost='localhost'>
            <Host name='localhost'  appBase='webapps' unpackWARs='true' autoDeploy='true'>
            </Host>
        </Engine>
    </Service>
</Server>
~~~

- Server: Server代表整个服务器, 在8005端口监听关闭命令"SHUTDOWN". Server下有一个叫Catalina的默认Service. 
- Service: Service里定义了两个Connector, 一个是HTTP协议一个是AJP协议; 还定义了一个叫Catalina的Engine. 
- Engine: Engine里定义了一个名为localhost的Host. 
  - defaultHost属性代表接收到的请求域名如果在所有Host的name和Alias都找不到时使用默认的Host. 如果使用IP直接访问也会用到defaultHost, 如果删掉该属性, 启动后通过IP就没有办法访问咯. 
- Host: 一个Host就代表一个主机咯. 
  - name属性代表主机名, 上面定义的主机名为localhost, 所以可以通过localhost可以访问. 
  - appBase属性指定站点的位置, 比如上面定义的站点就是默认的webapps目录. 
  - unpackWARs属性代表是否自动解压war文件
  - autoDeploy属性为true代表Tomcat在运行过程中在webapps目录中加入新的应用将会自动部署并启动. 
- Alias: 这个是Host下的一个标签, 这个标签定义主机别名. 比如你可以把Engine的defaultHost属性删掉, 然后再加上Alias标签, 标签内的值为www.localhost, 那么就能通过 www.localhost 主机别名也能访问了. 

Context的三种配置方法: 
- 1. 通过文件配置(有5个位置)
  - 1.1 conf/server.xml文件中的Context标签. (配置单独的应用)
  - 1.2 conf/[enginename]/[hostname]/目录下以应用命名的xml文件. (配置单独的应用)
  - 1.3 应用自己的/META-INF/context.xml文件. (配置单独的应用)
  - 1.4 conf/context.xml文件. (整个Tomcat中共享, Tomcat重启时才重新加载)
  - 1.5 conf/[enginename]/[hostname]/context.xml.default文件. (整个Host中共享)
  
- 2. 将WAR应用直接放到Host目录下, Tomcat会自动查找并添加到Host中. 
- 3. 将应用文件夹放到Host目录下, Tomcat也会自动查找并添加到Host中. 

Wrapper的配置就是再web.xml中配置的Servlet, 一个Servlet对应一个Wrapper. 也可以在conf/web.xml文件中配置全局的Wrapper, 处理Jsp的JspServlet就配置在这里, 所有不需要自己配置Jsp就可以处理Jsp请求了. 

注: conf/web.xml配置了两个全局Servlet, 一个是DefaultServlet, 另一个是JspServlet, 分别负责处理一些媒体文件和jsp文件. 如果应用中配置了对应的url-pattern标签将会覆盖这两个默认的Servlet. 所有在使用SpringMVC配置DispatcherServlet的mapping为/的时候, 会覆盖DefaultServlet, 这时类似.jpg, .png这种静态资源就得不到处理, 所以就会报404错误, 所以对于这些静态资源在SpringMVC里需要做格外的处理. 

由于Service下的所有站点都是共享Connector, 所以监听的端口都一样. 如果想要添加监听不同端口的站点可以通过不同的Service配置, Service也是在conf/server.xml文件中配置的. 

// TODO 然后巴拉巴拉就是Container启动的内容了, 这我懒了不写了, 启动完就会启动下面的管道了

### 4.2.8 Pipeline-Value管道

Container处理请求是使用Pipeline-Value管道来处理的. 这种处理方式能确保每一层都能有序的执行到, 从顶层Engine到Host到Context再到Wrapper...
这个模式类似于设计模式中的**责任链模式**. 但又有几点不同: 
- 1. 每个Pipeline都有特定的Valve(对, 是Valve不是Value), 且是最后一个执行, 这个Valve叫BaseValve, 是不可删除滴. 
- 2. 上层容器的管道的BaseValve会调用下层容器的管道. 四个容器的BaseValve分别是StandardEngineValve, StandardHostValve, StandardContextValve, StandardWrapperValve. 

它们的执行流程如下图: 

![](/picture/2020-11-09-10-45-31.png)

在Engine的管道中依次执行Engine的各个Valve, 最后执行StandardEngineValve用于调用Host的管道, 然后执行Host的Valve, 这样依此类推执行Wrapper管道中的StandardWrapperValve. 

在Filter中用到的FilterChain其实就是这种模式, FilterChain相当于Pipeline, 每个Filter都相当于一个Valve, Servlet相当于最后的BaseValve. 

管道呢既是一个组件(所以有生命周期), 又是拿来处理请求的, 所以就分两部分咯. 
- 首先就是org.apache.catalina.core.ContainerBase的startInternal()和stopInternal()对管道进行启动和关闭. 
- 然后处理请求是调用Valve的invoke()方法, 比如org.apache.catalina.core.StandardEngineValve中处理完会调用host的首个Valve, 一层一层下来直到最后的StandardWrapperValve. 

所以总的流程就是, Connector在接收到请求之后会调用最顶层容器的Pipeline来处理, 最顶层处理完会在BaseValve里调用下一层容器的Pipeline处理, 这样可以逐层调用所有容器的Pipeline来处理. 处理到最后, Wrapper的Pipeline最后会在其BaseValue(StandardWrapperValve)中创建FilterChain并调用其doFilter方法来处理请求, FilterChain包含着我们配置的与请求相匹配的Filter和Servlet, 其doFilter方法会依次调用所有Filter的doFilter方法和Servlet的service方法, 这样请求就得到处理了. 

### 4.2.9 Connector分析

简单来说Connector用来接收请求然后封装成Request和Response来具体处理, 它的底层就是使用Socket来进行连接的, Request和Response是按照HTTP协议来封装的, 所以Connector同时实现了TCP/IP协议和HTTP协议, 封装完之后就交给Container进行处理, Container就是Servlet的容器, 处理完后再返回给Connector最后Connector使用Socket将处理结果返回给客户端, 整个请求就处理完了. 作者提供的结构关系图: 

![](/picture/2020-11-09-11-54-34.jpg)

// TODO: 砍刀部动手, 这部分细节就砍了

## 4.3 Spring MVC

### 4.3.1 整体结构

上一波Spring MVC中核心的继承结构图: 

![](/picture/2020-11-09-14-24-42.png)

图中Java方面的三个类之前已经讲过了, 下面都是讲SpringMVC中的HttpServletBean, FrameworkServlet和DispatcherServlet这三个类. 
这三个类直接实现三个接口, EnvironmentAware, EnvironmentCapable, ApplicationContextAware. 这种XXXAware在Spring里表示对XXX可感知, 也就是说某个类想使用Spring的一些东西, 可以实现这个接口然后实现对应的set方法, Spring就会给你带过来. 

### 4.3.2 HttpServletBean介绍

在之前对Servlet的分析时, 我们知道创建Servlet时, 它的init()方法会被容器调用, 而org.springframework.web.servlet.HttpServletBean的init()方法将Servlet中配置的参数使用BeanWrapper设置到DispatcherServlet的相关属性中, 然后调用模板方法initServletBean(), 子类就通过这个方法初始化. 

对于这个HttpServletBean, 它的上一级GenericServlet有一个使用transient修饰的ServletConfig, 这个修饰符表示修饰的内容不会被序列化, 在这里可能是config不是很重要所以不用序列化吧. 

注: BeanWrapper怎么用? 它是Spring提供的操作JavaBean属性的工具, 用它可以直接修改一个对象的属性. 作者举了个例子: 

~~~ java
public class User {
    String userName;
    public String getUserName() {
        return userName;
    }
    public void setUserName(String userName) {
        this.userName = userName;
    }
}
public class BeanWrapperTest {
    public static void main(String[] args) {
        User user = new User();
        BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(user);
        bw.setPropertyValue("userName", "张三");
        System.out.println(user.getUserName());
        PropertyValue value = new PropertyValue("userName", "李四");
        bw.setPropertyValue(value);
        System.out.println(user.getUserName());
    }
}
~~~

### 4.3.3 FrameworkServlet介绍

根据上面HttpServletBean可知, 这个FrameworkServlet的初始化入口方法是initServletBean(), 然后它的核心代码主要是初始化WebApplicationContext还有初始化FrameworkServlet. // TODO: 下面巴拉巴拉就懒得写了

### 4.3.4 DispatcherServlet介绍

onRefresh()方法是DispatcherServlet入口方法, DispatcherServlet的创建过程主要是对9大组件进行初始化. 

### 4.3.5 HttpServletBean使用

作者说这个东西主要参与了创建工作, 没有涉及请求的处理. 

### 4.3.6 FrameworkServlet使用

- 对service()方法添加了对PATCH的处理, 将需要自己处理的请求都集中到了processRequest()进行统一处理, 这和HttpServlet中根据request的类型分开到不同方法处理相反. 
- processRequest里面主要的处理逻辑交给了doService, 这是一个模板方法等子类实现. 然后就是对request获取到的LocaleContext和RequestAttributes进行了保存, 处理完之后恢复, 最后发布了ServletRequestHandleEvent事件. 

### 4.3.7 DispatcherServlet使用

通过前面分析, 它的入口方法是doService()...// TODO: 具体的就不再写了, 这个类主要解释三个概念: HandlerMapping, Handler和HandlerAdapter. 

- Handler: 也就是处理器, 它直接对应MVC中的C也就是Controller层, 它的具体表现形式有很多, 可以是类也可以是方法, 作者说如果你想到别的表现形式也可以使用, 它的类型是Object嘛. 在平常我们写Controller时标注的@RequestMapping的方法都可以看成一个Handler, 只要可以实际处理请求就可以是Handler. 
- HandlerMapping: SpringMVC中会处理很多请求, 每个请求都需要一个Handler来处理, 而它就是拿来查找Handler的. 
- HandlerAdapter: 从名字能看出它是一个适配器, 因为Handler可以是任意形式的, 但是Servlet处理的方法结构是固定的, 都是以request和response为参数的方法(如doService方法), 而这个HandlerAdapter就是让固定的Servlet处理方法调用灵活的Handler来处理. 

另外View和ViewResolver的原理也和这个类似. View是用来展示数据的, 而ViewResolver用来查找View. View就像是模板, Model就是数据, ViewResolver就是使用哪个模板. 

这一节doDispatch()内容有点多, //TODO: 以后再补了直接上图: 

![](/picture/2020-11-09-17-07-08.jpg)

三个Servlet的处理过程大致如下: 
- HttpServletBean: 没有参与实际请求的处理. 
- FrameworkServlet: 将不同类型的请求合并到了processRequest方法统一处理, processRequest主要做了: 
  - 调用doService模板方法具体处理请求. 
  - 将LocaleContext和ServletRequestAttributes在请求前设置到了LocaleContextHolder和RequestContextHolder, 并在请求处理完成后恢复. 
  - 请求处理完后发布了ServletRequestHandledEvent事件. 
  
- DispatcherServlet: doService方法给request设置了一些属性并将请求交给doDispatch方法具体处理. 

DispatcherServlet中的doDispatch方法完成了SpringMVC中请求处理过程的顶层设计, 它使用了DispatcherServlet中的九大组件完成了具体的请求处理. 

## 4.4 Spring MVC组件

作者要跟我们讲讲DispatcherServlet中直接初始化的那9个组件啦. 

### 4.4.1 组件整体概览

#### HandlerMapping

它只有一个getHandler()方法返回HandlerExecutionChain. 
它的作用是根据request找到相应的处理器Handler和Interceptor. 
查找Handler是按照顺序遍历所有的HandlerMapping, 当找到一个HandlerMapping后立即停止查找并返回. 
在org.springframework.web.servlet.DispatcherServlet中, getHandler()会遍历所有的HandlerMapping, 当其中一个HandlerMapping返回了HandlerExecutionChain之后就直接返回. 

#### HandlerAdapter

它有三个方法: 
- supports() 判断是否可以使用某个Handler
- handle() 是用来具体使用Handler干活. 
- getLastModified() 是获取资源的LastModified, 资源最后一次修改时间

在org.springframework.web.servlet.DispatcherServlet中, getHandlerAdapter()将遍历所有的HandlerAdapter, 找到一个support对应handler的Adapter就直接返回. 

#### HandlerExceptionResolver

其他工作的组件工作中可能出现问题, 那出现问题后咋办咧? 很明显我们需要一个专门的角色对异常情况进行处理. 在SpringMVC中这个角色就是HandlerExceptionResolver. 
简单来说这个组件就是根据异常解析出ModelAndView, 然后再交给render方法进行渲染. 因为它在render之前工作的, 解析出ModelAndView之后render才去渲染, 所以它就不能处理render过程中的异常了. 

// TODO 懒了

### SpringMVC总结

Spring MVC是Spring的Web MVC模式框架,它主要是围绕处理请求分发的DispatcherServlet来设计的.它的具体流程如图4所示.

![](/picture/2021-03-01-23-18-17.jpg)
DispatcherServlet的处理工作流

Spring MVC的处理又和J2EE默认的分请求处理不一样,在这里它又用DispatcherServlet统一进行处理了,而请求如何进行处理又交给了doDispatch()方法来处理,其实就是调用各种相关的组件来各自处理.要解释各组件的处理流程,首先要分清楚三个概念.
1.Handler
Handler是处理器,它可以直接对应到MVC中的Controller层,它有很多种表现形式,可以是类也可以是方法,只要可以处理实际请求的就是Handler.比如项目中使用注解@RequestMapping标注的方法就是一个Handler.
2.HandlerMapping
HandlerMapping是处理器映射器的意思,它主要用来将请求映射到相应的Handler来处理. 
3.HandlerAdapter
HandlerAdapter是处理器适配器,因为Handler可以是多种形式的因此十分灵活,但是Servlet处理请求的方法是固定的,因此需要有这个适配器来将灵活的Handler转换成能供Servlet调用的方式来处理.

同样View和ViewResolver的原理也和上面三个概念的抽象类似,View是用来展示数据的模板,Model是数据,ViewResolver是用来查找使用哪个View模板来渲染的.

因此DispatcherServlet的处理工作流程如下:
1.客户端向服务器发送请求(Incoming request).
2.请求被前端控制器(Front controller主要是DispatcherServlet)捕获,组件HandlerMapping根据URI(请求资源标识符)获得相关的Handler.
3.DispatcherServlet将使用适合的HandlerAdapter来处理获得的Handler.(如果成功获得HandlerAdapter那就会执行相应Interceptor的preHandle()方法)
4.HandlerAdapter使用Handler处理请求.(在此之前根据配置Spring会帮你做一些格外的工作,比如提取request作为Handler的入参,数据转换,数据格式化,数据验证等.处理完后执行相应Interceptor的postHandle()方法)
5.Controller(也就是Handler被)执行完之后返回一个model对象.
6.前端控制器根据返回的model对象和view来选择合适的视图解析器(ViewResolver).
7.视图解析器通过model来对响应(response)进行渲染.
8.前端控制器根据返回的渲染结果返回给客户端

### 4.5 总结与补充

// TODO 后面Servlet3.0提供了使用异步处理请求的内容. 

参考: 
- 书籍: Spring技术内幕：深入解析Spring架
- 书籍: 看透Spring_MVC：源代码分析与实践
- [AOP的实现的几种方式](https://blog.csdn.net/csujiangyu/article/details/53455094)
