---
title: 编写可读代码的艺术 笔记
noindex: true
---

# 1. 代码应当易于理解

代码的写法应当使别人理解它所需的时间最小化.

# 2. 把信息装到名字里 (表面层次的改进)

## 2.1 选择专业的词

选择非常专业的词, 并且避免使用"空洞"的词. 

例如 "get" 这个词没有表达出很多信息, 是从本地缓存中得到, 还是从数据库中, 或者从互联网中? 如果是从互联网中, 更专业的名字可以是 "fetch" 或者 "download".

在 BinaryTree 类中, "size" 这个方法没有承载很多信息, 是表示树的高度, 节点数, 还是树在内存中所占的空间? 更专业的词可以是 "height", "numNodes", "memoryBytes".

在 Thread 类中, "stop" 这个名字根据它到底做什么, 可能会有更专业的名字. 例如重量级操作的 "kill", 它不能恢复. 或者如果有方法让它 "resume", 可以叫它 "pause".

### 找到更有表现力的词

下面是一些例子, 这些单词更有表现力, 可能适合你的语境:

单词: 更多选择
send: deliver, dispatch, announce, distribute, route
find: search, extract, locate, recover
start: launch, create, begin, open
make: create, set up, build, generate, compose, add, new

## 2.2 避免像 tmp 和 retval 这样泛泛的名字

像 tmp, retval 和 foo 这样的名字往往是"我想不出名字"的托辞. 与其使用这样空洞的名字, 不如挑一个能描述这个实体的值或者目的的名字.

### tmp

tmp 这个名字只应用于短期存在且临时性为其主要存在因素的变量.

### 循环迭代器

像 i, j, iter 和 it 等名字常用作索引和循环迭代器. 尽管这些名字很空泛, 但是大家都知道它们的意思是"我是一个迭代器". (实际上, 如果你用这些名字来表示其它含义, 那会很混乱, 所以不要这么做.)

但有时会有比 i, j, k 更贴切的迭代器命名. 例如, 下面的循环要找到哪个 user 属于哪个 club:

~~~ java
for (int i = 0; i < clubs.size(); i++) {
    for (int j = 0; j < clubs[i].members.size(); j++) {
        for (int k = 0; k < user.size(); k++) {
            if (clubs[i].members[k] == users[j]) { // #1
                // do something
            }
        }
    }
}
~~~

上面代码中的 #1 中, 如果使用更精确的名字会有帮助. 如果不把循环索引命名为(i, j, k), 另一个选择可以是(club_i, members_i, user_i)或者, 更简化一点(ci, mi, ui). 这种方式会帮助发现代码中的引用缺陷.

### 对于空泛名字的裁定

如果你要使用像 tmp, it 或者 retval 这样空泛的名字, 那么你要有个好的理由.

如果想不出更好的名字, 那么用个没有意义的名字, 像 foo, 然后继续做别的事, 这很容易. 但如果你养成习惯多花几秒钟想出个好名字, 你会发现你的"命名能力"很快提升.

## 2.3 用具体的名字代替抽象的名字

在给变量, 函数或者其它元素命名时, 要把它描述得更具体而不是更抽象.

例如, 假设你有一个内部方法叫做 ```ServerCanStart()```, 它检测服务是否可以监听某个给定的 TCP/IP 端口. 然而 ```ServerCanStart()``` 有点抽象. ```CanListenOnPort()``` 就更具体一些. 这个名字直接地描述了这个方法要做什么事情.

下面的两个例子更深入地描绘了这个概念.

### 例子: ```DISALLOW_EVIL_CONSTRUCTORS```

这个例子来自 Google 的代码库. 在 C++ 里, 如果你不为类定义拷贝构造函数或者赋值操作符, 那就会有一个默认的. 尽管这很方便, 这些方法很容易导致内存泄漏以及其他灾难, 因为它们在你可能想不到的"幕后"地方运行.

所以, Google 有个便利的方法来禁止这些"邪恶"的构造函数, 就是用命名为 ```DISALLOW_EVIL_CONSTRUCTORS``` 这个宏. 它包括禁止拷贝构造函数和赋值操作.

然而 ```DISALLOW_EVIL_CONSTRUCTORS``` 这个名字并不是很好. 对于 "邪恶" 这个词的使用包含了对于一个有争议话题过于强烈的立场. 更重要的是, 这个宏到底禁止了什么这一点是不清楚的. 最后这个名字使用了几年, 但最终换成了一个不那么嚣张而且更具体的名字: ```DISALLOW_COPY_AND_ASSIGN```.

### 例子: ```--run_locally``` (本地运行)

我们的一个程序有个可选的命令行标志叫做 ```--run_locally```. 这个标志会使得这个程序输出额外的调试信息, 但是会运行得更慢. 这个标志一般用于在本地机器上测试, 例如在笔记本电脑上. 但是当这个程序运行在远程服务器时, 性能是很重要的, 因此不会使用这个标志.

你能看出来为什么会有 ```--run_locally``` 这个名字, 但是它有几个问题:
- 团队里的新成员不知道它到底是做什么的. 可能在本地运行时使用它(想象一下), 但不明白为什么需要它.
- 偶尔, 我们在远程运行这个程序时也要输出调试信息. 向一个运行在远端的程序传递 ```--run_locally``` 看上去很滑稽, 而且很让人迷惑.
- 有时我们可能要在本地运行性能测试, 这时我们不想让日志把它拖慢, 所以我们不会使用 ```--run_locally```.

这里的问题是 ```--run_locally``` 是由它所使用的典型环境而得名. 用像 ```--extra_logging``` 这样的名字来代换可能会更直接明了.

但是如果 ```run_locally``` 需要做比额外日志更多的事情怎么办? 例如, 假设它需要建立和使用一个特殊的本地数据库. 现在 ```--run_locally``` 看上去更吸引人了, 因为它可以同时控制这两种情况.

但这样用的话就变成了因为一个名字含糊婉转而需要选择它, 这可能不是一个好主意. 更好的办法是再创建一个标志叫 ```--use_local_database```. 尽管你现在要用两个标志, 但这两个标志非常明确, 不会混淆两个正交的含义, 并且你可明确地选择一个.

## 2.4 为名字附带更多信息

一个变量名就像是一个小小的注释. 尽管空间是很大, 但不管你在名中挤进任何额外的信息, 每次有人看到这个变量名时都会同时看到这些信息.

因此, 如果关于一个变量有什么重要事情的读者必须知道, 那么是值得把额外的"词"添加到名字中的. 例如, 假设你有一个变量包含一个十六进制字符串:

~~~
string id; // Example: "abf4293cde32"
~~~

如果让读者记住这个 ID 的格式很重要的话, 你可以把它改名为 hex_id.

### 带单位的值

如果你的变量是一个度量的话(如时间长度或者字节数), 那么最好把名字带上它的单位.

例如开始时间 ```start``` 变量改名为 ```start_ms```, 可以让所有的地方更明确.

除了时间, 还有很多在编程时会遇到的单位. 下表列出一些没有单位函数参数以及带单位的版本:
- delay -> delay_secs
- size -> size_mb
- limit -> max_kbps
- angle -> degrees_cw

### 附带其他重要属性

这种给名字附带额外信息的技巧不仅限于单位. 在对于这个变量存在危险或者意外的任何时候你都该采用它.

例如, 很多安全漏洞来源于没有意识到你的程序接收到的某些数据还没有处于安全状态. 在这种情况下, 你可能想要使用像 ```untrustedUrl``` 或者 ```unsafeMessageBody``` 这样的名字. 在调用了清查不安全输入的函数后, 得到的变量可以命名为 ```trustedUrl``` 或者 ```safeMessageBody```.

下面给出了更多需要给名字附加上额外信息的例子:
- password -> plaintext_password (一个纯文本格式的密码, 需要加密后才能进一步使用)
- comment -> unescaped_comment (一条用户提供的注释, 需要转义之后才能用于显示)
- html -> html_utf8 (已转化为 UTF-8 格式的 html 字节)
- data -> data_urlenc (以 url 方式编码的输入数据)

但你不应该给程序中每个变量都加上像 unescaped_ 或者 _utf8 这样的属性. 如果有人误解了这个变量就很容易产生缺陷, 尤其是会产生像安全缺陷这样可怕的结果. 基本上, 如果这是一个需要理解的关键信息, 那就把它放在名字里.

拓展: 微软的匈牙利表示法

## 2.5 名字应该有多长

当选择好名字时, 有一个隐含的约束是名字不能太长.

如何来决定是把一变量命名为 d, days 还是 days_since_last_update 呢?

这是要你自己要拿主意的, 最好的答案和这个变量如何使用有关系, 但下面还是提出了一些指导原则.

### 在小的作用域里可以使用短的名字

"作用域"小的标识符(对于多少行其它代码可见)也不用带上太多信息. 也就是说, 因为所有的信息(变量的类型, 它的初值, 如何析构等)都很容易看到, 所以可以用很短的名字.

### 输入长名字 不再是个问题

利用编程文本编辑器的单词补全功能.

### 首字母缩略词和缩写

程序员有时会采用首字母缩略词和缩写来命名, 以便保持较短的名字, 例如把一个类命名为 BEManager 而不是 BackEndManager. 这种名字会让人费解, 冒这种风险是否值得?

经验原则是: 团队的新成员是否能理解这个名字的含义? 如果能, 那可能就没有问题.

例如, 对程序员来讲, 用 eval 来代替 evaluation, 用 doc 来代替 document, 用 str 来代替 string 是相当普遍的.

### 丢掉没用的词

有时名字中的某些单词可以拿掉而不会损失任何信息. 例如 Convert To String() 就不如 To String() 这个更短的名字, 而且没有丢失任何有用的信息. 同样, 不用 DoServeLoop(), ServeLoop() 也一样清楚.

### 利用名字的格式来传递含义

对于不同的实体使用不同的格式就像语法高亮显示的形式一样, 能帮你更容易地阅读代码.

比如 Google 开源项目格式规范的 C++ 代码中, 类成员变量和普通变量一样, 但必须以一条下划线结尾, 如 ```offset_```. 刚开始看可能觉得这个规范有点怪, 但是能立即区分出是成员变量还是其它变量, 这一点还是很方便的. 例如, 如果你在浏览一个大的方法中的代码, 看到这样一行: 

~~~
stats.clear()
~~~

你本来可能要想"stats 属于这个类吗? 这行代码是否会改变这个类的内部状态?", 如果使用了 member_ 这个规范, 你就能迅速得到结论: "不, stats 一定是个局部变量. 否则它就会命名为 stats_."

### 其他格式规范

根据项目上下文或语言的不同, 还可以采用其它一些格式规范使得名字包含更多信息. 

## 2.6 总结

本章的唯一主题是: 把信息塞入名字中. 这句话的含义是, 读者仅通过读到名字就可以获得大量信息.

# 第 3 章 不会误解的名字

前一章中, 我们讲到了如何把信息塞入名字. 本章会关注另一个话题: 小心可能会有歧义的名字.

关键思想: 要多问自己几遍: "这个名字会被别人解读成其它的含义吗?", 要仔细审视这个名字.

## 3.1 例子: Filter()

这里的问题是 filter 是个二义性单词. 我们不清楚它的含义到底是 "挑出" 还是 "减掉". 最好避免使用 filter 这个名字, 因为它太容易误解.

## 3.2 Clip(text, length)

假设你有个函数用来剪切一个段落的内容, 你可能想到 Clip() 的两种行为方式:
- 从尾部删除 length 的长度
- 截掉最大长度为 length 的一段

与其让读者乱猜代码, 不如把函数的名字改成 Truncate(text, length) 来表示第二种可能性.

然而, 参数名 length 也不太好. 如果叫 max_length 的话可能会更清楚.

这样也还没有完. 就算是 max_length 这个名字也还是会有多种解读:
- 字节数
- 字符数
- 字数

如你在前一章中所见, 这属于应当把单位附加在名字后面的那种情况. 在本例中, 我们是指 "字符数", 所以不应该用 max_length, 而要用 max_chars.

## 3.3 推荐用 min 和 max 来表示 (包含) 极限

~~~
[min, max]
~~~

像 ```limit``` 这种词, 我们无法知道它是 "少于" 还是 "少于且包括".

命名极限最清楚的方式是在要限制的东西前加上 ```max_``` 或者 ```min_```.

## 3.4 推荐用 first 和 last 来表示包含的范围

~~~
[first, last]
~~~

尽管 start 是个合理的参数名, 但 stop 可以有多种解读. 对于这样包含的范围(这种范围包含开头和结尾), 一个好的选择是 first/last. (或者 min/max 如果听上去合理的话)

## 3.5 推荐用 begin 和 end 来表示包含/排除的范围

~~~
[begin, end)
~~~

对于上面这种情况, 什么样的一对名字更好呢? 对于命名包含/排除范围典型的编程规范是使用 begin/end.

但是 end 这个词有点二义性. 例如, 在句子 "我读到这本书的 end 部分了", 这里的 end 是包含的. 遗憾的是, 英语中没有一个合适的词来表示 "刚好超过最后一个值".

因为对 begin/end 的使用是如此常见, 它已经是最好的选择了.

## 3.6 给布尔值命名

当为布尔变量或者返回布尔值的函数选择名字时, 要确保返回 true 和 false 的意义很明确.

~~~ c
bool read_password = true;
~~~

会有两种截然不同的解释:
- 我们需要读取密码
- 已经读取了密码

在本例中, 最好避免用 read 这个词, 用 need_password 或者 user_is_authenticated 这样的名字来代替.

通常来讲, 加上像 is, has, can 或 should 这样的词, 可以把布尔值变得更明确.

最后, 最好避免使用反义名字, 例如, 不要用:

~~~ c
bool disable_ssl = false
~~~

更简单易读 (而且更紧凑) 的表示方式是:

~~~ c
bool use_ssl = true;
~~~

## 3.7 与使用者的期望相匹配

有些名字之所以会让人误解是因为用户对它们的含义有先入为主的印象, 就算你的本意并非如此. 在这种情况下, 最好放弃这个名字而改用一个不会让人误解的名字.

### 例子: get*()

很多程序员都习惯了把以 get 开始的方法当作 "轻量级访问器" 这样的用法, 它只是简单地返回一个内部成员变量. 如果违背这个习惯很可能会误导用户.

以下是一个用 Java 写的例子, 请不要这样做:

~~~ java
public class StatisticsCollector {
    public void addSample(double x) { ... }

    public double getMean() {
        // Iterate through all samples and return total / num_samples
    }
}
~~~

在这个例子中, getMean() 的实现是要遍历所有经过的数据并同时计算中值. 如果有大量的数据的话, 这样的一步可能会有很大的代价. 但一个容易轻信的程序员可能会随意地调用 getMean(), 还以为这是个没什么代价的调用.

相反, 这个方法应当重命名为像 computeMean() 这样的名字, 后者听起来更像是有些代价的操作. (另一种做法是, 用新的实现方法使它真的成为一个轻量级的操作)

### 例子: list:size()

下面是一个来自 C++ 标准库中的例子. 曾经有个很难发现的缺陷, 使得我们的一台服务器慢的像蜗牛在爬, 就是下面的代码造成的:

~~~ C++
void ShrinkList(list<Node>& list, int max_size) {
    while (list.size() > max_size) {
        FreeNode(list.back());
        list.pop_back();
    }
}
~~~

这里的 "缺陷" 是, 作者不知道 ```list.size()``` 是一个 O(n) 操作, 它要一个节点一个节点地历数列表, 而不是只返回一个事先算好的个数, 这就使得 ShrinkList() 成了一个 O(n^2) 操作.

这段代码从技术上来讲 "正确", 事实上它也通过了所有的单元测试. 但当把 ShrinkList() 应用于有 100 万个元素的列表上时, 要花超过一个小时来完成.

可能你在想: "这是调用者的错, 他应该更仔细地读文档." 有道理, 但在本例中, list.size() 不是一个固定时间的操作, 这一点是出人意料的. 所有其它的 C++ 容器类的 size() 方法都是时间固定的.

假使 size() 的名字是 countSize() 或者 countElements(), 很可能就会避免相同的错误. C++ 标准库的作者可能是希望把它命名为 size() 以和其它的容器一致, 就像 vector 和 map. 但正是因为他们的这个选择使得程序员很容易误把它当成一个快速的操作, 就像其它的容器一样. 谢天谢地, 现在最新的 C++ 标准库把 size() 改成了 O(1).

## 3.8 例子: 如何权衡多个备选名字

当你要选一个好名字时, 可能会同时考虑多个备选方案. 通常你要在头脑中盘算一下每个名字的好处, 然后才能得出最后的选择. 下面的例子示范了这个评判过程.

// TODO 这里建议看书

## 3.9 总结

不会误解的名字是最好的名字, 阅读你代码的人应该理解你的本意, 并且不会有其它的理解. 遗憾的是, 很多英语单词在用来编程时是多义性的, 例如 filter, length 和 limit.

在你决定使用一个名字以前, 要吹毛求疵一点, 来想象一下你的名字会被误解成什么. 最好的名字是不会误解的.

当要定义一个值的上限或下限时, max_ 和 min_ 是很好的前缀. 对于包含的范围, first 和 last 是好的选择. 对于包含/排除范围, begin 和 end 是最好的选择, 因为它们最常用.

当为布尔值命名时, 使用 is 和 has 这样的词来明确表示它是个布尔值, 避免使用反义的词.

要小心用户对待特定词的期望. 例如, 用户会期望 get() 或者 size() 是轻量的方法.

# 第 4 章 审美

好的源码应当 "看上去养眼". 本章会告诉大家如何使用好的留白, 对齐及顺序来让你的代码变得更易读.

确切地说, 有三条原则:
- 使用一致的布局, 让读者很快就习惯这种风格.
- 让相似的代码看上去相似.
- 把相关的代码行分组, 形成代码块.

## 4.1 为什么审美这么重要

编程大部分时间都花在看代码上, 浏览代码的速度越快, 人们就越容易使用它.

## 4.2 重新安排换行来保持一致和紧凑

示例:

~~~ java
public class PerformanceTester {
    // TcpConnectionSimulator(throughput, latency, jitter, packet_loss)
    //                            [Kbps]    [ms]    [ms]    [percent]

    public static final TcpConnectionSimulator wifi = 
        new TcpConnectionSimulator(500, 80, 200 1);

    public static final TcpConnectionSimulator t3_fiber = 
        new TcpConnectionSimulator(45000, 10, 0, 0);

    public static final TcpConnectionSimulator cell = 
        new TcpConnectionSimulator(100, 400, 250, 5);
}
~~~

我们把注释挪到了上面, 然后把所有的参数都放在一行上. 现在尽管注释不再紧挨相邻的每个数字, 但 "数据" 现在排成更紧凑的一个表格.

## 4.3 用方法来整理不规则的东西

// TODO 还是看书吧

尽管书中例子的目的仅仅是让代码更有美感, 但这几个改动同时有几个附带的效果: 
- 它消除了原来代码中大量的重复, 让代码变得更紧凑
- 每个测试用例重要的部分 (名字和错误字符串) 现在都变得很直白. 以前, 这些字符串是混杂在像 database_connection 和 error 这样的标识之间的. 这使得一眼看全这段代码变得很难.
- 现在添加新测试应当更简单

这个故事想要传达的寓意是使代码 "看上去漂亮" 通常会带来不仅限于表面层次的改进, 它可能会帮你把代码的结构做得更好.

## 4.4 在需要时使用列对齐

整齐的边和列让读者可轻松地浏览文本.

有时你可以借用 "列对齐" 的方法来让代码易读. 例如, 在前一部分中, 你可以用空白把 CheckFullName() 的参数排成:

~~~ java
CheckFullName("Doug Adams"   , "Mr. Douglas Adams" , "");
CheckFullName(" Jake  Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy"  , ""                  , "no match found");
CheckFullName("John"         , ""                  , "more than one result");
~~~

在这段代码中, 很容易区分出 CheckFullName() 的第二个和第三个参数. 下面是一个简单的例子, 它有一大组变量定义:

~~~ python
# Extract POST parameters to local variables
details  = request.POST.get('detail') 
location = request.POST.get('location') 
phone    = equest.POST.get('phone') 
email    = request.POST.get('email') 
url      = request.POST.get('url') 
~~~

你可能注意到了, 第三个定义有个拼写错误 (把 request 写成了 equest). 当所有的内容都这么整齐地排列起来时, 这样的错误就很明显.

整齐的排列有助于从一列跳到另一列.

### 你应该用列对齐吗

列的边提供了 "可见的栏杆", 阅读起来很方便. 这是个 "让相似的代码看起来相似" 的好例子.

但有些程序员不喜欢它. 一个原因是, 建立和维护对齐的工作量很大. 另一个原因是, 在改动时它造成了更多的 "不同", 对一行的改动可能会导致另外 5 行也要改动. (大部分只是空白)

作者的建议是要试试. 在他们的经验中, 它并不像程序员担心的那么费工夫. 如果真的很费工夫, 你可以不这么做.

## 4.5 选一个有意义的顺序, 使用一致地使用它

在很多情况下, 代码的顺序不会影响其正确性. 把它们按有意义的方式排列会有帮助.

- 让变量的顺序与对应的 HTML 表单中 ```<input>``` 字段的顺序相匹配.
- 从 "最重要" 到 "最不重要" 排序
- 按字母顺序排序

无论使用什么顺序, 你在代码中应当始终使用这一顺序. 如果后面改变了这个顺序, 那会让人很困惑.

## 4.6 把声明按块组织起来

我们的大脑很自然地会按照分组和层次结构来思考, 因此你可以通过这样的组织方式来帮助读者快速地理解你的代码.

例如, 下面是一个前端服务器的 C++ 类, 这里有它所有方法的声明:

~~~ C++
class FrontendServer {
    public:
        FrontedServer();
        void ViewProfile(HttpRequest* request);
        void OpenDatabase(string location, string user);
        void SaveProfile(Httprequest* request);
        string ExtractQueryParam(HttpRequest* request, string param);
        void ReplyOK(HttpRequest* request, string html);
        void FindFriends(HttpRequest* request);
        void ReplyNotFound(HttpRequest* request, string error);
        void CloseDatabase(string location);
        ~FrontedServer();
}
~~~

这不是很难看的代码, 但可以肯定这样的布局不会对读者更快地理解所有的方法有什么帮助. 不要把所有的方法都放到一个巨大的代码块中, 应当按逻辑把它们分成组, 像以下这样:

~~~ C++
class FrontendServer {
    public:
        FrontedServer();
        ~FrontedServer();

        // Handlers
        void ViewProfile(HttpRequest* request);
        void SaveProfile(Httprequest* request);
        void FindFriends(HttpRequest* request);

        // Request/Reply Utilities
        string ExtractQueryParam(HttpRequest* request, string param);
        void ReplyOK(HttpRequest* request, string html);
        void ReplyNotFound(HttpRequest* request, string error);

        // Database Helpers
        void OpenDatabase(string location, string user);
        void CloseDatabase(string location);
}
~~~

这个版本容易理解多了. 它还更易读, 尽管代码行数更多了. 原因是你可以快速地找出 4 个高层次段落, 然后在需要时再阅读每个段落的具体内容.

## 4.7 把代码分成 "段落"

书面文字要分成段落是由于以下几个原因:
- 它是一种把相似的想法放在一起并于其它想法分开的方法.
- 它提供了可见的 "脚印", 如果没有它, 会很容易找不到你读到哪里了.
- 它便于段落之间的导航.

因为同样的原因, 代码也应当分成 "段落". 例如, 没有人会喜欢读下面这样一大块代码:

// TODO 看书

作者还给每个段加了一条总结性的注释, 这也会帮助读者浏览代码. 当然也有很多种方法可以分开代码, 程序员可能会对长一点或短一点的段落有不同的偏好.

## 4.8 个人风格与一致性

有相当一部分审美选择可以归结为个人风格. 例如, 类定义的大括号该放在哪里:

~~~ java
class Logger {
    ...
}
~~~

还是:

~~~ java
class Logger
{
    ...
}
~~~

选择一种风格而非另一种, 不会真的影响到代码的可读性. 但如果把两种风格混在一起, 就会对可读性有影响了.

曾经在作者所从事过的很多项目中, 作者感觉团队所用的风格是 "错误" 的, 但是他们还是遵守项目的习惯, 因为他们知道一致性要重要得多.

一致的风格比 "正确" 的风格更重要

## 4.9 总结

大家都愿意读有美感的代码. 通过把代码用一致的, 有意义的方式 "格式化", 可以把代码变得更容易读, 并且可以读得更快.

下面是讨论过的一些具体技巧:
- 如果多个代码块做相似的事情, 尝试让它们有同样的剪影.
- 把代码按 "列" 对齐可以让代码更容易浏览
- 如果在一段代码中提到 A, B 和 C, 那么不要在另一段中说 B, C 和 A. 选择一个有意义的顺序, 并始终用这样的顺序.
- 用空行来把大块代码分成逻辑上的 "段落".

# 第 5 章 该写什么样的注释

本章旨在帮助你明白应该写什么样的注释. 你可能以为注释的目的是 "解释代码做了什么", 但这只是其中很小的一部分.

注释的目的是尽量帮助读者了解得和作者一样多.

当你写代码时, 你的脑海里会有很多有价值的信息. 当其他人读你的代码时, 这些信息已经丢失了, 他们所见到的只是眼前的代码.

## 5.1 什么不需要注释

下面代码中所有的注释都是没有价值的:

~~~ C++
// The class definition for Account
class Account {
    public:
        // Constructor
        Account();

        // Set the profit member to a new value
        void SetProfit(double profit);

        // Return the profit from this Account
        double GetProfit();
};
~~~

这些注释没有价值是因为它们并没有提供任何新的信息, 也不能帮助读者更好地理解代码.

不要为那些从代码本身就能快速推断的事实写注释.

这里的 "快速" 是个重要的区别, 考虑一下下面这段 Python 代码:

~~~ python
# remove everything after the second '*'
name = '*'.join(line.split('*')[:2])
~~~

从技术上来讲, 这里的注释也没有表达出任何 "新信息". 如果你阅读代码本身, 你最终会明白它到底在做什么. 但对于大多数程序员来讲, 读有注释的代码比没有注释的代码理解起来要快速得多.

### 不要为了注释而注释

有些教授要求他们的学生在他们的代码作业中为每个函数都加上注释. 结果是, 有些程序员会对没有注释的函数有负罪感, 以至于他们把函数的名字和参数用句子的形式重写了一遍:

~~~ c
// Find the Node in the given subtree, with the given name, using the given depth.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
~~~

这种情况属于 "没有价值的注释" 一类, 函数的声明与其注释实际上是一样的. 对于这条注释要么删除它, 要么改进它.

如果你想要在这里写条注释, 它最好也能给出更多重要的细节:

~~~ c
// Find a Node with the given 'name' or return NULL.
// If depth <= 0, only 'subtree' is inspected.
// If depth == N, only 'subtree' and N levels below are inspected.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
~~~

### 不要给不好的名字加注释 应该把名字改好

注释不应用于粉饰不好的名字. 例如, 有一个叫做 CleanReply() 的函数, 加上了看上去有用的注释:

~~~ c
// Enforce limits on the Reply as stated in the Request,
// such as the number of items returned, or total byte size, etc.
void CleanReply(Request request, Reply reply);
~~~

这里大部分的注释只是在解释 "clean" 是什么意思. 更好的做法是把 "enforce limits" 这个词组加到函数名里:

~~~ c
// Mack sure 'reply' meets the count/byte/etc. limits from the 'request'
void EnforceLimitsFromRequest(Request request, Reply reply);
~~~

这个函数现在更加 "自我说明" 了. 一个好的名字比一个好的注释更重要, 因为在任何用到这个函数的地方都能看得到它.

下面是另一个例子, 给名字不大好的函数加注释:

~~~ c
// Releases the handle for this key. This doesn't modify the actual registry.
void DeleteRegistry(RegistryKey* key);
~~~

DeleteRegistry() 这个名字听起来像是一个很危险的函数 (它会删除注册表?!) 注释里的 "它不会改动真正的注册表" 是想澄清困惑.

我们可以用一个更加自我说明的名字, 就像:

~~~ c
void ReleaseRegistryHandle(RegistryKey* key);
~~~

通常来讲, 你不需要 "拐杖式注释", 试图粉饰可读性差的代码的注释. 写代码的人常常把这条规则表述成: 

好代码 > 坏代码 + 好注释.

## 5.2 记录你的思想

很多好的注释仅通过 "记录你的想法" 就能得到, 也就是那些你在写代码时有过的重要想法.

### 加入 "导演评论"

电影中常有 "导演评论" 部分, 电影制作者在其中给出自己的见解并且通过讲故事来帮助你理解这部电影是如何制作的. 同样, 你应该在代码中也加入注释来记录你对代码有价值的见解.

下面是一个例子:

~~~ c
// 出乎意料的是, 对于这些数据用二叉树比用哈希表快 40%
// 哈希运算的代价比左/右比较大得多
~~~

这段注释教会读者一些事情, 并且防止他们 为无谓的优化而浪费时间.

下面是另一个例子:

~~~ c
// 作为整体可能会丢掉几个词. 这没有问题. 要 100% 解决太难了
~~~

如果没有这段注释, 读者可能会一位这是个 bug 然后浪费时间尝试找到能让它失败的测试用例, 或者尝试改正这个 bug.

注释也可以用来解释为什么代码写得不那么整洁:

~~~ c
// 这个类正在变得越来越乱
// 也许我们应该建立一个 'ResourceNode' 子类来帮助整理
~~~

这段注释承认代码很乱, 但同时也鼓励下一个人改正它 (还给出了具体的建议). 如果没有这段注释, 很多读者可能会被这段乱代码吓到而不敢碰它.

### 为代码中的瑕疵写注释

代码始终在演进, 并且在这个过程中肯定会有瑕疵, 不要不好意思把这些瑕疵记录下来. 例如, 当代码需要改进时:

~~~ c
// TODO: 采用更快的算法
~~~

或者当代码没有完成时:

~~~ c
// TODO (dustin): 处理除 JPEG 以外的图像格式
~~~

有几种标记在程序员中很流行:

- TODO: 我还没有处理的事情
- FIXME: 已知的无法运行的代码
- HACK: 对一个问题不得不采用的比较粗糙的解决方案
- XXX: 危险! 这里有重要的问题

你的团队可能对于是否可以使用及何时使用这些标识有具体的规范. 例如, TODO: 可能只用于重要的问题. 如果是这样, 你可以用像 todo: (小写) 或者 maybe-later: 这样的方法表示次要的缺陷.

重要的是你应该可以随时把代码将来应该如何改动的想法用注释记录下来. 这种注释给读者带来对代码质量和当前状态的宝贵见解, 甚至可能会给他们指出如何改进代码的方向.

### 给常量加注解

当定义常量时, 通常在常量背后都有一个关于它是什么或者为什么它是这个值的 "故事". 例如, 你可能会在代码中看到如下常量:

~~~ python
NUM_THREADS = 8
~~~

这一行看上去可能不需要注解, 但很可能选择用这个值的程序员知道得比这个要多:

~~~ python
NUM_THREADS = 8 # as long as it's >= 2 * num_processors, that's good enough
~~~

现在, 读代码的人就有了调整这个值的指南了. (比如, 设置成 1 可能就太低了, 设置成 50 又太夸张了)

或者有时常量的值本身并不重要. 达到这种效果的注释也会有用:

~~~ c
// Impose a reasonable limit - no human can read that much anyway.
const int MAX_RSS_SUBSCRIPTIONS = 1000;
~~~

还有这样的情况, 它是一个高精度精细调整过的值, 可能不应该大幅改动.

~~~ c
image_quality = 0.72; // users thought 0.72 gave the best size/quality tradeoff
~~~

在上述所有例子中, 你可能不会想到要加注释, 但它们的确很有帮助.

有些常量不需要注释, 因为它们的名字本身已经很清楚 (例如 SECONDS_PER_DAY). 但是在我们的经验中, 很多常量可以通过加注释得以改进. 这不过是匆匆记下你在决定这个常量值时的想法而已.

## 5.3 站在读者的角度

作者在本书中所用的一个通用的技术是想象你的代码对于外人来讲看起来是什么样子的, 这个人并不像你那样熟悉你的项目. 这个技术对于发现什么地方需要注释尤其有用.

### 意料之中的提问

当别人读你的代码时, 有些部分更可能让他们有这样的想法: "什么? 为什么会这样?" 你的工作就是要给这些部分加上注释.

例如, 看看下面 Clear() 的定义:

~~~ c++
struct Recorder {
    vector<float> data;
    ...
    void Clear() {
        vector<float>().swap(data); // Huh? Why not just data.clear()?
    }
};
~~~

大多数 C++ 程序员看到这段代码时都会想: "为什么他不直接用 data.clear() 而是与一个空的向量交换?" 实际上只有这样才能强制使向量真正地把内存还给内存分配器. 这不是一个众所周知的 C++ 细节. 起码要加上这样的注释:

~~~ C++
// Force vector to relinquish its memory (look up "STL swap trick")
vector<float>().swap(data);
~~~

### 公布可能的陷阱

当为一个函数或者类写文档时, 可以问自己这样的问题: "这段代码有什么出人意料的地方? 会不会被误用?" 基本上就是说你需要 "未雨绸缪", 预料到人们使用你的代码时可能会遇到的问题.

例如, 假设你写了一个函数来向给定的用户发邮件:

~~~ c
void SendEmail(string to, string subject, string body);
~~~

这个函数的实现包括连接到外部邮件服务, 这可能会花整整一秒, 或者更久. 可能有人在写 Web 应用时在不知情的情况下错误地在处理 HTTP 请求时调用这个函数. (这么做可能会导致他们的 Web 应用在邮件服务宕机时 "挂起")

为了避免这种灾难, 你应当为这个 "实现细节" 加上注释:

~~~ c
// 调用外部服务来发送邮件. (1 分钟之后超时.)
void SendEmail(string to, string subject, string body);
~~~

下面有另一个例子: 假设你有一个函数 FixBrokenHtml() 用来尝试重写损坏的 HTML, 通过插入结束标记这样的方法:

~~~ c++
def FixBrokenHtml(html): ...
~~~

这个函数运行得很好, 但要警惕当有深嵌套而且不匹配的标记时, 它的运行时间会暴增. 对于很差的 HTML 输入, 该函数可能要运行几分钟.

与其让用户自己慢慢发现这一点, 不如提前声明:

~~~ c++
// 运行时间将达到 O(number_tags * average_tag_depth), 所以小心严重嵌套的输入.
def FixBrokenHtml(html): ...
~~~

### "全局观" 注释

对于团队的新成员来讲, 最难的事情之一就是理解 "全局观", 类之间如何交互, 数据如何在整个系统中流动, 以及入口点在哪里. 设计系统的人经常忘记给这些东西加注释, 只缘身在此山中.

下面是一个文件级别注释的简单例子:

~~~ c
// 这个文件包含一些辅助函数, 为我们的文件系统提供了更便利的接口
// 它处理了文件权限及其他基本的细节
~~~

不要对于写庞大的正式文档这种想法不知所措, 几句精心选择的话比什么都没有强.

### 总结性注释

就算在一个函数的内部, 给 "全局观" 写注释也是个不错的主意. 下面是一个例子, 这段注释巧妙地总结了其后的低层代码:

~~~ python
# Find all the items that customers purchased for themselves
for customer_id in all_customers:
    for sale in all_sales[customer_id].sales:
        if sale.recipient == customer_id:
            ...
~~~

没有这段注释, 每行代码都有些谜团. (我知道这是在遍历 all_customers, 但是为什么要这么做?)

在包含几大块的长函数中这种总结性的注释尤其有用:

~~~ python
def GenerateUserReport():
    # Acquire a lock for this user
    ...
    # Read user's info from the database
    ...

    # Write info to a file
    ...
    # Release the lock for this user
~~~

这些注释同时也是对于函数所作事情的总结, 因此读者可以在深入了解细节之前就能得到该函数的主旨. (如果这些大段很容易分开, 你可以直接把它们写成函数. 正如我们前面提到的, 好代码比有好的注释的差代码要强)

注释应该说明 "做什么", "为什么" 还是 "怎么做"?

你可能听说过这样的建议: "注释应该说明 '为什么这样做' 而非 '做什么' (或者 '怎么做')". 这虽然很容易记, 但我们觉得这种说法太简单化, 并且对于不同的人有不同的含义.

我们的建议是你可以做任何能帮助读者更容易理解代码的事. 这可能也会包含对于 "做什么", "怎么做" 或者 "为什么" 的注释. (或者同时注释这三个方面).

## 5.4 最后的思考 克服"作者心里阻滞"

很多程序员不喜欢写注释, 因为要写出好的注释感觉好像要花很多功夫. 当作者有了这种 "作者心理阻滞", 最好的办法就是现在就开始写. 因此下次当你对写注释犹豫不决时, 就直接把你心里想的写下来就好了, 虽然这种注释可能是不成熟的.

例如, 假设你正在写一个函数, 然后心想: "噢, 天啊, 如果一旦这东西在列表中有重复的话会变得很难处理的." 那么就直接把它写下来:

~~~ c
// 噢, 天啊, 如果一旦这东西在列表中有重复的话会变得很难处理的.
~~~

看到了, 这难吗? 它作为注释来讲实际上没那么差, 起码比没有强. 可能措施有点含糊. 要改正这一点, 可以把每个子句改得更专业一些:

- "噢, 天啊", 实际上, 你的意思是 "小心: 这个地方需要注意"
- "这东西", 实际上, 你的意思是 "处理输入的这段代码"
- "会变得很难处理", 实际上, 你的意思是 "会变得难以实现"

新的注释可以是:

~~~ c
// 小心: 这段代码不会处理列表中的重复 (因为这很难做到)
~~~

请注意我们把写注释这件事拆成了几个简单的步骤:

1. 不管你心里想什么, 先把它写下来.
2. 读一下这段注释, 看看有没有什么地方可以改进
3. 不断改进

当你经常写注释, 你就会发现步骤 1 所产生的注释变得越来越好, 最后可能不再需要做任何修改了. 并且通过早写注释和常写注释, 你可以避免在最后要写一大堆注释这种令人不快的状况.

## 5.5 总结

注释的目的是帮助读者了解作者在写代码时已经知道的那些事情. 本章介绍了如何发现所有的并不那么明显的信息块并且把它们写下来.

什么地方不需要注释:
- 能从代码本身中迅速地推断的事实.
- 用来粉饰烂代码 (例如撇脚的函数名) 的 "拐杖式注释", 应该把代码写好.

你应该记录下来的想法包括:
- 对于为什么代码写成这样而不是那样的内在理由 ("指导性批注").
- 代码中的缺陷, 使用像 TODO: 或者 XXX: 这样的标记.
- 常量背后的故事, 为什么是这个值.

站在读者的立场上思考:
- 预料到代码中哪些部分会让读者说: "啊?" 并且给它们加上注释.
- 为普通读者意料之外的行为加上注释.
- 在文件/类的级别上使用 "全局观" 注释来解释所有的部分是如何一起工作的.
- 用注释来总结代码块, 使读者不致迷失在细节中.

# 第 6 章 写出言简意赅的注释

前一章是关于发现什么地方要写注释的. 本章则是关于如何写出言简意赅的注释.

如果你要写注释, 最好把它写得精确, 越明确和细致越好. 另外, 由于注释在屏幕上也要占很多的地方, 并且需要花更多的时间来读, 因此, 注释也需要很紧凑.

注释应当有很高的信息/空间率.

## 6.1 让注释保持紧凑

下面的例子是一个 C++ 类型定义的注释:

~~~ c++
// The int is the CategoryType.
// The first float in the inner pair is the 'score'
// the second is the 'weight'
typedef hash_map<int, pair<float, float>> ScoreMap;
~~~

作者说用一行就可以了:

~~~ c++
// CategoryType -> (score, weight)
typedef hash_map<int, pair<float, float>> ScoreMap;
~~~

有写注释要占用三行那么多, 但这个不需要.

## 6.2 避免使用不明确的代词

读者要花更多的功夫来 "解读" 一个代词. 在有些情况下, "it" 或者 "this" 到底指代什么是不清楚的.

~~~ c
// Insert the data into the cache, but check if it's too big first.
~~~

上面的注释中, "it" 可能指数据也可能是指缓存.

最安全的方式是, 如果在有可能会造成困惑的地方填写代词. 假设 "it" 是指 "data" 那么:

~~~ c
// Insert the data into the cache, but check if the data is too big first.
~~~

你也可以重新组织这个句子来让 "it" 变得很明确:

~~~ c
// If the data is small enough, insert it into the cache.
~~~

## 6.3 润色粗糙的句子

在很多情况下, 让注释更精确的过程总是伴随着让注释更紧凑.

## 6.4 精确地描述函数的行为

假设你刚写了一个函数, 它统计一个文件中的行数:

~~~ c
// Return the number of lines in this file.
int CountLines(string filename) { ... }
~~~

上面的注释并不是很精确, 因为有很多定义 "行" 的方式. 下面列出几个特别的情况:
- " " (空文件), 0 或 1 行?
- "hello", 0 或 1 行?
- "hello\n", 1 或 2 行?
- "hello\n world", 1 或 2 行?
- "hello\n\r world\r", 2, 3 或 4 行?

最简单的实现方法是统计换行符 (\n) 的个数 (这就是 Unix 命令 wc 的工作原理). 下面的注释对于这种实现方法更好一些:

~~~ c
// Count how many newline bytes ('\n') are in the file.
int CountLines(string filename) { ... }
~~~

这条注释并没有比第一个版本长很多, 但包含更多信息. 它告诉读者如果没有换行符, 这个函数会返回 0. 它还告诉读者回车符 (\r) 会被忽略.

## 6.5 用输入/输出例子来说明特别的情况

对于注释来讲, 一个精心挑选的输入/输出例子比千言万语还要有效.

例如, 下面是一个用来移除部分字符串的通用函数:

~~~ c++
// Remove the suffix/prefix of 'chars' from the input 'src'.
String Strip(String src, String chars) { ... }
~~~

这条注释不是很精确, 因为它不能回答下列问题:
- chars 是整个要移除的子串, 还是一组无序的字母?
- 如果在 src 的结尾有多个 chars 会怎样?

然而一个精心挑选的例子就可以回答这些问题:

~~~ c++
// ...
// Example: Strip("abba/a/ba", "ab") returns "/a/"
String Strip(String src, String chars) { ... }
~~~

这个例子展示了 Strip() 的整个功能. 请注意, 如果一个更简单的示例不能回答这些问题的话, 它就不会那么有用:

~~~ c++
// Example: Strip("ab", "a") returns "b"
~~~

下面是另一个函数的例子, 也能说明这个用法:

~~~ c++
// Rearrange 'v' so that elements < pivot come before those >= pivot;
// Then return the largest 'i' for which v[i] < pivot (or -1 if none are < pivot)
int Partition(vector<int>* v, int pivot);
~~~

这段注释实际上很精确, 但是不直观. 可以用下面的例子来进一步解释:

~~~ c++
// ...
// Example: Partition([8 5 9 8 2], 8) might result in [5 2 | 8 9 8] and return 1
int Partition(vector<int>* v, int pivot);
~~~

对于我们所选择的特别的输入/输出的例子, 有以下几点值得提一下:
- pivot 与向量中的元素相等, 用来解释边界情况.
- 我们在元素中放入重复元素 8 来说明这是一种可以接受的输入.
- 返回的向量没有排序, 如果是排好序的, 读者可能会误解.
- 因为返回值是 1, 我们要确保 1 不是向量中的值, 否则会让人很困惑.

## 6.6 声明代码的意图

正如我们在前一章中提到的, 很多时候注释的作用就是要告诉读者当你写代码时你是怎么想的. 遗憾的是, 很多注释只描述代码字面上的意思, 没有包含多少新信息.

下面的例子就是一条这样的注释:

~~~ c++
void DisplayProducts(list<Product> products) {
    products.sort(CompareProductByPrice);

    // Iterate through the list in reverse order
    for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend(); ++it)
        DisplayPrice(it->price);
    ...
}
~~~

这里的注释只是描述了它下面的那行代码. 相反, 更好的注释可以是这样的:

~~~ c++
    // Display each price, from highest to lowest
    for (list<Product>::reverse_iterator it = products.rbegin(); ... )
~~~

这条注释从更高的层次解释了这段程序在做什么. 这更符合程序员写这段代码时的想法.

有趣的是, 这段程序中有一个 bug. 函数 CompareProductByPrice (例子中没有给出实现) 已经把高价的项目排在了前面. 这段代码所做的事情与作者的意图相反.

这是第二种注释更好的原因. 除了这个 bug, 第一条注释从技术上讲是正确的 (循环进行的确是反向遍历). 但是有了第二条注释, 读者更可能会注意到作者的意图 (先显示高价项目) 与代码实际所做的有冲突. 其效果是, 这条注释扮演了冗余检查的角色.

最终来讲, 最好的冗余检查是单元测试. 但是在你的程序中写这种解释意图的注释仍是值得的.

## 6.7 "具名函数参数"的注释

假设你见到下面这样的函数调用:

~~~ c
Connect(10, false);
~~~

因为这里传入的整数和布尔型值, 使得这个函数调用有点难以理解.

在像 Python 这样的语言中, 你可以按名字为参数赋值:

~~~ python
def Connect(timeout, use_encryption): ...

# Call the function using named parameters
Connect(timeout = 10, use_encryption = False))
~~~

在像 C++ 和 Java 这样的语言中, 你不能这么做. 然而, 你可以通过嵌入的注释达到同样的效果:

~~~ c++
void Connect(int timeout, bool use_encryption) { ... }

// Call the function with commented parameters
Connect(/* timeout_ms = */ 10, /* use_encryption = */ false);
~~~

对于布尔参数来讲, 在值的前面加上 /*name=*/ 尤其重要. 把注释写在值的后面让人困惑:

~~~ c++
// 不要这样做
Connect( ... , false /* use_encryption */);

// 也不要这样做
Connect( ... , false /* = use_encryption */);
~~~

在上面这些例子中, 我们不清楚 false 的含义是 "使用加密" 还是 "不使用加密".

大多数函数不需要这样的注释, 但这种方法可以方便 (而且紧凑) 地解释看上去难以理解的参数.

## 6.8 采用信息含量高的词

一旦你写了多年程序以后, 你会发现有些普遍的问题和解决方案会重复出现. 通常会有专门的词或短语来描述这种模式/定式. 使用这些词会让你的注释更加紧凑.

例如, 假设你原来的注释是这样的:

~~~ c
// This class contains a number of member of members that store the same information as in the 
// database, but are stored here for speed. When this class is read from later, those
// members are checked first to see if they exist, and if so are returned; otherwise the
// database is read from and that data stored in these field for next time.
~~~

那么你可以简单地说:

~~~ c
// This class acts as a caching layer to the database.
~~~

另一个注释的例子:

~~~ c
// Remove excess whitespace from the street address, and do lots of other cleanup
// like turn "Avenue" into "Ave." This way, if there are two different street addresses

// that are typed in slightly differently, they will have the same cleaned-up version and 
// we can detect that these are equal.
~~~

可以写成:

~~~ c
// Canonicalize the street address (remove extra spaces, "Avenue" -> "Ave.", etc.)
~~~

很多词和短语都具有多种含义, 例如 "heuristic", "bruteforce", "naive solution" 等. 如果你感觉到一段注释太长了, 那么可以看看是不是可以用一个典型的编程场景来描述它.

## 6.9 总结

本章是关于如何把更多的信息装入更小的空间里. 下面是一些具体的提示:
- 当像 "it" 和 "this" 这样的代词可能指代多个事物时, 避免使用它们.
- 尽量精确地描述函数的行为.
- 在注释中用精心挑选的输入/输出例子进行说明.
- 声明代码的高层次意图, 而非明显的细节.
- 用嵌入的注释 (如 Function (/*arg=*/ .......)) 来解释难以理解的函数参数
- 用含义丰富的词来使注释简洁.

# 第 7 章 把控制流变得易读

如果代码中没有条件判断, 循环或者任何其他的控制流语句, 那么它的可读性会很好. 而跳转和分支等困难部分则会很快地让代码变得混乱.

## 7.1 条件语句中参数的顺序

在比较的语句中, 对于大多数程序员来讲:

~~~ c
if (length >= 10)
while (bytes_received < bytes_expected)
~~~

~~~ c
if (10 <= length)
while (bytes_expected < bytes_received)
~~~

第一段比第二段更易读. 下面的这条指导原则很有帮助:
- 比较的左侧: "被询问的" 表达式, 它的值更倾向于不断变化.
- 比较的右侧: 用来做比较的表达式, 它的值更倾向于常量.

## 7.2 if/else 语句块的顺序

有些情况下有理由相信其中一种顺序比另一种好:
- 首先处理正逻辑而不是负逻辑的情况. 例如, 用 if (debug) 而不是 if(!debug).
- 先处理掉简单的情况. 这种方式可能还会使得 if 和 else 在屏幕之内都可见, 这很好.
- 先处理有趣的或者是可疑的情况

有时这些倾向性之间会有冲突, 那么你就要自己判断了. 但在很多情况下这都会有明确的选择.

例如, 假设你有一个 Web 服务器, 它会根据 URL 是否包含查询参数 expand_all 来构建一个 response: 

~~~ c++
if (!url.HasQueryParameter("expand_all")) {
    response.Render(items);
    ...
} else {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
    ...
}
~~~

当读者刚看到第一行代码时, 他的脑海中马上开始思考 expand_all 的情况. 这就像当有人说 "不要去想一头粉红色的大象" 时, 你会不由自主地去想. "不要" 这个词已经被更不寻常的 "粉红色的大象" 给淹没了.

这里的 expand_all 就是我们的 "粉红色的大象". 让我们先来处理这种情况, 因为它更有趣 (并且也是正逻辑):

~~~ c++
if (url.HasQueryParameter("expand_all")) {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
} else {
    response.Render(items);
    ...
}
~~~

另外, 下面所示是负逻辑更简单并且更有趣或更危险的情况, 那么会先处理它:

~~~ python
if not file:
    # Log the error ...
else:
    # ...
~~~

同样, 根据具体情况的不同, 这也是需要你自己来判断的.

作为小结, 我们的建议很简单, 就是要注意这些因素并且小心那些会使你的 if/else 顺序很别扭的情况.

## 7.3 ?: 条件表达式 (三目运算符)

在类 C 的语言中, 可以把一个条件表达式写成 ```cond ? a : b``` 这样的形式, 其实就是一种对 ```if (cond) {a} else {b}``` 的紧凑写法.

它对于可读性的影响是富有争议的.

相对于追求最小化代码行数, 一个更好的度量方法是最小化人们理解它所需的时间.

默认情况下都用 if/else. 三目运算符 ?: 只有在最简单的情况下使用.

## 7.4 避免 do/while 循环

很多推崇的编程语言, 包括 Perl, 都有 do {expression} while (condition) 循环. 其中的表达式至少会执行一次.

do/while 的奇怪之处是一个代码块是否会执行是由其后的一个条件决定的. 通常来讲, 逻辑条件应该出现在它们所 "保护" 的代码之前, 这也是 if, while 和 for 语句的工作方式. 因为你通常会从前向后来读代码, 这就使得 do/while 循环有点不自然了. 很多读者最后都会读这段代码两遍.

while 循环相对更易读, 因为你会先读到所有迭代的条件, 然后再读到其中的代码块. 但仅仅是为了去掉 do/while 循环而重复一段代码是有点愚蠢的做法.

~~~ c
// 机械地模仿 do/while 循环 -- 不要这样做
body

while (condition) {
    body (again)
}
~~~

幸运的是, 我们发现实践当中大多数的 do/while 循环都可以写成开头的 while 循环.

另外一个要避免 do/while 循环的原因是其中的 continue 语句会让人迷惑. 例如, 下面这段代码会做什么?

~~~ c
do {
    continue;
} while (false);
~~~

它会永远循环下去还是只执行一次? 大多数程序员都不得不停下来想一想. (它只会循环一次)

最后, C++ 的开创者 Bjarne Stroustrup 讲得好 (在 C++ 程序设计语言 一书中):
我的经验是, do 语句是错误和困惑的来源. 我倾向于把条件放在 "前面我能看的到的地方".其结果是, 我倾向于避免使用 do 语句.

## 7.5 从函数中提前返回

有些程序员认为函数中永远不应该出现多条 return 语句. 这是胡说八道. 从函数中提前返回没有问题, 而且常常很受欢迎. 例如:

~~~ c++
public boolean Contains(String str, String substr) {
    if (str == null || substr == null) return false;
    if (substr.equals("")) return true;
    ...
}
~~~

如果不用 "保护语句" (guard clause) 来实现这种函数将会很不自然.

想要单一出口点的一个动机是保证调用函数结尾的清理代码. 但现代的语言为这种保证提供了更精细的方式:

语言: 清理代码的结构化术语
- C++ -> 析构函数
- Java, Python -> try finally 
- Python -> with
- C# -> using

在单纯由 C 语言组成的代码中, 当函数退出时没有任何机制来触发特定的代码. 因此, 如果一个大函数有很多清理代码, 提前返回可能很难做得没有问题. 在这种情况下, 其他的选择包括重构函数, 甚至慎重地使用 goto cleanup;.

## 7.6 臭名昭著的 goto

除了 C 语言之外, 其它语言一般不大需要 goto, 因为有太多更好的方式能完成同样的工作. 同时 goto 也因为草草了事使代码难以理解而声名狼藉.

但是你还是会在各种 C 项目中见到对 goto 的使用, 最值得注意的就是 Linux 内核. 在你认定所有对 goto 的使用都是一种亵渎之前, 仔细研究为什么某些对 goto 的使用比其它更好将会大有帮助.

如果只允许出现一种 goto 的形式, goto 不会成为什么大问题.

当有多个 goto 的目标时可能就会有问题了, 尤其当这些路径交叉时. 需要特别指出的是, 向前 goto 可能会产生真正的意大利面条式代码, 并且它们肯定可以被结构化的循环替代. 大多数时候都应该避免使用 goto.

## 7.7 最小化嵌套

嵌套很深的代码很难以理解. 每个嵌套层次都在读者的 "思维栈" 上又增加了一个条件. 当读者见到一个右大括号 (}) 时, 可能很难 "出栈" 来回忆起它背后的条件是什么.

下面是一个相对简单的例子, 当你回头复查你在读的是哪一个条件语句块时, 你是否能注意到你自己:

~~~ c++
if (user_result == SUCCESS) {
    if (permission_result != SUCCESS) {
        reply.WriteErrors("error reading permissions");
        reply.Done();
        return;
    }
    reply.WriteErrors("");
} else {
    reply.WriteErrors(user_result);
}
reply.Done();
~~~

上例中的代码尤其不好.

### 嵌套是如何累积而成的

在我们修正前面的实例代码之前, 先来看看是什么导致它成了现在的样子. 一开始, 代码是很简单的:

~~~ c++
if (user_result == SUCCESS) {
    reply.WriteErrors("");
} else {
    reply.WriteErrors(user_result);
}
reply.Done();
~~~

这段代码很容易理解, 它找出该写什么错误信息, 然后回复并结束.

但是后来那个程序员添加了第二个操作:

~~~ c++
if (user_result == SUCCESS) {
    if (permission_result != SUCCESS) {
        reply.WriteError("error reading permissions");
        reply.Done();
        return;
    }
    reply.WriteErrors("");
} else {
    reply.WriteErrors(user_result);
}
reply.Done();
~~~

这个改动有合理的地方, 该程序员要插入一段新代码, 并且他找到了最容易插入的地方. 对于他来讲, 新代码很整洁, 并且很明确. 这个改动的差异也很清晰, 这看上去像是个简单的改动.

但是以后当其他人遇到这段代码时, 所有的上下文早已不在了. 这就是你在本节一开始读到这段代码时的情况, 你不得不一下子全盘接受它.

关键思想:
当你对代码做改动时, 从全新的角度审视它, 把它作为一个整体来看待.

### 通过提早返回来减少嵌套

好的, 那么让我们来改进这段代码. 像这种嵌套可以通过马上处理 "失败情况" 并从函数早返回来减少:

~~~ c++
if (user_result != SUCCESS) {
    reply.WriteErrors(user_result);
    reply.Done();
    return;
}
if (permission_result != SUCCESS) {
    reply.WriteError(permission_result);
    reply.Done();
    return;
}
reply.WriteError("");
reply.Done();
~~~

上面这段代码只有一层嵌套, 而不是两层. 但更重要的是, 读者不再需要从思维堆栈里 "出栈" 了, 每个 if 块都以一个 return 结束.

### 减少循环内的嵌套

提早返回这个技术并不总是合适的, 在循环中, 与提早返回类似的技术是 continue.

与 if (...) return 在函数中所扮演的保护语句一样, 这些 if (...) continue; 语句是循环中的保护语句.

一般来讲, continue 语句让人很困惑, 因为它让读者不能连续地阅读, 就像循环中有 goto 语句一样. 

如果要用 continue 语句, 应尽量使循环中每个迭代是相互独立的 (类似 "for each" 循环), 因此读者可以很容易地领悟到这里 continue 的意思就是 "跳过该项".

## 7.8 你能理解执行的流程吗

本章介绍低层次控制流: 如何把循环, 条件和其它跳转写得简单易读. 但是你也应该从高层次来考虑程序的 "流动". 理想的情况是, 整个程序的执行路径都很容易理解, 从 main() 开始, 然后在脑海中一步步执行代码, 一个函数调用另一个函数, 直到程序结束.

然而在实践中, 编程语言和库的结构让代码在 "幕后" 运行, 或者让流程难以理解. 下面是一些例子:

编程结构 : 高层次程序流程是如何变得不清晰的
- 线程 -> 不清楚什么时间执行什么代码
- 信号量/中断处理程序 -> 有些代码随时都有可能执行
- 异常 -> 可能会从多个函数调用中向上冒泡一样地执行
- 函数指针和匿名函数 -> 很难知道到底会执行什么代码, 因为在编译时还没有决定
- 虚方法 -> object.virtualMethod() 可能会调用一个未知子类的代码.

这些结构中有些很有用, 它们甚至可以让你的代码更具可读性, 并且冗余更少. 但是作为程序员, 有时候我们得意忘形了, 于是用得太多了, 却没有发现以后它会有多么令人难以理解. 并且, 这些结构使得更难以追踪 bug.

关键是不要让代码中使用这些结构的比例太高. 如果你滥用这些功能, 它可能会让跟踪代码像赌徒三张牌赌博游戏一样.

## 7.9 总结

有几种方法可以让代码的控制流更易读.

在写一个比较时, 把改变的值写在左边并且把稳定的值写在右边更好些.

你也可以重新排列 if/else 语句中的语句块. 通常来讲, 先处理 正确的/简单的/有趣的情况. 有时这些准则会冲突, 但是当不冲突时, 这是要遵循的经验法则.

某些编程结构, 像三目运算, do/while 循环, 以及 goto 经常会导致代码的可读性变差. 最好不要使用它们, 因为总是有要更整洁的代替方式.

嵌套的代码需要更加集中精力去理解. 每层新的嵌套都需要读者把更多的上下文 "压入栈". 应该把它们改写成更加 "线性" 的代码来避免深嵌套.

通常来讲提早返回可以减少嵌套并让代码整洁. "保护语句" (在函数顶部处理简单的情况时) 尤其有用.

# 第 8 章 拆分超长的表达式

简单地说, 代码中的表达式越长, 它就越难以理解.

关键思想:
把你的超长表达式拆分成更容易理解的小块.

## 8.1 用做解释的变量

拆分表达式最简单的方法就是引入一个额外的变量, 让它来表示一个小一点的子表达式. 这个额外的变量有时叫做 "解释变量", 因为它可以帮助理解子表达式的含义. 例子:

~~~ c++
if line.split(':')[0].strip() == "root":
    ...
~~~

下面是和上面同样的代码, 但是现在有了一个解释变量:

~~~ c++
username = line.split(':')[0].strip()
if username == "root":
    ...
~~~

## 8.2 总结变量

即使一个表达式不需要解释 (因为你可以看出它的含义), 把它装入一个新的变量中仍然有用. 我们把它叫做总结变量, 它的目的只是用一个短很多的名字来代替一大段代码, 这个名字会更容易管理和思考. 例:

~~~ c++
if (request.user.id == document.owner_id) {
    // user can edit this document ...
}
...
if (request.user.id != document.owner_id) {
    // document is read-only ...
}
~~~

这里的表达式 request.user.id == document.owner_id 看上去可能并不长, 但它包含 5 个变量, 所以需要多花时间来想一想如何处理它.

这段代码中的主要概念是: "该用户拥有此文档吗?" 这个概念可以通过增加一个总结变量来表达得更清楚.

~~~ c++
final boolean user_owns_document = (request.user.id == document.owner_id);

if (user_owns_document) {
    // user can edit this document ...
}
...
if (!user_owns_document) {
    // document is read-only ...
}
~~~

上面的代码看上去改动并不大, 但语句 ```if (user_owns_document)``` 更容易理解一些. 并且, 在一开始就定义了 ```user_owns_document```, 用于提前告诉读者 "这是在整个函数中都会引用的一个概念".

## 8.3 使用德摩根定理

如果你学过 "电路" 或者 "逻辑" 课, 你应该还记得德摩根定理. 对于一个布尔表达式, 有两种等价的写法:

~~~
1) not (a or b or c) <=> (not a) and (not b) and (not c)
2) not (a and b and c) <=> (not a) or (not b) or (not c)
~~~

如果你记不住这两条定理, 一个简单的小结是 "分别取反, 转换与/或" (反向操作是 "提出取反因子")

有时, 你可以使用这些法则让布尔表达式更具可读性. 例:

~~~ c
if (!(file_exists && !is_protected)) Error("Sorry, could not read file.");
~~~

那么可以把它改写成:

~~~ c
if (!file_exists || is_protected) Error("Sorry, could not read file.");
~~~

## 8.4 滥用短路逻辑

在很多编程语言中, 布尔操作会做短路计算. 例如, 语句 ```if (a || b)``` 在 a 为真时不会计算 b. 使用这种行为很方便, 但有时可能会被滥用以实现复杂逻辑.

下面例子中的语句当初是由某一位作者写的:

~~~ c++
assert((!(bucket = FindBucket(key))) || !bucket -> IsOccupied());
~~~

用英语来讲, 这段代码是在说: "得到 key 的 bucket. 如果这个 bucket 不是空, 那么确定它是不是已经被占用."

尽管它只有一行代码, 但是它的确要让大多数程序员停下来想一想才行. 现在和下面的代码比一比:

~~~ c++
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket -> IsOccupied());
~~~

它做的事情完全一样, 尽管它有两行代码, 但它要容易理解得多.

那么无论如何为什么要把代码写在一个巨大的表达式里呢? 这就像在猜一个谜团, 我们都想让工作有乐趣. 问题是这种代码对于任何读它的人来讲都是个思维上的减速带.

关键思想:
要小心 "智能" 的小代码段, 它们往往在以后会让别人读起来感到困惑.

这不意味着你要避免利用这种短路行为, 很多情况下可以用它达到整洁的目的:

~~~ c++
if (object && object -> method()) ...
~~~

还有一个比较新的习惯用法值得一提: 在像 Python, JavaScript 以及 Ruby 这样的语言中, "or" 操作符会返回其中一个参数 (它不会转换成布尔值), 所以这样的代码:

~~~ python
x = a || b || c
~~~

可以用来从 a, b 或 c 中找出第一个为 "真" 的值.

## 8.5 例子: 与复杂的逻辑战斗

// TODO 作者举出一个范围覆盖的例子

### 找到更优雅的方式

// 作者从反方向思考找到了更优雅的解决方案

## 8.6 拆分巨大的语句

本章是关于拆分独立的表达式的, 但同样的技术也可以用来拆分大的语句. 例如, 下面的 JavaScript 代码需要一次读很多东西:

~~~ javascript
var update_highlight = function (message_num) {
    if ($("#vote_value" + message_num).html() === "Up") {
        $("#thumbs_up" + message_num).addClass("highlighted");
        $("#thumbs_down" + message_num).removeClass("highlighted");
    } else if ($("#vote_value" + message_num).html() === "Down") {
        $("#thumbs_up" + message_num).removeClass("highlighted");
        $("#thumbs_down" + message_num).addClass("highlighted");
    } else {
        $("#thumbs_up" + message_num).removeClass("highlighted");
        $("#thumbs_down" + message_num).removeClass("highlighted");
    }
}
~~~

代码中的每个表达式并不是很长, 但当把它们放在一起时, 它们就形成了一条巨大的语句.

幸运的是, 其中很多表达式是一样的, 这意味着可以把它们提取出来作为函数开头的总结变量 (这同时也是一个 DRY, Don't Repeat Yourself 的例子):

~~~ javascript
var update_highlight = function (message_num) {
    var thumbs_up = $("#thumbs_up" + message_num);
    var thumbs_down = $("#thumbs_down" + message_num);
    var vote_value = $("#vote_value" + message_num).html();
    var hi = "highlighted";

    if (vote_value === "Up") {
        thumbs_up.addClass(hi);
        thumbs_down.removeClass(hi);
    } else if (vote_value === "Down") {
        thumbs_up.removeClass(hi);
        thumbs_down.addClass(hi);
    } else {
        thumbs_up.removeClass(hi);
        thumbs_down.removeClass(hi);
    }
};
~~~

创建 ```var hi = "highlighted"``` 严格来讲不是必需的, 但鉴于这里有 6 次重复, 有很多好处驱使我们这样做:
- 它帮助避免录入错误.
- 它进一步缩短了行的宽度, 使代码更容易快速阅读.
- 如果类的名字需要改变, 只需要改一个地方即可.

## 8.7 另一个简化表达式的创意方法

// 作者使用 C++ 的宏来简化了一些重复操作

## 8.8 总结

本章给出了几种拆分巨大的表达式的方法.

一个是引入 "解释变量" 来代表较长的子表达式. 好处:
- 它把巨大的表达式拆成小段.
- 它通过用简单的名字描述子表达式来让代码文档化.
- 它帮助读者识别代码中的主要概念.

另一个技术是用德摩根定理来操作逻辑表达式, 可以把布尔表达式用更简洁的方式重写.

还给出了一个有时候需要把问题 "反向" 或者考虑目标的对立面例子.

最后, 尽管本章是关于拆分独立的表达式的, 同样, 这些技术也常应用于大的代码块. 所以, 你可以在任何见到复杂逻辑的地方大胆地去拆分它们.

# 第 9 章 变量与可读性

本章会探讨对于变量的草率运用, 如何让程序更难理解, 本章会讨论三个问题:
- 1. 变量越多, 就越难全部跟踪它们的动向.
- 2. 变量的作用域越大, 就需要跟踪它的动向越久.
- 3. 变量改变得越频繁, 就越难以跟踪它的当前值.

## 9.1 减少变量

### 没有价值的临时变量

在下面的一小段 Python 代码中, 考虑 now 这个变量:

~~~ python
now = datetime.datetime.now()
root_message.last_view_time = now
~~~

now 是一个值得保留的变量吗? 不是, 下面是原因:
- 它没有拆分任何复杂的表达式.
- 它没有做更多的澄清, 表达式 ```datetime.datetime.now()``` 已经很清楚了.
- 它只用过一次, 因此它并没有压缩任何冗余代码

没有了 ```now```, 代码一样容易理解.

~~~ python
root_message.last_view_time = datetime.datetime.now()
~~~

像 ```now``` 这样的变量通常是在代码编辑过后的 "剩余物". ```now``` 这个变量可能从前在多个地方用到. 或者可能那个程序员料想 ```now``` 会多次用到, 但实际上再也没用过它.

### 减少中间结果

下面的例子是一个 JavaScript 函数, 用来从数组中删除一个值:

~~~ js
var remove_one = function (array, value_to_remove) {
    var index_to_remove = null;
    for (var i = 0; i < array.length; i++) {
        if (array[i] === value_to_remove) {
            index_to_remove = i;
            break;
        }
    }
    if (index_to_remove !== null) {
        array.splice(index_to_remove, 1);
    }
}
~~~

变量 ```index_to_remove``` 只是用来保存临时结果. 有时这种变量可以通过得到后立即处理它而消除.

~~~ js
var remove_one = function (array, value_to_remove) {
    for (var i = 0; i < array.length; i++) {
        if (array[i] === value_to_remove) {
            array.splice(i, 1);
            return;
        }
    }
}
~~~

通过让代码提前返回, 我们不再需要 ```index_to_remove```, 并且大幅简化了代码.

通常来讲, "速战速决" 是一个好的策略.

### 减少控制流变量

~~~ c++
boolean done = false;
while(/* condition */ && !done) {
    ...
    if (...) {
        done = true;
        continue;
    }
}
~~~

这种模式甚至可以在循环里多处把变量 ```done``` 设置为 ```true```.

这样的代码通常是为了满足某些心照不宣的规则, 即使你不该从循环中间跳出去. 根本就没有这样的规则.

像 ```done``` 这样的变量, 称为 "控制流变量". 它们唯一的目的就是控制程序的执行, 它们没有包含任何程序的数据. 在我们的经验中, 控制流变量通常可以通过更好地运用结构化编程而消除.

~~~ c++
while(/* condition */) {
    ...
    if (...) {
        break;
    }
}
~~~

这个例子改起来很简单, 但是如果有多个嵌套循环, 一个简单的 ```break``` 根本不够怎么办呢? 在这种更复杂的情况下, 解决方案通常包括把代码挪到一个新函数中 (要么是循环中的代码, 要么是整个循环).

你希望你的同事随时都觉得是在面试吗?

引用: 来自微软的 Eric Brechner 曾说过一个好的面试官问题起码要涉及三个变量. 可能是因为同时处理三个变量会强迫你努力思考! 这对于面试来讲还说得过去, 因为你要尝试找到候选人的极限. 但是你希望你的同事在读你的代码时感觉就像你在面试他们吗?

## 9.2 缩小变量的作用域

"避免全局变量" 这是一条好的建议, 因为很难跟踪这些全局变量在哪里以及如何使用它们.

让所有的变量都 "缩小作用域" 是一个好主意, 并非只是针对全局变量.

让你的变量对尽量少的代码行可见.

这样做能减少读者同时需要考虑的变量个数.

例: 假设类中的成员变量只由两个方法用到:

~~~ c++
class LargeClass {
    string str_;

    void Method1() {
        str_ = ...;
        Method2();
    }

    void Method2() {
        // Uses str_
    }

    // Lots of other methods that don't use 'str_' ...
};
~~~

类的成员变量就像是在该类内部世界中的 "小型全局变量". 在大的类中很难追踪所有的成员变量以及哪个方法修改了哪个变量.

本例中最好把 ```str_``` "降格" 为局部变量:

~~~ c++
class LargeClass {
    void Method1() {
        string str = ...;
        Method2(str);
    }

    void Method2(string str) {
        // Uses str
    }

    // Now other methods can't see 'str' ...
};
~~~

另一个对类成员访问进行约束的方法是 "尽量使方法变成静态的". 静态方法是让读者知道 "这几行代码与那些变量无关" 的好办法.

或者还有一种方式是 "把大的类拆分成小一些的类". 这种方法只有在这些小一些的类事实上相互独立时才能发挥作用. 如果你只是创建两个类来互相访问对方的成员, 那你什么目的也没达到.

### C++ 中 if 语句的作用域

假设你有以下 C++ 代码:

~~~ c++
PaymentInfo* info = database.ReadPaymentInfo();
if (info) {
    ...
}
// Many more lines of code below ...
~~~

变量 ```info``` 在此函数的余下部分仍在作用域内, 因此, 读这段代码的人要始终记得它, 猜测它是否或者怎样再次用到.

但是在本例中, ```info``` 只有在 if 语句中才用到. 在 C++ 语言中, 我们实际上可以把 ```info``` 定义在条件表达式中:

~~~ c++
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    ...
}
~~~

### 在 JavaScript 中创建 "私有" 变量

假设你有一个长期存在的变量, 只有一个函数会用到它:

~~~ js
submitted = false; // Note: global variable

var submit_form = function (form_name) {
    if (submitted) {
        return; // don't double-submit the form
    }
    ...
    submitted = true;
};
~~~

像 ```submitted``` 这种全局变量会让读代码的人非常不安. 看上去好像只有 ```submit_form()``` 使用 ```submitted```, 但你就是没办法确定.

你可以把 ```submitted``` 放在一个 "闭包" 中来避免这个问题:

~~~ js
var submit_form = (function () {
    var submitted = false; // Note: can only be access by the function below

    return function (form_name) {
        if (submitted) {
            return; // don't double-submit the form
        }
        ...
        submitted = true;
    };
}());
~~~

在最后一行上的圆括号 ```()```, 它会使外层的这个匿名函数立即执行, 返回内层的函数.

这种技巧的效果是营造一个 "私有" 作用域, 只有内层函数才能访问. 现在就不必再去猜 "```submitted``` 还在什么地方用到了?" 或者担心与其他同名的全局变量冲突.

### JavaScript 全局作用域

在 JavaScript 中, 如果你在变量定义中省略 ```var``` 关键字 (例如, 写成 ```x=1``` 而非 ```var x=1```), 这个变量会放在全局作用域中, 所有的 JavaScript 文件和 ```<script>``` 块都可以访问它.

~~~ html
<script>
    var f = function() {
        // DANGER: 'i' is not declared with 'var'!
        for (i = 0; i < 10; i ++) ...
    }
</script>
~~~

这段代码不慎把 ```i``` 放在了全局作用域中, 那么以后的代码块也能看到它:

~~~ html
<script>
    alert(i); // Alerts '10'. 'i' is a global variable!
</script>
~~~

### 在 Python 和 JavaScript 中没有嵌套的作用域

// TODO

### 把定义向下移

原来的 C 语言要求把所有的变量定义放在函数或语句块的顶端. 这个要求很令人遗憾, 因为对于有很多变量的函数, 它强迫读者马上思考所有这些变量, 即使是要到很久之后才会用到它们.

作者说还是把这些定义移到使用之前吧, 避免变量在读者脑中不断切换.

## 9.3 只写一次的变量更好

"永久固定" 的变量更容易思考. 当前, 像这种常量:

~~~ c++
static const int NUM_THREADS = 10;
~~~

不需要读者思考很多, 基于同样的原因, 鼓励在 C++ 中使用 ```const``` (在 Java 中使用 ```final```).

就算你不能让变量只写一次, 让变量在较少的地方改动仍有帮助.

## 9.4 最后的例子

// TODO 作者演示如何一步一步使用上面的技巧

## 9.5 总结

本章是关于程序中的变量是如何快速累积而变得难以跟踪的. 你可通过减少变量的数量和让它们尽量 "轻量级" 来让代码更有可读性. 具体有:
- 减少变量, 即那些妨碍的变量. 我们给出了几个例子来演示如何通过立即处理结果来消除 "中间结果" 变量.
- 减少每个变量的作用域, 越小越好. 把变量移到一个有最少代码可以看到它的地方.
- 只写一次的变量更好. 那些只设置一次值的变量 (或者 ```const```, ```final```, 常量) 使得代码更容易理解.

# 第 10 章 抽取不相关的子问题

所谓工程系就是关于把大问题拆分成小问题再把这些问题的解决方案放回一起. 把这条原则应用于代码会使代码更健壮并且更容易读.

本章的建议是 "积极地发现并抽取出不相关的子逻辑". 我们是指:
- 1. 看看某个函数或代码块, 问问你自己: 这段代码高层次的目标是什么?
- 2. 对于每一行代码, 问一下: 它是直接为了目标而工作吗? 这段代码高层次的目标是什么呢?
- 3. 如果足够的行数在解决不相关的子问题, 抽取代码到独立的函数中.

本章讲的诀窍是主动地寻找那些不相关的子问题.

## 10.1 介绍性的例子: findClosestLocation()

当代码中的大部分都旨在解决一个不相关的子问题时, 可以把它们抽取到一个独立的函数.

## 10.2 纯工具的代码

如果你在想: "我希望我们的库里有 ```XYZ()``` 函数", 那么就写一个! (如果它还不存在的话) 经过一段时间, 你会建立起一组不错的工具代码, 后者可以应用于多个项目.

## 10.3 其他多用途代码

同样也是抽取一些处理不相关子问题的代码.

### 意料之外的好处

当你在使用一个独立的小函数时, 感觉添加功能, 改进可读性, 处理边界情况等都更容易.

## 10.4 创建大量通用代码

作者认为从你的项目中拆分出越多的独立库越好, 因为你代码的其他部分会更小而且更容易思考.

## 10.5 项目专有的功能

理想情况下, 抽取出的子问题对项目一无所知. 就算不是这样, 也没有问题, 分离子问题仍然可以创造奇迹.

## 10.6 简化已有接口

人人都爱提供简洁接口的库, 那种参数少, 不需要很多设置并且通常只需花一点工夫就可以使用的库. 它让你的代码看起来优雅: 简单而又强大.

作者说 "你永远都不要安于使用不理想的接口". 你总是可以创建你自己的包装函数来隐藏接口的粗陋细节, 让它不再成为你的阻碍.

## 10.7 按需重塑接口

对于那些用于支持其他代码的代码, 例如, 为函数设置输入或者对输出做后期处理. 这类代码是抽取到独立函数的最好机会.

## 10.8 过犹不及

我们的目标是 "积极地发现和抽取不相关的子问题". 我们说 "积极地" 是因为大多数程序员不够积极. 但也可能会过于积极, 导致过犹不及.

如果引入很多小函数, 实际上对可读性是不利的, 因为读者要关注更多东西, 并且按照执行的路径需要跳来跳去.

为代码增加一个函数存在一个小的 (却有形的) 可读性代价. 在前面的情况里, 付出这种代价却什么也没有得到. 如果你的项目的其他部分也需要这些小函数, 那么增加它们是有道理的. 但是如果没有, 就不大需要啦.

## 10.9 总结

"把一般代码和项目专有的代码分开". 大部分都是一般代码. 通过建立一大组库和辅助函数来解决一般问题, 剩下的只是让你的程序与众不同的核心部分.

# 第 11 章 一次只做一件事

同时在做几件事的代码很难理解. 一个代码块可能初始化对象, 清除数据, 解析输入, 然后应用业务逻辑, 所有这些都同时进行. 如果所有这些代码都纠缠在一起, 对于每个 "任务" 都很难靠其自身来帮你理解它从哪里开始, 到哪里结束.

下面是用于使代码 "一次只做一件事" 所用到的流程:
- 1. 列出代码所做的所有 "任务". 这里的 "任务" 没有很严格的定义, 它可以小得如 "确保这个对象有效", 或者含糊得如 "遍历树中的所有结点".
- 2. 尽量把这件任务拆分到不同的函数中, 或者至少是代码中不同的段落中.

## 11.1 任务可以很小

// TODO 作者用投票数举了一个例子

## 11.2 从对象中抽取值

//TODO 作者举了一个 把用户的位置格式化成 "城市, 国家" 这样友好的字符串的例子.

### 应用 "一次只做一件事情" 原则

作者把上面例子中的代码, 分成多个任务. 每个任务都很容易解决.

### 另一种做法

// TODO 作者利用 JS 特性来简化

## 11.3 更大型的例子

作者把一些任务分割到代码中单独的区域来改进大型的代码.

好处是, 当你在读一个区域时, 你不必去想其他的区域.

### 进一步的改进

可以引入多个辅助函数, 移除保存中间结果的变量.

## 11.4 总结

本章给出了一个组织代码的简单技巧: 一次只做一件事情.

如果有很难读的代码, 尝试把它所做的所有任务列出来. 其中一些任务可以很容易地变成单独的函数 (或类). 其他的可以简单地成为一个函数中的逻辑 "段落". 具体如何拆分这些任务没有它们已经分开这个事实那样重要. 难的是要准确地描述你的程序所做的所有这些小事情.

# 第 12 章 把想法变成代码

在本章中, 我们会用一个简单的过程来使你编写更清晰的代码:
- 1. 像对着一个同事一样用自然语言描述代码要做什么.
- 2. 注意描述中所用的关键词和短语.
- 3. 写出与描述所匹配的代码.

## 12.1 清楚地描述逻辑

下面是来自一个网页的一段 PHP 代码. 这段代码在一段安全代码的顶部. 它检查是否授权用户看到这个页面, 如果没有, 马上返回一个页面来告诉用户他没有授权:

~~~ php
$is_admin = is_admin_request();
if ($document) {
    if (!$is_admin && ($document['username'] != $_SESSION['username'])) {
        return not_authorized();
    }
} else {
    if (!$is_admin) {
        return not_authorized();
    }
}
// continue rendering the page ...
~~~

这些代码逻辑可以简化, 从用自然语言描述这个逻辑开始:
授权你有两种方式:
- 1. 你是管理员
- 2. 你拥有当前文档 (如果有当前文档的话)
否则, 无法授权你.

~~~ php
if (is_admin_request()) {
    // authorized
} elseif ($document && ($document['username'] == $_SESSION['username'])) {
    // authorized
} else {
    return not_authorized();
}
// continue rendering the page ...
~~~

这个版本有点不寻常, 因为它有两个空语句体. 但是代码要少一些, 并且逻辑也简单, 因为没有反义 (前一个方案中有三个 "not").

## 12.2 了解函数库是有帮助的

编写精炼代码的一部分工作是了解你的库提供了什么.

## 12.3 把这个方法应用于更大的问题

// TODO

## 12.4 总结

本章讨论了一个简单的技巧, 用自然语言描述程序然后用这个描述来帮助你写出更自然的代码.

# 第 13 章 少写代码

知道什么时候不写代码可能对一个程序员来讲是他所要学习的最重要的技巧. 你所写的每一行代码都是要测试和维护的. 通过重用库或者减少功能, 你可以节省时间并且让你的代码库保持精简节约.

关键思想:
最好读的代码就是没有代码.

## 13.1 别费神实现那个功能, 你不会需要它

当你开始一个项目, 自然会很兴奋并且想着你希望实现的所有很酷的功能. 但是程序员倾向于高估有多少功能真的对于他们的项目来讲是必不可少的. 很多功能结果没有完成, 或者没有用到, 也可能只是让程序更复杂.

程序员还倾向于低估实现一个功能索要花的工夫. 我们乐观地估计了实现一个粗糙原型所要花的时间, 但是忘记了在将来代码库的维护, 文件以及后增的 "重量" 所带来的额外时间.

## 13.2 质疑和拆分你的需求

不是所有的程序都需要运行得快, 100% 准确, 并且能处理所有的输入. 如果你真的仔细检查你的需求, 有时你可以把它削减成一个简单的问题, 只需要较少的代码.

### 例子: 商店定位器

假设你要给某个生意写个 "商店定位器". 你以为你的需求是:
对于任何给定用户的 经度 / 纬度, 找到距离该 经度 / 纬度 最近的商店.

为了 100% 正确地实现, 你要处理:
- 当位置处于国际日期分界线两侧的情况.
- 接近北极或南极的位置.
- 按 "每英里所跨经度" 不同, 处理地球表面的曲度.

处理所有这些情况需要相当多的代码.

然而, 对于你的应用程序来讲, 只有在德州的 30 家店. 在这么小的范围里, 上面列出的三个问题并不重要. 结果是, 你可以把需求缩减为:
- 对于德州附近的用户, 在德州找到 (近似) 最近的商店.

解决这个问题很简单, 因为你只要遍历每个商店并计算它们与这个经纬度之间的欧几里得距离就可以了.

### 例子: 增加缓存

作者使用单条目的简单缓存举例, 用较少的代码得到了 90% 的好处.

怎么说 "减少需求" 和 "解决更简单的问题" 的好处都不为过. 需求常常以微妙的方式互相影响. 这意味着解决一半的问题可能只需要花四分之一的工夫.

## 13.3 保持小代码库

宇宙的自然法则: 随着任何坐标系统的增长, 把它粘合在一起所需的复杂度增长得更快.

最好的解决办法就是 "让你的代码库越小, 越轻量级越好", 就算你的项目在增长. 那么你就要:
- 创建越多越好的 "工具" 代码来减少重复代码.(见第 10 章)
- 减少无用代码或没有用的功能.
- 让你的项目保持分开的子项目状态.
- 总的来说, 要小心代码的 "重量". 让它保持又轻又灵.

删除没有用的代码例子:
1. 你一开始把系统设计成能处理多语言文件名, 现在代码中到处都充满了转换代码. 然而, 那段代码不能很好地工作, 实现上你的程序也从来没有用到过任何多语言文件名. (为什么不删除这个功能呢?)
2. 你希望你的程序在内存耗尽的情况下仍能工作, 因此你有很多耍小聪明的逻辑来试着从内存耗尽的情况下恢复. 这是个好主意, 但在实践中, 当系统内存耗尽时, 你的程序将变成不稳定的僵尸, 所有的核心功能都不可用, 再点一下鼠标它就死了. (为什么不通过一句简单的提示 "系统内存不足, 抱歉" 并删除所有内存不足的代码, 终止程序呢?)

## 13.4 熟悉你周边的库

很多时候, 程序员就是不知道现有的库可以解决他们的问题. 或者有时, 它们忘了库可以做什么. 知道你的库能做什么以便你可以使用它, 这一点很重要.

这里有一条比较中肯的建议: 每隔一段时间, 花 15 分钟来阅读标准库中的所有 函数 / 模块 / 类型的名字. 这包括 C++ 标准模板库 (STL), Java API, Python 内置的模块以及其他内容.

这样做的目的不是记住整个库. 这只是为了了解有什么可以用的, 以便下次你写新代码时会想: "等一下, 这个听起来和我在 API 中见到的东西有点像..." 我们相信提前做这种准备很快就会得到回报, 起码因为你会更倾向于使用库了.

### 例子: Python 中的列表和集合

假设你有一个使用 Python 写的列表 (如[2, 1, 2]), 你想要一个拥有不重复元素的列表 (在上例中, 就是 [2, 1]). 你可以用字典来完成这个任务, 它有一个键列表保证元素是唯一的:

~~~ python
def unique(elements):
    temp = {}
    for element in elements:
        temp[element] = None # The value doesn't matter.
    return temp.keys()
unique_elements = unique([2, 1, 2])
~~~

但是你可以用较少人知道的集合类型:

~~~ python
unique_elements = set([2, 1, 2]) # Remove duplicates
~~~

这个对象是可以枚举的, 就像一个普通的 list 一样. 如果你很想要一个 list 对象, 你可以用:

~~~ python
unique_elements = list(set([2, 1, 2])) # Remove duplicates
~~~

很明显, 这里集合才是正确的工具. 但如果你不知道 set 类型, 你可能会写出像前面 unique() 一样的代码.

### 为什么重用库有这么大的好处

在一个成熟的库中, 每一行代码都代表相当大量的设计, 调试, 重写, 文档, 优化和测试. 任何经受了这样达尔文进化过程一样的代码行就是很有价值的. 这就是为什么重用库有这么大的好处, 不仅节省时间, 还少写了代码.

## 13.5 例子: 使用 Unix 工具而非编写代码

// TODO 作者用 Unix 工具统计日志文件实现了和编写代码统计同样的工作. 它费时更少.

## 13.6 总结

本章是关于写越少代码越好的. 每行新的代码都需要测试, 写文档和维护. 另外, 代码库中的代码越多, 它就越 "重", 而且在其上开发就越难.

你可以通过以下方法避免编写新代码:
- 从项目中消除不必要的功能, 不要过度设计.
- 重新考虑需求, 解决版本最简单的问题, 只要能完成工作就行.
- 经常性地通读标准库的整个 API, 保持对它们的熟悉程度.

# 第 14 章 测试与可读性

在本章中, "测试" 是指任何仅以检查另一段 ("真实") 代码的行为为目的的代码. 我们会关注测试的可读性方面, 不会讨论你是否应该在写真实代码之前写测试代码 ("测试驱动的开发") 或者测试开发的其它哲学方面.

## 14.1 使测试易于阅读和维护

测试代码的可读性同样重要. 它记录了真实代码如何工作和应该如何使用. 如果测试很容易阅读, 使用者对于真实代码的行为会有更好的理解.

关键思想:
测试应当具有 可读性, 以便其它程序员可以舒服地改变或者增加测试.

你希望鼓励你代码的使用者 (尤其是你自己!) 习惯于测试代码. 他们应该能在新改动破坏已有测试时做出分析, 并且应该感觉增加新测试很容易.

## 14.2 这段测试什么地方不对

在代码库中, 有一个函数, 它对于一个打过分的搜索结果列表进行排序和过滤. 下面是函数的声明:

~~~ c++
// Sort 'docs' by score (highest first) and remove negative-scored documents.
void SortAndFilterDocs(vector<ScoredDocument>* docs);
~~~

该函数的测试最初如下所示:

~~~ c++
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;

    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;

    SortAndFilterDocs(&docs);

    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
}
~~~

作者说上面的代码起码有 8 个不同的问题.

## 14.3 使这个测试更可读

作为一条普遍的测试原则, 你应当 "对使用者隐去不重要的细节, 以便更重要的细节会更突出".

前一节中的测试代码明显违反了这条规则. 该测试的所有细节都摆在那里, 比如像建立一个 ```vector<ScoredDocument>``` 这样不重要的细枝末节. 很多都是 C++ 对象如何创建的细节, 而不是关于所测试内容的高层次描述.

作为清理这些代码的第一步, 可以创建一个这样的辅助函数:

~~~ c++
void MakeScoredDoc(ScoredDocument* sd, double score, string url) {
    sd->score = score;
    sd->url = url;
}
~~~

使用这个函数, 测试代码变得紧凑一点了:

~~~ c++
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    MakeScoredDoc(&docs[0], -5.0, "http://example.com");
    MakeScoredDoc(&docs[1], 1, "http://example.com");
    MakeScoredDoc(&docs[2], 4, "http://example.com");
    MakeScoredDoc(&docs[3], -99998.7, "http://example.com");
    ...
}
~~~

但是它还是不够好, 在我们面前还是有不重要的细节. 参数 "http://example.com" 看着很不自然. 它每次都是一样的, 而且具体 URL 是什么根本没关系, 只要填进一个有效的 MakeScoredDoc 就可以了.

我们被迫要看的另一个不重要的细节是 ```docs.resize(5)``` 和 ```docs[0]```, ```&docs[1]``` 等. 让我们修改辅助函数来做更多事情, 并给它命名为 ```AddScoredDoc()```:

~~~ c++
void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
}
~~~

使用这个函数, 测试代码更紧凑了:

~~~ c++
void Test1() {
    vector<ScoredDocument> docs;
    AddScoredDoc(docs, -5.0);
    AddScoredDoc(docs, 1);
    AddScoredDoc(docs, 4);
    AddScoredDoc(docs, -99998.7);
    ...
}
~~~

这段代码已经好多了, 但仍不满足 "高度易读和易写" 测试的要求. 如果你希望增加一个测试, 其中用到一组新的 scored docs, 这会需要大量的拷贝和粘贴.

### 创建最小的测试声明

要改进这段测试代码, 让我们使用从第 12 章学来的技巧. 让我们用自然语言来描述我们的测试要做什么:

~~~ c++
// 我们有一个文档列表, 它们的分数为 [-5, 1, 4, -99998.7, 3].
// 在 SortAndFilterDocs() 之后, 剩下的文档应当有的分数是 [4, 3, 1], 而且顺序也是这样.
~~~

在描述中没有在任何地方提及 ```vector<ScoredDocument>```. 这里最重要的是分数数组. 理想的情况下, 测试代码应该看起来这样:

~~~ c++
CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
~~~

大多数测试的基本内容都能精炼成 "对于这样的输入/情形, 期望有这样的行为/输出". 很多时候这个目的可以用一行代码来表达. 这除了让代码紧凑而又易读, 让测试的表述保持很短还会让增加测试变得很简单.

### 实现定制的 "微语言"

注意到 ```CheckScoresBeforeAfter()``` 需要两个字符串参数来描述分数数组. 在较新版本的 C++ 中, 这样可以传入数组常量:

~~~ c++
CheckScoresBeforeAfter({-5, 1, 4, -99998.7, 3}, {4, 3, 1});
~~~

因为当时我们还不能这么做, 所以我们就把分数放在字符串中, 用逗号分开. 为了让这个方法可行, ```CheckScoresBeforeAfter()``` 就不得不解析这些字符串参数.

一般来讲, 定义一种定制的微语言可能是一种占用很少空间来表达大量信息的强大方法. 其他例子包含 ```printf()``` 和正则表达式库.

在本例中, 如果实现了只调用 ```CheckScoresBeforeAfter()``` 一次就能写出整个测试, 因此人们会更倾向于增加更多的测试.

## 14.4 让错误消息更具有可读性

上面的代码已经很不错了, 但是当 ```assert(output++expected_output)``` 这一行失败时会发生什么呢? 它会产生一行这样的错误消息:

~~~
Assertion failed: (output == expected_output),
    function CheckScoresBeforeAfter, file test.cc, line 37.
~~~

显然, 如果你看到这个错误, 你会想 ```output``` 和 ```expected_output``` 出错时的值是什么呢?

### 更好版本的 assert()

幸运的是, 大部分语言和库都有更高级版本的 ```assert()``` 给你用. 所以不用这样写:

~~~ c++
assert(output == expected_output);
~~~

你可以使用 C++ 的 Boost 库:

~~~ c++
BOOST_REQUIRE_EQUAL(output, expected_output)
~~~

现在, 如果测试失败, 你会得到更具体的消息:

~~~ c++
test.cc(37): fatal error in "CheckScoresBeforeAfter":
    critical check output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
~~~

这更有帮助.

如果有的话, 你应该使用这些更有帮助的断言方法. 每当你的测试失败时, 你就会受益.

### 手工打造错误消息

使用 ```BOOST_REQUIRE_EQUAL()``` 可以得到更好的错误信息:

~~~
output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
~~~

然而, 这条消息还能进一步改进. 比如增加原本触发这个错误的输入等等 balabala... 你可以手工定制, 也可以使用第三方库的, 觉得错误消息越有帮助就越好.

## 14.5 选择好的测试输入

有一门为测试选择好的输入的艺术.

基本原则是, 你应当选择一组最简单的输入, 它能完整地使用被测试代码.

比如代码中 "过滤掉负的分数" 这一行为. 另外, 有些测试值复杂得没有必要 (并且甚至也没能完整地测试代码):

~~~ c++
CheckScoresBeforeAfter("123014, -1437298, 436242, -64526453, 24342", "325146, 345456, 625643");
~~~

### 简化输入值

上面的例子中有一个非常 "嚣张" 的值 ```-99998.7```. 这个值的含义只是 "任何负数", 所以最简单的值就是 ```-1```.

又简单又能完成工作的测试值更好.

下面是测试的新版本:

~~~ c++
CheckScoresBeforeAfter("1, 2, -1, 3", "3, 2, 1");
~~~

简化了测试的值, 却并没有降低它的效果.

对于大的, 不切实际的输入进行测试当然是有价值的. 它在发现 bug 方面很有作用, 比如缓冲区溢出或者其他出乎意料的情况. 但是那样的代码又大看上去又吓人, 对于代码的压力测试来讲并无很好的效果. 相反, 用编程的方法来生成大型输入会更有效果, 例如, 生产 100000 个值.

### 一个功能的多个测试

与其建立单个 "完美" 输入来完整地执行你的代码, 不如写多个小测试, 后者往往会更容易, 更有效并且更有可读性.

每个测试都应该把代码推往某一个方向, 尝试找到某种 bug. 例如, 下面有 SortAndFilterDocs() 的 4 个测试:

~~~ c++
CheckScoresBeforeAfter("2, 1, 3", "3, 2, 1"); // Basic sorting
CheckScoresBeforeAfter("0, -0.1, -10", "0"); // All values < 0 removed
CheckScoresBeforeAfter("1, -2, 1, -2", "1, 1"); // Duplicates not a problem
CheckScoresBeforeAfter("", ""); // Empty input OK
~~~

如果要非常地彻底, 还可以写更多的测试. 有分开的测试用例还可以使下一个负责代码相关工作的人更轻松. 如果有人不小心引入了一个 bug, 测试的失败会指向那个具体的失效测试用例.

## 14.6 为测试函数命名

测试代码一般以函数的形式组织起来, 你所测试的每个方法或情形对应一个测试函数. 例如, 测试 ```SortAndFilterDocs()``` 的测试代码是在函数 ```Test1()``` 中:

~~~ c++
void Test1() {
    ...
}
~~~

为测试函数选择一个好名字可能看上去很无聊且也无关紧要, 但是不要因此而诉诸没有意义的名字, 像是 ```Test1()```, ```Test2()``` 这样.

应当用这个名字来描述这个测试的细节, 如果读测试代码的人可以很快搞明白这些的话, 这一点尤其便利:
- 被测试的类 (如果有的话)
- 被测试的函数
- 被测试的情形或 bug

一种构造好的测试函数名的简单方式是把这些信息拼接在一起, 可能再加上一个 ```Test_``` 前缀.

例如, 不要用 ```Test1()``` 这个名字, 可以用 ```Test_<FunctionName>()``` 这样的格式:

~~~ c++
void Test_SortAndFilterDocs() {
    ...
}
~~~

依照测试的精细度不同, 你可能会考虑为测试的每种情形写一个单独的测试函数. 可以使用 ```Test_<FunctionName>_<Situation>()``` 这样的格式:

~~~ c++
void Test_SortAndFilterDocs_BasicSorting() {
    ...
}

void Test_SortAndFilterDocs_NegativeValues() {
    ...
}
~~~

这里不要怕名字太长或者太繁琐. 在你的整个代码库中不会调用这个函数, 因此那个要避免使用长函数名的理由在这里并不适用. 测试函数的名字的作用就像是注释. 并且, 如果测试失败了, 大部分测试框架会输出其中断言失败的那个函数的名字, 因此一个具有描述性的名字尤其有帮助.

请注意如果你在使用一个测试框架, 可能它已经有方法命名的规则和规范了. 例如, 在 Python 的 unittest 模块中它需要测试方法的名字以 ```test``` 开头.

当为测试代码的辅助函数命名时, 标明这个函数是否自身有任何断言或者只是一个普通的 "对测试一无所知" 的辅助函数. 例如, 在本章中, 所有调用了 ```assert()``` 的辅助数都命名成 ```Check***()```. 但是函数 ```AddScoredDoc()``` 就只是像普通辅助函数一样命名.

## 14.7 那个测试有什么地方不对

本章开头, 作者说那个测试中至少有 8 个地方不对, 现在我们已经学到了一些编写更好测试的技巧, 让我们来找出他们:
- 1. 这个测试很长, 并且充满了不重要的细节, 你可以用一句话来描述这个测试所做的事情, 因此这条测试的语句不应该太长.
- 2. 增加新测试不会很容易. 你会倾向于拷贝/粘贴/修改, 这样做会让代码更长而且充满重复.
- 3. 测试失败的消息不是很有帮助. 如果测试失败的话, 它只是说 ```Assertion failed: docs.size() == 3```, 这并没有为进一步调试提供足够的信息.
- 4. 这个测试想要同时测试完所有东西. 它想要即测试对负数的过滤又测试排序的功能. 把它们拆分成多个测试会更可读.
- 5. 这个测试的输入不是很简单. 尤其是, 样本分数 ```99998.7``` 很 "嚣张", 尽管它是什么值并不重要但是它会引起你的注意. 一个简单的负数值就足够了.
- 6. 测试的输入没有彻底地执行代码. 例如, 它没有测试到当分数为 0 时的情况. (这种文档会过滤掉吗?)
- 7. 它没有测试其他极端的输入, 例如空的输入向量, 很长的向量, 或者有重复分数的情况.
- 8. 测试的名字 ```Test1()``` 没有意义, 名字应当能描述被测试的函数或情形.

## 14.8 对测试较好的开发方式

对于测试来讲理想的代码要有明确定义的接口, 没有过多的状态或者其他的 "设置", 并且没有很多需要审查的隐藏数据.

如果你写代码的时候就知道以后你要为它写测试的话, 会发生有趣的事情: 你开始把代码设计得容易测试. 幸运的是, 这样的编程方式一般来讲也意味着会产生更好的代码. 对测试友好的设计往往很自然地会产生有良好组织的代码, 其中不同的部分做不同的事情.

测试驱动开发 (TDD)

测试驱动开发是一种编程风格, 你在写真实代码之前就写出测试. TDD  的支持者相信这种流程对没有测试的代码来讲会做出极大的质量改进, 比写出代码之后再写测试要大得多.

至少, 我们发现仅通过在写代码时想着测试这件事就能帮助把代码写得更好.

在所有的把一个程序拆分成类和方法的途径中, 解耦合最好的那一个往往就是最容易测试的哪一个. 另一方面, 假设你的程序内部联系很强, 在类与类之间有很多方法的调用, 并且所有的方法都有很多参数. 不仅这个程序会有难以理解的代码, 而且测试代码也会很难看, 并且即难读又难写.

有很多 "外部" 组件 (需要初始化的全局变量, 需要加载的库或者配置文件等) 对写测试来讲也是很讨厌的.

// TODO 作者列了一些表来对比不同特征的代码和设计问题

## 14.9 走得太远

对于测试的关注也会过多. 下面是一些例子:
- 牺牲真实代码的可读性, 只是为了使能测试. 把真实代码设计得具有可测试性, 这应该是个双赢的局面: 真实的代码变得简单而且低耦合, 并且也容易为他写测试. 但是如果你仅仅是为了测试它而不得不在真实代码中插入很多难看的塞子, 那肯定有什么地方不对了.
- 着迷于 100% 的测试覆盖率. 测试你代码的前面 90% 通常要比那后面的 10% 所花的工夫少. 后面那 10% 包括用户接口或者很难出现的错误情况, 其中 bug 的代价并不高, 花工夫来测试它们并不值得. 实际上你永远也不会达到 100% 的测试覆盖率. 如果不是因为漏掉的 bug, 也可能是因为漏掉的功能或者你没想到说明书应该改一改. (根据 bug 的成本不同, 对于你花在测试代码上的开发时间有一个合理的范围. 如果你在建一个网站原型, 可能写任何测试都是不值得的. 另一方面, 如果你在为一架飞船或者一台医用设备编写控制器, 测试可能是你的重点.)
- 让测试成为产品开发的阻碍. 我们曾见过这样的情形, 测试, 本应只是项目的一个方面, 却主导了整个项目. 测试成了要敬畏的上帝, 程序猿只是走走这些仪式和过场, 没有意识到他们在工程上宝贵的时间花在别的地方可能会更好.

## 14.10 总结

在测试代码中, 可读性仍然很重要. 如果测试的可读性很好, 其结果是它们也会变得很容易写, 因此大家会写更多的测试. 并且, 如果你把事实代码设计得容易测试, 代码的整个设计会变得更好.

以下是如何改进测试的几个具体要点:
- 每个测试的最高一层应该越简明越好. 最好每个测试的输入/输出可以用一行代码来描述.
- 如果测试失败了, 它所发出的错误消息应该能让你容易跟踪并修正这个 bug.
- 使用最简单的并且能够完整运用代码的测试输入.
- 给测试函数取一个有完整描述性的名字, 以使每个测试所测试到的东西很明确. 不要用 ```Test1()```, 而用像 ```Test_<FunctionName>_<Situation>``` 这样的名字.

最重要的是, 要使它易于改动和增加新的测试.

# 第 15 章 设计并改进 "分钟/小时计数器"

## 15.1 问题

## 15.2 定义类接口

### 改进命名

### 改进注释

## 15.3 尝试1: 一个幼稚的方案

### 这段代码易于理解吗?

### 一个更易读的版本

### 性能问题

## 15.4 尝试2: 传送带设计方案

### 实现两阶段传送带设计

### 这样就完成了吗?

## 15.5 尝试3: 时间桶设计方案

### 实现时间桶设计

### 实现 TrailingBucketCounter

### 实现 ConveyorQueue

## 比较三种方案

## 总结

