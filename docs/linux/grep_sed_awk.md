# 文本处理三剑客

包括awk、sed和grep三种文本处理工具。

## 一、grep

grep命令：通过正则表达式查找文件中的关键字

### 1. 语法格式

1. `grep [option] [pattern] [file1,file2]`
2. `command | grep [option] [pattern]`

### 2. 选项

| 选项     | 含义                                                |
| -------- | --------------------------------------------------- |
| `-v`     | 不显示匹配行信息                                    |
| `-i`     | 搜索时忽略大小写                                    |
| `-n`     | 显示行号                                            |
| `-r`     | 递归搜索                                            |
| `-E`     | 支持扩展正则表达式                                  |
| `-F`     | 不按正则表达式匹配，按照字符串字面意思匹配          |
| `-c`     | 只显示匹配行数                                      |
| `-w`     | 匹配整词                                            |
| `-x`     | 匹配整行                                            |
| `-l`     | 只显示文件名，不显示内容                            |
| `-s`     | 不显示错误信息                                      |
| `-A <N>` | 显示所找的匹配字段，并显示下面指定的行数的信息 |
| `-B <N>` | 显示所找的匹配字段，并显示上面指定的行数的信息 |
| `-C <N>` | 打印出匹配的上下文（上N行，下N行）前后的N行         |

## 二、sed

sed命令：字符流编辑工具，对标准输出或文件逐行进行处理。

sed的两个临时缓冲区（空间）：

1. 模式空间：保存当前被处理的行
2. 保持空间

### 1.功能

1. 对行进行操作处理
2. 对文件内容信息进行修改，删除、添加、查询

### 2.语法格式

1. `sed [OPTION] "pattern command" file`
2. `command | sed [OPTION] "pattern command"`

### 3.工作模式

1. sed 一次处理一行文件的文本并将当前处理的行存储在临时缓冲区中（此又被称为模式空间“ pattern space”)
2. Sed 一旦完成对模式空间中的行的处理，行将从空间中输出到屏幕。
3. sed 在继续读入下一行，在如此继续，直至全部执行完成

### 4.选项

| 选项 | 含义                               |
| ---- | ---------------------------------- |
| `-n` | 只打印模式匹配行，默认输出         |
| `-e` | 允许多点编辑                       |
| `-f` | 编辑动作保存在文件中，指定文件执行 |
| `-r` | 支持扩展正则表达式                 |
| `-i` | 直接修改文件内容                   |

### 5.pattern用法

| 匹配模式                       | 含义                                               |
| ------------------------------ | -------------------------------------------------- |
| `10command`                    | 匹配第10行                                         |
| `10,20command`                 | 从10行开始匹配到20行结束                           |
| `10,+5command`                 | 从10行开始匹配，到15行结束                         |
| `/pattern/command`             | 匹配有pattern的行，pattern可以是字符串或正则表达式 |
| `/pattern1/,/pattern2/command` | 匹配到pattern1的行开始，到匹配到pattern2的行结束   |
| `10,/pattern/command`          | 从10行开始，到匹配到pattern的行结束                |
| `/pattern/,10command`          | 从匹配到pattern的行开始，到第10行结束              |

### 6.sed命令

命令语法：

1. `[address]command`
2. `[address]{command1;command2}`

#### 6.1 基础命令

##### 6.1 替换（s）

```bash
root@gp:~# sed -i 's/UNIX/unix/g' file
```

常用的定界符`#`、`@`、`/`

##### 6.2 删除（d）

如果匹配会删除整行

```bash
# 删除空行
root@gp:~# sed -i '/^$/d' file  
```

##### 6.3 追加（a）

在匹配行后添加

```bash
sed  '/word/a \text' file
```

##### 6.4 插入（i）

在匹配行前添加

```bash
sed  '/word/i \text' file
```

##### 6.5 更改（c）

删除匹配的行并替换为指定内容

```bash
root@gp:~# sed  '/hello/c \HELLO WORLD' file
HELLO WORLD
```

##### 6.5 列表（l）

显示模式空间的内容，可以显示非打印字符，如制表符等

```bash
root@gp:~# sed  -n 'l' file
hello$
\tworld$
```

##### 6.6 打印（p）

输出模式空间的内容。

```bash
root@gp:~# sed  -n '$p' /etc/passwd
```

##### 6.7 打印行号（=）

打印被匹配的行的行号

##### 6.8 下一步（n）

小写n命令会告诉sed编辑器移动到数据流的下一行文本，而不用重新回到命令最开始再执行一遍。通常sed编辑器在移动到数据流中的下一行文本之前，会在当前行上执行完所有定义好的目命令。

```bash
$ sed -i "/baidu/{n;/baidu/d}" hosts
```

##### 6.9 读（r）

将一个文件的内容插入到指定位置

##### 6.9 写（w）

将模式空间的内容写到文件中

##### 6.10 退出（q）

使sed停止读取新的输入行

```bash
# 输出前5行
root@gp:~# sed '5q' /etc/passwd
```

#### 6.2 高级命令

高级命令改变了执行或控制的流程顺序。

##### 6.2.1 多行Next（N）

读取新的输入行，并将它添加到模式空间的现有内容之后。

例1：

```bash
root@gp:~# cat file
This is the header line.
This is the first line.
This is the second line.
This is the last line.
# 将两行合并为一行
root@gp:~# sed '/first/{N; s/\n/ /}' file
This is the header line.
This is the first line. This is the second line.
This is the last line.
```

例2：

```bash
root@gp:~# cat file.txt
On Tuesday, the Linux System
Administrator's group meeting will be held.
All System Administrator should attent.
Thank you for your attend.
# 替换分散在两行中的文本短语
root@gp:~# sed 's/System Administrator/Desktop User/;N;s/System\nAdministrator/Desktop\nUser/' file.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All Desktop User should attent.
Thank you for your attend.

```

##### 6.2.2 多行删除（D）

只删除模式4空间的第一行。该命令会删除到换行符为止的所有字符。

D命令会强制sed编辑器返回到脚本的起始处，对模式空间中的内容重新执行这些命令（不会从数据流中读取新的文本行）

例1：删除匹配行的前一行文本

```bash
root@gp:~# cat file

This is the header line.

This is the first line.
This is the second line.
This is the last line.
# 删除匹配行的前一行文本
root@gp:~# sed '/^$/{N;/header/D}' file
This is the header line.

This is the first line.
This is the second line.
This is the last line.

```

例2：将多个空行合并为一个空行

```bash
root@gp:~# sed '/^$/{N;/^\n$/D}' file
```

##### 6.2.3 多行打印（P）

输出多行模式空间的第一部分，直到第一个换行符为止。这个命令经常出现在Next之后和Delete之前。

#### 6.3 保持空间

用途: 在处理模式空间中的某些行时，可以用保持空间来临时保存一些行。

| 命令 | 描述                         |
| ---- | ---------------------------- |
| `h`  | 将模式空间复制到保持空间     |
| `H`  | 将模式空间追加到保持空间     |
| `g`  | 将保持空间复制到模式空间     |
| `G`  | 将保持空间追加到模式空间     |
| `x`  | 交换模式空间和保持空间的内容 |

#### 6.4 流程控制

##### 1. 分支（b）

在脚本中将控制权转移到另一行

`[address]b[label]`

address：决定哪些行的数据会触发分支命令

label：定义要跳转到的位置，如果没有label，就跳转到脚本的结尾

例1：不加标签

```bash
root@gp:~# cat file
This is the header line.
This is the first line.
This is the second line.
This is the last line.
# 跳过第2,3行
root@gp:~# sed '{2,3b; s/This is/Is this/; s/line./test?/}' file
Is this the header test?
This is the first line.
This is the second line.
Is this the last test?
```



例2：使用标签



| 编辑命令     | 含义                                            |
| ------------ | ----------------------------------------------- |
| `p`          | 打印                                            |
| `a`          | 行后追加                                        |
| `i`          | 行前追加                                        |
| `r`          | 外部文件读取，行后追加                          |
| `w`          | 匹配行写入外部文件                              |
| `d`          | 删除                                            |
| `s/old/new/`   | 将行内的第一个old替换为new                      |
| `s/old/new/g` | 将行内全部的old替换为new                        |
| `s/old/new/2g` | 同一行内，只替换从第2个开始到剩下所有符合条件的 |
| `s/old/new/ig` | 将行内全部的old替换为new，忽略大小写            |
| `=`            | 显示匹配的行号，但是不显示内容                  |

### 7.反向引用

| 字符 | 功能                           |
| ---- | ------------------------------ |
| `&`    | 保存所搜索字符用来替换其他字符 |
| `()`   | 分组，把需要引用的部分括起来   |

7.1. 使用`&`引用

```bash
$ sed -n 's/ubuntu/++&++/p' passwd
++ubuntu++:x:1000:1000:ubuntu,,,:/home/ubuntu:/bin/bash
```

7.2. 使用`()`分组引用

```bash
$ sed -n 's/\(ro\)../\1OT/gp' passwd
roOT:x:0:0:roOT:/roOT:/bin/bash
```

### 8.变量引用

匹配模式中使用变量时，使用双引号，否则需要把变量用单引号括起来

`"s/$old/$new/g"` 或 `'s/'$old'/'$new'/g'`



## 三、awk

awk：报告生成器，是 Alfred Aho,Peter Weinberger,Brian Kernighan 三位作者联合开发的。

### 1.语法格式

1. `awk 'BEGIN{}pattern{commmand}END{}' file`
2. `command | awk 'BEGIN{}pattern{commmand}END{}'`

| 语法格式   | 含义                       |
| ---------- | -------------------------- |
| `BEGIN{}`    | 正式处理数据之前执行       |
| `pattern`    | 匹配模式                   |
| `{commands}` | 处理命令，可以多行         |
| `END{}`      | 处理完所有匹配数据后的执行 |

### 2.内置变量

| 内置变量   | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| `$0`       | 整行内容                                                     |
| `$`        | 当前行的第n个字段                                            |
| `NF`       | Number Field，当前行的字段个数，也就是有多少列               |
| `NR`       | Number Row，当前行的行号，从1开始计数                        |
| `FNR`      | File Number Row多文件处理是，每个文件行号单独计数，都是从1开始 |
| `FS`       | Field Separator，输入字段分割符，默认以空格或tab分割         |
| `RS`       | Row Separator，输入行分隔符，默认回车换行                    |
| `OFS`      | Output Field Separator，输出字段分隔符，默认空格             |
| `ORS`      | Output Row Separator，输出行分隔符，默认回车换行             |
| `FILENAME` | 当前输入的文件名称                                           |
| `ARGC`     | 命令行参数个数                                               |
| `ARGV`     | 命令行参数数组                                               |

### 3. 输出

#### 3.1. printf

| 格式符 | 含义                     |
| ------ | ------------------------ |
| `%s`   | 打印字符串               |
| `%d`   | 打印十进制数             |
| `%f`   | 打印浮点数               |
| `%x`   | 打印十六进制数           |
| `%o`   | 打印八进制数             |
| `%e`   | 打印数字的科学技术法格式 |
| `%c`   | 打印单个字符的ASCII      |

| 修饰符 | 含义                                     |
| ------ | ---------------------------------------- |
| `-`      | 左对齐                                   |
| `+`      | 右对齐                                   |
| `#`      | 显示8进制在前面加0，显示16进制在前面加0x |

```bash
# printf
$ awk 'BEGIN{FS=":"}{printf "%-20s %10s\n",$1,$7}' /etc/passwd
```

#### 3.2 print

```bash
$ awk 'BEGIN{FS=":"}/root/{printf $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

### 4.匹配模式

#### 4.1.正则表达式匹配

```bash
$ awk 'BEGIN{FS=":"}/root/{printf $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

#### 4.2.关系运算匹配

| 关系运算符 | 含义             |
| ---------- | ---------------- |
| `<`        | 小于             |
| `<=`       | 小于等于         |
| `>`        | 大于             |
| `>=`       | 大于等于         |
| `==`       | 等于             |
| `!=`       | 不等于           |
| `~`        | 匹配正则表达式   |
| `!~`       | 不匹配正则表达式 |

```bash
# 关系运算符匹配
$ awk -F ":" '$3>=1000 {print $0}' /etc/passwd
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
ubuntu:x:1000:1000:ubuntu,,,:/home/ubuntu:/bin/bash
```

```bash
# 正则匹配
$ awk -F ":" '$1~/root/ {print $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

#### 4.3. 布尔运算符匹配

| 布尔运算符 | 含义 |
| ---------- | ---- |
| `||`       | 或   |
| `&&`       | 与   |
| `!`        | 非   |

```bash
# 布尔运算符匹配
$ awk -F ":" '$3==0 || $3>=1000 {printf "%s\n",$0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
ubuntu:x:1000:1000:ubuntu,,,:/home/ubuntu:/bin/bash
```

### 5. 表达式

#### 5.1. 算数运算符

| 运算符  | 含义                      |
| ------- | ------------------------- |
| `+`     | 加                        |
| `-`     | 减                        |
| `*`     | 乘                        |
| `/`     | 除                        |
| `%`     | 取模                      |
| `^或**` | 乘方                      |
| `++x`   | 在返回x变量之前，x变量加1 |
| `x++`   | 在返回x变量之后，x变量加1 |

```bash
# 算数运算
$ awk 'BEGIN{x=10;y=30;print x+y}'
40
```

```bash
# 统计空行
$ awk '/^$/{cnt++}END{print cnt}' /etc/services
6
```

### 6. 逻辑控制

语法格式同C语言。

#### 6.1. `if`

```bash
$ awk -F ":"  '{if ($3==0) {printf "%-10s%s\n","root用户:",$1} else {printf "%-10s%s\n","普通用户:",$1}}' /etc/passwd
root用户:   root
普通用户:     daemon
普通用户:     bin
普通用户:     sys
普通用户:     sync
普通用户:     games
普通用户:     man
普通用户:     lp
普通用户:     mail
普通用户:     news
普通用户:     uucp
普通用户:     proxy
普通用户:     www-data
普通用户:     backup
普通用户:     list
普通用户:     irc
普通用户:     gnats
普通用户:     nobody
普通用户:     systemd-timesync
普通用户:     systemd-network
普通用户:     systemd-resolve
普通用户:     systemd-bus-proxy
普通用户:     syslog
普通用户:     _apt
普通用户:     lxd
普通用户:     messagebus
普通用户:     uuidd
普通用户:     dnsmasq
普通用户:     sshd
普通用户:     ubuntu
```

#### 6.2. `do...while`

```bash
$ awk 'BEGIN{do{sum+=i;i++}while(i<=100) print sum}'
5050
```

#### 6.3. `while`

```bash
$ awk 'BEGIN{while(i<=100){sum+=i;i++}print sum}'
5050
```

#### 6.4. `for`

### 7. 字符串函数

| 函数名              | 解释                                                    | 返回值                    |
| ------------------- | ------------------------------------------------------- | ------------------------- |
| `length(str)`        | 计算字符串长度                                          | 整数长度值                |
| `index(str1,str2)`   | 在str1中查找str2的位置                                  | 返回值为位置索引，从1计数 |
| `tolower(str)`       | 转换为小写                                              | 转换后的小写字符串        |
| `toupper(str)`       | 转换为大写                                              | 转换后的大写字符串        |
| `substr(str,m,n)`    | 从str的m个字符开始，截取n位                             | 截取后的子串              |
| `split(str,arr,fs)`  | 按fs分割字符串，结果保存arr                             | 切割后的子串个数          |
| `match(str,RE)`      | 在str中按照RE查找，返回位置                             | 返回索引位置              |
| `sub(RE,RepStr,str)` | 在str中搜索符合RE的字串，将其替换为RepStr，只替换第一个 | 替换的个数                |
| `gsub(RE,RepStr,str)` | 在str中搜索符合RE的字串，将其替换为RepStr，替换所有     | 替换的个数                |

### 8. 选项

| 选项 | 解释           |
| ---- | -------------- |
| `-v` | 定义或引用变量 |
| `-f` | 指定脚本文件   |
| `-F` | 指定分隔符     |
| `-V` | 查看版本号     |

```bash
# 把外部变量引入awk
$ x=12
$ awk -v y="$x" 'BEGIN{print y}'
12
```
