---
title: Java并发编程实战 笔记
noindex: true
---

书配套管网: [https://jcip.net/](https://jcip.net/)

# 1. 简介

## 1.2 线程的优势

线程能更好地模拟人类的工作方式和交互方式, 比如烧水时人可以做其他事. 在 GUI 应用程序中线程可以提高用户界面的响应灵敏度. 在服务器应用程序中线程可以提升资源利用率以及系统吞吐率. 线程还可以简化 JVM 的实现, 垃圾收集器通常在一个或者多个专门的线程中运行.
- 发挥多处理器的强大能力
- 建模的简单性 (通常线程只完成单一的简单任务, 所以逻辑上会简单些)
- 异步事件的简化处理 (作者认为服务器中使用多线程处理请求以及使用同步 I/O 会降低应用程序的开发难度. 而单线程服务器应用必须使用非阻塞 I/O, 这种 I/O 的复杂性要远高于同步 I/O)
- 响应更灵敏的用户界面

## 1.3 线程带来的的风险

如果没有同步, 无论是编译器, 硬件还是运行时, 都可以随意安排操作的执行时间和顺序, 例如对寄存器或者处理器中的变量进行缓存, 而这些缓存的变量对于其他线程来说是暂时 (甚至永久) 不可见的.

### 安全性问题(永远不发生糟糕的事情) (Safety hazards)

执行顺序不可预测. 容易产生竞态条件 (Race Condition). 因为多个线程要共享相同的内存地址空间, 并且是并发运行, 因此它们可能会访问或修改其他线程正在使用的变量. 要使多线程程序的行为可以预测, 必须对共享变量的访问操作进行协同, 而Java提供了各种同步机制来协同这种访问.

### 活跃性问题(某件正确的事情最终会发生) (Liveness hazards)

当某个操作无法继续执行下去时, 就会发生活跃性问题. 在串行程序中, 活跃性问题的形式之一是无意中造成的无限循环, 使得循环之后的代码无法得到执行. 而线程将带来其他一些活跃性问题, 例如线程A等待线程B释放其持有的资源, 而线程B永远都不释放该资源, 那么A就会永久地等待下去.

### 性能问题(某件正确的事情尽快发生) (Performance hazards)

线程会带来某种程度的运行时开销. 当线程调度器临时挂起活跃线程并转而运行另一个线程时, 就会频繁的出现上下文切换 (Context Switch) 操作, CPU 时间将更多地花在线程调度而不是线程运行上. 当线程共享数据时, 必须使用同步机制, 而这些机制往往会抑制某些编译器优化. 所有这些因素都将带来格外的性能开销.

(不理解: 当某个框架在应用程序中引入并发性时, 通常不可能将并发性仅局限于框架代码, 因为框架本身会回调 (Callback) 应用程序的代码, 而这些代码将访问应用程序的状态. 同样, 对线程安全性的需求也不能局限于被调用的代码, 而是要延伸到需要访问这些代码所访问的程序状态的所有代码路径. 因此, 对线程安全性的需求将在程序中蔓延开来.)

# 2. 线程安全性(第一部分)

三种方式修复多线程访问可变状态变量没有使用同步出现的错误:
- 不在线程之间共享该状态变量
- 将状态变量修改为不可变的变量
- 在访问状态变量时使用同步

## 2.1 什么是线程安全性

线程安全的定义中, 最核心的概念是正确性. 正确性的含义是, 某个类的行为与其规范完全一致. 在良好的规范中通常会定义各种不变性条件来约束对象的状态, 以及定义各种后验条件来描述对象操作的结果. 我们通常不会编写详细的规范证实这些类正确工作, 但这不妨碍我们确信"类的代码可以工作", 这种"代码可信性"非常接近我们对正确性的理解, 也就是近似地定义为"所见即所知 (we know it when we see it)". 
对"正确性"给出了较为清晰的定义后, 就可以定义线程安全性: 当多个线程访问某个类时, 这个类始终都能表现出正确的行为, 那么就称这个类是线程安全的. 

在线程安全类的对象实例上执行的任何串行或并行操作都不会使对象处于无效状态.
在线程安全类中封装了必要的同步机制, 因此客户端无须进一步采取同步措施.

### 无状态对象一定是线程安全的

如果一个类不包含任何域, 也不包含任何其他类中域的引用. 计算过程中的临时状态仅存在于线程栈上的局部变量中, 并且只能由正在执行的线程访问, 线程访问无状态对象的行为并不会影响其他线程中操作的正确性, 因此无状态对象是线程安全的.

## 2.2 原子性 (Atomicity)

我们把"先检查后执行"(延迟初始化)和"读取-修改-写入"(递增运算)等操作称为复合操作, Java中提供了加锁机制来使上面的操作以原子方式执行. 
对于计数器的自增, 作者使用 java.util.concurrent.atomic 包中的AtomicLong来代替原来的long类型, 从而确保了对计数器状态的访问操作是原子的. 但当状态变量的数量由一个变为多个时, 并不会像状态变量数量由零个变为一个那样简单.

## 2.3 加锁机制 (Locking)

Java提供了一种内置的锁机制来支持原子性: 同步代码块(Synchronized Block). 它包括两部分:
- 一个作为锁的对象引用(如果是静态方法, 则是Class对象作为锁)
- 一个作为由这个锁保护的代码块

~~~ java
synchronized (lock) {
    // 同步代码块
}
~~~

- 以关键字 synchronized 来修饰的方法就是一种横跨整个方法体的同步代码块, 其中该同步代码块的锁就是方法调用所在的对象.
- 静态的 synchronized 方法以 Class 对象作为锁.

每个Java对象都可以作为一个实现同步的锁, 这些锁被称为**内置锁**(Intrinsic Lock)或**监视器锁**(Monitor Lock). 获得内置锁的唯一途径就是进入由这个锁保护的同步代码块或方法. 线程在进入同步代码块前会自动获得锁, 在退出或者抛出异常时自动释放锁. Java的内置锁相当于一种互斥锁, 线程A尝试获取线程B持有的锁时必须等待或者阻塞, 如果B永远不释放锁, 那么A会永远等下去.

内置锁是**可重入的**, 如果某个线程试图获得一个已经由它自己持有的锁, 那么这个请求就会成功. "重入"意味着获取锁的操作粒度是"线程", 而不是"调用"(这和POSIX线程的粒度不同). 重入的一种实现方法是, 为每个锁关联一个**获取计数值**和一个**所有者线程**. 当计数值为0时, 这个锁就被认为是没有任何线程持有. 当线程请求一个未被持有的锁时, JVM将记下锁的持有者, 并将获取计数值置为1, 如果同一个线程再次获取这个锁, 计数值将递增, 而当线程退出同步代码块时, 计数器会相应地递减.
子类覆盖并调用父类synchronized修饰的方法, 其实是获取它自己持有的锁, 如果内置锁不支持重入, 那就会导致死锁的发生, 而内置锁的可重入避免了这种死锁的发生.

## 2.4 用锁来保护状态 (Guarding state with locks)

并非所有数据都需要锁的保护, 只有被多个线程同时访问的可变数据才需要通过锁来保护. 而对于每个包含多个变量的不变性条件, 其中涉及的所有变量都需要由同一个锁来保护.

如果只是每个方法都作为同步方法, 例如Vector, 那么并不足以确保Vector上复合操作都是原子的:

~~~ java
if (!vector.contains(element)) {
    vector.add(element);
}
~~~

虽然contains和add都是原子方法, 但这上面仍存在竞态条件, 还是需要格外的加锁机制.

对于同步代码块的合理大小, 需要在各种设计需求之间进行权衡, 包括安全性(这个需求必须得到满足), 简单性和性能. 当执行时间较长的计算或者可能无法快速完成的操作时(例如网络I/O或控制台I/O), 一定不要持有锁.

# 3. 对象的共享 (Sharing Objects)

通常, 我们无法确保执行读操作的线程能适时地看到其他线程写入的值, 有时甚至是根本不可能的事情. 为了确保一个线程修改了对象状态后, 其他线程能够看到发生的状态变化, 必须使用同步机制.

## 3.1 可见性 (Visibility)

没有同步时共享变量(不要这么做):

~~~ java
public class NoVisibility {
    private static boolean ready;
    private static int number;
    private static class ReaderThread extends Thread {
        public void run() {
            while (!ready) {
                Thread.yield();
            }
            System.out.println(number);
        }
    }
    public static void main(String[] args) {
        new ReaderThread().start();
        number = 42;
        ready = true;
    }
}
~~~

上面的代码中读线程和主线程都将访问共享变量ready和number. 
- 读线程: 一直循环观察ready的值, ready为true时输出number
- 主线程: 将number设为42, 将ready设为true

虽然代码看起来像是会输出42, 但事实上它可能会输出0, 或者根本无法终止. 这是因为代码中没有使用足够的同步机制, 因此无法保证主线程写入的ready值和number值对于读线程来说是可见的.

- NoVisibility可能会持续循环下去, 因为读线程可能永远都看不到ready的值.
- NoVisibility可能会输出0, 因为读线程可能只看到了写入ready的值, 但没有看到之后写入number的值.(这种现象可能是因为JVM编译器做了相应的优化, 比如重排序(Reordering)导致的.)

### 3.1.1 失效数据 (Stale data)

失效数据类似于脏读, 读到错误的数据严重时会导致安全问题或活跃性问题. 比如错误的对象引用, 链表指针...
以下是非线程安全的可变整数类(不要这么做):

~~~ java
public class MutableInteger {
    private int value;
    
    public int get() {return value;}
    public void set(int value) {this.value = value;}
}
~~~

以下是线程安全的可变整数类, 仅对 set 方法进行同步是不够的, 调用 get 的线程仍然会看见失效值:

~~~ java
public class SynchronizedInteger {
    private int value;

    public synchronized int get() {
        return value;
    }

    public synchronized void set(int value) {
        this.value = value;
    }
}
~~~

### 非原子的 64 位操作

在线程没有同步的情况下读取变量时, 可能会得到一个**失效值**, 但它至少是一个合法的(正确的)失效值, 而不是随机值. 这种安全保证也被称之为**最低安全性**(out-of-thin-airsafety). 这适用于绝大多数变量.

而对于非 volatile 类型的 64 位数值变量( double 和 long ), JVM 会允许将 64 位的读操作和写操作分解为两个 32 位的操作, 所以很有可能读到不匹配的高 32 位和低 32 位, 导致产生**随机值**而不是**失效值**. 所以在多线程中使用可变的 long 或者 double 等类型的变量也是不安全的, 建议用关键字 volatile 修饰或用锁保护.

### Volatile 变量

Java 提供了一种稍弱的同步机制, 即 volatile 变量, 用来确保变量的更新操作通知到其他线程. 把变量声明为 volatile 后, 编译器和运行时会注意到这个变量是共享滴, 因此不会把它和其它操作一起重排序, 在读取 volatile 类型变量时总会返回最新写入的值, 它也不会执行加锁操作, 因此不会使得执行线程阻塞. 因此它比 synchronized 关键字更轻量.

一种volatile变量的典型用法:

~~~ java
volatile boolean asleep;
...
    while (!asleep) {
        countSomeSheep();
    }
~~~

上面实例中通过数绵羊的方法进入休眠状态, 为了使得实例正确执行, asleep 必须为 volatile 变量. 否则, 当asleep被另一个线程修改时, 执行判断的线程却发现不了(没错, 这又是 JVM 的锅. 对于服务器应用, JVM 会执行更多的优化, 例如将循环体中未更改的变量提升到循环体外部. 因此无论在开发还是测试阶段, 启动 JVM 时一定都要指定 -server 命令行选项. 比如上面的代码, 因为 asleep 在循环中未被更改, 可能会被 server 模式下的 JVM 提到循环体外部, 这会导致无限循环.). 看到这你可能就理解了可见性的意思了吧?

加锁机制既可以确保可见性又可以确保原子性, 而 volatile 变量只能确保可见性. 也因此 volatile 变量通常用作某个操作完成, 发生中断或者状态的标志. 当且仅当满足以下所有条件时, 才应该使用 volatile 变量:
- 对变量的写入操作不依赖变量的当前值, 或者你只能确保只有单个线程更新变量的值.
- 该变量不会与其他状态变量一起纳入不变性条件中.
- 在访问变量时不需要加锁

## 3.2 发布与逸出 (Publication and escape)

**发布** (Publish) 一个对象的意思是指, 使对象能够在当前作用域之外的代码中使用. 在很多情况下, 我们要确保对象以及内部状态不被发布. 发布内部状态可能会破坏封装性, 并使得程序难以维持不变性条件. 例如在对象构造完成之前就发布该对象, 就会破坏线程安全性.
当某个不应该发布的对象被发布时, 这种情况就被称为**逸出** (Escape) .

发布对象最简单的方法是将对象的引用保存到一个公有的静态变量中, 以便任何类和线程都能看到该对象.

~~~ java
    public static Set<Secret> knownSecrets;

    public void initialize() {
        knownSecrets = new HashSet<Secret>();
    }
~~~

当发布某个对象时, 可能会间接发布其他对象, 比如上面的 knownSecrets 集合就可以添加其他对象的引用, 其它线程可以遍历这个集合获取对象的引用.
同样, 如果从非私有方法中返回一个引用, 那么同样会发布返回的对象.
比如下面就发布了本应为私有的状态数组.(不要这么做):

~~~ java
public class UnsafeStates {
    private String[] states = new String[] {"AK", "AL"};
    public String[] getStates () { return states; }
}
~~~

上面的例子中, 声明为private的states已经逸出了它所在的作用域.

当发布一个对象时, 在该对象的非私有域(变量或方法)中引用的所有对象同样会被发布. 

一个对象被发布后, 误用发布引用的风险始终存在, 这就是需要使用封装的主要原因: 封装能够使得对程序的正确性进行分析变得可能, 并使得无意中破坏设计约束条件变得更难.

对于一个类 C 来说, 类 C 的 "外部 (Alien) 方法" 是指行为并不完全由 C 来规定的方法, 包括其他类中定义的方法以及类 C 中可以被改写的方法 (既不是私有 private 方法也不是 final 方法). 当把一个对象传递给某个外部方法时, 就相当于发布了这个对象.

作者给出的隐式地使 this 引用逸出例子(不要这么做):

~~~ java
public class ThisEscape {
    public ThisEscape(EventSource source) {
        source.registerListener(
                new EventListener() {
                    @Override
                    public void onEvent(Event e) {
                        doSomething(e); // "this" escape
                    }
                }
        );
    }
    void doSomething(Event e) {}
    interface EventSource {
        void registerListener(EventListener e);
    }
    interface EventListener {void onEvent(Event e);}
    interface Event {}
}
~~~

上面的例子中, 当 ThisEscape 发布 EventListener 时, 也隐含地发布了 ThisEscape 实例本身, 因为在这个内部类的实例中包含了对 ThisEscape 实例的隐含引用(反编译后你会发现 EventListener 这个内部类中包含了其外部类 ThisEscape 的引用, 所以内部类中才能调用外部类中的 doSomething() 方法). 它不应该在构造函数返回前逸出.

如果你和我一样第一眼看不懂上面的例子, 可以运行一下我自己写的实例, 看完跑一下下面这段程序你就知道啥是 this 隐式逸出了:

~~~ java
public class ThisEscape {
    private final int num;

    public ThisEscape(EventSource source) throws InterruptedException {
        System.out.println("构造中");
        source.registerListener(new EventListener() {
            @Override
            public void onEvent() {
                // this 从匿名内部类中逸出了, 其它线程可以通过 EventSource 拿到该匿名内部类
                doSomething();
            }
        });
        System.out.println("依然在构造中... 构造未完成... ");
        // 未完成构造, 但是已经逸出
        Thread.sleep(3000L);
        num = 42;
    }

    void doSomething() {
        if (num != 42) {
            System.out.println("检测到未构造完成的 ThisEscape 逸出 " + new Date() + "num: " + num);
        }
    }

    static class EventSource {
        private final List<EventListener> listeners = new ArrayList<>();

        public synchronized void registerListener(EventListener e) {
            listeners.add(e);
        }

        public synchronized EventListener get() {
            if (listeners.size() > 0) {
                return listeners.get(0);
            }
            return null;
        }
    }

    interface EventListener {
        void onEvent();
    }

    public static void main(String[] args) throws InterruptedException {
        EventSource eventSource = new EventSource();
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("监听线程启动");
                while (true) {
                    EventListener eventListener = eventSource.get();
                    if (eventListener != null) {
                        System.out.println("其它线程获取到");
                        eventListener.onEvent();
                        break;
                    }
                }
            }
        }).start();
        Thread.sleep(1000L);
        new ThisEscape(eventSource);
        System.out.println("构造完成");
    }

}
~~~

在构造过程中使 this 引用逸出的一个常见错误是, 在构造函数中启动一个线程. 当对象在其构造函数中创建一个线程时, 无论是显式创建(通过将它传给构造函数)还是隐式创建(由于 Thread 或 Runnable 是该对象的一个内部类), this 引用都会被新创建的线程共享. 在对象尚未完全构造之前, 新的线程就可以看见它. 在构造函数中创建线程并没有错误, 但最好不要立即启动它, 而是通过一个 start 或 initialize 方法来启动.

在构造函数中调用一个可改写的实例方法时(既不是 private 方法, 也不是 final 方法), 同样会导致 this 引用在构造过程中逸出.

如果想在构造函数中注册一个事件监听器或启动线程, 那么可以使用 private 构造函数和一个工厂方法, 从而避免不正确的构造过程:

~~~ java
public class SafeListener {
    private final EventListener listener;
    
    private SafeListener() {
        listener = new EventListener() {
            @Override
            public void onEvent(Event e) {
                doSomething(e);
            }
        };
    }
    public static SafeListener newInstance(EventSource source) {
        SafeListener safe = new SafeListener(); // 构造完成
        source.registerListener(safe.listener);
        return safe;
    }
    void doSomething(Event e) {}
    interface EventSource {
        void registerListener(EventListener e);
    }
    interface EventListener {
        void onEvent(Event e);
    }
    interface Event {
    }
}
~~~

如果你和我一样也不明白上面为什么能防止 this 隐式逸出, 恭喜你, 我也写好了一段小例子供你参考: 

~~~ java
public class NoThisEscape {
    private final EventListener safeListener;
    private final int num;

    private NoThisEscape() throws InterruptedException {
        System.out.println("构造中");
        this.safeListener = new EventListener() {
            @Override
            public void onEvent() {
                // 虽然这里也有 this, 但是这个是 private 构造方法, 再构造完成之前没有逸出
                doSomething();
            }
        };
        System.out.println("依然在构造中... 构造未完成... ");
        // 未完成构造
        Thread.sleep(3000L);
        num = 42;
    }

    public static NoThisEscape newInstance(EventSource source) throws InterruptedException {
        NoThisEscape safe = new NoThisEscape();
        System.out.println("构造完成");
        source.registerListener(safe.safeListener);
        return safe;
    }

    void doSomething() {
        if (num != 42) {
            System.out.println("检测到未构造完成的 ThisEscape 逸出 " + new Date() + "num: " + num);
        }
    }

    static class EventSource {
        private final List<EventListener> listeners = new ArrayList<>();

        public synchronized void registerListener(EventListener e) {
            listeners.add(e);
        }

        public synchronized EventListener get() {
            if (listeners.size() > 0) {
                return listeners.get(0);
            }
            return null;
        }
    }

    interface EventListener {
        void onEvent();
    }

    public static void main(String[] args) throws InterruptedException {
        EventSource eventSource = new EventSource();
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("监听线程启动");
                while (true) {
                    EventListener eventListener = eventSource.get();
                    if (eventListener != null) {
                        System.out.println("其它线程获取到");
                        eventListener.onEvent();
                        break;
                    }
                }
            }
        }).start();
        Thread.sleep(1000L);
        NoThisEscape.newInstance(eventSource);
    }

}
~~~

## 3.3 线程封闭 (Thread confinement)

当访问共享的可变数据时, 通常需要使用同步, 而一种避免同步的方式就是不共享数据. 比如仅在单线程内访问数据, 就不需要同步. 这种技术就称为**线程封闭** (Thread Confinement). 当某个对象封闭在一个线程中时, 将自动实现线程安全性, 即使被封闭的对象本身不是线程安全的.

线程封闭技术一种常见应用是 JDBC (Java Database Connectivity) 的 Connection 对象. JDBC 规范并不要求 Connection 对象必须是线程安全的, 但连接池在分配一个 Connection 对象之后, 在该对象返回之前, 连接池不会再将它分配给其它线程, 而对于 Servlet 请求或 EJB 调用等都是由单个线程采用同步的方式来处理, 因此这种连接管理模式在处理请求时隐含地将 Connection 对象封闭在线程中.

Java 语言没有提供强制线程封闭的功能, 所以线程封闭必须在程序中实现. Java 语言以及核心库提供了一些机制来帮助维持线程封闭性, 例如局部变量和 ThreadLocal 类. 即便如此, 程序员仍然需要负责确保封闭在线程中的对象不会从线程中逸出.

### 3.3.1 Ad-hoc线程封闭

Ad-hoc 线程封闭是指, 维护线程封闭性的职责完全由程序实现来承担, 然而这是非常脆弱的, 因此在程序中尽少使用它.

### 3.3.2 栈封闭

栈封闭是线程封闭的一种特例, 在栈封闭中, 只能通过局部变量才能访问对象. 局部变量位于执行线程的栈中, 其它线程无法访问这个栈, 因此栈封闭(也被称为线程内部使用或线程局部使用, 与核心库中的 ThreadLocal 不一样)比 Ad-hoc 线程封闭更易于维护.

对于**基本类型**的**局部变量**, 无论如何都不会破坏栈封闭性, 因为任何方法都无法获得对**基本类型的引用**, 因此 Java 语言的这种语义就确保了基本类型的局部变量始终封闭在线程内.

而对于维持**对象引用**的栈封闭性时, 程序员需要多做一些工作以确保这个被引用对象不会逸出.

### ThreadLocal 类

维持线程封闭性的一种更规范的方法是使用 ThreadLocal, 它能使线程中的某个值与保存的线程关联起来. ThreadLocal 提供了 get 和 set 等访问接口或方法, 这些方法为使用该变量的线程都存有一份独立的副本, 因此 get 总是返回由当前执行线程在调用 set 时设置的最新值.

ThreadLocal 对象通常用于防止对可变的单实例变量 (Singleton) 或全局变量进行共享. 例如在单线程应用中需要维持一个全局的数据库连接, 并在启动时初始化这个连接对象, 从而避免在调用每个方法时都要传递一个 Connection 对象. 通过将 JDBC 的连接保存到 ThreadLocal 中, 每个线程都会拥有属于自己的连接, 如下面代码中的 ConnectionHolder 所示:

~~~ java
    private static ThreadLocal<Connection> connectionHolder
        = new ThreadLocal<Connection> () {
            public Connection initialValue() {
                return DriverManager.getConnection(DB_URL);
            }
        };
    public static Connection getConnection() {
        return connectionHolder.get();
    }
~~~

当某个线程初次调用 ThreadLocal.get 方法时, 就会调用 initialValue 方法来获取初始值. 从概念上可以把 ThreadLocal<T> 视为包含了 Map<Thread, T> 对象, 其中保存了特定于该线程的值, 但 ThreadLocal 的实现并不是这样的. 这些特定于线程的值保存在 Thread 对象中, 当线程终止后, 这些值会作为垃圾回收.

当某个频繁执行的操作需要一个临时对象, 而同时又希望避免在每次执行时都重新分配该临时对象, 就可以使用这项技术, 例如缓冲区. 这种机制很方便, 它还能避免在调用每个方法时传递上下文信息, 但是这种机制也会让代码耦合在一起, 因此在使用时要格外小心.

## 3.4 不变性 (Immutability)

满足同步需求的另一种方法是使用不可变对象 (Immutable Object). 不可变对象一定是线程安全的.

不可变对象只有一种状态, 并且该状态由构造函数来控制.
在 Java 语言规范和内存模型中没有给出不可变性的正式定义, 但不可变性不等于将对象中所有的域都声明为 final 类型, 即使对象中所有的域都是 final 类型的, 这个对象也仍然是可变的, 因为在 final 类型的域中可以保存可变对象的引用. 而不定义成 final 的不可变类型例子也有, 例如 String 会把散列值计算推迟到第一次调用 hash Code 时进行, 当且仅当每次计算结果相同时(因为基于一个不可变的状态)才可以这么做, 而自己在编写代码时还是不要这么做吧.

当满足以下条件时, 对象才是不可变的:
- 对象创建以后其状态就不能修改
- 对象的所有域都是 final 类型
- 对象是正确创建的(在对象创建期间, this 引用没有逸出)

在可变对象基础上构建不可变类:

~~~ java
public final class ThreeStooges {
    private final Set<String> stooges = new HashSet<>();

    public ThreeStooges() {
        stooges.add("Moe");
        stooges.add("Larry");
        stooges.add("Curly");
    }

    public boolean isStooge(String name) {
        return stooges.contains(name);
    }
}
~~~

由于程序的状态在不断地改变, 你可能以为需要使用不可变对象的地方不多, 但实际情况并非如此. 在**不可变的对象**与**不可变的对象引用**之间存在着差异(一个是指实例化出来的对象不可变, 一个是指引用不可变). 保存在不可变对象中的程序状态仍然可以更新, 即通过将一个保存新状态的实例来"替换"原有的不可变对象.

### 3.4.1 Final 域

在 Java 中, final 域能确保初始化过程的安全性, 从而可以不受限制地访问不可变对象, 并在共享这些对象时无需同步.

正如"除非需要更高的可见性, 否则应将所有的域都声明为私有域"是一个良好的编程习惯. "除非需要某个域是可变的, 否则应将其声明为final域"也是良好的编程习惯.

### 3.4.2 使用 volatile 类型来发布不可变对象

在某些情况下, 不可变对象能提供一种弱形式的原子性. 每当需要对一组相关数据以原子方式执行某个操作时, 就可以考虑创建一个不可变的类来包含这些数据, 比如下面的对数值以及其因数分解结果进行缓存的不可变容器类:

~~~ java
class OneValueCache {
    private final BigInteger lastNumber;
    private final BigInteger[] lastFactors;

    public OneValueCache(BigInteger i, BigInteger[] factors) {
        lastNumber = i;
        lastFactors = Arrays.copyOf(factors, factors.length);
    }

    public BigInteger[] getFactors(BigInteger i) {
        if (lastNumber == null || !lastNumber.equals(i))
            return null;
        else
            return Arrays.copyOf(lastFactors, lastFactors.length);
    }
}
~~~

对于在访问和更新多个相关变量时出现的竞争条件问题, 可以通过将这些变量全部保存在一个不可变对象中来消除. 如果是一个可变的对象, 那么就必须使用锁来确保原子性. 如果是一个不可变的对象, 那么当线程获得了对该对象的引用后, 就不必担心另一个线程会修改对象的状态. 如果要更新这些变量, 那么可以创建一个新的容器对象, 但其它使用原有对象的线程仍然会看到对象处于一致的状态.

下面的 VolatileCachedFactorizer 类使用了 OneValueCache 容器来保存缓存的数值以及因数. 当一个线程将 volatile 类型的 cache 设置为引用一个新的 OneValueCache 时, 其它线程就会立即看到新缓存的数据. 使用指向不可变容器对象的 volatile 类型引用以缓存最新的结果:

~~~ java
public class VolatileCachedFactorizer implements Servlet {
    private volatile OneValueCache cache = new OneValueCache(null, null);

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = cache.getFactors(i); // 从缓存中获取
        if (factors == null) { // 如果为null, 计算后更新缓存
            factors = factors(i);
            cache = new OneValueCache(i, factors);
        }
        encodeIntoResponse(resp, factors);
    }
}
~~~

与 cache 相关的操作不会相互干扰, 因为 OneValueCache 是不可变的. 通过使用包含多个状态变量的容器对象来维持不变性条件, 并使用一个 volatile 类型的引用来确保可见性, 使得 VolatileCachedFactorizer 在没有显式地使用锁的情况下仍然是线程安全的.

## 3.5 安全发布 (Safe publication)

我们希望在多个线程间共享对象, 这时需要确保安全地进行共享, 也就是安全发布. 如果是像下面这样将对象引用保存到公有域中, 那么还不足以安全地发布这个对象.(不要这么做):

~~~ java
    public Holder holder; // 不安全的发布
    public void initialize() {
        holder = new Holder(42);
    }
~~~

### 3.5.1 不正确的发布: 正确的对象被破坏

由于未被正确发布(像上面代码中的发布), 这个类可能会出现故障:

~~~ java
public class Holder {
    private int n;
    public Holder(int n) { this.n = n; } // 不正确发布
    public void assertSanity() {
        if (n != n) { 
            throw new AssertionError("This statement is false.");
        }
    }
}
~~~

在 assertSanity 方法中, 某个线程可能第一次读取的 n 为失效值(比如 Object 构造函数会在子类构造函数运行之前先将默认值写入所有的域), 然后第二次读取 n 是其他线程的更新值, 这样就会抛出上面定义的异常. // TODO 这个怎么修复?

### 3.5.2 不可变对象与初始化安全性

由于不可变对象是一种非常重要的对象, 因此Java内存模型为不可变对象的共享提供了一种特殊的初始化安全性保证(也就是 final 关键字). 如果上面的 Holder 对象是不可变的, 那么即使 Holder 没有被正确地发布, 在 assertSanity 中也不会抛出 AssertionError. 但如果 final 类型的域所指向的是可变对象, 那么在访问这些域所指向的对象的状态时仍然需要同步.

### 3.5.3 安全发布的常用模式

可变对象必须通过安全的方式来发布, 这通常意味着在发布和使用该对象的线程时都必须使用同步.

要安全地发布一个对象, 对象的引用以及对象的状态必须同时对其他线程可见. 一个正确构造的对象可以通过以下方式来安全地发布:
- 在静态初始化函数中初始化一个对象引用
- 将对象的引用保存到 volatile 类型的域或者 AtomicReference 对象中
- 将对象的引用保存到某个正确构造对象的 final 类型域中
- 将对象的引用保存到一个由锁保护的域中

线程安全库中的容器类提供了以下的安全发布保证:
- 键值
  - 放入 Hashtable, ConcurrentMap 或 Collections.synchronizedMap() 中, 可以安全地将它发布给任何从这个容器中访问它的线程(无论是直接访问还是通过迭代器访问)
- List 和 Set
  - 放入 Vector, CopyOnWriteArrayList, CopyOnWriteArraySet, Collections.synchronizedSet() 中.
- Queue (队列)
  - 放入 BlockingQueue 或者 ConcurrentLinkedQueue 中.

类库中的其它数据传递机制(例如 Future 和 Exchanger )同样能实现安全发布.

通常, 要发布一个静态构造的对象, 最简单和最安全的方式是使用静态的初始化器:

~~~ java
public static Holder holder = new Holder(42);
~~~

静态初始化器由 JVM 在类的初始化阶段执行, 由于在 JVM 内部存在着同步机制, 因此通过这种方式初始化的任何对象都可以被安全地发布.

### 3.5.4 事实不可变对象 (Effectively immutable objects)

如果对象从技术上来看是可变的, 但其状态在发布后不会再改变, 那么把这种对象称之为**事实不可变对象**(Effectively Immutable Object). 在这些对象发布后, 程序只需要将它们视为不可变对象即可. 使用这种对象可以简化开发过程, 同时还能由于减少了同步而提高性能.

### 3.5.5 可变对象 (Mutable objects)

如果对象在构造后可以修改, 那么安全发布只能确保"发布当时"状态的可见性. 对于可变对象, 不仅在发布对象时需要使用同步, 而且在每次对象访问时同样需要使用同步来确保后续修改操作的可见性. 
- 不可变对象可以通过任意机制来发布
- 事实不可变对象必须通过安全方式来发布
- 可变对象必须通过安全方式来发布, 并且必须是线程安全的或者由某个锁保护起来

### 3.5.6 安全地共享对象

当发布一个对象时, 必须明确地说明对象的访问方式.
在并发编程中使用和共享对象时, 可以使用一些实用的策略:
- **线程封闭**: 线程封闭的对象只能由一个线程拥有, 对象被封闭在该线程中, 并且只能由这个线程修改.
- **只读共享**: 在没有额外同步的情况下, 共享的只读对象可以由多个线程并发访问, 但任何线程都不能修改它. 共享的只读对象包括不可变对象和事实不可变对象.
- **线程安全共享**: 线程安全的对象在其内部实现同步, 因此多个线程可以通过对象的公有接口来进行访问而不需要进一步的同步.
- **保护对象**: 被保护的对象只能通过持有特定的锁来访问. 保护对象包括封装在其它线程安全对象中的对象, 以及已发布的并且由某个特定锁保护的对象.

# 4. 对象的组合 (Composing Objects)

## 4.1 设计线程安全的类

不变性条件用于确保对象不会在多线程访问时容易被改变.
后验条件用来判断状态迁移是否有效.

## 4.2 实例封闭 (Instance confinement)

对于不是线程安全的对象, 可以使用线程封闭确保只能由单个线程访问, 或者通过一个锁来保护该对象的所有访问.

下面的程序通过封闭机制来确保线程安全:

~~~ java
public class PersonSet {
    private final Set<Person> mySet = new HashSet<>();
    
    public synchronized void addPerson(Person p) {
        mySet.add(p);
    }
    public synchronized boolean containsPerson(Person p) {
        return mySet.contains(p);
    }
}
~~~

PersonSet的状态由HashSet来管理, 而HashSet并非线程安全的, 但由于mySet是私有的而且不会逸出, 因此HashSet被封闭在PersonSet中. 唯一能访问的代码路径是addPerson和containsPerson, 在执行它们时都要获得PersonSet上的锁, PersonSet的状态完全由它的内置锁保护, 因而PersonSet是一个线程安全的类.

但是这个实例并未对Person的线程安全性做任何假设, 如果Person类是可变的, 那么在访问从PersonSet中获得的Person对象时, 还需要额外的同步. 想要安全地使用Person对象, 最可靠的方法就是使Person成为一个线程安全的类, 也可也使用锁来保护Person对象.

Java平台的有些类唯一用途就是将非线程安全的类转化为线程安全的类. 例如Collections.synchronizedList之类的一些工厂方法, 通过装饰器模式将容器类封装在一个同步的包装器对象中.

### 4.2.1 Java监视器模式 (The Java monitor pattern)

遵循Java监视器模式的对象会把对象的所有可变状态都封装起来, 并由对象自己的内置锁来保护.许多类中都使用了Java监视器模式, 例如Vector和Hashtable.

Java监视器模式仅仅是一种编写代码的规定, 对于任何一种锁对象, 只要自始至终都是用该锁对象, 都可以用来保护对象的状态. 下面的程序给出了如何使用私有锁来保护状态:

~~~ java
public class PrivateLock {
    private final Object myLock = new Object();
    int myInt;
    
    void someMethod() {
        synchronized (myLock) {
            // 访问或修改myInt的状态
        }
    }
}
~~~

使用私有的锁对象而不是对象的内置锁(或任何其它可通过公有方式访问的锁), 有许多优点. 私有的锁对象可以将锁封装起来, 使客户代码无法得到锁, 但客户代码可以通过公有方法来访问锁, 以便(正确或者不正确地)参与到它的同步策略中. 如果客户代码错误地获得了另一个对象的锁, 那么可能会产生活跃性问题. 此外, 要想验证某个公有访问的锁在程序中是否被正确地使用, 则需要检查整个程序而不是单个的类.

// 作者给出了一个车辆追踪的示例

## 4.3 线程安全性的委托

作者利用一些线程安全的类来重新构造车辆追踪器.
相互独立的状态变量可以使用委托, 但是状态变量之间有关联这样就会使委托失效了, 必须使用加锁或者其他机制来确保了.

// TODO作者的例子

## 4.4 在现有的线程安全的类中添加功能

Java类库提供了很多有用的"基础模块"类, 那假设我们要扩展一个它没有的原子的"若没有则添加的操作, 有哪些做法呢?

### 修改原始的类

这通常是最安全的操作, 同时这通常无法做到, 因为你可能无法访问或修改类的源代码. 想要修改原始类, 你还需要理解代码中的同步策略, 这样增加的功能才与原有的设计保持一致. 直接将新方法添加到类中, 那么意味着实现同步策略的所有代码仍然处于一个源代码文件中, 从而更容易理解与维护.

### 扩展这个类

假定在设计这个类时考虑了可扩展性, 比如下面的扩展Vector类, 但并非所有的类都能将状态向子类公开, 因此也就不适合采用这种方法.

~~~ java
public class BetterVector<E> extends Vector<E> {
    public synchronized boolean putIfAbsent(E x) {
        boolean absent = !contains(x);
        if (absent) {
            add(x);
        }
        return absent;
    }
}
~~~

"扩展"方法比直接将代码添加到类中更加脆弱, 因为现在的同步策略实现被分不到多个单独维护的源代码文件中. 如果底层的类改变了同步策略并选择了不同的锁来保护它的状态变量, 那么子类会被破坏, 因为在同步策略改变后它无法再使用正确的锁来控制对基类状态的并发访问.(在Vector的规范中定义了它的同步策略, 因此BetterVector不存在这个问题.)

### 客户端加锁 (Client-side locking)

对于由Collections.synchronizedList封装的ArrayList, 上面两种方法在原始类中添加一个方法或者对类进行扩展都行不通, 因为客户代码并不知道在同步封装器工厂方法中返回的List对象的类型. 第三种策略是扩展类的功能, 但并不是扩展类本身, 而是将扩展代码放入一个"辅助类"中.
下面的代码就实现了一个"若没有则添加"操作的辅助类, 用于对线程安全的List执行操作, 但其中的代码是错误的.

~~~ java
public class ListHelper<E> {
    public List<E> list = Collections.synchronizedList(new ArrayList<>());
    public synchronized boolean putIfAbsent(E x) {
        boolean absent = !list.contains(x);
        if (absent) {
            list.add(x);
        }
        return absent;
    }
}
~~~

虽然putIfAbsent已经声明为synchronized类型的变量, 但是它是在错误的锁上进行了同步. 对于List来说, 不知道它是用什么锁来保护它的状态, 但可以肯定的是这个锁一定不是ListHelper上的锁. ListHelper只是带来了同步的假象, 尽管所有的链表操作都被声明为synchronized, 但却使用了不同的锁, 这就意味着putIfAbsent相对于List的其它操作来说并不是原子的, 因此就无法确保当putIfAbsent执行时另一个线程不会修改链表.

要想使这个方法能正确执行, 必须使List在客户端加锁或外部加锁时使用同一个锁. 客户端加锁是指, 对于使用某个对象X的客户端代码, 使用X本身用于保护其状态的锁来保护这段客户代码. 要使用客户端加锁, 你必须知道对象X使用的是哪个锁.

在Vector和同步封装器类的文档中指出, 它们通过使用Vector或封装器容器的内置锁来支持客户端加锁. 下面的程序给出了在线程安全的List上执行putIfAbsent操作, 其中使用了正确的客户端加锁.

~~~ java
public class ListHelper<E> {
    public List<E> list = Collections.synchronizedList(new ArrayList<>());

    public boolean putIfAbsent(E x) {
        synchronized (list) {
            boolean absent = !list.contains(x);
            if (absent) {
                list.add(x);
            }
            return absent;
        }
    }
}
~~~

通过添加一个原子操作来扩展类是脆弱的, 因为它将类的加锁代码分布到多个类中. 然而, 客户端加锁却更加脆弱, 因为它将类C的加锁代码放到与C完全无关的其他类中. 当在那些并不承诺遵循加锁策略的类上使用客户端加锁时, 要特别小心.

客户端加锁机制与扩展机制有许多共同点, 二者都是将派生类的行为与基类的实现耦合在一起. 正如扩展会破坏实现的封装性, 客户端加锁同样会破坏同步策略的封装性.

### 组合

这是更好的方法: **组合**(Composition). 下面的程序中的ImprovedList通过将List对象的操作委托给底层的List实例来实现List的操作, 同时还添加了一个原子的putIfAbsent方法. (与Collections.synchronizedList和其他容器封装器一样, ImprovedList假设把某个链表对象传给构造函数以后, 客户代码不会再直接使用这个对象, 而只能通过ImprovedList来访问它.)(这其实和Collections.synchronizedList内部的同步类一样嘛, 多了层封装然后使用的是新对象或类的锁.)

~~~ java
public class ImprovedList<T> implements List<T> {
    private final List<T> list;
    
    public ImprovedList(List<T> list) { this.list = list; }
    public synchronized boolean putIfAbsent(T x) {
        boolean contains = list.contains(x);
        if (contains) {
            list.add(x);
        }
        return !contains;
    }
    // ...其它未实现的方法...
}
~~~

ImprovedList通过自身的内置锁添加了一层额外的加锁. 它并不关心底层的List是否是线程安全的, 即使List不是线程安全的或者修改了它的加锁实现(这个是重点, 底层类可能会改噢), ImprovedList也会提供一致的加锁机制来实现线程安全性. 虽然额外的同步层可能导致轻微的性能损失(性能损失很小, 因为在底层List上的同步不存在竞争, 所以速度很快. 作者说你去看11章). 与模拟另一个对象的加锁策略相比, ImprovedList更为健壮. 事实上, 我们使用了Java监视器模式来封装现有的List, 并且只要在类中拥有指向底层List的唯一外部引用, 就能确保线程安全性.

## 4.5 将同步策略文档化 (Documenting synchronization policies)

// TODO这里以后要回文章看
作者的意思是写完的代码很快会忘, 然后你写的那些变量就不清楚是需要保护的还是不需要的, 因此写一个类时一定要同时维护一份良好的文档. 如果文档没说就不要假设它是线程安全的.
// TODO作者吐槽了以下ServletContext, HttpSession或DataSource这些类的文档...

# 5. 基础构建模块 (Building Blocks)

## 5.1 同步容器类 (Synchronized collections)

同步容器类包括Vector和Hashtable, 以及由Collections.synchronizedXxx()方法创建的类等. 它们实现线程安全的方式是将它们的状态封装起来, 并对每个公有方法都进行同步, 使得每次只有一个线程能访问容器的状态.

### 同步容器类的问题

下面的程序使用了客户端加锁, 确保了Vector的大小在调用size和get之间不会发生变化.

~~~ java
    public static Object getLast(Vector list) {
        synchronized (list) { // 客户端加锁
            int lastIndex = list.size() - 1;
            return list.get(lastIndex);
        }
    }
    public static void deleteLast(Vector list) {
        synchronized (list) { // 客户端加锁
            int lastIndex = list.size() - 1;
            list.remove(lastIndex);
        }
    }
~~~

同样, 可以使用客户端锁防止迭代时其它线程修改了vector.

~~~ java
    synchronized (vector) { // 客户端加锁
        for (int i = 0; i < vector.size(); i++) {
            doSomething(vector.get(i));
        }
    }
~~~

### 迭代器与ConcurrentModificationException

如果有其它线程并发地修改容器, 那么使用迭代器是线程不安全的, 这意味着你在多线程环境使用线程安全容器的迭代器时需要加锁. 在设计同步容器的迭代器时并没有考虑到并发修改的问题.

Vector容器内部有一个modCount字段用于保存修改版本号, 在迭代器迭代时, 先保存当前的版本号快照, 每次迭代都判断一下容器的版本号和迭代器的版本号是否一致, 如果不一致将抛出ConcurrentModificationException异常.

这种"及时失败"(fail-fast)的迭代器并不是一种完备的处理机制, 它只是"善意地"捕获并发错误, 因此只能作为并发问题的预警指示器.

下面的程序使用了for-each循环语法对List容器进行迭代, 如果从内部来看, javac将生成使用Iterator的代码, 反复调用hasNext和next来迭代List对象. 与迭代Vector一样, 想要避免ConcurrentModificationException就必须在迭代过程中持有容器的锁.

~~~ java
public class ConcurrentModificationTest {
    private List<String> stringList = Collections.synchronizedList(new ArrayList<>());

    public void beCareful() {
        for (String s : stringList) { // 可能抛出ConcurrentModificationException
            doSomething(s);
        }
    }
}
~~~

如果不希望在迭代期间对容器加锁, 那么一种替代方法就是"克隆"容器, 在副本上进行迭代. 副本被封闭在线程内, 其它线程不会在迭代期间对其进行修改, 这样就避免了抛出ConcurrentModificationException(在克隆过程中仍然需要对容器加锁). 至于在克隆时的开销和迭代时开销, 取决于不同的因素环境, 所以这个还是看需求.

### 隐藏迭代器 (Hidden iterators)

有些迭代操作是隐式的, 不会显示的显示出来. 比如下面的代码中就有可能抛出ConcurrentModificationException.(不要这么做)

~~~ java
public class HiddenIterator {
    private final Set<Integer> set = new HashSet<>();
    
    public synchronized void add(Integer i) { set.add(i); }
    public synchronized void remove(Integer i) { set.remove(i); }
    
    public void addTenThings() {
        Random r = new Random();
        for (int i = 0; i < 10; i++) {
            add(r.nextInt());
        }
        System.out.println("DEBUG: added ten elements to " + set); // 可能抛出ConcurrentModificationException
    }
}
~~~

上面可能会抛出ConcurrentModificationException是因为在生产调试消息的过程中, toSting对容器进行迭代. 当然真正的问题在于HiddenIterator不是线程安全的. 同样这里得到的教训是, 如果状态与保护它的同步代码之间相隔越远, 那么开发人员就越容易忘记在访问状态时使用正确的同步. 如果HiddenIterator用synchronizedSet来包装HashSet, 并且对同步代码进行封装, 那么就不会发生这种错误. 

同样容器的hashCode和equals等方法也会间接地执行迭代操作, containsAll, removeAll和retainAll等方法, 以及把容器作为参数的构造函数, 都会对容器进行迭代, 所有这些间接的迭代操作都有可能抛出ConcurrentModificationException.

## 5.2 并发容器 (Concurrent collections)

Java 5.0 提供了多种 **并发容器**类 来改进 **同步容器** 的性能. 并发容器是针对多个线程并发访问设计的. Java 5.0 中增加了 ConcurrentHashMap (替代同步且基于散列的Map), 以及 CopyOnWriteArrayList (用于在遍历操作为主要操作的情况下代替同步的 List). 还在 ConcurrentMap 接口中增加了对一些常见复合操作的支持.

通过并发容器来代替同步容器, 可以极大地提高伸缩性并降低风险.

Java 6 引入了 ConcurrentSkipListMap 和 ConcurrentSkipListSet 分别作为同步的 SortedMap 和 SortedSet 的并发替代品.

### 5.2.1 ConcurrentHashMap

ConcurrentHashMap 使用 分段锁 (Lock Striping) 机制来实现更大程度的共享. 它在并发访问环境下实现更高的吞吐量, 在单线程环境中只损失非常小的性能.

ConcurrentHashMap 与其他并发容器一起增强了同步容器类, 它们提供的迭代器不会抛出 ConcurrentModificationException, 因此不需要在迭代过程中对容器加锁. ConcurrentHashMap 返回的迭代器具有弱一致性(Weakly Consistent), 而并非"及时失败". 弱一致性的迭代器可以容忍并发的修改, 当创建迭代器时会遍历已有的元素, 并可以(但是不保证)在迭代器被构造后将修改操作反映给容器.

ConcurrentHashMap 对一些需要在整个 Map 上进行计算的方法, 例如 size 和 isEmpty, 这些方法的语义被略微减弱了以反映容器的并发特性. 也就是 size 返回一个近似值而不是一个精确值. 这些操作的需求被弱化了, 以换取对其他更重要操作的性能优化.

与 Hashtable 和 synchronizedMap 相比, ConcurrentHashMap 有着更多的优势以及更少的劣势, 因此在大多数情况下, 用 ConcurrentHashMap 来代替同步 Map 能进一步提高代码的可伸缩性. 只有当应用程序需要加锁 Map 以进行独占访问时, 才应该放弃使用 ConcurrentHashMap.

### 5.2.2 额外的原子 Map 操作

由于 ConcurrentHashMap 不能被加锁来执行独占访问, 因此我们无法使用客户端加锁来创建新的原子操作. 如果你需要在现有的同步 Map 中添加这样的功能, 那么很可能就意味着应该考虑使用 ConcurrentMap 了.

### 5.2.3 CopyOnWriteArrayList

CopyOnWriteArrayList 用于替代同步 List, 在某些情况下它提供了更好的并发性能, 并且在迭代期间不需要对容器进行加锁或复制.

Copy-On-Write 容器的线程安全性在于, 只要正确地发布一个事实不可变的对象, 那么在访问该对象时就不需要进一步的同步. 在每次修改时, 都会创建并重新发布一个新的容器副本, 从而实现可变性.

每当修改容器时都会复制底层数组, 这需要一定的开销, 特别是当容器的规模较大时. 仅当迭代操作远远多于修改操作时, 才应该使用 Copy-On-Write 容器.

## 5.3 阻塞队列和生产者-消费者模式

阻塞队列提供了可阻塞的 put 和 take 方法, 以及支持定时的 offer 和 poll 方法. 如果队列已经满了, 那么 put 方法将阻塞直到有空间可用; 如果队列为空, 那么 take 方法将会阻塞直到有元素可用. 队列可以是有界的也可以是无界的, 无界队列永远都不会充满, 因为无界队列上的 put 方法也永远不会阻塞.

一种最常见的生产者-消费者设计模式就是线程池与工作队列的组合, 在 Executor 任务执行框架中就体现了这种模式.

在构建高可靠的应用程序时, 有界队列是一种强大的资源管理工具, 它们能抑制并防止产生过多的工作项, 使应用程序在负荷过载的情况下变得更加健壮.

类库中包含了 BlockingQueue 的多种实现, 其中, LinkedBlockingQueue 和 ArrayBlockingQueue 是 FIFO 队列, 二者分别与 LinkedList 和 ArrayList 类似, 但比同步 List 拥有更好的并发性能. PriorityBlockingQueue 是一个按优先级排序的队列, 当你希望按照某种顺序而不是 FIFO 来处理元素时, 这个队列将非常有用. 正如其他有序的容器一样, PriorityBlockingQueue 既可以根据元素的自然顺序来比较元素, 也可以使用 Comparator 来比较.

最后一个 BlockingQueue 实现是 SynchronousQueue, 实际上它不是一个真正的队列, 因为它不会为队列中元素维护存储空间. 与其他队列不同的是, 它维护一组线程, 这些线程在等待着把元素加入或移出队列. SynchronousQueue 没有存储功能, 因此 put 和 take 会一直阻塞, 直到有另一个线程已经准备好参加到交付过程中. 仅当有足够多的消费者, 并且总是有一个消费者准备好获取交付的工作时, 才适合使用同步队列.

### 5.3.1 示例: 桌面搜索

代理程序适合被分解为生产者和消费者, 它将扫描本地驱动器上的文件并建立索引以便随后进行搜索, 类似于某些桌面搜索程序或者 Windows 索引服务.

### 5.3.2 串行线程封闭

因为 JUC 包中实现的各种阻塞队列都包含了足够的内部同步机制, 从而安全地将对象从生产者线程发布到消费者线程.

对于可变对象, 生产者-消费者这种设计与阻塞队列一起, 促进了串行线程封闭, 从而将对象所有权从生产者交付给消费者.

对象池利用了串行线程封闭, 将对象"借给"一个请求线程. 只要对象池包含足够的内部同步来安全地发布池中的对象, 并且只要客户代码本身不会发布池中的对象, 或者在将对象返回给对象池后就不再使用它, 那么就可以安全地在线程之间传递所有权.

我们也可以使用其他发布机制来传递可变对象的所有权, 但必须确保只有一个线程能接受被转移的对象. 阻塞队列简化了这项工作. 除此之外, 还可以通过 ConcurrentMap 的原子方法 remove 或者 AtomicReference 的原子方法 compareAndSet 来完成这项工作.

### 5.3.3 双端队列与工作密取

Java6 增加了两种容器类型, Deque (发音为"deck") 和 BlockingDeque, 它们分别对 Queue 和 BlockingQueue 进行了扩展. Deque 是一个双端队列, 实现了在队列头和队列尾的高校插入和移除. 具体实现包括 ArrayDeque 和 LinkedBlockingDeque.

双端队列适用于工作密取(Work Stealing)模式. 在这种模式设计中, 每个消费者都有各自的双端队列. 如果一个消费者完成了自己双端队列中的全部工作, 那么它可以从其他消费者双端队列末尾秘密地获取工作. 这种设计让工作者线程不会在单个共享的任务队列上发生竞争, 当工作者线程需要访问另一个队列时, 会从队列尾部而不是头部获取工作, 进一步降低了队列上的竞争程度, 确保每个线程都保持忙碌状态.

## 5.4 阻塞方法与中断方法 (Blocking and interruptible methods)

线程可能会阻塞或暂停执行(原因: 等待 I/O 操作结束, 等待获得一个锁, 等待从 Thread.sleep 方法中醒来, 或是等待另一个线程的计算结果). 当线程阻塞时, 它通常被挂起, 并处于某种阻塞状态(BLOCKED, WAITING 或 TIMED_WAITING). 阻塞操作与执行时间很长的普通操作的差别在于, 被阻塞的线程必须等待某个不受它控制的事件发生后才能继续执行, 例如等待 I/O 操作完成, 等待某个锁变成可用, 或者等待外部计算的结束. 当某个外部事件发生时, 线程被置回 RUNNABLE 状态, 并可以再次被调度执行.

BlockingQueue 的 put 和 take 等方法会抛出受检查异常 (Checked Exception) InterruptedException, 这与类库中其它一些方法的做法相同, 例如 Thread.sleep. 当某方法抛出 InterruptedException 时, 表示该方法是一个阻塞方法, 如果这个方法被中断, 那么它将努力提前结束阻塞状态.

Thread 提供了 interrupt 方法, 用于中断线程或者查询线程是否已经被中断. 每个线程都有一个布尔类型的属性, 表示线程的中断状态, 当中断线程时将设置这个状态.

中断是一种协作机制. 一个线程不能强制其他线程停止正在执行的操作而去执行其他的操作. 当 线程A 中断 线程B 时, A 仅仅是要求 B 在执行到某个可以暂停的地方停止正在执行的操作--前提是如果 线程B 愿意停下来. 虽然在 API 或者语言规范中并没有为中断定义任何特定应用级别的语义, 但最常使用中断的情况就是取消某个操作. 方法对中断请求的响应度越高, 就越容易及时取消那些执行时间很长的操作.

当在代码中调用了一个将抛出 InterruptedException 异常的方法时, 你自己的方法也就变成了一个阻塞方法, 并且必须要处理对中断的响应. 对于库代码来说, 有两种基本选择:
- 传递 InterruptedException: 避开这个异常通常是最明智的策略--只需把该异常传递给方法的调用者. 传递该异常的方法包括, 根本不捕获该异常, 或者捕获该异常, 然后执行某种简单的清理工作后再次抛出这个异常.
- 恢复中断: 有时候不能抛出 InterruptedException, 例如当代码是 Runnable 的一部分时. 在这些情况下, 必须捕获 InterruptedException, 并通过调用当前线程上的 interrupt 方法恢复中断状态, 这样在调用栈更高层的代码将看到引发了一个中断, 如:

~~~ java
public class TaskRunnable implements Runnable {

    @Override
    public void run() {
        while (true) {
            if (Thread.currentThread().isInterrupted()) { // #1 该行代码检测当前线程是否被中断
                System.out.println(Thread.currentThread().getName() + "检测到中断, 停止执行");
                break;
            }
            try {
                // 模拟处理任务
                Thread.sleep(1000L);
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + "抛出 InterruptedException");
                // #2 恢复被中断的状态, 以至于上面的 #1 代码可以看到引发了中断
                Thread.currentThread().interrupt();
            }
        }
    }

    public static void main(String[] args) {
        Thread thread = new Thread(new TaskRunnable());
        thread.start();
        try {
            // #3 先休眠, 然后再让线程中断
            Thread.sleep(5000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "开始中断" + thread.getName());
        thread.interrupt();
    }
}
~~~

还可以采用一些更复杂的中断处理方法, 但上述两种方法已经可以应付大多数情况了. 然而在出现 InterruptedException 时不应该做的事情是, 捕获它但不做任何响应. 这将使调用栈上更高层的代码无法对中断采取处理措施, 因为线程被中断的证据已经丢失. 只有在一种特殊的情况中才能屏蔽中断, 即对 Thread 进行扩展, 并且能控制调用栈上所有更高层的代码. 第7章将进一步介绍取消和中断操作.

## 5.5 同步工具类 (Synchronizers)

容器类的阻塞队列不仅能作为保存对象的容器, 还能协调生产者和消费者等线程之间的控制流, 因为 take 和 put 等方法将阻塞, 直到队列达到期望的状态(队列既非空, 也非满).
同步工具类可以是任何一个对象, 只要它根据其自身的状态来协调线程的控制流. 阻塞队列可以作为同步工具类, 其他类型的同步工具类还包括信号量(Semaphore), 栅栏(Barrier)以及闭锁(Latch). 在平台类库中还包含其他一些同步工具类的类, 如果这些类还无法满足需要, 那么可以按照第14章中给出的机制来创建自己的同步工具类.
所有的同步工具类都包含一些特定的结构化属性: 它们封装了一些状态, 这些状态将决定执行同步工具类的线程是继续执行还是等待, 此外还提供了一些方法对状态进行操作, 以及另一些方法用于高效地等待同步工具类进入到预期状态.

### 5.5.1 闭锁 Latches

闭锁是一种同步工具类, 它可以延迟线程的进度直到闭锁到达终止状态. 闭锁的作用相当于一扇门: 在闭锁达到结束状态之前, 这扇门一直是关闭的, 并且没有任何线程能通过, 当到达结束状态时, 这扇门会打开并允许所有的线程通过. 当闭锁到达结束状态后, 将不会再改变状态, 因此这扇门将永远保持打开状态. 闭锁可以用来确保某些活动直到其他活动都完成后才继续进行, 例如:
- 确保某个计算在其需要的所有资源都被初始化之后才继续执行. 二元闭锁(包括两个状态)可以用来表示"资源R已经被初始化", 而所有需要R的操作都必须先在这个闭锁上等待.
- 确保某个服务在其依赖的所有其他服务都已经启动之后才启动. 每个服务都有一个相关的二元闭锁. 当启动服务S时, 将首先在S依赖的其他服务的闭锁上等待, 在所有依赖的服务都启动后会释放闭锁S, 这样其他依赖S的其他服务才能执行.
- 等待直到某个操作的所有参与者(例如, 在多玩家游戏中的所有玩家)都就绪再继续执行. 在这种情况中, 当所有玩家都准备就绪时, 闭锁将到达结束状态.

CountDownLatch 是一种灵活的闭锁实现, 可以在上述各种情况中使用, 它可以使一个或多个线程等待一组事件发生. 闭锁状态包括一个计数器, 该计数器被初始化为一个正数, 表示需要等待的事件数. countDown 方法递减计数器, 表示有一个事件已经发生了, 而 await 方法等待计数器到达0, 这表示所有需要等待的事件都已经发生. 如果计数器的值非0, 那么 await 会一直阻塞直到计数器为0, 或者等待中的线程中断, 或者等待超时.

作者写了 net.jcip.examples.TestHarness 类来举例, 统计任务消耗的时间.

### 5.5.2 Future Task

FutureTask 也可以用做闭锁. (FutureTask 实现了 Future 语义, 表示一种抽象的可生成结果的计算) FutureTask 表示的计算是通过 Callable 来实现的, 相当于一种可生成结果的 Runnable, 并且可以处于以下3种状态: 等待运行(Waiting to run), 正在运行(Running)和运行完成(Completed). "执行完成"表示计算的所有可能结束方式, 包括正常结束, 由于取消而结束和由于异常而结束等. 当 FutureTask 进入完成状态后, 它会永远停止在这个状态上.

Future.get 的行为取决于任务的状态. 如果任务已经完成, 那么 get 会立即返回结果, 否则 get 将阻塞直到任务进入完成状态, 然后返回结果或者抛出异常. FutureTask 将计算结果从执行计算的线程传递到获取这个结果的线程, 而 FutureTask 的规范确保了这种传递过程能实现结果的安全发布.

FutureTask 在 Executor 框架中表示异步任务, 此外还可以用来表示一些时间较长的计算, 这些计算可以在使用计算结果之前启动. 作者写的程序清单 Preloader 就使用了 FutureTask 来执行一个高开销的计算, 并且计算结果将在稍后使用. 通过提前启动计算, 可以减少在等待结果时需要的时间.

作者写了一个 Preloader 例子. 例子中 Preloader 创建了一个 FutureTask, 其中包含从数据库加载产品信息的任务, 以及一个执行运算的线程. 由于在构造函数或者静态初始化方法中启动线程并不是一种好方法, 因此提供了一个 start 方法来启动线程. 当程序随后需要 ProductInfo 时, 可以调用 get 方法, 如果数据已经加载, 那么将返回这些数据, 否则将等待加载完成后再返回.

Callable 表示的任务可以抛出受检查的或未受检查的异常, 并且任何代码都可能抛出一个 Error. 无论任务代码抛出什么异常, 都会被封装到一个 ExecutionException 中, 并在 Future.get 中被重新抛出. 这将使调用 get 的代码变得复杂, 因为它不仅需要处理可能出现的 ExecutionException (以及未检查的 CancellationException), 而且还由于 ExecutionException 是作为一个 Throwable 类返回的, 因此处理起来并不容易.

在 Preloader 例子中, 当 get 方法抛出 ExecutionException 时, 可能是以下三种情况之一: Callable 抛出的受检查异常, RuntimeException, 以及 Error. 我们必须对每种情况进行单独处理, 但我们将使用程序 (作者给出的 LaunderThrowable 例子) 的辅助方法来封装一些复杂的异常处理逻辑. 在调用 launderThrowable 之前, Preloader 会首先检查已知的受检查异常, 并重新抛出它们. 剩下的是未检查异常, Preloader 将调用 launderThrowable 并抛出结果. 如果 Throwable 传递给 launderThrowable 是一个 Error, 那么 launderThrowable 将直接再次抛出它; 如果不是 RuntimeException, 那么将抛出一个 IllegalStateException 表示这是一个逻辑错误. 剩下的 RuntimeException, launderThrowable 将把它们返回给调用者, 而调用者通常会重新抛出它们.

### 5.5.3 信号量 Semaphores

计数信号量 (Counting Semaphore) 用来控制同时访问某个特定资源的操作数量, 或者同时执行某个指定操作的数量. 计数信号量还可以用来实现某种资源池, 或者对容器施加边界.

Semaphore 中管理着一组虚拟的许可 (permit). 在执行操作时可以首先获得许可 (只要还有剩余的许可), 并在使用以后释放许可. 如果没有许可, 那么 acquire 将阻塞直到有许可 (或者直到被中断或者操作超时). release 方法将返回一个许可给信号量. (注: 这种实现中不包含真正的许可对象, 只是存储了一个可用计数, 并且 Semaphore 也不会将许可与线程关联起来, 因此在一个线程中获得的许可可以在另一个线程中释放. 可以将 acquire 操作视为是消费一个许可, 而 release 操作是创建一个许可, Semaphore 并不受限于它在创建时的初始许可数量) 计算信号量的一种简化形式是二值信号量, 即初始值为 1 的 Semaphore. 二值信号量可以用作互斥体 (mutex), 并具备不可重入的加锁语义: 谁拥有这个唯一的许可, 谁就拥有了互斥锁.

Semaphore 可以用于实现资源池, 例如数据库连接池. 我们可以构造一个固定长度的资源池, 当池为空时, 请求资源将会失败, 但你真正希望看到的行为是阻塞而不是失败, 并且当池非空时解除阻塞. 如果将 Semaphore 的计数值初始化为池的大小, 并在从池中获取一个资源之前首先调用 acquire 方法获取一个许可, 在将资源返回给池之后调用 release 释放许可, 那么 acquire 将一直阻塞直到资源池不为空. 在第 12 章的有界缓冲类中将使用这项技术. (在构造阻塞对象池时, 一种更简单的方法是使用 BlockingQueue 来保存池的资源)

同样, 你也可以使用 Semaphore 将任何一种容器变成有界阻塞容器, 例如作者写的 BoundedHashSet 例子.

### 5.5.4 栅栏 Barrier

闭锁是一次性对象, 一旦进入终止状态, 就不能被重置.
栅栏 (Barrier) 类似于闭锁, 它能阻塞一组线程直到某个事件发生. 栅栏与闭锁的关键区别在于, 所有线程必须同时到达栅栏位置, 才能继续执行. 闭锁用于等待事件, 而栅栏用于等待其它线程. 栅栏用于实现一些协议, 例如几个家庭决定在某个地方集合: "所有人 6:00 在麦当劳碰头, 到了以后要等其他人, 之后再讨论下一步要做的事情."
CyclicBarrier 可以使一定数量的参与方反复地在栅栏位置汇集, 它在并行迭代算法中非常有用: 这个方法将阻塞直到所有线程都到达栅栏位置. 如果所有线程都到达了栅栏位置, 那么栅栏将打开, 此时所有线程都被释放, 而栅栏将被重置以便下次使用. 如果对 await 的调用超时, 或者 await 阻塞的线程被中断, 那么栅栏就被认为是打破了, 所有阻塞的 await 调用都将终止并抛出 BrokenBarrierException. 如果成功地通过栅栏, 那么 await 将为每个线程返回一个唯一的到达索引号, 我们可以利用这些索引来 "选举" 产生一个领导线程, 并在下一次迭代中由该领导线程执行一些特殊的工作. CyclicBarrier 还可以使你将一个栅栏操作传递给构造函数, 这是一个 Runnable, 当成功通过栅栏时会 (在一个子任务线程中) 执行它, 但在阻塞线程被释放之前是不能执行的. (不懂)
// TODO 先看完一遍, 再写笔记

## 6.2 Executor 框架

上标测试<sup>[[参考]](http://www.baidu.com)</sup>, 哈哈;
测试<sup>[22](#22)</sup>