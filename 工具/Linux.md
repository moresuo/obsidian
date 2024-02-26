***
## 命令行的基本操作
### 命令的使用方法
Linux 命令使用格式：
```shell
command [-options] [argument1]
```
- Command: 命令名
- []: 中括号中的内容代表可选项
- Options: 选项，用来对命令的控制
- Argument : 传递给命令的参数
### 查看帮助文档
#### Help
Linux 命令自带的帮助信息
例如：`ls --help`
![[Pasted image 20231210171844.png]]
#### Man
Man 是 Linux 提供的一个帮助手册，包含了绝大多数命令和函数的使用
例如：`man ls`
![[Pasted image 20231210171920.png]]

| 功能键 | 功能                 |
| ------ | -------------------- |
| 空格键 | 显示手册页的下一页   |
| 回车键 | 一次滚动手册页的一行 |
| b      | 回滚一屏             |
| f      | 前滚一屏             |
| q      | 退出手册             |
| h      | 列出所有功能键       |
| /word  | 搜索 word 字符串     |
### Tab 键自动补全
在敲出命令的前几个字母后使用 tab 键可以自动补全

### History 游览历史命令
当执行过一些命令后，可以使用上下键快速生成以前使用的命令，也可以使用 `history` 命令查看最近 1000 条使用的命令
常见用法：
```shell
history N #显示最近N条命令
history -c #清除所有命令记录
history -w xxx.txt #保存历史记录命令到xxx.txt文件中
```
![[Pasted image 20231210173219.png]]
![[Pasted image 20231210173249.png]]

### 快捷键组合

| 快捷键   | 功能                 |
| -------- | -------------------- |
| Ctrl+C   | 结束正在运行的程序   |
| Ctrl+d   | 结束输入或退出 shell |
| Ctrl+s   | 锁住终端             |
| Ctrl+q   | 解锁终端             |
| Ctrl+l   | 清屏                 |
| Ctrl+a   | 将光标移动到行首     |
| Ctrl+e   | 将光标移动到行尾     |
| Ctrl+u   | 删除当前光标到行首   |
| Ctrl+k   | 删除当前光标到行尾   |
| Ctrl+y   | 在光标处粘贴         |
| Ctrl+r   | 查找历史命令         |
| Ctrl+x+u | 撤销操作             |
| Ctrl+z   | 转入后台运行         |
### 关机和重启
- 立刻关机：`shutdown -h now`, `poweroff`
- 定时关机：`shutdown -h time`
- 重启：`reboot`
## Linux 命令
### 权限管理
- 超级用户 (/root)命令提示符：`~#`,普通用户 (/用户名)命令提示符：`~$`
- 主目录：`~`，根目录：`/`
#### 列出目录内容：ls
1. Linux 文件或者目录名称最长可以有 265 个字符
2. '.'代表当前目录，'..'代表上一级目录
3. 以'.'开头的文件为隐藏文件，需要加上 `-a` 参数才能看见
Ls 常用参数：

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| -a   | 显示指定目录下的子目录和文件，包括隐藏文件 |
| -l   | 以列表方式显示文件的详细信息               |
| -h   | 配合-l 以人性化方式显示文件大小            |
![[Pasted image 20231210185954.png]]
从左往右各标识的含义：<mark style="background: #ABF7F7A6;">文件类型标识符，权限访问位，硬链接数，文件属主，文件属组，大小，修改时间，文件名</mark>
Ls 支持通配符：

| 通配符 | 含义 |
| ---- | ---- |
| * | 代表文件名中的所有字符 |
| ls te* | 查找已 te 开头的文件 |
| ? | 代表文件名中的任意一个字符 |
| ls ?. C | 第一个字符任意，后缀为. C 的文件 |
| ls [abc] | 匹配 abc 中任意一个 |
| ls [a-f]* | 从 a-f 中任意一个字符开头的文件 |
| \ | 如果要使用通配符作为普通字符使用，在前面加\ |
| ls \*a | 查找名为*a 的文件 |
|  |  |
|  |  |
#### 文件访问权限
- 读权限 (r): 对于文件，用户可以<mark style="background: #FF5582A6;">读取</mark>文件内容，对于目录，用户具有<mark style="background: #FF5582A6;">浏览目录</mark>的权限
- 写权限 (w): 对于文件，用户可以<mark style="background: #FF5582A6;">修改文件</mark>的内容，对于目录，用户具有<mark style="background: #FF5582A6;">删除，移动</mark>目录下文件的权限
- 执行权限 (x): 对于文件，用户可以<mark style="background: #FF5582A6;">执行文件</mark>，对于目录，用户具有<mark style="background: #FF5582A6;">进入该目录</mark>的权限
第一个字母代表文件类型标识符：
- `d` 代表目录
- `-` 代表普通文件
- `c` 代表字符设备
- `b` 代表硬件块设备
- `s` 代表管道文件
- `l` 代表软连接文件
#### 修改文件权限：chmod
Chmod 修改文件权限有两种方法：<mark style="background: #ABF7F7A6;">字母法与数字法</mark>
字母法：`chmod u/g/o/a +/-/=rwx 文件名`

| [ugoa] | 含义             |
| ------ | ---------------- |
| u      | user: 文件所有者 |
| g      | group: 文件组    |
| o      | other: 其他用户  |
| a      | all: 所有用户    |

| [+-=] | 含义     |
| ----- | -------- |
| +     | 增加权限 |
| -     | 删除权限 |
| =     | 赋予权限 |

<mark style="background: #FFB86CA6;">案例：创建一个 test1. sh 文件，并给所有者添加可执行权限</mark>
```shell
#创建test1.sh文件
sudo touch test1.sh
#添加可执行权限
sudo chmod u+x test1.sh
```

| 字母 | 说明                      |
| ---- | ------------------------- |
| r    | 读取权限，用数字 4 表示   |
| w    | 写权限，用数字 2 表示     |
| x    | 可执行权限，用数字 1 表示 |

<mark style="background: #FFB86CA6;">案例：给 test1.sh 文件的所有者赋予读、写、执行权限，同组用户拥有读、执行权限，其他用户拥有执行权限</mark>
```shell
sudo chmod 751 test1.sh
```

#### 修改文件所有者：chown
操作：`chown 新用户名 文件名`
<mark style="background: #FFB86CA6;">案例：将 test1.sh 文件所有者从 root 改为当前用户</mark>
```shell
sudo chown yhk test1.sh
```

#### 修改文件所属组：chgrp
操作：`chgrp 新组名 文件名`
<mark style="background: #FFB86CA6;">案例：将 test1.sh 文件的属组从 root 改为当前用户所在组</mark>
```shell
sudo chgrp yhk test1.sh
```

### 文件内容查看
Linux 系统使用以下命令查看文件内容：
- `cat`：从第一行开始显示文件内容
- `tac`: 从最后一行开始显示
- `nl`: 显示的时候，顺道输出行号
- `more`: 一页一页显示文件内容
- `less`：与 more 相似，可以往前翻页，<mark style="background: #FF5582A6;">查看结束不会退出</mark>
- `head`：只看文件前几行
- `tail`: 只看文件最后几行
#### 基本显示：cat, tac
操作：`cat [-AbEnTv]`

| [AbEnTv] | 含义                             |
| -------- | -------------------------------- |
| -A       | 可列出一些特殊字符而不是空白字符 |
| -v       | 列出一些看不出来的特殊字符       |
| -E       | 将结尾的断行字节$显示出来        |
| -T       | 将 tab 键以^显示出来             |
| -b       | 显示行号，空白行不显示行号       |
| -n       | 显示行号，空白行也显示行号       |

![[Pasted image 20231210200732.png]]

`tac` 与 `cat` 显示相反，从最后一行开始显示

#### 显示行号：nl
操作：`nl [-bnw] 文件名`
![[Pasted image 20231210201357.png]]

#### 分屏显示：more，less
操作：`more 文件名`
More 运行时可以输入的命令有：
- 空白键：代表向下翻一页
- 回车键：向下翻一行
- /word: 查询字符串
- : f  : 显示出文档名和目前显示的行数
-  q: 退出 more 操作
-  b: 往回翻页

操作：`less 文件名`
Less 运行时可以输入的命令有：
- 空白键：向下翻一页
- Pagedown: 向下翻一页
- Pageup: 向上翻一页
- /word：向下搜索字符串
- ?Word: 向上搜索字符串
-  n: 重复前一个搜寻
- N: 反向重复前一个搜寻
-  q: 退出 less 操作

#### 读取首尾 n 行：head, tail
操作：`head [-n number] 文件名`
- -n 后面的数字代表读取文件前几行
<mark style="background: #FFB86CA6;">案例：读取/etc/brltty.conf 文件的前 20 行</mark>
```shell
head -n 20 /etc/brltty.conf
```

操作：`tail [-n number] 文件名`
操作和 head 命令一致
<mark style="background: #FFB86CA6;">案例：读取/etc/brltty.conf 的最后 20 行</mark>
```shell
tail -n 20 brltty.conf
```

### 文件管理
#### 输出重定向：>
将左边命令的输出重定向到指定文件，原文件内容会被覆盖
操作例子：`ls -l > test.txt`,如果 test.txt 不存在则自动创建

> [!warning] 注意
> `>` 会将原文件覆盖，`>>` 则会在原文件末尾追加

<mark style="background: #FFB86CA6;">案例：创建一个 test2.txt 文件，将当前目录列表重定向到该文件中</mark>
```shell
#创建一个test2.txt文件
sudo touch test2.txt
#给当前用户赋予可以对test2.txt写的权限
sudo chmod o+x test2.txt
#将当前目录列表重定向到test2.txt
sudo ls -l > test2.txt
#查看test2.txt
cat test2.txt
```

#### 管道：|
一个命令的输出可以作为另一个命令的输入
`|` 分为两端，左端的输出作为右端的输入
操作例子：`ls -l | more`

#### 清屏：clear
清空终端显示内容
操作：`clear`
快捷键：Ctrl+l

#### 切换工作目录：cd
**linux 操作系统中所有目录和文件名大小写敏感**

| 命令  | 含义                           |
| ----- | ------------------------------ |
| cd    | 相当于 `cd~`                   |
| cd ~  | 切换到当前用户的主目录 (/home) |
| cd .  | 切换到当前目录                 |
| cd .. | 切换到上一级目录               |
| cd -  | 进入上次所在的目录             |

#### 显示当前路径：pwd
![[Pasted image 20231212202905.png]]
#### 创建目录：mkdir
新建目录不能与当前目录下的文件重名，并且目录创建者必须对当前目录具有<mark style="background: #ABF7F7A6;">写权限</mark>
操作：`mkdir [-mp] 目录名称`

| 参数 | 含义                 |
| ---- | -------------------- |
| -m   | 指定创建目录的权限   |
| -p   | 递归创建所需要的目录 |

<mark style="background: #FFB86CA6;">案例：创建一个 aaa/aa/a 多级目录</mark>
```shell
sudo mkdir -p aaa/aa/a
```
![[Pasted image 20231212204243.png]]
<mark style="background: #FFB86CA6;">案例：将创建的目录属主权限设置为可读，可写，可执行，属组权限为只能执行，其他人权限为只能执行</mark>
```shell
sudo mkdir -m 711 test3
```
![[Pasted image 20231212204715.png]]

#### 删除文件：rm
Rm 既可以删除文件也可以删除目录，<mark style="background: #ABF7F7A6;">linux 下一切皆是文件</mark>
操作：`rm [-ifr] 文件名`

| 参数 | 含义                       |
| ---- | -------------------------- |
| -i   | 删除文件之前进行确认       |
| -f   | 强制删除，不论文件是否存在 |
| -r   | 递归删除目录下的内容       |

常用操作：`rm -rf /*` ☠️

#### 建立链接文件：ln
链接文件分为软链接和硬链接
软链接：`ln -s 源文件 链接文件`
硬链接：`ln 源文件 链接文件`
软链接类似于 windows 下的快捷方式，只是保存了源文件的一个绝对路径，链接文件的修改不会引起源文件的修改
<mark style="background: #ABF7F7A6;">硬链接只能链接普通文件不能链接目录</mark>，硬链接可以看做一个指针执行文件的索引节点，文件系统是通过索引节点来识别文件的，硬链接没有主次之分，及时删除了源文件，链接文件还是存在

#### 文本搜索：grep
Linux 系统中 grep 命令是一种强大的文本搜索工具，grep 允许对文本文件进行模式查找。如果找到匹配模式， grep 打印包含模式的所有行，通常和管道 `|` 进行组合操作
操作：`grep [-参数] 搜索内容 文件名`

| 参数 | 含义                     |
| ---- | ------------------------ |
| -l   | 显示所有包含的行         |
| -n   | 显示行号                 |
| -i   | 显示匹配行，不区分大小写 |
| -v   | 显示所有不匹配的行       |
| -q   | 找到匹配行，但不显示     |
| -c   | 只显示匹配行数       |


<mark style="background: #FFB86CA6;">案例：在当前目录下，查找前缀包含 test 字符串的文件下的含有test 字样的行</mark>
```shell
sudo grep 'test' test*
```
![[Pasted image 20231212212420.png]]

<mark style="background: #FFB86CA6;">案例：以递归的方式查找符合条件的文件，查找指定目录/etc/acpi 及其子目录（如果存在子目录的话）下所有文件中包含字符串"update"的文件，并打印出该字符串所在行的内容</mark>
```shell
sudo grep -r 'update' /etc/acpi
```
![[Pasted image 20231212213106.png]]

<mark style="background: #FFB86CA6;">案例：反向查找，查找文件名前缀为 test 的文件下不包含 test 的行</mark>
```shell
sudo grep -v 'test' test*
```
![[Pasted image 20231212213445.png]]

#### 查找文件：find
操作：`find path -option [-print] -exec [外部命令] {} \;`
**常见用法：** 

| 命令                             | 含义                                       |
| -------------------------------- | ------------------------------------------ |
| `find ./ -name test.sh`          | 查询当前目录下所有名为 test.sh 的文件      |
| `find ./ -name '*.sh'`           | 查找当前目录下所有以. Sh 结尾的文件        |
| `find ./ -name [A-Z]*`           | 查找当前目录下以大写字母开头的文件         |
| `find /tmp -size 2M`             | 查找 tmp 目录下大小等于 2 M 的文件         |
| `find /tmp -size +2M`            | 查找 tmp 目录下大小大于 2 M 的文件         |
| `find ./ -size +4K -a -size -2M` | 查找当前目录下大小大于 4 k 小于 2 M 的文件 |
| `find ./ -perm 0777`             | 查找当前目录下文件权限为 777 的文件        |
**常用参数：**
- `-perm xxxx` : 权限为 xxxx 的文件或目录
- `-user`: 按照文件属主来查找文件
- `-size n`: 按照文件大小查找文件
- `-amin n`: 在过去 n 分钟被读取过
- `-anewer file`: 比文件 file 更晚被读取的文件
- `-atime n`: 在过去 n 天内被读取的文件
- `-cmin n`: 在过去 n 分钟内被修改的文件
- `-cnewer file`: 比文件 file 更晚被修改的文件
- `-ctime n`: 在过去 n 天内被修改的文件
- `-mtime +n`: 查找在 n 天之前更改过的文件
- `-empty`: 查找空文件
- `-name`: 根据文件名查找
- `-type`: 根据文件类型查找
	-  b: 块设备文件
	-  d: 目录文件
	-  c: 字符设备文件
	-  p: 管道文件
	-  l: 符号链接文件
	-  f: 普通文件
- `-exec 命令 {} \;`
可以将多个操作联合起来，使用 `-a` 表示 and, `-o` 表示 or

<mark style="background: #FFB86CA6;">案例：</mark>
1. 显示当前目录中大于 20 字节并以 .c 结尾的文件
```shell
sudo find . -size +20c -a -name "*.c"
```
2. 将当前目录及其子目录下所有普通文件列出
```shell
sudo find . -type f
```
3. 将当前目录及其子目录下最近 20 天更新过的文件列出
```shell
sudo find . -ctime -20
```
4. 查询目录/var/log 下更改时间在 7 天之前的普通文件，并在删除之前询问
```shell
sudo find /var/log -type f -a -mtime +7 -ok rm {} \;
```
5. 查询当前目录下的文件属主具有读写权限，所在组用户和其他用户具有读权限的普通文件，并以列表的方式显示出来
```shell
sudo find . -type f -a perm 0644 -exec ls -l {} \;
```
6. 查询系统下所有空普通文件，并显示其完整路径
```shell
sudo find / -empty -a -type f -exec ls -l {} \;
```
7. 从根目录上查找所有符号链接文件，并将其删除
```shell
sudo find / -type l -exec rm -rf {} \;
```
8. 从当前目录查找用户 yhk 所有的文件，并显示在屏幕上
```shell
sudo find . -user yhk -exec ls -l {} \;
```
9. 从当前目录查找以. Doc 结尾的文件，修改时间要在 3 天以上，并将其删除，删除时进行询问
```shell
sudo find . -name ".doc" -a -mtime +3 -ok rm {} \;
```
10. 查找当前目录下所有链接文件，并以长格式显示文件信息
```shell
sudo find . -type l -exec ls -l {} \;
```
11. 在当前目录下查找文件名由一个小写字母，一个大写字母，两个数字组成，并且文件后缀为. Doc 的文件
```shell
sudo find . -name "[a-z][A-Z][0-9][0-9].doc"
```

#### 拷贝文件：cp
将文件或目录复制到另一个文件或目录中
操作：`cp [参数] source path`

| 参数 | 含义 |
| ---- | ---- |
| `-a` | 通常在复制目录时使用，保留文件属性并递归复制 |
| `-i` | 交互式确认 |
| `-r` | 递归复制目录下的所有文件 |
| `-v` | 显示拷贝进度 |
| `-l` | 创建硬链接 |
| `-u` | 文件更新，目标文件比源文件旧更新 |

#### 移动文件和重命名：mv
**移动文件：**`mv [参数] source path`
**重命名：**`mv 原名称 新名称`

| 参数 | 含义                                                                   |
| ---- | ---------------------------------------------------------------------- |
| `-f` | 不进行交互式操作，报错也不会提示                                         |
| `-i` | 交互式操作，如果移动的目标目录已存在相同文件名，会提示用户是否进行覆盖 |
| `-v` | 显示移动进度                                                           |
<mark style="background: #FFB86CA6;">案例：创建一个 aaa 文件，将他移动到根目录下，并将其重命名为 bbb</mark>
```shell
sudo touch aaa #创建aaa
sudo mv aaa / #移动到根目录
cd /
sudo mv aaa bbb #重命名bbb
```

#### 文件打包：tar
将多个文件打包为一个文件，也可以把档案文件解开恢复数据，通常和压缩操作联合在一起
操作：`tar [参数] 打包文件名 文件`

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| `-c` | 生成归档文件                               |
| `-y` | 列出归档解档详细过程，显示进度             |
| `-f` | 指定档案文件名称，一定在参数选项的最后一个 |
| `-t` | 列出档案中包含的文件                       |
| `-x` | 解开档案                                   |
| `-z` | 压缩归档文件，使用 gzip 方式               |
| `-j` | 压缩归档文件，使用 bzip2 方式              |

<mark style="background: #FFB86CA6;">案例：将 test 1.sh, test2.txt, test3/归档为一个 test.tar 文件</mark>
```shell
sudo tar -cvf test.tar test1.sh test2.txt test3/
```
![[Pasted image 20231216112547.png]]

#### 文件压缩解压：gzip, bzip2
tar 与 gzip 命令结合实现文件打包，压缩。tar 负责打包，gzip 负责压缩, 使用 gzip 和 tar 打包压缩后的文件后缀名为 `.tar.gz`
操作：`gzip [参数] 文件名`

| 参数 | 含义     |
| ---- | -------- |
| `-d` | 解压文件（默认） |
| `-r` | 压缩文件 |
<mark style="background: #FFB86CA6;">案例：将 test.tar 进行压缩</mark>
```shell
sudo gzip test.tar
```
<mark style="background: #FFB86CA6;">案例：将 test.tar.gz 解压并解档</mark>
```shell
sudo tar -zxvf test.tar.gz
```
![[Pasted image 20231216114229.png]]
<mark style="background: #FFB86CA6;">案例：将 test.tar.gz 解压解档到根目录下</mark>
```shell
sudo tar -zxvf test.tar.gz -C /
```

> [!NOTE] bzip2 与 gzip 用法相似
> - 压缩：tar jcvf 压缩包包名 文件
> - 解压：tar jxvf 压缩包包名 -C /解压路径

#### 文件压缩与解压：zip, unzip
- 压缩文件：`zip [-r] 目标文件 源文件(可以使用通配符方式)`
- 解压文件：`unzip -d 解压后存放的目录 压缩文件`

#### 查看命令位置：which
操作：`which 命令`
![[Pasted image 20231216115943.png]]
### 用户和用户组管理
#### 查看当前用户：whoami
查看当前系统账户的用户名，可以通过 cat /etc/passwd 查看系统用户信息
![[Pasted image 20231216120637.png]]
#### 查看当前登录的用户：who
![[Pasted image 20231216120843.png]]
显示内容：用户名，显示终端，登录时间
#### 退出登录账户：exit
- 如果是图形界面，则退出当前终端
- 如果是 ssh 远程连接，则退出登录账户
- 如果是切换后的用户，则返回到上一个用户
#### 添加用户：useradd
操作：`useradd [参数] 用户名`

| 参数 | 含义                       |
| ---- | -------------------------- |
| `-d` | 指定用户登录系统时的主目录 |
| `-m` | 自动建立目录               |
| `-g` | 指定组名称                 |
- Linux 每个用户必须要有一个主目录，主目录就是用户第一次登录时用户默认的当前目录
- 每一个必须要有一个主目录，使用 useradd 创建用户时，一定给用户指定一个主目录
- 创建用户时，如果不指定用户属于哪一个用户组，系统会自动创建一个和用户名一致的用户组

<mark style="background: #FFB86CA6;">案例：创建一个用户并将他纳入 root 组</mark>
```shell
sudo useradd yhk2 -g root
```
#### 修改用户：usermod
usermod 不仅能改用户的 SHELL 类型，所归属的用户组，也能改用户密码的有效期，还能改登录名。usermod 如此看来就是能做到用户帐号大转移；比如我把用户 A 改为新用户 B
操作：`usermod [-u uid] [-g group] [-d 主目录] [-s shell] [-c 注释] [-l 新名称] [-f 失效日] [-e 过期日] [-p 密码] 用户名`
usermod 命令会参照你命令列上指定的部份修改系统帐号档。下列为 usermod 可选用的参数。
`-c comment`
更新用户帐号 password 档中的注解栏，一般是使用 chfn(1)来修改。
`-d home_dir`
更新用户新的登入目录。如果给定-m 选项，用户旧目录会搬到新的目录去，如旧目录不存在则建个新的。
`-e expire_date` 加上用户帐号停止日期。日期格式为 MM/DD/YY.
`-f inactive_days` 帐号过期几日后永久停权。当值为 0 时帐号则立刻被停权。而当值为-1 时则关闭此功能。预设值为-1。
`-g initial_group` 更新用户新的起始登入用户组。用户组名须已存在。用户组 ID 必须参照既有的的用户组。用户组 ID 预设值为 1。
`-G group`,[...] 定义用户为一堆 groups 的成员。每个用户组使用","区格开来，不可以夹杂空白字元。用户组名同-g 选项的
限制。如果用户现在的用户组不再此列，则将用户由该用户组中移除。
`-l login_name` 变更用户 login 时的名称为 login_name。其它不变。特别是，用户目录名应该也会跟着更动成新的登入名。
`-s shell` 指定新登入 shell。如此栏留白，系统将选用系统预设 shell。
`-u uid` 用户 ID 值。必须为唯一的 ID 值，除非用-o 选项。数字不可为负值。预设为最小不得小于/etc/login.defs 中定义的
`UID_MIN` 值。0 到 UID_MIN 值之间是传统上保留给系统帐号使用。用户目录树下所有的档案目录其 userID 会自动改变。放在用户目录外的档案则要自行手动更动。

> [!warning] 注意
> 警告: usermod 不允许你改变正在线上的用户帐号名称。当 usermod 用来改变 userID, 必须确认这名 user 没在电脑上执行任何程序。你需手动更改用户的 crontab 档。也需手动更改用户的 at 工作档。采用 NISserver 须在 server 上更动相关的 NIS 设定。

- Usermod 最好不要用它来改用户的密码，因为他在/etc/shadow 中显示的是明口令；修改用户的口令最好用 passwd
<mark style="background: #FFB86CA6;">案例：创建一个用户 aaa, 将将他纳入 test 用户组，然后将它重命名为 bbb, 并修改主目录</mark>
```shell
sudo useradd aaa -g test
sudo usermod -l bbb -d /home/bbb aaa
```
#### 设置用户密码：passwd
passwd 作为普通用户和超级权限用户都可以运行，但作为普通用户只能更改自己的用户密码，但前提是没有被 root 用户锁定；如果 root 用户运行 passwd ，可以设置或修改任何用户的密码
操作：`passwd [参数] 用户名`

| 参数 | 含义                           |
| ---- | ------------------------------ |
| `-l` | 锁定口令，禁用账号             |
| `-u` | 口令解锁                       |
| `-d` | 使用账号无口令                 |
| `-f` | 强迫用户下次使用时修改口令     |
| `-S` | 查看用户密码状态，仅 root 用户 |

#### 删除用户：userdel
操作：`userdel [-r] 用户名`
>使用-r 参数会将用户下的主目录一并删除
>请不要轻易用-r 参数；他会删除用户的同时删除用户所有的文件和目录，切记；如果用户目录下有重要的文件，在删除前请备份
>其实也有最简单的办法，但这种办法有点不安全，也就是直接在/etc/passwd 中删除您想要删除用户的记录；但最好不要这样做，/etc/passwd 是极为重要的文件，可能您一不小心会操作失误

#### 切换用户：su
su 命令就是切换用户的工具，怎么理解呢？比如我们以普通用户 beinan 登录的，但要添加用户任务，执行 useradd ，beinan 用户没有这个权限，而这个权限恰恰由 root 所拥有。解决办法无法有两个，一是退出 beinan 用户，重新以 root 用户登录，但这种办法并不是最好的；二是我们没有必要退出 beinan 用户，可以用 su 来切换到 root 下进行添加用户的工作，等任务完成后再退出 root。我们可以看到当然通过 su 切换是一种比较好的办法
>对于 ubuntu 平台，必须使用 sudo su 进入 root 账号

#### 以 root 身份执行指令：sudo
sudo 的配置文件是<mark style="background: #FF5582A6;">/etc/sudoers</mark> ，我们可以用他的专用编辑工具 visodu ，此工具的好处是在添加规则不太准确时，保存退出时会提示给我们错误信息；配置好后，可以用切换到您授权的用户下，通过 sudo -l 来查看哪些命令是可以执行或禁止的
/etc/sudoers 的规则可分为两类；<mark style="background: #ABF7F7A6;">一类是别名定义，另一类是授权规则</mark>；别名定义并不是必须的，但授权规则是必须的
授权操作：`授权用户 主机=[(切换到哪些用户或用户组)][是否需要命令验证] 命令1,[(切换到哪些用户或用户组)][是否需要命令验证] 命令2……`


> [!seealso] 实例
>```shell
>Yhk   ALL=(root)     /bin/chown,/bin/chmod 
>```
>表示 yhk 可以在任何可能出现的主机名的主机中，可以切换到 root 下执行 chown，chmod 命令
>```shell
>Yhk ALL=(root) NOPASSWD: /bin/chown,/bin/chmod
>```
>表示 yhk 用户可以在登录任何主机上切换到 root 下执行 chown, chmod 命令，并且不需要输入密码
>```
>%yhk ALL=/usr/sbin/*
>```
>表示 yhk 用户组下所有成员可以在任何主机上以 root 的身份执行 sbin 下的所有指令

#### 添加和删除用户组：groupadd, groupdel
- 添加用户组：`groupadd 组名`
- 删除用户组：`groupedel 组名`
#### 用户组管理：groupmod
操作：`groupmod 选项 用户组`

| 选项 | 含义                       |
| ---- | -------------------------- |
| `-g` | 为用户组指定新的组标识符   |
| `-n` | 将用户组的名字修改为新名字 |

<mark style="background: #FFB86CA6;">案例：将 test 用户组的组 id 更改为 8848</mark>
```shell
groupmod -g 8848 test
```

<mark style="background: #FFB86CA6;">案例：将 test 用户组名改为 tset</mark>
```shell
groupmod -n tset test
```

### 系统管理
#### 查看当前日历：cal
这个功能需要事先下载：`sudo apt install ncal`
![[Pasted image 20231217163740.png]]
#### 显示或设置当前时间：date
设置时间需要管理员权限
1. 显示当前时间：
![[Pasted image 20231217164117.png]]
2. 设置时间为 2021 年 6 月 18 日 12:00：
![[Pasted image 20231217164340.png]]
3. 设置时间为 12:00:00
![[Pasted image 20231217164513.png]]
4. 显示时区
![[Pasted image 20231217164613.png]]
#### 查看网络状态：netstat
该功能需要事先下载：`sudo apt install net-tools`
![[Pasted image 20231217165527.png]]
#### 显示进程信息：ps
进程一般分为三类：
1. 交互进程
2. 批处理进程
3. 守护进程：后台进程，在系统开机时通过脚本自动激活
**进程的属性：**
- 进程 ID: 进程的唯一标识符
- 进程的父进程 ID:PPID
- 启动进程的用户 ID（UID）和所归属的组（GID）
- 进程状态：运行 R, 休眠 S, 僵尸 Z
- 进程执行的优先级
- 进程所连接的终端名
- 进程的资源占用：占用资源大小（内存，CPU）
**父进程与子进程**：他们的关系是管理与被管理的关系，当父进程终止时子进程也随之终止，子进程终止时父进程不一定终止

| 参数 | 含义                                   |
| ---- | -------------------------------------- |
| `-a` | 显示终端上的所有进程，包括其他用户进程 |
| `-u` | 显示进程的详细状况                     |
| `-x` | 显示没有控制终端的进程                 |
| `-w` | 显示加宽，以便显示更多信息             |
| `-r` | 只显示正在运行的进程                   |

**常见操作：**
- `ps -e`: 显示所有进程信息，瞬时的
- `ps -u root -N`: 显示所有不是 root 运行的进程
- `ps -aux`: 显示所有进程详细状况
- `ps -ef | grep xxx`: 显示所有包含 xxx 的进程
#### 以树状图显示进程关系：pstree
![[Pasted image 20231217171447.png]]
#### 动态显示进程：top
top 命令用来动态显示运行中的进程。top 命令能够在运行后，在指定的时间间隔更新显示信息。-d 参数可以指定显示信息更新的时间间隔
在 top 命令执行后，可以按下按键得到对显示的结果进行排序：

| 参数 | 含义                            |
| ---- | ------------------------------- |
| M    | 根据内存使用量来排序            |
| P    | 根据 CPU 占用率来排序，默认显示 |
| T    | 根据进程运行的时间长短来排序    |
| U    | 输入用户名来选择进程            |
| K    | 输入 PID 杀死对应的进程         |
| q    | 退出                            |
| h    | 帮助                            |
![[Pasted image 20231217172851.png]]
- 第一行显示的是<mark style="background: #FF5582A6;">当前时间</mark>，<mark style="background: #FF5582A6;">系统启动时间</mark>，<mark style="background: #FF5582A6;">当前系统用户的登录数</mark>，<mark style="background: #FF5582A6;">平均负载</mark>
- 第二行显示的是<mark style="background: #FF5582A6;">进程总数</mark>，<mark style="background: #FF5582A6;">正在运行的进程数</mark>，<mark style="background: #FF5582A6;">休眠状态的进程数</mark>，<mark style="background: #FF5582A6;">停止运行的进程数</mark>，<mark style="background: #FF5582A6;">僵尸状态的进程数</mark>
- 第三行显示的是目前 CPU 的使用情况，us 代表用户空间占用的 CPU 百分比，sy 代表内核空间占用的百分比，ni 表示用户进程空间内改变过优先级的进程占用 CPU 百分比，id 表示空闲 CPU 百分比，wa 代表等待输入输出的 CPU 时间百分比
- 第四行显示的的是物理内存使用情况，total 代表物理内存总量，free 空闲内存总量，used 代表使用的物理内存总量，buff 代表做内核缓存的总量
- 第五行显示的是交换分区的使用情况，total 代表交换分区的总量，free 代表空闲的交换分区总量，used 代表使用的大小

| 列名    | 含义                                        |
| ------- | ------------------------------------------- |
| USER    | 进程所有者的用户名                          |
| PR      | 优先级                                      |
| NI      | nice 值，负值表示高优先级，正值表示低优先级 |
| VIRT    | 进程占用的虚拟内存总量                      |
| RES     | 进程占用的物理内存值                        |
| SHR     | 共享内存大小                                |
| %CPU    | CPU 的使用率                                |
| %MEM    | 进程使用的物理内存百分比                    |
| TIME+   | 进程使用的 CPU 时间                         |
| COMMAND | 进程启动的启动命令                          |
#### 终止进程：kill
杀死对应 PID 的进程
操作：`kill [-signal] pid`
信号值从 0-15，9 为强制终止，95 至尊强制终止😏
#### 查看配置网卡信息：ifconfig
![[Pasted image 20231217183546.png]]

#### 测试远程主机连通性：ping
![[Pasted image 20231217184053.png]]
### 磁盘管理
#### 文件系统整体磁盘使用情况：df
df 命令用于检测文件系统的磁盘占用和空余情况，可以显示所有文件系统对磁盘块的使用情况
操作：`df [参数]`

| 参数 | 含义                           |
| ---- | ------------------------------ |
| `-a` | 显示所有文件系统的磁盘使用情况 |
| `-m` | 以 1024 字节为单位显示         |
| `-t` | 显示指定文件系统磁盘的使用情况 |
| `-T` | 显示文件系统                   |
![[Pasted image 20231217184856.png]]
#### 列出目录所占空间：du
du 命令用于统计目录或文件所占磁盘空间的大小，该命令的执行结果与 df 类似，du 更侧重于磁盘的使用状况
操作：`du [参数] 文件名或目录`

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| `-a` | 递归显示目录中子目录或文件所占磁盘空间大小 |
| `-s` | 显示指定文件或目录占用的数据块             |
| `-b` | 以字节为单位显示磁盘占用情况               |
| `-l` | 计算文件大小                               |
#### 磁盘分区：fdisk
Fdisk 不支持大于 2 T 的磁盘
Fdisk -l 显示系统中所有分区内容
![[Pasted image 20231217190231.png]]
Fdisk 是一个交互式命令

| 参数 | 含义                     |
| ---- | ------------------------ |
| n    | 创建一个分区             |
| d    | 删除一个分区             |
| p    | 显示硬盘分区表           |
| q    | 退出 fdisk, 但不保存     |
| t    | 修改分区类型             |
| w    | 保存修改结果并退出 fdisk |
<mark style="background: #FFB86CA6;">案例：创建一个回环设备，并为他进行分区，先创建一个主分区，将主分区修改为一个交换分区</mark>
1. 创建一个用于承载虚拟文件系统的空文件。这个文件的大小将成为挂载后文件系统的大小。创建指定大小文件的简单方法是通过 dd 命令。这个命令以块为单位（通常为 512 字节，或者你也可以自定义块大小）从一个文件向另一个文件复制数据。/dev/zero 文件则是一个很好的数据来源
```shell
sudo dd if=/dev/zero of=/vartualfs bs=1024 count=30720
```
![[Pasted image 20231217193024.png]]
2. 回环设备以 /dev/loop 0、/dev/loop 1 等命名。每个设备可虚拟一个块设备。为了确认当前系统是否有在使用回环设备，你需要使用下面的语句
`losetup /dev/loop0`,查看回环设备 loop0 是否存在，如果 loop0 存在就在检测 loop1 是否存在，直到找到一个不存在的回环设备，然后创建它，或者直接使用 `fdisk -l` 查看有哪些回环设备
![[Pasted image 20231217193610.png]]
3. 接下来使用 losetup 命令来把常规文件或块设备（/dev/loop 0）关联到一个 loop 文件（virtualfs）上。注意只有超级用户才有权限设置回环设备
```shell
sudo losetup /dev/loop14 /vartualfs
```
![[Pasted image 20231217193821.png]]
4. 使用 fdisk 创建主分区
![[Pasted image 20231217194256.png]]
![[Pasted image 20231217194423.png]]
![[Pasted image 20231217194635.png]]
5. 创建一个交换分区
![[Pasted image 20231217194841.png]]

#### 磁盘格式化：mkfs
磁盘分割完毕后自然就是要进行文件系统的格式化，格式化的命令非常的简单，使用 mkfs （make filesystem） 命令
操作：`mkfs [-t 文件系统格式] 设备名`

<mark style="background: #FFB86CA6;">案例：将/dev/loop14 格式化为 ext3 格式</mark>
```shell
sudo mkfs -t ext3 /dev/loop14
```
![[Pasted image 20231217195539.png]]

#### 设置卷标：e2label
卷标可用在挂载文件系统时代替设备名
操作：`e2label 设备名 新卷标`

<mark style="background: #FFB86CA6;">案例：将/dev/loop14 的卷标设置为 test</mark>
```shell
sudo e2label /dev/loop14 test
```
#### 磁盘挂载与卸载：mount, umount
操作：`mount [-t 文件系统] [-L 卷标名] [-o 额外选项] [-n] 设备名 挂载点`

<mark style="background: #FFB86CA6;">案例：将/dev/loop14 使用卷标的方式挂载到/mnt/test 下</mark>
```shell
sudo mkdir /mnt/test
sudo mount -L test /mnt/test
```
![[Pasted image 20231217200715.png]]
<mark style="background: #FFB86CA6;">案例：卸载/dev/loop14</mark>
操作：`umount [-fn] 装置文件名或挂载点`
- -f: 强制卸载
- -n: 不升级/etc/mytab 下卸载
>/etc/mytab 用于记录当前已挂载的文件系统信息，执行默认的挂载和卸载都会动态更新该文件

```shell
sudo umount /dev/loop14
```

### 管道命令
Linux 的管道命令是‘|’，通过它可以对命令进行连续的处理

> [!warning] 注意
> - 管道命令仅处理标准输出，对于标准错误输出将忽略
> - 管道命令右边的命令，必须能够接受标准输入流命令才行，否则传递过程中数据将抛弃
> - 常用来接受数据管道命令的有：less, more, head, tail, 而 ls, cp, mv 就不行

#### 统计字数：wc
可以计算文件的 Byte 数、字数、或是列数，若不指定文件名称、或是所给予的文件名为"-"，则 wc 指令会从标准输入设备读取数据
操作：`wc [-lwm] 文件名`

| 参数 | 含义         |
| ---- | ------------ |
| `-l` | 统计行数     |
| `-w` | 统计英文单词 |
| `-m` | 统计字节数   |

>在默认的情况下，wc 将计算指定文件的行数、字数，以及字节数。使用的命令为 wc filename
>如果想一次性统计多个文件的信息，可以这样：wc filename1 filename2 filename3

#### 列选取命令：cut
根据分隔符进行分割成数列，查看指定列，cut以行为单位，根据分隔符把行分成若干列，这样就可以指定选取哪些列了
操作：`cut -d '分割符' -f 列号`

<mark style="background: #FFB86CA6;">案例：只显示/etc/passwd 下的用户名和 shell</mark>
```shell
cat /etc/passwd | cut -d ':' -f 1,7
```

#### 行选取命令：grep [[#文本搜索：grep]]
操作：`grep [-cinv] '查找的字符串' 文件名`
Grep 查询的内容串可以使用正则表达式，常用的正则表达式：

| 参数            | 含义                                                                               |
| --------------- | ---------------------------------------------------------------------------------- |
| `^a`             | 行首，搜寻以 a 开头的行，grep -n ^a 1.txt                                          |
| ke$             | 行尾，搜寻以 ke 结尾的行，grep -n ke$ 1.txt                                        |
| `[Ss] igna[Ll]` | 匹配中括号一系列字符中的其中一个，搜寻匹配单词 signal、signaL、Signal、SignaL 的行 |
| .               | 任意一个字符，不包括换行符，grep -n 'e.e' 1.txt                                    |
| *               | 匹配零个或多个任意字符                                                             |
| `[^]`           | 匹配一个不在指定范围内的字符                                                       |
| `x\{m\}`        | 重复字符 x, m 次                                                                   |
| `x\{m,\}`       | 重复字符 x, 至少 m 次                                                              |
| `x\{m,n\}`      | 重复字符 x, 至少 m 次，最多 n 次                                                   |
| `\w`            | 匹配英文字符和数字字符                                                             |
| `\b`            | 单词锁定符                                                                         |
**实例：**

1. 显示所有以“h”结尾的行
`grep h$`
2. 匹配所有以“a”开头且以“e”结尾的，中间包含2个字符的单词
`grep '<a..e>'`
3. 显示所有包含一个”y”或”h”字符的行
`grep [yh]`
4. 显示不包含字母 a~k 且后紧跟“pple”的单词
`grep [^a-k]pple`
5. 从系统词典中选择所有以“c”开头且以“o”结尾的单词
`grep '\<c.*o\>'`
6. 找出一个文件中或者输出中找到包含 * 的行
`grep '\*'`
7. 显示所有包含每个字符串至少有20个连续字母的单词的行
`grep [a-Z]\{20,\}`

#### 行排序：sort
操作：`sort [参数] 文件名`

| 参数 | 含义                    |
| ---- | ----------------------- |
| `-f` | 忽略大小写差异          |
| `-b` | 忽略前面空格字符部分    |
| `-M` | 以月份的名称来排序      |
| `-n` | 使用纯数字进行排序      |
| `-r` | 反向排序                |
| `-t` | 分隔符，默认 tab 键分隔 |
| `-k` | 以哪个区间进行排序      |
>默认是以第一个字符进行升序排序

#### 去重：uniq

| 参数 | 含义           |
| ---- | -------------- |
| `-i` | 忽略大小写不同 |
| `-c` | 进行计数       |
| `-u` | 只显示唯一的行 |
| `-d` | 只显示重复的行 |

- 该命令用于排序完之后对排序结果去重
```shell
last | cut -d ' ' -f 1 | sort | uniq
```
- 排序完之后删除重复行，并在前面加上该行重复的次数
```shell
sort testfile | uniq -c
```
- 排序完之后只显示重复的行，并显示重复的次数
```shell
sort testfile | uniq -dc
```
- 仅显示不重复的行
```shell
sort testfile | uniq -c
```
#### 同时输出多个文件：tee
从标准输入设备中读取数据，将其内容输出到标准输出设备，同时保存成文件
一般情况下用重定向即可，需要输出多个文件时使用该命令
**参数：**
`-a`: 附加到既有文件的后面而非覆盖原文件
- 将输出同时保存到多个文件中，同时将输出内容显示到控制台
```shell
python@xxx:~/test$ echo "hello world"|tee f1 f2
hello world
python@xxx:~/test$ cat f1
hello world
python@xxx:~/test$ echo "hello world"|tee f1 f2 -a
hello world
python@xxx:~/test$ cat f1
hello world
hello world
```
#### 文件按行进行连接：join
将两个文件中指定栏位相同的行连接起来。即按照两个文件中共同拥有的某一列，将对应的行拼接成一行
两个相连接的文件尽量是相关联的，在进行 join 之前要先进行排序
```shell
$ cat testfile_1
Hello 95 #例如，本例中第一列为姓名，第二列为数额
Linux 85
test 30
cmd@hdd-desktop:~$ cat testfile_2
Hello 2005 #例如，本例中第一列为姓名，第二列为年份
Linux 2009
test 2006
```
使用 join 将两个文件连接
```shell
$ join testfile_1 testfile_2 #连接testfile_1、testfile_2中的内容
Hello 95 2005 #连接后显示的内容
Linux 85 2009
test 30 2006
```
#### 将多个文件对应行链接在一起：paste
paste 指令会把每个文件以列对列的方式，一列列地加以合并
操作：`paste [-s][-d<间隔字符>] 文件`

| 参数 | 含义                   |
| ---- | ---------------------- |
| `-s` | 将多行数据合并为一行   |
| `-d` | 以指定间隔字符进行分割 |
```shell
$ cat file #file文件的内容
xiongdan 200
lihaihui 233
lymlrl 231
$ cat testfile #testfile文件的内容
liangyuanm ss
$ cat testfile1 #testfile1文件的内容
huanggai 56
zhixi 73
$ paste file testfile testfile1
xiongdan 200 liangyuanm ss huanggai 56
lihaihui 233 zhixi 73
lymlrl 231
$ paste -d ':' file testfile testfile1
xiongdan 200:liangyuanm ss:huanggai 56
lihaihui 233::zhixi 73
lymlrl 231::
```
#### 文件分割：split
Split 将一个文件分割为多个文件
该指令将大文件分割为较小的多个文件，在默认情况下将每 1000 行分割为一个文件
操作：`split [-bl] file prefix`

| 参数   | 含义             |
| ------ | ---------------- |
| `-b`   | 以大小进行分割   |
| `-l`   | 以行数进行分割   |
| prefix | 切割后文件的前缀 |
<mark style="background: #FFB86CA6;">案例：使用 split 指令将 readme 文件每 6 行分割为一个文件</mark>
```shell
$ split -6 README #将README文件每六行分割成一个文件
$ ls #执行ls指令
#获得当前目录结构
README xaa xad xag xab xae xah xac xaf xai
```

#### 参数代换：xargs
不是所有的命令都支持管道，如 ls，对于不支持管道的命令，可以通过 xargs 让其有管道命令的效果，如下所示：
```shell
# find /sbin -perm +7000 | xargs ls -l
-rwsr-x--- 1 root ecryptfs 19896 Feb 23 2012 /sbin/mount.ecryptfs_private
-rwsr-xr-x 1 root root 75496 Jan 9 2013 /sbin/mount.nfs
-rwsr-xr-x 1 root root 75504 Jan 9 2013 /sbin/mount.nfs4
-rwxr-sr-x 1 root root 8544 Feb 22 2012 /sbin/netreport
-rwsr-xr-x 1 root root 14112 Nov 2 2010 /sbin/pam_timestamp_check
-rwsr-xr-x 1 root root 75504 Jan 9 2013 /sbin/umount.nfs
-rwsr-xr-x 1 root root 75504 Jan 9 2013 /sbin/umount.nfs4
-rwsr-xr-x 1 root root 19768 Nov 2 2010 /sbin/unix_chkpwd
```
如果没有 xargs, ls -l 的结果将不会显示到标准输出中，因为 ls 不支持管道命令
xargs 用作替换工具，读取输入数据重新格式化后输出
定义一个测试文件，内有多行文本数据：
```shell
# cat test.txt
a b c d e f g
h i j k l m n
o p q
r s t
u v w x y z
```
多行输入单行输出：
```shell
# cat test.txt | xargs
a b c d e f g h i j k l m n o p q r s t u v w x y z
```

## Linux 命令练习
***
### 常用命令
1. 进入到用户主目录
```shell
cd ~
```
2. 查看当前所在目录
```shell
yhk@yhk-virtual-machine:~$ pwd
/home/yhk
```
3. 查看当前用户主目录下的所有文件，目录（包括隐藏文件）
```shell
ls -la
```
4. 在根目录下创建一个 hadoop 文件夹
```shell
sudo mkdir /hadoop
```
5. 在 hadoop 目录下创建 src 和 webroot 文件夹
```shell
sudo mkdir /hadoop/{src,webroot}
```
6. 进入到 hadoop 目录下，创建 .classpath 文件和 README 文件
```shell
cd /hadoop
sudo touch {.classpath,README}
```
7. 查看 hadoop 目录下所有文件
```shell
ls -la
```
8. 在 hdoop 目录下创建一个 test. Txt 文件，并写入"this is test"
```shell
echo "this is test" > test.txt
```
9. 查看 test. Txt 的内容
```shell
cat test.txt
```
10. 向 README 文件追加"please read me first"
```shell
echo "please read me first" >> README
```
11. 将 test. Txt 中的内容追加到 README 中
```shell
cat test.txt >> README
```
12. 拷贝 hadoop 目录下所有文件到/hadoop-bak 中
```shell
cp -r /hadoop /hadoop-bak
```
13. 进入/hadoop-bak 目录下，将 test. Txt 文件移动到 src 目录下，并重命名为 Student. Java
```shell
sudo mv test.txt src/Student.java
```
14. 在 src 目录下创建一个 struct. Xml 文件
```shell
sudo touch struct.xml
```
15. 删除所有 xml 文件
```shell
sudo rm -rf *.xml
```
16. 删除 hadoop-bak 目录及其以下所有文件
```shell
sudo rm -rf /hadoop-bak
```
17. 返回 hadoop 目录，查看 README 有多少个单词，多少行
```shell
cd /hadoop
wc -w README
wc -l README
```
18. 返回根目录，将 hadoop 目录打包，然后再用 gzip 压缩
```shell
sudo tar hadoop.tar.gz hadoop
```
19. 将 hadoop. Tar. Gz 解压并解档
```shell
sudo tar -zxvf hadoop.tar.gz
```
20. 将 hadoop 目录打包，并用 bzip 2 压缩，保存到 tmp 目录下
```shell
sudo tar -jcvf /tmp/hadoop.tar.bz2 hadoop
```
21. 将 hadoop. Tar. Bz 2 解压并解档到 usr 目录下
```shell
sudo tar hadoop.tar.bz2 -C /usr
```
### 系统命令
1. 查看主机名
```shell
hostname
```
2. 修改主机名（重启后无效）
```shell
hoastname moresuo
```
3. 修改主机名（永久生效）
```shell
vim /etc/sysconfig/network
```
4. 修改 IP (重启后无效)
```shell
sudo ifconfig eth0 ip
```
5. 修改 IP (重启后永久生效)
```shell
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```
6. 查看系统信息
```shell
uname -a
uname -r
```
7. 查看 id 命令
```shell
id -u
id -g
```
8. 查看当前日期
```shell
date
```
9. 日历
```shell
cal
```
10. 查看文件信息
```shell
file filename
```
11. 查看文件大小
```shell
du -h server.c
du -ah net #列出目录下所有文件大小
```
12. 查看分区
```shell
df -h
```
13. .ssh
```shell
ssh moresuo@192.168.10.133
```
### 用户和组
1. 添加一个用户 tom, 将他纳入 user 组，并添加注释信息
```shell
sudo useradd -g user -c "hr tom" tom
```
2. 设置 tom 用户密码
```shell
sudo passwd tom
```
3. 将 tom 的用户名修改为 tomcat
```shell
sudo usermod -l tomcat tom
```
4. 将 tomcat 添加到 sys, root 组
```shell
sudo usermod -G sys,root tomcat
```
5. 查看 tomcat 组信息
```shell
groups tomcat
```
6. 添加一个 jerry 用户并设置密码
```shell
sudo useradd jerry
sudo passwd jerry
```
7. 添加一个 america 组
```shell
sudo groupadd america
```
8. 将 jerry 添加到 america 组
```shell
sudo usermod -g america jerry
```
9. 将 tomcat 从 root 组和 sys 组删除
```shell
sudo gpasswd -d tomcat root
sudo gpasswd -d tomcat sys
```
10. 将 america 组改为 am
```shell
sudo groupmod -n am america
```

### 权限
1. 创建 a.txt 文件和 b.txt 文件，并将他们拥有者和所在组设置为可写入，其他人不可写入
```shell
touch {a.txt,b.txt}
chmod ug+w,o-w a.txt b.txt
```
2. 创建 c.txt 所有人都可写和执行
```shell
touch c.txt
chmod a=wx c.txt
```
3. 将 hadoop 目录下的所有文件设置为所有人可读
```shell
sudo chmod -R a+r /hadoop
```
4. 将/hadoop 目录下的所有文件与子目录的拥有者设为 root，用户拥有组为 users
```shell
sudo chown -R root:users /hadoop
```
5. 将当前目录下的所有文件与子目录的用户皆设为 hadoop，组设为 users
```shell
chown -R hadoop:users *
```
### 查找
1. 查找可执行的命令
```shell
which ls
```
2. 查找可执行命令的位置
```shell
whereis ls
```
3. 查找 a.txt 文件
```shell
locate a.txt
```
4. 从当前目录下开始查找 a.txt 文件
```shell
find . -name "a.txt"
```
5. 从当前目录下查找 a.txt 文件，并删除
```shell
find . -name "a.txt" -exec rm {} \;
```
6. 查找用户为 yhk 的文件
```shell
find -user yhk
```
7. 查找用户为 yhk，拥有组为 root 的文件
```shell
find -user yhk -a -group root 
```
8. 查找用户为 yhk 或者 (-o)拥有组为 root 并且是文件夹类型的文件
```shell
find -user yhk -o group root -a -type d
```
9. 查找权限为 777 的文件
```shell
find perm 777
```
10. 显示命令历史
```shell
history
```
### 打包与压缩
1. 用 gzip 将 b.txt 压缩
```shell
gzip b.txt
```
2. 将 b.txt 解压
```shell
gunzip b.txt
gzip -d b.txt
```
3. 用 bzip 2 将 b.txt 压缩
```shell
bzip2 b.txt
```
4. 将 b.txt 解压
```shell
bzip2 -d b.txt.bz2
```
5. 将当前目录下的文件打包
```shell
tar -cvf bak.tar .
```
6. 将/etc/passwd 追加到 bak. Tar 中
```shell
tar -rvf bak.tar /etc/passwd
```
7. 将 bak. Tar 解档
```shell
tar -xvf bak.tar
```
8. 将 vmvare 目录打包并压缩
```shell
tar -zcvf vmvare.tar.gz vmvare
```
9. 将 vmvare. Tar. Gz 解压到/usr 下
```shell
sudo tar -zxvf vmvare.tar.gz -C /usr
```
10. 查看压缩包内容
```shell
tar -ztvf vmvare.tar.gz
```
### 输入输出重定向
1. 将错误信息存入 error. Txt
```shell
find /etc -name qwe.txt 2> error.txt
```
2. 将正确信息和错误信息都存入 log. Txt 文件中
```shell
find /etc -name "passwd" &> /tmp/log.txt
```
3. 查看/etc 下的目录有多少个
```shell
ls -l | grep "^d" | wc -l
```
4. 查看/etc 下的目录有多少个，并将文件详情输入到 result. Txt 中
```shell
ls -l /etc | grep "^d" | tee result.txt | wc -l
```
### 进程控制
1. 查看用户最近登录情况
```shell
lastlog
```
2. 查看硬盘使用情况
```shell
df -h
```
3. 查看文件大小
```shell
du -h filename
du -ah dir
```
4. 查看内存使用情况
```shell
free -h
```
5. 查看日志
```shell
ls /var/log
```
6. 实时查看进程运行情况
```shell
top
```
### 设置或显示环境变量：export
在 shell 中执行程序时，shell 会提供一组环境变量。export 可新增，修改或删除环境变量，供后续执行的程序使用。
<mark style="background: #ABF7F7A6;">export 的效力仅限于该次登陆操作</mark>
操作：`export [-fnp][变量名称]=[变量设置值]`
### 批量添加用户
#### 与用户账号有关的系统文件
完成用户管理的工作本质都是对有关的系统文件进行修改，这些系统文件包括<mark style="background: #FF5582A6;">/etc/passwd, /etc/shadow, /etc/group</mark>等
- **etc/passwd 记录的基本属性**
```bash
＃ cat /etc/passwd
root:x:0:0:Superuser:/:
daemon:x:1:1:System daemons:/etc:
bin:x:2:2:Owner of system commands:/bin:
sys:x:3:3:Owner of system files:/usr/sys:
adm:x:4:4:System accounting:/usr/adm:
uucp:x:5:5:UUCP administrator:/usr/lib/uucp:
auth:x:7:21:Authentication administrator:/tcb/files/auth:
cron:x:9:16:Cron daemon:/usr/spool/cron:
listen:x:37:4:Network daemon:/usr/net/nls:
lp:x:71:18:Printer administrator:/usr/spool/lp:
sam:x:200:50:Sam san:/home/sam:/bin/sh
```
- /etc/passwd 中一行记录对应着一个用户，每行记录又被冒号 (:)分隔为 7 个字段，其格式和具体含义如下：
- `用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell`
	- 用户名：通常长度不超过 8 个字符，由大小写字母和/或数字组成，不能有冒号 (:)。登录名中不能有冒号 (:)，因为冒号在这里是分隔符。为了兼容起见，登录名中最好不要包含点字符 (.)，并且不使用连字符 (-)和加号 (+)打头
	- 口令：本身存放用户口令的加密串，但现在许多 Linux 系统都使用了 shadow 技术，把真正的加密后的用户口令字存放到/etc/shadow文件中，而/etc/passwd文件的口令字段中只存放一个特殊的字符，例如“x”或者“*”
	- 用户标识符：是一个整数，系统内部用它来标识用户。一般情况下它与用户名是一一对应的，如果几个用户名对应的用户标识号是一样的，系统内部将把它们视为同一个用户，但是它们可以有不同的口令、不同的主目录以及不同的登录 Shell 等。通常用户标识号的取值范围是 0～65 535。0 是超级用户 root 的标识号，1～99 由系统保留，作为管理账号，普通用户的标识号从100开始
	- 组标识符：记录用户所属的用户组，对应着/etc/group 文件中的一条记录
	- 注释性描述：一段任意编写的注释，创建账户时可以通过 useradd -c 用户名的 -c 参数指定
	- 主目录：用户登录之后所处的目录
	- 登录 shell: 用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即 Shell. <mark style="background: #FF5582A6;">Shell 是用户与 Linux 系统之间的接口</mark>。Linux 的 Shell 有许多种，每种都有不同的特点。常用的有 sh (Bourne Shell), csh (C Shell), ksh (Korn Shell), tcsh (TENEX/TOPS-20 type C Shell), bash (Bourne Again Shell)等, 可以通过 usermod 的 -s 参数为用户指定某个 Shell。如果 useradd 不通过 -s 参数指定 shell，那么系统使用 bash 为默认的登录 Shell，即这个字段的值为/bin/bash

 - **伪用户**：这些用户的登陆 shell 为/usr/sbin/nologin，即不能登录。它们的存在主要是方便系统管理，满足相应的系统进程对文件属主的要求
```shell
bin 拥有可执行的用户命令文件
sys 拥有系统文件
adm 拥有帐户文件
uucp UUCP使用
lp lp或lpd子系统使用
nobody NFS使用
```

- **etc/shadow**
- 对安全性要求较高的 Linux 系统都把/etc/passwd 文件中的<mark style="background: #FF5582A6;">口令字段</mark>保存在/etc/shadow 文件中，超级用户才拥有该文件读权限
- /etc/shadow 中的记录行与/etc/passwd 中的一一对应，它由 pwconv 命令根据/etc/passwd 中的数据自动产生
- `登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志`
	- 登录名：与/etc/passwd 文件中的登录名相一致的用户账号
	- 加密口令："口令"字段存放的是加密后的用户口令字，长度为 13 个字符。如果为空，则对应用户没有口令，登录时不需要口令；如果含有不属于集合 { ./0-9 A-Za-z }中的字符，则对应的用户不能登录
	- 最后一次修改时间："最后一次修改时间"表示的是从某个时刻起，<mark style="background: #FF5582A6;">到用户最后一次修改口令时的天数</mark>。大部分 linux 系统的时间起点是 1970 年 1 月 1日
	- 最小时间间隔：两次修改口令之间所需的最小天数
	- 最大时间间隔：口令保持有效的最大天数
	- 警告时间：表示的是从系统开始警告用户到用户密码正式失效之间的天数
	- 不活动时间：是用户没有登录活动但账号仍能保持有效的最大天数
	- 失效时间：字段给出的是一个绝对的天数，如果使用了这个字段，那么就给出相应账号的生存期。期满后，该账号就不再是一个合法的账号，也就不能再用来登录了

- **etc/group 记录用户组信息**
- 每个用户都属于某个用户组；一个组中可以有多个用户，一个用户也可以属于不同的组
- 当一个用户同时是多个组中的成员时，在/etc/passwd 文件中记录的是用户所属的<mark style="background: #FF5582A6;">主组</mark>，也就是<mark style="background: #FF5582A6;">登录时所属的默认组</mark>，而其他组称为<mark style="background: #FF5582A6;">附加组</mark>
- 用户组的所有信息都存放在/etc/group 文件中，字段有：
- `组名:口令:组标识号:组内用户列表`
	- 组名："组名"是用户组的名称，由字母或数字构成。与/etc/passwd 中的登录名一样，组名不应重复
	- 口令：存放的是用户组加密后的口令字。一般 Linux 系统的用户组都没有口令，即这个字段一般为空，或者是*。
	- 组标识符：与用户标识号类似，也是一个整数，被系统内部用来标识组
#### 实操
**创建一个用户文本文件 user. Txt，格式要和 etc/passwd 一致**
```
user001::601:100:user:/home/user001:/bin/bash
user002::602:100:user:/home/user002:/bin/bash
user003::603:100:user:/home/user003:/bin/bash
user004::604:100:user:/home/user004:/bin/bash
user005::605:100:user:/home/user005:/bin/bash
user006::606:100:user:/home/user006:/bin/bash
```
**将 user. Txt 的用户信息存入 /etc/passwd 中**
```shell
sudo newusers < user.txt
```
**取消 shodow passwd 功能**
将 /etc/shadow 产生的 shadow 密码解码，然后回写到 /etc/passwd 中，并将 /etc/shadow 的 shadow 密码栏删掉。这是为了方便下一步的密码转换工作，即先取消 shadow password 功能
```shell
sudo pwunconv
```
**创建一个 passwd. Txt 文件保存用户和密码**
```shell
user001:123456
user002:123456
user003:123456
user004:123456
user005:123456
user006:123456
```
**执行 /usr/sbin/chpasswd 命令**
执行 chpasswd 命令会将对应的用户密码写入/etc/passwd 中, 并且是以加密的方式存入
```shell
chpasswd < passwd.txt
```
**将密码存入/etc/shadow 中**
```shell
pwconv
```
### Crontab 的使用
crontab 命令是 cron table 的简写，它是 cron 的配置文件，也可以叫它作业列表。
**相关配置文件：**
- /var/spool/cron/ 目录下存放的是每个用户包括 root 的 crontab 任务，每个任务以创建者的名字命名
- /etc/crontab 这个文件负责调度各种管理和维护任务
- /etc/cron. D/ 这个目录用来存放任何要执行的 crontab 文件或脚本
- 还可以把脚本放在/etc/cron. Hourly、/etc/cron. Daily、/etc/cron. Weekly、/etc/cron. Monthly 目录中，让它每小时/天/星期、月执行一次
**命令格式：**
```shell
crontab [ -u user ] { -l | -r | -e }
crontab [ -u user ] { -l | -r | -e }
//省略用户表表示操作当前用户的crontab
-e (编辑工作表)
-l (列出工作表里的命令)
-r (删除工作表)
```
`crontab -e` 进入当前用户的工作表编辑，是常见的 vim 界面。每行是一条命令
crontab 的命令构成为时间+动作，其时间有<mark style="background: #FF5582A6;">分、时、日、月、周</mark>五种，操作符有
- * 取值范围内的所有数字
- / 每过多少个数字
- - 从 X 到Z
- ，散列数字
**基本格式**
```shell
f1 f2 f3 f4 f5 command
分 时 日 月 周 命令
第1列表示分钟0～59 每分钟用*或者 */1表示
第2列表示小时0～23（0表示0点）
第3列表示日期1～31
第4列表示月份1～12
第5列标识号星期0～6（0表示星期天）
第6列要运行的命令
```
- 其中 f 1 是表示分钟，f 2 表示小时，f 3 表示一个月份中的第几日，f 4 表示月份，f 5 表示一个星期中的第几天。Command 表示要执行的命令。
- 当 f 1 为 * 时表示每分钟都要执行 program，f 2 为 * 时表示每小时都要执行程序，以此类推
- 当 f 1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f 2 为 a-b 时表示从第 a 到第 b 小时都要执行，以此类推
- 当 f 1 为 \*/n 时表示每 n 分钟个时间间隔执行一次，f 2 为 \*/n 表示每 n 小时个时间间隔执行一次，以此类推
- 当 f 1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f 2 为 a, b, c,... 时表示第 a, b, c... 个小时要执行，以此类推

<mark style="background: #FFB86CA6;">案例：在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup</mark>
```shell
0 6-12/3 * 12 * /usr/bin/backup
```
<mark style="background: #FFB86CA6;">周一到周五每天下午 5:00 寄一封信给 alex@domain.name </mark>
```shell
0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata
```
<mark style="background: #FFB86CA6;">每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 echo "haha"</mark>
```shell
20 0,2,4 * * * echo "haha"
```
<mark style="background: #FFB86CA6;">每月1、10、22日的4 : 45重启 smb</mark>
```shell
45 4 1,10,22 * * /etc/init.d/smb restart
```
<mark style="background: #FFB86CA6;">每周六、周日的1 : 10重启 smb</mark>
```shell
10 1 * * 0,6 /etc/init.d/smb restart
```
<mark style="background: #FFB86CA6;">每天18 : 00至23 : 00之间每隔30分钟重启 smb</mark>
```shell
*/30 18-23 * * * /etc/init.d/smb restart
```
<mark style="background: #FFB86CA6;">每星期六的晚上11 : 00 pm 重启 smb</mark>
```shell
0 23 * * 6 /etc/init.d/smb restart
```
<mark style="background: #FFB86CA6;">每一小时重启 smb</mark>
```shell
0 */1 * * * /etc/init.d/smb restart
```
<mark style="background: #FFB86CA6;">晚上11点到早上7点之间，每隔一小时重启 smb</mark>
```shell
0 */1 23-7/1 * * /etc/init.d/smb restart
```

**环境变量问题**
有时创建了一个 crontab，但是这个任务却无法自动执行，而手动执行这个任务却没有问题，这种情况一般是由于在 crontab 文件中没有配置环境变量引起的
所以注意如下3点：
- 脚本中涉及文件路径时写全局路径
- 脚本执行要用到 java 或其他环境变量时，通过 source 命令引入环境变量，如：
```shell
cat start_cbp.sh
#!/bin/sh
source /etc/profile
export RUN_CONF=/home/d139/conf/platform/cbp/cbp_jboss.conf
/usr/local/jboss-4.0.5/bin/run.sh -c mev &
```
- 当手动执行脚本 OK，但是 crontab 死活不执行时，可以尝试在 crontab 中直接引入环境变量解决问题。如：
```shell
0 * * * * . /etc/profile;/bin/sh /var/www/java/audit_no_count/bin/restart_audit.sh
```
### 三种特殊权限
#### Suid
**功能：临时使用命令的属主权限执行该命令**，即如果文件有 suid 权限时，那么普通用户去执行该文件时，会以该文件的所属用户的身份去执行
SetUID（简写 suid）：<mark style="background: #FF5582A6;">会在属主权限位的执行权限上写个 s</mark>。如果该属主权限位上有执行权限，则会在属主权限位的执行权限上写个 s（小写）；如果该属主权限位上没有执行权限，则会在属主权限位的执行权限上写个 S（大写）
<mark style="background: #ABF7F7A6;">suid 数字权限是4000</mark>,设置方法：
```shell
方式1：
[root@centos7 ~]# chmod u+s filename
方式2：
[root@centos7 ~]# chmod 4755 filename
```
<mark style="background: #ABF7F7A6;">查看 passwd 命令的权限：</mark>
```shell
`[root@localhost ftl]``# ll /usr/bin/passwd ` `问题： ``passwd``文件的属组是root,表示只有
root用户可以访问的文件，为什么普通用户依然可以使用该命令更改自己的密码？``答案：当普通用户[omd]使用
``passwd``命令的时候，系统看到``passwd``命令文件的属性有大写s后，表示这个命令的属主权限被omd用户获得,
也就是omd用户获得文件``/etc/shadow``的root的rwx权限
```
由于 passwd 具有 s 权限，普通用户使用该命令的时候，就会以该命令的属主身份 root 执行该命令，于是能够顺利修改普通用户不具备修改权限的 /etc/shadow 文件
**SetUID（简称 suid）总结：**
- 让普通用户对可执行的二进制文件，临时拥有二进制文件的属主权限
- 如果设置的二进制文件没有执行权限，那么 suid 的权限显示就是 S（大写字母 S）
- 特殊权限 suid 仅对二进制可执行程序有效，其他文件或目录则无效
- Suid 极其危险，如果给 vim 或者 rm 命令设置了 setUID，那么任何文件都能编辑或者删除了，相当于有 root 权限了
#### Sgid
**命令功能**：使用 sgid 可以使得多个用户之间共享一个目录的所有文件变得简单。<mark style="background: #FF5582A6;">当某个目录设置了 sgid 后，在该目录中新建的文件不在是创建该文件的默认所属组</mark>
如果该属组权限位上有执行权限，则会在属组主权限位的执行权限上写个 s（小写字母）；如果该属组权限位上没有执行权限，则会在属组主权限位的执行权限上写个 S（大写字母 S）
<mark style="background: #ABF7F7A6;">sgid 数字权限是2000，设置方法：</mark>
```shell
方式1：
[root@VM_0_9_centos ~]# chmod 2755 test/
方式2：
[root@VM_0_9_centos ~]# chmod g+s test/
[root@VM_0_9_centos ~]# ll -d test/
drwxr-sr-x 2 root root 4096 Nov 22 21:02 test/
```
#### Sbit 沾滞位
**命令功能**：粘滞位，只对目录有效，对某目录设置粘滞位后，<mark style="background: #FF5582A6;">普通用户就算有 w 权限也只能删除该目录下自己建立的文件，而不能删除其他用户建立的文件</mark>
如果该其他用户权限位上有执行权限，则会在其他用户权限位的执行权限上写个 t（小写）；如果该其它用户权限位上没有执行权限，则会在其他用户权限位的执行权限上写个 T（大写）
<mark style="background: #ABF7F7A6;">sbit数字权限是1000，设置方法：</mark>
```shell
方法1：
[root@VM_0_9_centos ~]# chmod 1755 test/
方法2：
[root@VM_0_9_centos ~]# chmod o+t test/
查看权限：
[root@VM_0_9_centos ~]# ll -d test/
drwxr-xr-t 2 root root 4096 Nov 22 21:15 test/
```
### 掩码 umask
<mark style="background: #FF5582A6;">umask 值用于设置用户在创建文件时的默认权限</mark>，当我们在系统中创建目录或文件时，目录或文件所具有的默认权限就是由 umask 值决定的
对于 root 用户，系统默认的 umask 值是0022；对于普通用户，系统默认的 umask 值是0002。执行 umask 命令可以查看当前用户的 umask 值

> [!NOTE] umask 改变新建文件的访问权限
> Umask 值一共有 4 组数字，其中第 1 组数字用于定义特殊权限，一般不予考虑，与一般权限有关的是后 3 组数字。
> 默认情况下，<mark style="background: #FFB8EBA6;">对于目录，用户所能拥有的最大权限是 777</mark>；<mark style="background: #FFB8EBA6;">对于文件，用户所能拥有的最大权限是目录的最大权限去掉执行权限，即 666</mark>。因为 x 执行权限对于目录是必须的，没有执行权限就无法进入目录，而对于文件则不必默认赋予 x 执行权限
> 对于 root 用户，他的 umask 值是 022。当 root 用户创建目录时，默认的权限就是用最大权限 777 去掉相应位置的 umask 值权限，即对于所有者不必去掉任何权限，对于所属组要去掉 w 权限，对于其他用户也要去掉 w 权限，所以目录的默认权限就是 755；当 root 用户创建文件时，默认的权限则是用最大权限 666 去掉相应位置的 umask 值，即文件的默认权限是644

>umask 命令只能临时修改 umask 值，系统重启之后 umask 将还原成默认值。如果要永久修改 umask 值，可修改 <mark style="background: #FF5582A6;">/etc/bashrc</mark> 或 <mark style="background: #FF5582A6;">/etc/profile</mark> 文件
>例如要将默认 umask 值设置为 027，那么可以在文件中增加一行 umask 027 


