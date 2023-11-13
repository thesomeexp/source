---
title: MySQL必知必会 笔记
noindex: true
---

注意: 以下的SQL语句一般都是针对MySQL5+, 不同版本之间可能有差异, 具体请参考官方文档.

# 1.了解SQL

参见 SQL必知必会 第1章. 

# 2.MySQL简介

DBMS可以分为两类:
- 基于共享文件系统的DBMS(如: Microsoft Access和FileMaker)
- 基于客户机-服务器(如: MySQL)

MySQL相关的3个工具: 
- mysql命令行程序
- MySQL Administrator(MySQL管理器)是图形交互客户机, 用来简化MySQL服务器的管理.
- MySQL Query Browser是图形交互客户机, 用来编写和执行MySQL命令.

# 3.使用MySQL

连接上数据库后, 必须使用USE打开数据库, 才能读取其中的数据.
SHOW DATABASES;返回可用数据库的列表.
SHOW TABLES;返回当前选择数据库内的表的列表.
SHOW COLUMNS FROM table;返回table表的列的列表. (DESCRIBE table;语句起到同样的效果)

支持的其他SHOW语句:
- SHOW STATUS;显示广泛的服务器状态信息.
- SHOW CREATE DATABASE database;和SHOW CREATE TABLE table;分别用来显示创建特定数据库或表的MySQL语句.
- SHOW GRANTS;用来显示授予用户(所有/特定用户)的安全权限.
- SHOW ERRORS;和SHOW WARNINGS;用来显示服务器错误或警告消息.

# 4.检索数据

MySQL中多条SQL语句必须以分号(;)分隔. 而单条SQL语句后和多数DBMS一样不需要语句后加分号.

注: 不能部分使用DISTINCT. DISTINCT关键字应用于所有列而不仅是前置它的列. 

LIMIT 5,6指示MySQL返回从第5行开始的6行. 第一个数字为开始位置(从第0行开始), 第二个数为要检索的行数.
由于上面的容易搞糊涂, 所以MySQL5支持另一种替代语法:

~~~ sql
LIMIT 4 OFFSET 3 -- 从行3开始取4行 也就是LIMIT 3, 4
~~~

# 5.排序检索数据

使用ORDER BY和LIMIT的组合, 能够找出一个列中最高或最低的值.

注意: ORDER BY子句应该保证它位于FROM子句之后, 如果使用LIMIT, 它必须位于ORDER BY之后. 使用子句次序不对将产生错误信息.

# 6.过滤数据

MySQL在匹配字符串时默认不区分大小写.

# 7.数据过滤

参见 SQL必知必会 第5章.

# 8.用通配符进行过滤

参见 SQL必知必会 第6章.

# 9.用正则表达式进行搜索

REGEXP关键字, MySQL用WHERE子句对正则表达式提供了初步的支持.
MySQL中的正则表达式匹配(自版本3.23.4后)不区分大小写. 为区分小写可以使用BINARY关键字. 如WHERE prod_name REGEXP BINARY 'JetPack .000'.

注: MySQL中使用正则表达式. 多数正则表达式实现使用单个反斜杠转义特殊字符, 以便能使用这些字符本身. 但MySQL要求两个反斜杠, 因为MySQL自己解释一个, 正则表达式库解释另一个.

这一章主要说正则表达式, 还是以后用到再看看吧.

# 10.创建计算字段

- 拼接(concatenate)MySQL的SELECT语句中可以用Concat()函数来拼接两个列.Concat()需要一个或多个指定的串, 各个串之间用逗号分隔.

~~~ sql
Concat(vend_name, ' (', vend_country, ')')
~~~

# 11.使用数据处理函数

使用函数的SQL可移植性没这么强, 如果决定使用函数, 应该保证做好代码注释, 以便以后能确切地知道所编写SQL代码的含义.

大多数SQL实现支持以下类型的函数:
- 用于处理文本串
- 用于在数值数据上进行算术操作
- 用于处理日期和时间值并从这些值中提取特定成分
- 返回DBMS正使用的特殊信息的系统函数

# 12.汇总数据

参见 SQL必知必会 第9章. 差不多的.

# 13.分组数据

参见 SQL必知必会 第10章. 差不多的.

# 14.使用子查询

参见 SQL必知必会 第11章. 差不多的.

# 15.联结表

参见 SQL必知必会 第12章. 差不多的.

# 16.创建高级联结

参见 SQL必知必会 第13章. 差不多的.

# 17.组合查询

参见 SQL必知必会 第14章. 差不多的.

# 18.全文本搜索

注意: 并非所有引擎都支持全文本搜索, MySQL中最常用的引擎为MyISAM和InnoDB, 前者支持全文本搜索, 而后者不支持.

使用ENGINE=MyISAM指定引擎, 使用FULLTEXT()指定全文本搜索的索引列.

~~~ sql
CREATE TABLE productnotes
(
    note_id int NOT NULL AUTO_INCREMENT,
    prod_id char(10) NOT NULL,
    note_date datetime NOT NULL,
    note_text text NULL,
    PRIMARY KEY(note_id),
    FULLTEXT(note_text)
) ENGINE=MyISAM;
-- 作者提供的数据 来源: http://www.forta.com/books/0672327120/
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(101, 'TNT2', '2005-08-17',
'Customer complaint:
Sticks not individually wrapped, too easy to mistakenly detonate all at once.
Recommend individual wrapping.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(102, 'OL1', '2005-08-18',
'Can shipped full, refills not available.
Need to order new can if refill needed.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(103, 'SAFE', '2005-08-18',
'Safe is combination locked, combination not provided with safe.
This is rarely a problem as safes are typically blown up or dropped by customers.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(104, 'FC', '2005-08-19',
'Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(105, 'TNT2', '2005-08-20',
'Included fuses are short and have been known to detonate too quickly for some customers.
Longer fuses are available (item FU1) and should be recommended.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(106, 'TNT2', '2005-08-22',
'Matches not included, recommend purchase of matches or detonator (item DTNTR).'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(107, 'SAFE', '2005-08-23',
'Please note that no returns will be accepted if safe opened using explosives.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(108, 'ANV01', '2005-08-25',
'Multiple customer returns, anvils failing to drop fast enough or falling backwards on purchaser. Recommend that customer considers using heavier anvils.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(109, 'ANV03', '2005-09-01',
'Item is extremely heavy. Designed for dropping, not recommended for use with slings, ropes, pulleys, or tightropes.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(110, 'FC', '2005-09-01',
'Customer complaint: rabbit has been able to detect trap, food apparently less effective now.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(111, 'SLING', '2005-09-02',
'Shipped unassembled, requires common tools (including oversized hammer).'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(112, 'SAFE', '2005-09-02',
'Customer complaint:
Circular hole in safe floor can apparently be easily cut with handsaw.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(113, 'ANV01', '2005-09-05',
'Customer complaint:
Not heavy enough to generate flying stars around head of victim. If being purchased for dropping, recommend ANV02 or ANV03 instead.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(114, 'SAFE', '2005-09-07',
'Call from individual trapped in safe plummeting to the ground, suggests an escape hatch be added.
Comment forwarded to vendor.'
);
~~~

注意: 不要在导入数据时使用FULLTEXT, 建立索引需要时间, 建议先导入完数据再修改表定义FULLTEXT.

进行全文本搜索:

~~~
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit');
~~~

Match(note_text)指示MySQL针对指定的列进行搜索(传递给Match()的值必须与FULLTEXT()定义中的相同, 如果指定多个列, 则必须根据次序列出他们), Against('rabbit')指定词rabbit作为搜索文本(除非用BINARY否则不区分大小写).

虽然以上的搜索用LIKE同样可以实现, 但是使用全文本搜索返回的是匹配和排序良好的数据. 全文本搜索会将具有高等级的行先返回(比如rabbit排在第3个词要优于排在20个词).

演示排序如何工作:

~~~ sql
SELECT note_text, Match(note_text) Against('rabbit') AS rank
FROM productnotes;
~~~

以上通过Match()和Against()建立了一个计算列rank, 这一列包含全文本搜索计算出的等级值(由行中词数目, 唯一词的数目, 整个索引中词的总数以及包含该词的的行的数目计算出来). rank值靠前的行等级值比词靠后的行等级值高.

所以, 全文本搜索提供了简单LIKE搜索不能提供的功能. 而且因为数据是索引的, 所以全文本搜索还相当快.

使用查询扩展:

~~~ sql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION);
~~~

第二行查询的结果与anvils无关, 但因为它包含第一行中的两个词(customer和recommend), 所以也被检索出来. 查询扩展极大地增加了返回的行数.

布尔文本搜索:

这是一种高级的搜索, 可以选择包含或不包含的词, 类似于正则表达式, 设定一些高级匹配规则过滤数据等.

全文本搜索还有一些说明:
比如有一些词在进行全文本搜索时会被忽略(3个字符以下, 内建非用词, 出现频率高的词, 不具有词分隔符等...), 有一些是可以配置的.

# 19.插入数据

参见 SQL必知必会 第15章.

如果数据检索是重要的, 那么可以通过给INSERT和INTO之间添加LOW_PRIORITY关键字, 指示MySQL降低INSERT语句的优先级. 同样也适用于UPDATE和DELETE语句.

可以用单条语句插入多行以提高INSERT性能:

~~~ sql
INSERT INTO table(col1, 
    col2, 
    col3)
VALUES(
        val11,
        val12,
        val13
    ),
    (
        val21,
        val22,
        val23
    );
~~~

# 20.更新和删除数据

参见 SQL必知必会 第16章.

IGNORE关键字: 在UPDATE更新多行时, 如果出现一个错误整个UPDATE操作都会被取消(恢复到更新前的值). 如果希望发生错误也继续更新, 可以使用IGNORE关键字.

~~~ sql
UPDATE IGNORE table ...
~~~

小心使用: MySQL没有撤销(undo)按钮, 应该要非常小心的使用UPDATE和DELETE.

# 21.创建和操纵表

参见 SQL必知必会 第17章.

如果你仅想在一个表不存在时创建它, 应该在表明后给出IF NOT EXISTS. 这只检查表明是否存在, 不存在则创建该表, 而不是检查表模式是否相匹配.

覆盖AUTO_INCREMENT: 你可以在INSERT语句中指定一个唯一的至今未使用过的值, 那么这个值会被用来替代自动生成的值, 后续的增量也会从这个值开始.

确定AUTO_INCREMENT值: 可以使用last_insert_id()函数获得这个值, 具体用法还是看文档吧.

与大多数DBMS不一样, MySQL不允许使用函数作为默认值, 它只支持常量.

MySQL与其他DBMS不一样, 它有很多种引擎, 为不同的任务选择正确的引擎能获得良好的功能和灵活性. 下面是需要知道的引擎:
- InnoDB是一个可靠的事务处理引擎, 不支持全文本搜索.
- MEMORY功能上等同于MyISAM, 但数据存储在内存而不是磁盘, 所以速度很快, 特别适合用于临时表.
- MyISAM是高性能引擎, 支持全文本搜索, 不支持事务.

不同表可以有不同的引擎, 也就是引擎混用, 但是外键不能跨引擎.

删除表(删除整个表而不是其内容)使用DROP TABLE语句. 删除表不能撤销.

~~~ sql
DROP TABLE table;
~~~

重命名表:

~~~
RENAME TABLE table1 TO table2;
~~~

# 22.使用视图

参见 SQL必知必会 第18章.

性能问题: 如果使用多个联结和过滤创建了复杂的视图或嵌套了视图, 可能会发现性能下降得很厉害, 因此在部署使用了大量视图的应用前, 应该进行测试.

- CREATE VIEW创建视图.
- SHOW CREATE VIEW viewname;来查看创建视图的语句.
- DROP VIEW viewname;删除视图.
- 更新时可以先删除再创建, 也可也直接用CREATE OR REPLACE VIEW.

# 23.使用存储过程

参见 SQL必知必会 第19章.

MySQL称存储过程的执行为调用, 因此执行语句为CALL.

~~~ sql
CALL productpricing(@pricelow, 
                    @pricehigh, 
                    @priceaverage);
~~~

执行名为productpricing的存储过程, 计算并返回产品的最低, 最高和平均价格.

创建存储过程:

~~~ sql
CREATE PROCEDURE productpricing()
BEGIN
    SELECT Avg(prod_price) AS priceaverage
    FROM products;
END;
~~~

此sql语句创建一个名为productpricing的存储过程, 接收参数在括号()中列出来. 这里只是创建一个新的存储过程, 并没有调用执行所以没有返回数据.

注意: 如果你使用的是mysql命令行实用程序, 因为MySQL默认的语句分隔符为分号;. 所以如果创建存储过程体中包含了分号;, 那么它就会以为语句结束了, 因而会出现语法错误. 解决办法是临时更改命令行实用程序的分隔符, 如下所示:

~~~ sql
DELIMITER // -- 使用//作为语句分隔符

CREATE PROCEDURE productpricing()
BEGIN
    SELECT Avg(prod_price) AS priceaverage
    FROM products;
END //

DELIMITER ; -- 还原本来的分隔符;
~~~

使用上述存储过程:

~~~ sql
CALL productpricing()
~~~

删除存储过程:

~~~ sql
DROP PROCEDURE productpricing
~~~

使用参数:

~~~ sql
CREATE PROCEDURE productpring(
    OUT pl DECIMAL(8, 2),
    OUT ph DECIMAL(8, 2),
    OUT pa DECIMAL(8, 2)
)
BEGIN
    SELECT Min(prod_price)
    INTO pl
    FROM products;
    SELECT Max(prod_price)
    INTO ph
    FROM products;
    SELECT Avg(prod_price)
    INTO pa
    FROM products;
END;
~~~

此存储过程接受3个参数, pl存储产品最低价格, ph存储产品最高价格, pa存储产品平均价格. MySQL支持IN(传递给存储过程), OUT(从存储过程传出), INOUT(对存储过程传入和传出)类型的参数. 在BEGIN和END语句之间是SELECT语句来检索值然后保存到相应的变量(通过指定INTO关键字).

为了调用这个带参数的存储过程, 需要指定三个变量名(MySQL变量都必须以@开始):

~~~ sql
CALL productpring(@pricelow, @pricehigh, @priceaverage);
~~~

在调用时, 这条语句不会显示任何数据. 它返回以后可以使用以下语句获得3个值:

~~~ sql
SELECT @pricelow, @pricehigh, @priceaverage;
~~~

检查存储过程:

~~~ sql
SHOW CREATE PROCEDURE productpring;
~~~

# 24.使用游标

创建游标:

~~~ sql
CREATE PROCEDURE processorders()
BEGIN
    DECLARE ordernumbers CURSOR
    FOR
    SELECT order_num FROM orders;
END;
~~~

这个存储过程没有做啥事, 就是定义和命名了游标ordernumbers. 存储过程处理完后游标消失(因为它局限于存储过程, 隐含关闭). 逻辑应该写在OPEN cursor和ENDcursor之间, 作者举了个例子我就不摘了.

# 25.使用触发器

触发器是MySQL响应以下语句而自动执行的一条MySQL语句:
- DELETE
- INSERT
- UPDATE

注意: 即使MySQL触发器必须在每个表中唯一, 而不是在每个数据库中唯一, 但考虑到以后的版本可能会使命名规则更为严格, 因此最好还是在数据库范围内使用唯一的触发器名.

创建触发器:

~~~ sql
CREATE TRIGGER newproduct AFTER INSERT ON products
FOR EACH ROW 
BEGIN 
    SELECT 'Product added';
END;
~~~

上面使用CREATE TRIGGER来创建名为newproduct的新触发器. 触发器可以在操作发生前或之后执行, 这里给出了AFTER INSERT, 所以此触发器将在INSERT语句成功执行后执行. FOR EACH ROW表示此代码对每个插入行执行. 在这个例子中是每插入一行后显示一次'Product added'.

注意: 只有表才支持触发器, 视图和临时表是不支持滴.

触发器按每个表每个事件每次地定义, 每个表每个事件每次只允许一个触发器, 因此每个表最多支持6个触发器. 

注意: 如果BEFORE触发器失败, 则MySQL将不执行请求的操作. 如果BEFORE触发器或语句本身失败, MySQL将不执行AFTER触发器(如果有).

删除触发器:

~~~ sql
DROP TRIGGER newproduct;
~~~

触发器不能更新或覆盖, 为了修改必须先删除然后重新创建.

INSERT触发器:
- 在INSERT触发器内, 可以引用一个名为NEW的虚拟表访问插入的行
- 在BEFORE INSERT触发器中, NEW中的值也可也被更新, 也就是允许修改被插入的值
- 对于AUTO_INCREMENT列, NEW在INSERT执行前包含0, 在INSERT执行后包含新的自动生成值

通常BEFORE用于数据验证和净化, 保证插入表中的数据确实是需要的数据.

DELETE触发器:
- 在DELETE触发器代码内, 可以引用一个名为OLD的虚拟表访问被删除的行.
- OLD中的值全都是只读的, 不能更新

使用BEFORE DELETE触发器的优点(相对于AFTER DELETE触发器来说)为, 如果由于某种原因, 订单不能存档(就是利用触发器将被删除的数据备份), DELETE本身将被放弃(AFTER出错会回滚).

UPDATE触发器:
- 在UPDATE触发器代码中, 可以使用OLD虚拟表访问UPDATE语句执行前的值, 引用一个名为NEW的虚拟表访问新更新的值
- 在BEFORE UPDATE触发器中, NEW中的值可能也被更新, 也就是允许更改将要用于UPDATE语句中的值
- OLD中的值全都是只读的, 不能更新

触发器的一种非常有意义的使用是创建审计跟踪.

# 26.管理事务处理

参见 SQL必知必会 第20章.

MySQL标示事务的开始:

~~~ sql
START TRANSACTION
~~~

使用ROLLBACK:

~~~ sql
SELECT * FROM ordertotals;
START TRANSACTION;
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;
SELECT * FROM ordertotals;
~~~

这个例子首先显示ordertotals表不为空, 然后事务内执行删除, 再显示数据已被删除, 然后回滚, 再查看数据不为空.

使用COMMIT:
一般的MySQL语句都是直接针对数据库执行和编写的(隐含提交implicit commit也就是提交是自动进行的). 但是在事务中不会隐含地提交.

注意: 隐含事务关闭: 当COMMIT或者ROLLBACK语句执行后, 事务会自动关闭.

使用保留点:

对于复杂的事务可能需要部分提交或回退.

~~~ sql
SAVEPOINT delete1; -- 创建保留点
-- do something...
ROLLBACK TO delete1; -- 回退到保留点
~~~

更改默认提交行为:

为指示MySQL不自动提交更改, 需要使用:

~~~ sql
SET autocommit=0;
~~~

autocommit标志决定是否自动提交更改, 不管有没有COMMIT语句. 这个标志是针对每个连接的而不是服务器的.

# 27.全球化和本地化

- 字符集: 字母和符号的集合
- 编码: 某个字符集成员的内部表示
- 校对: 规定字符如何比较的指令

~~~ sql
SHOW CHARACTER SET;
~~~

显示所有可用的字符集以及每个字符集的描述和默认校对.

~~~ sql
SHOW COLLATION
~~~

显示所有可用的校对, 以及它们适用的字符集. 比如latin1对不同的欧洲语言有几种校对(区分大小写_cs, 或者不区分大小写_ci).

通常系统管理在安装时定义一个默认的字符集和校对, 也可也在创建数据库时指定默认的字符集和校对. 使用下面的语句来查看所用的字符集和校对:

~~~ sql
SHOW VARIABLES LIKE 'character%';
SHOW VARIABLES LIKE 'collation%';
~~~

实际上字符集很少是服务器范围(甚至是数据库范围)的设置. 不同的表, 甚至不同的列都可能需要不同的字符集, 而且两者都可以在创建表时指定.

带指定字符集和校对子句的CREATE TABLE:

~~~ sql
CREATE TABLE mytable
(
    column1 INT,
    column2 VARCHAR(10)
) DEFAULT CHARACTER SET hebrew
    COLLATE hebrew_general_ci;
~~~

如果不指定CHARACTER SET和COLLATE则使用数据库默认.

MySQL允许对每个列设置字符集和校对:

~~~ sql
CREATE TABLE mytable
(
    column1 INT,
    column2 VARCHAR(10),
    column3 VARCHAR(10) CHARACTER SET latin1 COLLATE latin1_general_ci
) DEFAULT CHARACTER SET hebrew
    COLLATE hebrew_general_ci;
~~~

如果你需要用与创建表时不同的校对顺序排序特定的SELECT语句, 也可以在语句自身指定:

~~~ sql
SELECT * FROM customers
ORDER BY lastname, firstname COLLATE latin1_general_cs;
~~~

# 28.安全管理

访问控制的目的不仅仅是防止用户的恶意企图, 数据梦魇更为常见的是无意识错误的结果(比如实习的我不小心删错库了, 导师劝我提桶跑路). 通过保证用户不能执行他们不应该执行的语句, 访问控制有助于避免这些情况的发生.

注意: 不要使用root, 应该严肃地对待root登录的使用, 仅在绝对需要时使用它, 不应该在日常的MySQL操作中使用root.

创建用户账号:

~~~ sql
CREATE USER ben IDENTIFIED BY 'p@$$w0rd';
~~~

上面创建了ben用户以及制定了文本口令.

注意: GRANT或者INSERT语句也可也创建用户账号, 但为了安全起见一般不建议直接操作MySQL用来存储用户账号信息的表, 对它们的任何毁坏都有可能严重地伤害到MySQL服务器. 因此最好还是用标记和函数来处理这些表.

重命名一个用户账号:

~~~ sql
RENAME USER ben to bforta;
~~~

删除用户账号和相关的权限:

~~~ sql
DROP USER bforta;
~~~

查看用户访问权限:

~~~ sql
SHOW GRANTS FOR bforta;
~~~

我的新创建用户的结果是返回GRANT USAGE ON *.* TO 'bforta'@'%', USAGE表示这个用户根本没有权限(很不直观). 所以那个结果表示在任意数据库和任意表上对任何东西都没有权限.

注意: 用户定义为user@host, MySQL的权限用用户名和主机名结合定义. 如果不指定主机名则使用默认的主机名%.

使用GRANT语句设置权限:
- 授予的权限
- 被授予访问权限的数据库或表
- 用户名

~~~ sql
GRANT SELECT ON crashcourse.* TO bforta;
~~~

它允许用户在crashcourse数据库的所有表使用SELECT, 也就是对该数据库中的所有表有只读权限.

GRANT的反操作是REVOKE:

~~~ sql
REVOKE SELECT ON crashcourse.* FROM bforta;
~~~

它们可以在几个层次上控制访问权限, 可以参照书给出的表或者官网文档.

更改口令:

~~~ sql
SET PASSWORD FOR bforta = Password('n3w p@$$w0rd');
~~~

设置自己的口令:

~~~ sql
SET PASSWORD = Password('n3w p@$$w0rd');
~~~

# 29.数据库维护

由于MySQL数据库是基于磁盘的文件, 但是由于这些文件总是处于打开和使用状态, 普通的文件副本备份不一定总是有效. 因此可以使用:
- mysqldump程序转存数据库内容到外部某个文件.
- mysqlhotcopy从一个数据库复制所有数据(不是所有引擎都支持)
- 使用MySQL的BACKUP TABLE或SELECT INTO OUTFILE转存数据到外部某个已存在的文件. 并使用RESTORE TABLE来复原.

注意: 首先刷新未写数据, 为了保证所有数据被写到磁盘(包括索引数据), 可能需要在进行备份前使用FLUSH TABLES语句.

数据维护, 检查表键是否正确:

~~~ sql
ANALYZE TABLE orders;
~~~

- CHECK TABLE用来针对许多问题对表进行检查. 
- CHANGED检查自最后一次检查以来改动过的表
- EXTENDED执行最彻底的检查
- FAST只检查未正常关闭的表
- MEDIUM检查所有被删除的链接并进行键检验
- QUICK只进行快速扫描

- 如果MyISAM表访问产生不正确和不一致的结果, 可能需要用REPAIR TABLE来修复相应的表, 但这条语句不应该经常使用, 如果经常用那可能会有更大的问题需要解决噢.
- 如果从一个表中删除大量数据, 应该使用OPTIMIZE TABLE来收回所用的空间, 从而优化表的性能.

诊断启动问题:
服务器启动问题通常是在MySQL配置或服务器本身进行更改时出现. 因为多数MySQL服务器是以系统进程或服务自动启动的, 所以这些报告错误消息可能看不到, 因此排除问题时应尽量手动启动服务器. 一般是执行mysqld启动, 以下是几个重要滴参数:

~~~
--help显示帮助
--safe-mode装载减去某些最佳配置的服务器
--verbose显示全文本消息
--version显示版本信息然后退出
~~~

查看日志文件:
- 错误日志: 包含启动和关闭问题问题以及任意关键错误的细节
- 查询日志: 记录所有MySQL活动, 可能很快的变得很大的日志, 不应该长期使用它
- 二进制日志: 记录更新过(或可能)数据的所有语句
- 缓慢查询日志: 记录执行缓慢的任何查询, 通常在优化时很有用.

# 30.改善性能

作者说他只是回顾前面的重点, 提供进行性能优化探讨和分析的一个出发点:
- 对于生产的服务器, MySQL应该遵守它推荐的硬件建议
- 一般来说生产DBMS应该运行在自己的专用服务器上
- 默认的预先配置通常很好, 但隔一段时间可能需要调整内存分配, 缓冲区大小等
- MySQL是一个用户多线程DBMS, 所以这些多线程任务中某一个执行缓慢就会导致所有请求都执行缓慢. 如果遇到显著的性能不良, 可以使用SHOW PROCESSLIST显示所有活动进程(以及线程ID和执行时间). 必要时可以KILL掉它
- 总是有不止一种方法编写同一条SELECT语句, 应该试验联结, 并, 子查询等找出最佳的方法
- 使用EXPLAIN语句让MySQL解释它如何执行一条SELECT语句
- 一般来说存储过程比一条条执行其中的各条MySQL快
- 应该总是使用正确的数据类型
- 绝不要检索比需求还多的数据, 换言之不要使用SELECT *(除非真的想要每个列)
- 有的操作(包括INSERT)支持一个可选的DELAYED关键字, 它立即返回, 一旦有可能就实际执行该操作
- 在导入数据时, 应该关闭自动提交. 建议删除索引然后导入完成后再重建它们
- 必须索引数据库表以改善数据检索性能. 通过分析SELECT找出重复的WHERE和ORDER BY子句来确定索引
- SELECT语句有复杂的OR条件的话, 可以试试多条SELECT和UNION语句来改善性能
- 索引改善数据检索的性能, 但损害数据插入, 删除和更新的性能(根据需要再添加)
- LIKE很慢, 最好使用FULLTEXT
- 数据库是不断变化的实体, 一组优化良好的表一会后可能就面目全非了, 由于表的使用和内容的更改, 理想的优化和配置也会改变
- 最重要的规则是, 每条规则在某些条件下都会被打破

作者说[https://dev.mysql.com/doc/](https://dev.mysql.com/doc/)有很多提示和技巧.

