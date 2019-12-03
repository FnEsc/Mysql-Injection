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




