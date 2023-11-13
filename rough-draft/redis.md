---
title: Redis 草稿
---
> Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, and message broker. Redis provides data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions, and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster. 

Redis 是一个开源的, 使用内存存储的, 可以作为数据库, 缓存和消息中间件使用的数据库. 

Redis 提供一些数据结构, 例如 strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, streams.

Redis 有内置的复制, Lua 脚本, LRU缓存替换算法, 事务和不同级别的磁盘持久性, 还通过 Redis 哨兵和 Redis 集群的自动分区提供高可用性.

> Redis is in the family of databases called key-value stores.
The essence of a key-value store is the ability to store some data, called a value, inside a key. This data can later be retrieved only if we know the exact key used to store it.
Often Redis it is called a data structure server because it has outer key-value shell, but each value can contain a complex data structure, such as a string, a list, a hashes, or ordered data structures called sorted sets as well as probabilistic data structures like hyperloglog.
As a first example, we can use the command SET to store the value "fido" at key "server:name":
    SET server:name "fido"
Redis will store our data permanently, so we can later ask "What is the value stored at key server:name?" and Redis will reply with "fido":
    GET server:name => "fido"
There is a command in order to test if a give key exists or not:
    EXISTS server:name => 1
    EXISTS server:blabla => 0
Tip: You can click the commands above to automatically execute them. The text after the arrow (=>) shows the expected output.
Type NEXT to continue the t

Redis 是键值对存储数据库的一员. 

键值对存储的本质是, 能够在一个键中存储一些称为值的数据. 通过我们存储时的key, 我们之后可以使用它来拿到对应的值.

通常 Redis 被叫做数据结构服务, 因为它有一个外部使用的 shell, 但是每个值可以包含一个复杂的数据结构, 例如一个 string, 一个 list, 一个 hashes, 或者被叫做 sorted sets 的排序的数据结构, 还包括概念数据结构比如 hyperloglog.

作为第一个例子, 我们可以使用 SET 命令来把值为 "fido" 的数据存储在 "server:name" 这个 key 中.

~~~
SET server:name "fido"
~~~

Redis 会永远存储着我们的数据, 所以我们一会可以问 "key 为 "server:name" 中存储了什么值?", 而 Redis 会回复 "fido".

~~~
GET server:name => "fido"
~~~

有一个命令是用来测试给定 key 是否存在数据:

~~~
EXISTS server:name => 1
EXISTS server:blabla => 0
~~~

> Other basic operations provided by Redis are DEL to delete a given key and associated value, INCR to atomically increment a number stored at a given key:
    SET connections 10
    INCR connections => 11
    INCR connections => 12
    DEL connections
    INCR connections => 1
It is also possible to increment the number contained inside a key by a specific amount:
    INCRBY connections 100 => 101
And there are similar commands in order to decrement the value of the key.
    DECR connections => 100
    DECRBY connections 10 => 90
When you manipulate Redis strings with incrementing and decrementing commands, you are implementing counters. Counters are a very popular application for Redis.

Redis 提供的其它基本操作, 一个是 DEL 用于删除给定的 key 和关联的值, 另一个是 INCR 对给定的 key 的值进行原子自增.

~~~
SET connections 10
INCR connections => 11
INCR connections => 12
DEL connections
INCR connections => 1
~~~

也可以将一个键内包含的数字按特定数量递增.

~~~
INCRBY connections 100 => 101
~~~

同样, 会有相似的命令用于对给定 key 的值进行递减.

~~~
DECR connections => 100
DECRBY connections 10 => 90
~~~

当你使用递增和递减的命令来操作 Redis 的 strings 时, 你就是在实现计数器. 计数器是 Redis 非常流行的应用.

> There is something special about INCR. Why do we provide such an operation if we can do it ourself with a bit of code? After all it is as simple as:
x = GET count
x = x + 1
SET count x
The problem is that doing the increment in this way will only work as long as there is a single client using the key. See what happens if two clients are accessing this key at the same time:
1.Client A reads count as 10.
2.Client B reads count as 10.
3.Client A increments 10 and sets count to 11.
4.Client B increments 10 and sets count to 11.
We wanted the value to be 12, but instead it is 11! This is because incrementing the value in this way is not an atomic operation. Calling the INCR command in Redis will prevent this from happening, because it is an atomic operation.
All the Redis operations implemented by single commands are atomic, including the ones operating on more complex data structures. So when you use a Redis command that modifies some value, you don't have to think about concurrent access.

INCR 有一些特殊之处. 如果我们可以用一点代码自己做, 为什么我们还提供这样的操作呢? 毕竟, 它就像这样简单:

~~~
x = GET count
x = x + 1
SET count x
~~~

问题在于, 使用这种自增方式, 只有在只有一个客户端使用该 key 的情况下才有效. 如果两个客户端同时访问这个 key 猜猜会发生什么:

1. 客户端 A 读到 count 的值为 10.
2. 客户端 B 读到 count 的值为10.
3. 客户端 A 自增 10 使得 count 值为 11.
4. 客户端 B 自增 10 使得 count 值为 11.

我们希望值是 12, 但是这确是 11. 这是因为使用这种方式自增值, 并不是一次原子操作. 在 Redis 中使用 INCR 命令能预防这种形式发生, 因为它是一次原子操作.

所有通过单个命令实现的 Redis 操作都是原子的, 包括操作那些复杂的数据结构. 所以当你使用 Redis 命令修改一些值时, 你不需要去考虑并发访问.

> Redis can be told that a key should only exist for a certain length of time. This is accomplished with the EXPIRE and TTL commands, and by the similar PEXPIRE and PTTL commands that operate using time in milliseconds instead of seconds.
    SET resource:lock "Redis Demo"
    EXPIRE resource:lock 120
This causes the key resource:lock to be deleted in 120 seconds. You can test how long a key will exist with the TTL command. It returns the number of seconds until it will be deleted.
    TTL resource:lock => 113
    // after 113s
    TTL resource:lock => -2
The -2 for the TTL of the key means that the key does not exist (anymore). A -1 for the TTL of the key means that it will never expire. Note that if you SET a key, its TTL will be reset.
    SET resource:lock "Redis Demo 1"
    EXPIRE resource:lock 120
    TTL resource:lock => 119
    SET resource:lock "Redis Demo 2"
    TTL resource:lock => -1
The SET command is actually able to accept further arguments in order to directly set a time to live (TTL) to a key, so you can alter the value of a key and set its TTL at the same time in a single atomic operation:
    SET resource:lock "Redis Demo 3" EX 5
    TTL resource:lock => 5
It is also possible to cancel the time to live of a key removing the expire and making the key permanent again.
    SET resource:lock "Redis Demo 3" EX 5
    PERSIST resource:lock
    TTL resource:lock => -1

Redis 可以被告知一个 key 的存在时长. 这是通过 EXPIRE 和 TTL 命令, 以及使用毫秒而不是秒的 PEXPIRE 和 PTTL 实现的.

~~~
SET resource:lock "Redis Demo"
EXPIRE resource:lock 120
~~~

这会导致 key resource:lock 会在 120秒内被删除. 你可以使用 TTL 命令来查看它还能存在多长时间. 它返回到它将被删除的秒数.

~~~
TTL resource:lock => 113
// after 113s
TTL resource:lock => -2
~~~

TTL 指定 key 返回 -2 表示这个 key 不存在. 返回 -1 表示它将永不会过期. 注意, 如果重新 SET 一个 key, 那么它的 TTL 将会被重置.

~~~
SET resource:lock "Redis Demo 1"
EXPIRE resource:lock 120
TTL resource:lock => 119
SET resource:lock "Redis Demo 2"
TTL resource:lock => -1
~~~

SET 命令实际上可以接受更多的参数, 以便直接为 key 设置一个生存时间, 因此你可以在一次原子操作中同时改变一个键的值并设置其TTL.

~~~
SET resource:lock "Redis Demo 3" EX 5
TTL resource:lock => 5
~~~

取消一个 key 的生存时间让它永久储存同样也是可能的.

~~~
SET resource:lock "Redis Demo 3" EX 5
PERSIST resource:lock
TTL resource:lock => -1
~~~

> Redis also supports several more complex data structures. The first one we'll look at is a list. A list is a series of ordered values. Some of the important commands for interacting with lists are RPUSH, LPUSH, LLEN, LRANGE, LPOP, and RPOP. You can immediately begin working with a key as a list, as long as it doesn't already exist as a different type.
This concept is generally true for every Redis data structure: you don't create a key first, and add things to it later, but you can just directly use the command in order to add new elements. As side effect the key will be created if it did not exist. Similarly keys that will result empty after executing some command will automatically be removed from the key space.
RPUSH puts the new element at the end of the list.
    RPUSH friends "Alice"
    RPUSH friends "Bob"
LPUSH puts the new element at the start of the list.
    LPUSH friends "Sam"

Redis 也支持几种更复杂的数据结构. 首先我们先看 list. list 是一系列有序的值. 一些与 list 交互重要的命令是 RPUSH, LPUSH, LLEN, LRANGE, LPOP, 和 RPOP. 你可以立即将一个 key 作为 list 使用, 只要该 key 不存在一个不一样的类型.

这个概念对每个 Redis 数据结构都是适用的: 你不用先创建一个 key, 然后再添加东西进去, 但你可以直接使用命令来添加新元素. 作为副作用, 如果这个 key 不存在, 它将会被创建. 同样, 在执行某些命令后, 空的 key 将会自动的从 key 空间中移除.

RPUSH 将新元素添加到 list 尾部.

~~~
RPUSH friends "Alice"
RPUSH friends "Bob"
~~~

LPUSH 将新元素添加到 list 的开始.
~~~
LPUSH friends "Sam"
~~~

> LRANGE gives a subset of the list. It takes the index of the first element you want to retrieve as its first parameter and the index of the last element you want to retrieve as its second parameter. A value of -1 for the second parameter means to retrieve elements until the end of the list, -2 means to include up to the penultimate, and so forth.
    LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
    LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
    LRANGE friends 1 2 => 1) "Alice", 2) "Bob"

LRANGE 给出了 list 的子集. 第一个参数为你想获取的元素索引, 第二个参数为你想获取的最后一个元素的索引. 第二个参数的值为 -1 意味着检索元素到列的末尾(也就是包括最后一个), -2 意味着包括到倒数第二个, 以此类推.

~~~
LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
LRANGE friends 1 2 => 1) "Alice", 2) "Bob"
~~~

> So far we explored the commands that let you add elements to the list, and LRANGE that let you inspect ranges of the list. A fundamental functionality of Redis lists is the ability to remove, and return to the client at the same time, elements in the head or the tail of the list.
LPOP removes the first element from the list and returns it.
    LPOP friends => "Sam"
RPOP removes the last element from the list and returns it.
    RPOP friends => "Bob"
Note that the list now only has one element:
    LLEN friends => 1
    LRANGE friends 0 -1 => 1) "Alice"

到目前为止, 我们探索了添加元素到 list 的命令, 以及让你检查列表范围的 LRANGE. Redis lists 的一个基本功能是能够在删除头部或者尾部元素时同时返回给客户端.

LPOP 从 list 中移除第一个元素并返回给客户端.

~~~
LPOP friends => "Sam"
~~~

RPOP 从 list 中移除最后一个元素并返回给客户端.

~~~
RPOP friends => "Bob"
~~~

注意此时 list 还剩下一个元素.

~~~
LLEN friends => 1
LRANGE friends 0 -1 => 1) "Alice"
~~~

> Both RPUSH and LPUSH commands are variadic, so you can specify multiple elements in the same command execution.
    RPUSH friends 1 2 3 => 4
Tip: RPUSH and LPUSH return the total length of the list after the operation.
You can also use LLEN to obtain the current length of the list.
    LLEN friends => 4

RPUSH 和 LPUSH 命令都是可变的, 所以你可以在同一命令执行中指定多个元素.

~~~
RPUSH friends 1 2 3 => 4
~~~

提示: RPUSH 和 LPUSH 返回操作后 list 的总长度.
你也可以使用 LLEN 来获取 list 的当前长度.

~~~
LLEN friends => 4
~~~

> The next data structure that we'll look at is a set. A set is similar to a list, except it does not have a specific order and each element may only appear once. Both the data structures are very useful because while in a list is fast to access the elements near the top or the bottom, and the order of the elements is preserved, in a set is very fast to test for membership, that is, to immediately know if a given element was added or not. Moreover in a set a given element can exist only in a single copy.
Some of the important commands in working with sets are SADD, SREM, SISMEMBER, SMEMBERS and SUNION.
SADD adds the given member to the set, again this command is also variadic.
    SADD superpowers "flight"
    SADD superpowers "x-ray vision" "reflexes"
SREM removes the given member from the set, returning 1 or 0 to signal if the member was actually there or not.
    SREM superpowers "reflexes" => 1
    SREM superpowers "making pizza" => 0

我们要看的下一个数据结构是 set. set 和 list 很像, 只是它没有特定的顺序以及每个元素只能出现一次. 两种数据结构都非常有用, 在 list 中可以快速访问头部或者尾部的元素, 而且元素的顺序得以保留. 在 set 中能快速测试成员是否存在, 也就是立即知道给定的元素是否添加. 同时在一个 set 中给定的元素只能存在于一个副本中.

在处理 set 时, 一些重要的命令是 SADD, SREM, SISMEMBER, SMEMBERS 和 SUNION.

SADD 添加给定成员到 set, 同样这个命令也是可变的.

~~~
SADD superpowers "flight"
SADD superpowers "x-ray vision" "reflexes"
~~~

SREM 从 set 中移除给定的成员, 返回 1 或者 0 代表是否真的有这个成员.

> SISMEMBER tests if the given value is in the set. It returns 1 if the value is there and 0 if it is not.
    SISMEMBER superpowers "flight" => 1
    SISMEMBER superpowers "reflexes" => 0
SMEMBERS returns a list of all the members of this set.
    SMEMBERS superpowers => 1) "flight", 2) "x-ray vision"
SUNION combines two or more sets and returns the list of all elements.
    SADD birdpowers "pecking"
    SADD birdpowers "flight"
    SUNION superpowers birdpowers => 1) "pecking", 2) "x-ray vision", 3) "flight"

SISMEMBER 测试给定的值是否在 set 中, 如果是返回 1 不是返回 0.

~~~
SISMEMBER superpowers "flight" => 1
SISMEMBER superpowers "reflexes" => 0
~~~

SMEMBERS 返回 set 中所有元素的列表.

~~~
SMEMBERS superpowers => 1) "flight", 2) "x-ray vision"
~~~

SUNION 结合两个或更多的 set 然后返回所有元素的列表.

~~~
SADD birdpowers "pecking"
SADD birdpowers "flight"
SUNION superpowers birdpowers => 1) "pecking", 2) "x-ray vision", 3) "flight"
~~~

> The return value of SADD is as important as the one of SREM. If the element we try to add is already inside, then 0 is returned, otherwise SADD returns 1:
    SADD superpowers "flight" => 0
    SADD superpowers "invisibility" => 1

SADD 的返回值和 SREM 的返回值一样重要, 如果我们尝试添加的元素已经存在, 那么它返回 0 否则返回 1.

~~~
SADD superpowers "flight" => 0
SADD superpowers "invisibility" => 1
~~~

> Sets also have a command very similar to LPOP and RPOP in order to extract elements from the set and return them to the client in a single operation. However since sets are not ordered data structures the returned (and removed) elements are totally casual in this case.
    SADD letters a b c d e f => 6
    SPOP letters 2 => 1) "c" 2) "a"
The argument of SPOP after the name of the key, is the number of elements we want it to return, and remove from the set.
Now the set will have just the remaining elements:
    SMEMBERS letters => 1) "b" 2) "d" 3) "e" 4) "f"
There is also a command to return random elements without removing such elements from the set, it is called SRANDMEMBER. You can try it yourself, the arguments are similar to SPOP, but if you specify a negative count instead of a positive one, it may also return repeating elements.

set 也提供一个和 LPOP 和 RPOP 非常相似的命令来从集合中提取元素并返回给客户端. 然而, 由于 set 不是有序数据结构, 在这种情况下返回(和移除)的元素是完全随机的.

~~~
SADD letters a b c d e f => 6
SPOP letters 2 => 1) "c" 2) "a"
~~~

在 SPOP 命令后的 key 名后, 是我们想要它返回(和移除)的元素数量.
现在这个 set 会只有剩余这些元素:

~~~
SMEMBERS letters => 1) "b" 2) "d" 3) "e" 4) "f"
~~~

还有一个命令是返回随即元素而不是从 set 中删除这些元素, 它被称之为 SRANDMEMBER. 你可以自己试一试, 参数与 SPOP 类似, 但如果你指定一个负数而不是正数, 它也可能返回重复的元素.

> Sets are a very handy data type, but as they are unsorted they don't work well for a number of problems. This is why Redis 1.2 introduced Sorted Sets.
A sorted set is similar to a regular set, but now each value has an associated score. This score is used to sort the elements in the set.
    ZADD hackers 1940 "Alan Kay"
    ZADD hackers 1906 "Grace Hopper"
    ZADD hackers 1953 "Richard Stallman"
    ZADD hackers 1965 "Yukihiro Matsumoto"
    ZADD hackers 1916 "Claude Shannon"
    ZADD hackers 1969 "Linus Torvalds"
    ZADD hackers 1957 "Sophie Wilson"
    ZADD hackers 1912 "Alan Turing"
In these examples, the scores are years of birth and the values are the names of famous hackers.
    ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"

set 是一种非常方便的数据类型, 但由于它们是未排序的, 所以在一些问题上并不顺利. 这就是为什么 Redis 1.2 引进了 sorted set.

sorted set 和常规的 set 类似, 但现在每个值都有一个相关的分数. 这个分数用给 set 中的元素排序.

~~~
ZADD hackers 1940 "Alan Kay"
ZADD hackers 1906 "Grace Hopper"
ZADD hackers 1953 "Richard Stallman"
ZADD hackers 1965 "Yukihiro Matsumoto"
ZADD hackers 1916 "Claude Shannon"
ZADD hackers 1969 "Linus Torvalds"
ZADD hackers 1957 "Sophie Wilson"
ZADD hackers 1912 "Alan Turing"
~~~

在这个例子中, 分数是出生年月, 值是著名骇客的名字.

~~~
ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"
~~~

> Simple strings, sets and sorted sets already get a lot done but there is one more data type Redis can handle: Hashes.
Hashes are maps between string fields and string values, so they are the perfect data type to represent objects (eg: A User with a number of fields like name, surname, age, and so forth):
    HSET user:1000 name "John Smith"
    HSET user:1000 email "john.smith@example.com"
    HSET user:1000 password "s3cret"
To get back the saved data use HGETALL:
    HGETALL user:1000
You can also set multiple fields at once:
    HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"
If you only need a single field value that is possible as well:
    HGET user:1001 name => "Mary Jones"

简单的 string, set 和 sorted set 已经完成了很多工作, 但还有一种数据类型 Redis 可以处理: hash.

hash 是在字符串字段和字符串值之间的映射, 所以它可以完美表示对象这种数据类型. (例如: 一个用户有许多字段, 姓名, 年龄等)

~~~
HSET user:1000 name "John Smith"
HSET user:1000 email "john.smith@example.com"
HSET user:1000 password "s3cret"
~~~

要取回保存的数据, 使用 HGETALL:

~~~
HGETALL user:1000
~~~

你也可以一次设置多个字段:

~~~
HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"
~~~

如果你只需要单个字段值:

~~~
HGET user:1001 name => "Mary Jones"
~~~

> Numerical values in hash fields are handled exactly the same as in simple strings and there are operations to increment this value in an atomic way.
    HSET user:1000 visits 10
    HINCRBY user:1000 visits 1 => 11
    HINCRBY user:1000 visits 10 => 21
    HDEL user:1000 visits
    HINCRBY user:1000 visits 1 => 1

hash 字段中的数值处理与 string 中的处理完全相同, 并且有一些操作可以以原子方式增加这个值.

~~~
HSET user:1000 visits 10
HINCRBY user:1000 visits 1 => 11
HINCRBY user:1000 visits 10 => 21
HDEL user:1000 visits
HINCRBY user:1000 visits 1 => 1
~~~

# An introduction to Redis data types and abstractions

https://redis.io/topics/data-types-intro#an-introduction-to-redis-data-types-and-abstractions

# Implement a Twitter Clone in Redis

https://redis.io/topics/twitter-clone
https://docs.spring.io/spring-data/data-keyvalue/examples/retwisj/current/

用户:
专用计数器生成新用户id:
global:uid -> [uid] (string)
根据uid查询用户信息:
uid:[uid] -> {name:xxx, pass:xxx} (hash)
登录时需要根据用户名查询用户id:
user:[name]:uid -> [uid] (string)
还可以添加一个键来保存所有用户的名字:
users -> {张三, 李四} (list)

发表的内容:
专用计数器生成文章id:
global:pid -> [pid] (string)
帖子内容保存在:
pid:[pid] -> {uid: 1, time: 1301931414757, content: Hello World} (hash)
每个用户的帖子:
uid:[uid]:posts -> {1} (list)
所有帖子:
timeline -> {1, 2, 3} (list)

用户与用户之间的关系:
某个用户的关注:
uid:[uid]:following -> {1, 2} (set)
用户的关注者:
uid:[uid]:follower -> {1, 2} (set)

用户帖子视图:
某个用户浏览的:
uid:[uid]:timeline -> {1, 2} (list)

登录追踪相关:
登录跟踪值:
uid:[uid]:auth -> "xxxxxxxxxxx" (string)
根据token反向查询uid:
auth:[token] -> [uid] (string)
