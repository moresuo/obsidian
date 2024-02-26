***
## 概述-知识测试 
1. Linux 发行版主要由 <mark style="background: #FF5582A6;">linux 内核</mark>和实用程序组成
2. 因 Linux 发行版操作系统中除了内核外，其他程序都是 GNU 软件，所以理查德·斯托曼认为 Linux 发行版应该称为 GNU/Linux------<mark style="background: #FF5582A6;">对</mark>
3. Copyleft 是 GNU GPL 有别于其他自由软件许可证的主要特征，它的主要作用是（<mark style="background: #FF5582A6;">要求发布自由软件的衍生版本时，必须也是自由的</mark> ）
4. 下列不被定义为 linux 操作系统的是（<mark style="background: #FF5582A6;">A</mark>）
- A.ios
- B.ubuntu
- C.Android
- D.CentOs
5. Ubuntu 22.04 LTS 的版本代号是（<mark style="background: #FF5582A6;">Jammy Jellyfish</mark>）
6. 下列系统中，默认使用 KDE 桌面环境的是（<mark style="background: #FF5582A6;">A</mark>）
- A.Kubuntu
- B. Ubuntu Kylin
- C.Ubuntu MATE
- D. Lubuntu
7.  Ubuntu Desktop 默认选用的桌面环境是 ( <mark style="background: #FF5582A6;">GNOME</mark> )
8. Linux-6.1.53 是预发布的内核测试版本-----<mark style="background: #FF5582A6;">错</mark>
9. 下列不是衍生自 Debian 的系统是（<mark style="background: #FF5582A6;">D</mark>）
- A.Ubuntu
- B. Linux Mint
- C.deepin
- D.openSUSE
10.  中国首个桌面操作系统是（<mark style="background: #FF5582A6;">openKylin</mark>）
## 目录与文件管理
1. 下列通配符中,匹配零个或多个字符的是 (<mark style="background: #FF5582A6;">D</mark>)
- A.?
- B.-
- C.@
- D.*
2. 下列采用绝对路径表示目录或文件位置的是 ( <mark style="background: #FF5582A6;">B</mark>  )
- A. ./var/log. D/dmsg. Log
- B. /var/log. D/dmsg. Log
- C. var/log. D/dmsg. Log
- D. …/var/log. D/dmsg. Log
3. 使用 tar 命令创建 xz 压缩格式归档文件的选项是(<mark style="background: #FF5582A6;">B</mark>)
- A. -a
- B. -J
- C. -j
- D. -z
4. 执行命令 mkdir  one/two/three 后, 返回提示信息“No such file or directory”。下列哪项操作可以解决这个错误 (   <mark style="background: #FF5582A6;">在 mkdir 命令后添加选项-p</mark> )
5. 以长格式显示目录 dir 001. D 内容 (包括隐藏文件)的命令是 (<mark style="background: #FF5582A6;">ls -la dir 001. D</mark>)
6. 在 Linux 系统中,修改文件名称会改变文件的 ( <mark style="background: #FFB8EBA6;">变更时间（change time）</mark>)
7. 关于符号链接,下列表述正确的是 ( )
- A. 删除符号链接不会删除被链接的文件
- B. 创建为符号链接的文件和被链接的文件大小是一样的
- C. 并能在两个不同的文件系统之间创建符号链接
- D. 不能创建目录的符号链接
8. 执行命令 cp -R  MyFile/  /media/usb 的结果是 (  <mark style="background: #FF5582A6;">把当前目录的子目录 MyFile 及其内容复制到目录/media/usb 中 </mark> )
9.  把文件 newfile. Txt 重命名为 afile. Txt 的命令是 ( <mark style="background: #FF5582A6;">mv  newfile. Txt  afile. Txt</mark>  )
10. 关于硬链接,下列表述错误的是 ( <mark style="background: #FF5582A6;">D</mark>)
- A. 不能在不同文件系统间创建硬链接
- B. 不能创建目录的硬链接
- C. 创建为硬链接的文件和被链接的文件是同一个文件
- D. 删除硬链接的同时, 被链接的文件也会被删除
11. 如果工作目录是一个三级目录, 则将工作目录转换为一级目录 tmp 的一级子目录 mine,可以使用的命令是 ( <mark style="background: #FF5582A6;">cd  /tmp/mine</mark>)
12. 为了总是以其所有者身份运行某个程序文件，需要置位该文件的权限位是 <mark style="background: #FF5582A6;">suid</mark>
13. 在 Linux FHS 中，用户账户 tom 主目录中的一级子目录 ilta 的绝对路径是<mark style="background: #FF5582A6;">/home/tom/ilta</mark>
14. 如果某个普通文件的访问模式是 0644, 则执行命令 chmod +x, o+t file. Txt 后 ,文件 file. Txt 的权限是<mark style="background: #FF5582A6;">rwxr-xr-t;-rwxr-xr-t</mark>
15. 执行命令“umask 022”后，新建目录的访问权限是 <mark style="background: #FF5582A6;">rwx-r-xr-x</mark>
16. 使用命令 mv 重命名文件名称时，只是修改了该文件的符号名称，其索引节点编号并不发生变化----<mark style="background: #FF5582A6;">对</mark>
17. 置位某个目录文件的 sgid 位，会使在该目录中创建的所有文件将与该目录的所属组一致----<mark style="background: #FF5582A6;">对</mark>
18. 使用相对路径创建的链接文件，移动或复制到文件层级结构的其他位置时，链接会失效----<mark style="background: #FF5582A6;">对</mark>
19. 命令 rmdir 不能删除非空目录----<mark style="background: #FF5582A6;">对</mark>
20. 为某个文件创建符号链接，该文件的链接数不会发生变化----<mark style="background: #FF5582A6;">对</mark>
## 文本编辑
### Nano 快捷键
- 显示行编号：`alt+n`
- 搜索字符串：`ctrl+w`
- 标记文本：`ctrl+a`
- 复制文本：`alt+6`
- 定位光标至文件末尾：`ctrl+/`
- 粘贴文本：`ctrl+u`
- 保存文件副本：`ctrl+o`
### Vim 操作
- 显示行编号：`:set nu`
- 忽略大小写：`:set ignorecase`
- 搜索字符串：`/word`
- 进入可视模式：`v`
- 复制可视模式选择的文本：`y`
- 定位光标至文件末行：`G`
- 粘贴文件：`p`
- 保存文件副本：`:w`
- 放弃所有操作，退出 vim 编辑：`:q!`
## Sed 和 awk
### Sed 操作
**有示例文本如下：**
```shell
~$cat students.csv
2023101102101,John,Doe,male,FALSE,73,81,93  
2023101102102,Jason,Smith,female,FALSE,90,86,96
2023101102103,Raj,Reddy,female,TRUE,81,97,95
2023101102104,Anand,Ram,male,FALSE,71,74,88
2023101102105,Jane,Miller,male,TRUE,84,77,65
```
1. 执行命令 `$ sed  -n '2p' students.csv` 的输出结果是: <mark style="background: #FF5582A6;">2023101102102, Jason, Smith, female, FALSE, 90,86,96</mark>(显示第二行)
2. 执行命令 `$ sed  -n '1,3p' students.csv` 的输出结果是：
<mark style="background: #FF5582A6;">2023101102101, John, Doe, male, FALSE, 73,81,93  
2023101102102, Jason, Smith, female, FALSE, 90,86,96
2023101102103,Raj,Reddy,female,TRUE,81,97,95</mark>（显示 1 到 3 行）
3. 执行命令 `$ sed  -n '/TRUE/p' students.csv` 的输出结果是：<mark style="background: #FFB8EBA6;">2023101102103, Raj, Reddy, female, TRUE, 81,97,95 2023101102105, Jane, Miller, male, TRUE, 84,77,65</mark>（显示包含 TRUE 的行）
4. 执行命令 `$sed '/\bJ/s/\b[A-Z]/\L&/g' student.csv` 的输出结果是：
<mark style="background: #FF5582A6;">2023101102101, john, doe, male, fALSE, 73,81,93  
2023101102102, jason, smith, female, fALSE, 90,86,96
2023101102103,Raj,Reddy,female,TRUE,81,97,95
2023101102104,Anand,Ram,male,FALSE,71,74,88
2023101102105,jane,miller,male,tRUE,84,77,65</mark>
5. 如果输出结果是“2023101102104,anand,ram,male,false,71,74,88”,执行的命令是：`$ sed '4s/[A-Z]/\L$/g' student.csv`
### Awk 操作
1.  执行命令 `$ awk -F, '{ t=($6+$7+$8)/3;if(t>=80&&t<90) print $2,"\047s test scores is:",t}' students.csv` 的输出是：<mark style="background: #FF5582A6;">John 's test scores is: 82.3333</mark>
2. 要求输出结果为下列内容的命令是：`$ awk -F, '{print $2,$3,"\t",$5}' student.csv`
```
John Doe         FALSE
Jason Smith        FALSE
Raj Reddy            TRUE
Anand Ram        FALSE
Jane Miller          TRUE
```
3. 要求输出结果为下列内容的命令是: `$ awk -F, '$2 ~ /^[AR]/ {print $2,$3}' student.csv`
```
Raj Reddy  
Anand Ram
```
4. 要求输出第二行的部分内容（要求指定行号）的命令是: `$ awk -F, 'NR==2{print $2,$3,"\t"$1}' student.csv`
```
Jason Smith       2023101102102
```
5. 要求修改 Jason Smith 的性别，将 female 修改为 male，执行的命令是: `$ awk -F, '{$4="male"}'`
## 存储设备管理
1. 创建两个镜像文件 leecz 1. Img 和 leecz. Img，使用你的姓名缩写替换文件名中的“leecz”，大小分别设置为 2 GiB 和 3 GiB，借用两个文件创建两个块回环设备
```shell
dd if=/dev/zero of=yhk.img bs=1M count=2048
dd if=/dev/zero of=yhk1.img bs=1M count=3072
```
![[Pasted image 20231230121912.png]]
```shell
sudo losetup /dev/loop14 yhk.img
sudo losetup /dev/loop15 yhk1.img
```
2. 分别使用 fdisk 和 partec 命令在上题创建的回环设备上创建 1 个主分区，一个扩展分区和一个逻辑分区，大小均为 1 GB，并设置逻辑分区的文件系统类型为交换分区
![[Pasted image 20231230123237.png]]
![[Pasted image 20231230123623.png]]
![[Pasted image 20231230123724.png]]
![[Pasted image 20231230123913.png]]
![[Pasted image 20231230124030.png]]
![[Pasted image 20231230125219.png]]
![[Pasted image 20231230125349.png]]
![[Pasted image 20231230125755.png]]

3. 使用文件系统 ext 4 格式化主分区，并设置其卷标为你的学号，给出执行命令 blkid 的查询结果
```shell
sudo mkfs.ext4 /dev/loop15p1
sudo e2label /dev/loop15p1 202106661615
```
4. 挂载主分区至目录/mnt/leecz（使用你的姓名缩写替换目录名中的“leecz”），给出命令 df 的执行结果中，挂载该分区的信息。
```shell
sudo mount /dev/loop15p1 /mnt/yhk
```
5. 卸载上述步骤中挂载的主分区
```shell
sudo unmount /dev/loop15p1
```
6. 将逻辑分区添加至交换空间，给出命令 swapon --show 的查询结果
```shell
sudo mkswap /dev/loop15p5
swapon --show
```
7. 取消使用逻辑分区添加的交换空间
```shell
sudo swapoff /dev/loop15p5
```
8. 将挂载主分区和逻辑分区的配置信息添加到文件 fstab 中，执行 mount 命令挂载它们。
```shell
sudo vim /etc/fstab
```
![[Pasted image 20231230140028.png]]
```shell
sudo mount /dev/loop15p1 /mnt/yhk
sudo mount /dev/loop15p5 /mnt/yhk

sudo mount -a
```
## 用户管理
1.  新建用户（账户名称为你的姓名缩写），设置 UID 为你学号的后四位，GID 与 UID 相同，GECOS 字段内容为你完整的 12 位数字学号，shell 设置为/bin/bash；
```shell
sudo useradd -u 1615 -c "202106661615" -s /bin/bash moresuo
sudo groupmod -g 1615 moresuo
```
2. 新建组账户 datamine，将新建用户账户添加为组成员
```shell
sudo groupadd datamine
sudo gpasswd -a moresuo datamine
```
3.  分别以与账户同名的组和 datamine 组成员身份各创建一个文件
```shell
su moresuo
touch new.txt
```
4. 将新建用户添加到 sudo 组中，然后执行命令“sudo id”
```shell
sudo gpasswd -a moresuo sudo
```
5. 从 sudo 组中去除新建账户
```shell
sudo gpasswd -d moresuo sudo
```
6. 编辑 sudoers 文件，允许新建用户以 root 身份执行 id 命令
```shell
sudo vim /etc/sudoers

#moreuso    ALL=(root) /bin/id
```
7.  在上述新建用户账户名称前增加前缀字符 cu，即将名称 leecz 更改为 culeecz
```shell
sudo usermod -l cumoresuo moresuo
```
8. 将新建用户账户默认设置的命令解释程序变更为/bin/bash
```shell
sudo usermod -s /bin/bash cumoresuo
```
9. 重置新建用户密码，并设置要求其下次登录时修改密码
```shell
sudo passwd -e cumoresuo
sudo chage -d 0 cumoresuo
```
10. 删除新建的用户和组
```shell
sudo userdel cumoresuo
sudo groupdel moresuo
sudo groupdel datamine
```
## 系统管理-多用户
1. 下列用于用户账户身份验证的文件是（D）
- A. /etc/pam.d
- B. /etc/id. Conf
- C. /etc/passwd
- <mark style="background: #FF5582A6;">D. /etc/shadow</mark>
2. 下列与新建用户无关的文件和目录是（ ）
- A. /etc/login. Defs
- B. /etc/default/useradd
- <mark style="background: #FF5582A6;">C. /etc/sudoers</mark>
- D. /etc/skel
3. 下列描述，表述错误的是（）
- A. root 账户的主目录是一级目录，不是/home 的子目录
- B. 管理员账户是普通用户账户
- C. 和普通用户比较，系统用户没有主目录
- <mark style="background: #FF5582A6;">D. 和系统用户比较，普通用户无法登录系统</mark>
4. 一个用户账号可以从属于多个组账户，但只能有一个主组----<mark style="background: #FF5582A6;">对</mark>
5. 当用户以组成员身份访问系统资源时，其对资源的访问权限受限于该用户主组对资源的访问限制----<mark style="background: #FF5582A6;">错</mark>
6. 执行命令 useradd 新建普通用户账户 tom，要求指定命令解释程序为/bin/bash：`sudo useradd -s /bin/bash tom`
7. 执行命令设置用户账户 tom 的登录密码，并要求该用户下次登录时修改密码: `passwd -e tom`
8. Ubuntu 22.04 的系统默认设置中，普通用户账户的默认命令解释程序是:<mark style="background: #FF5582A6;">/bin/sh</mark>
9. 在文件/etc/sudoers 中配置允许用户 jerrey 以组账户 tom 的身份在所有终端设备上执行命令/bin/poweroff
```
jerry    ALL=(:tom)   /bin/poweroff
```
10. 在 Ubuntu22.04的默认设置中，命令提示符为“#”的账户是:<mark style="background: #FF5582A6;">根用户root</mark>
## 系统管理-块设备
1. 下列需要占用块设备空间的文件系统是（ ）
- A. procfs
- <mark style="background: #FF5582A6;">B. ext4  </mark>
- C. tmpfs  
- D. sysfs
2. 把文件a.img 创建为包含多分区信息的回环设备/dev/loop 5 的命令是：`sudo losetup -P /dev/loop5 a.img`
3. 下列可以查询保存指定文件或目录的块设备名称的命令是（ ）
- A. file
- <mark style="background: #FF5582A6;">B. df</mark>
- C. du
- D. stat
4. 执行 fdisk 命令创建一个类型为 swap 的分区，下列执行序列正确的是（ ）
- A. l-t-n-p-w
- B. m-g-n-p-l-w
- C. m-g-p-t-l-w
- <mark style="background: #FF5582A6;">D. n-l-t-p-w</mark>
5. 执行 fdisk 命令创建 MBR 磁盘分区时，文件系统类型别名为 swap 的 Hex 代码是:<mark style="background: #FF5582A6;">82</mark>
6. 在 fstab 文件中，每个块设备的配置项有 6 个字段，其中第一个字段描述的是:<mark style="background: #FF5582A6;">块设备标识</mark>
7. 挂载 fstab 中所有文件系统的的命令是: `sudo mount -a`
8. 启用 fstab 中所有交换分区的命令是: `sudo swapon -a`
9. 创建用于交换空间的文件时，需要将该文件的访问权限设置为: `rw-------`
10. 下列无需和根文件系统使用同一磁盘卷的一级目录是（ ）
- A. /etc
- <mark style="background: #FF5582A6;">B. /usr</mark>
- C. /lib
- D. /bin
## 系统管理-任务管理
1. 下列不能在桌面环境中切换应用程序窗口的快捷键是（ ）
- A. Alt+Tab  
- B. Alt+Esc  
- C. <mark style="background: #FF5582A6;">Super+`</mark>
- D. Super+Tab
2. 在 shell 终端中，在后台恢复挂起的进程使用的命令是：<mark style="background: #FF5582A6;">bg</mark>
3. 执行一个不受终端退出（或关闭）影响的命令是（ ）  
- A. sleep 60
- B. sleep 60 &
- C. sudo sleep 60
- <mark style="background: #FF5582A6;">D. nuhup sleep 60 &</mark>
4. 下列命令中，可显示某进程执行的所有线程信息的命令是（ ）
- A. ps -j
- B. ps -g
- C. ps -f  
- <mark style="background: #FF5582A6;">D. ps -L</mark>
5. 在 Linux 系统中，可以通过命令名称唯一标识进程----<mark style="background: #FF5582A6;">错</mark>
6. 下列不能按命令名称降序显示进程的操作命令是（ ）
- <mark style="background: #FF5582A6;">A. ps -Fk -cmd</mark>
- B. ps auxk -cmd
- C. ps -f --sort=-cmd
- D. top -o -COMMAND
7. 以亲和数 5 执行程序 xeyes 的命令是: `nice -n 5 xeyes`
8. 以后台运行方式执行程序 xeyes 的命令是: `xeyes &`
9. 执行命令 xeyes & nohup xeyes & xeyes 后，执行快捷键 Ctrl+Z，未被挂起的执行命令 xeyes 的进程还有（ ）个
- A. 0
- B. 1
- <mark style="background: #FF5582A6;">C. 2</mark>
- D. 3
10. 进程对文件的访问权限，与执行命令的用户账户对该文件的访问权限一致----<mark style="background: #FF5582A6;">错</mark>
11. 执行 systemctl 命令，总是需要根用户权限----<mark style="background: #FF5582A6;">错</mark>
12. 以管理员身份执行 systemctl 管理服务单元文件时，重新加载修改的单元文件需要执行的命令是：`sudo systemctl daemon-reload`
13. 在系统中安装的 systemd 单元文件默认保存的目录是：`/usr/lib/systemd/system`
14. 以管理员身份执行 systemctl 管理系统服务时，查询服务单元状态的子命令是: <mark style="background: #FF5582A6;">status</mark>
15. 以管理员身份，执行 systemctl 临时切换启动目标单元为 multi-user. Target 的命令是: `sudo systemctl isolate multi-user. Target`
16. 添加服务单元至配置目录后，查看可用服务单元的命令是（ ）
- A. systemctl is-active 
- B. systemctl get-default
- <mark style="background: #FF5582A6;">C. systemctl list-unit-files </mark>
- D. systemctl list-units
17. 在 systemd 日志记录中查询服务 mine. Service 状态的命令是: `journalctl -u mine.service`
18. 系统引导程序启动后，首先执行的程序是( )
- A. systemd
- <mark style="background: #FF5582A6;">B. vmlinuz</mark>
- C. grub
- D. initrd
19. 在 Linux 系统中，一般把在后台运行的，没有控制终端的进程称为: <mark style="background: #FF5582A6;">守护进程</mark>
20. 下列选项中，不影响进程执行性能的指标是
- <mark style="background: #FF5582A6;">A. 磁盘空间使用率</mark>
- B. 内存空间使用率
- C. 网络I/O传输率
- D. CPU负载
- E. 数据块的 I/O 效率
## 软件包管理
***
MongoDB社区版mongodb-org的软件源信息如下：
数字签名文件的URL地址是：[https://pgp.mongodb.com/server-7.0.asc](https://pgp.mongodb.com/server-7.0.asc)
软件源是：deb https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse
按下列要求给出命令的执行序列。
[1]要求给出实用上述软件源安装MongoDB的命令序列
[2]启动Mongod，确认Mongod的执行状态，如果启动成功，再把Mongod添加至系统启动目标
[3] 显示 Mongod 服务单元的内容
```shell
# 导入 MongoDB 的数字签名
wget -qO - https://pgp.mongodb.com/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-archive-keyring.gpg

# 添加 MongoDB 软件源
echo "deb [signed-by=/usr/share/keyrings/mongodb-archive-keyring.gpg] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# 更新软件包列表
sudo apt-get update

# 安装 MongoDB
sudo apt-get install -y mongodb-org

```
```shell
# 启动 Mongod
sudo systemctl start mongod

# 确认 Mongod 执行状态
sudo systemctl status mongod

# （如果启动成功）将 Mongod 添加至系统启动目标
sudo systemctl enable mongod

```
```shell
# 显示 Mongod 服务单元的内容
sudo systemctl cat mongod
```

按下列要求执行命令并给出执行结果的截图
[1] 给出安装软件包sqlite-autoconf-3440200.tar.gz的命令序列 (安装目录为/opt/sqlite)
[2] 给出执行下列命令序列的步骤截图：
$ sqlite3 ex1  
SQLite version 3.36.0 2021-06-18 18:36:39
Enter ".help" for usage hints.
sqlite> create table stu(sno text, sname text);
sqlite> insert into tbl1 values('computer science and engineering','081200');
sqlite> insert into tbl1 values('Lees', '202312131205');  #此处要求将字符串的信息替换为你的个人信息
sqlite> select * from tbl1;
sqlite>.quit
[3] 给出安装并执行软件包DB_Browser_for_Sqlite的命令序列。下载地址如下：
https://download.sqlitebrowser.org/DB_Browser_for_SQLite-v3.12.2-x86_64.AppImage
```shell
# 1. 安装sqlite-autoconf-3440200.tar.gz
sudo curl -o sqlite-autoconf-3440200.tar.gz https://fossies.org/linux/misc/sqlite-autoconf-3440200.tar.gz
# 2. 解压
sudo tar -zxvf sqlite-autoconf-3440200.tar.gz
# 3. 配置安装目录
./configure --prefix=/opt/sqlite
# 4. 编译并安装
sudo make install
# 5.建表并添加数据
# 进入 SQLite 数据库 
sqlite3 ex1
# 6.下载DB_Browser_for_SQLite-v3.12.2-x86_64.AppImage
wget https://download.sqlitebrowser.org/DB_Browser_for_SQLite-v3.12.2-x86_64.AppImage
# 7. 赋予执行权限
chmod +x DB_Browser_for_SQLite-v3.12.2-x86_64.AppImage
# 8.运行
./DB_Browser_for_SQLite-v3.12.2-x86_64.AppImage
```

按下列要求完成操作，并给出执行的命令序列及执行结果
[1] 下载并安装软件包baidunetdisk_4.17.7_amd64.deb，在gnome桌面创建百度云盘的启动图标
下载地址： https://pan.baidu.com/
```shell
sudo curl -o baidunetdisk_4.17.7_amd64.deb https://issuepcdn.baidupcs.com/issue/netdisk/LinuxGuanjia/4.17.7/baidunetdisk_4.17.7_amd64.deb
sudo dpkg -i baidunetdisk_4.17.7_amd64.deb 
```
## 任务管理
1. 列出当前系统运行的所有关联控制终端的进程（非守护进程），要求可分页显示输出结果
```shell
ps -aux | less
```
2. 按执行的命令名称降序显示系统中执行的所有守护进程，要求可分页显示输出结果
```shell
ps -ajx --sort=-comm | less
```
3. 在仿真终端 pts/2 会话中创建一个子 shell 进程，并在该子 shell 进程中执行命令 nohup xeyes & xeyes & xeyes
```shell
nohup xeyes & xeyes & xeyes
```
4. 在仿真终端pts/0中，执行ps命令，显示在终端pts/2中执行的进程，要求显示下列字段信息：
     Pid、ppid、sid、tpgid、state、tty、nice、command
```shell
ps -e -t /dev/pts/1 -o pid,ppid,sid,tpgid,state,tty,nice,command | less
```
5. 执行 top 命令，显示步骤4要求的内容，按 PPID 字段排序
```shell
top
# f:显示指定字段
# shift+o:对PPID进行排序
```
6.  分别调整任意两个 xeyes 进程的亲和数为 5 和-5
```
暂时不知道
```
7. 说明在终端pts/0分别执行下列命令（不区分先后顺序）后，终端pts/2中各个进程的执行状态
      $ kill -sigterm    1001    #1001用子shell的PID代替  
      $ kill -sigint       1001             
      $ kill -sigstop   1001  
      $ killall xeyes
```
如果进程1001捕获了`SIGTERM`信号并正确处理了它，那么它可能会正常终止；如果进程1001没有捕获`SIGTERM`信号，它将被终止

`SIGINT`信号通常会导致进程立即终止

`SIGSTOP`信号将使进程暂停，直到接收到`SIGCONT`信号为止

`killall xeyes` 将试图终止所有名为 `xeyes` 的进程
```
## Shell 脚本
1. 编写脚本 valuepm. Sh，读取并输出变量 v 1 和 v2的值
```shell
#!/bin/bash

v1="这是v1"
v2="这是v2"
echo ${v1},${v2}
```
2. 编写脚本 pospm. Sh，输出脚本命令参数列表（Sun Mon Tue Wed）的值
```shell
#!/bin/bash

for i in $@;do
	echo $i
done
```
3. 编写脚本 spepm. Sh，输出特殊变量#、@、\*和 0 的值，测试参数列表为：one  two three four
```shell
#!/bin/bash

echo "参数个数为：$#"
echo "参数列表：$@"
echo "参数列表：$*"
echo "脚本名：$0"
```
4. 编写脚本 Althexpr，输出由标准输入获取的算术表达式的值
```shell
#!/bin/bash

echo "输入一个算术表达式"
read e
value=$(($e))
echo ${value}
```
5. 编写脚本 conexpr.sh，要求从标准输入一个字符串，并判断该字符串是否符合标识符定义规则。  
     符合输出 yes，不符合输出 No
```shell
#!/bin/bash

read -p "请输入一个字符串：" str
regex="^[a-z|A-Z|_][a-z|A-Z|0-9|_]*$"
if [[ $str =~ $regex ]];then
	echo "Yes"
else echo "No"
fi
```
6. 编写脚本 cycle. Sh，接收命令参数提供的两个数值 x 和 y，输出 x 至 y 之间所有的整数，要求每个数字之间包含不少于4个空格的分隔符
```shell
#!/bin/bash
start=$(($1+1))
while (( $start<$2 ));do
	echo -e "${start}\t\c"
	start=$(($start+1))
done
echo ""
```
7. 编写脚本 isexable. Sh，判断命令参数中指定的文件名，是否是可执行文件
```shell
#!/bin/bash

if [ -x $1 ];then
	echo "可执行文件"
else echo "不可执行文件"
fi
```
8. 编写脚本文件judge.sh，接收标准输入中提供的成绩列表所对应的等级，输出结果示例如下
       98    76    67    53   84  
        A      C      D     E     B
```shell
#!/bin/bash

read -a scores
level=()
index=0
for x in "${scores[@]}";do
	score=$(($x/10))
	case $score in
		9) level[$index]="A"
			;;
		8) level[$index]="B"
			;;
		7) level[$index]="C"
			;;
		6) level[$index]="D"
			;;
		0|1|2|3|4|5) level[$index]="E"
			;;
		*) level[$index]="F"
			;;
	esac
	
	index=$(($index+1))
done
echo "${level[@]}"
```
9. 编写脚本，列出命令参数指定的软件包中，包含的所有可执行文件的名称 (不含路径名)
```shell
#!/bin/bash
dpkg -L $1 | xargs -I {} file {} | grep "executable" | cut -d: -f1 | xargs -n 1 basename
```
10. 编写函数，判断命令参数指定的目录中，包含的普通文件的名称及数量（包括子目录中的文件）
```shell
#!/bin/bash


count_files(){
	files=($(find $1 -type f))
	for x in ${files[@]};do
		echo "$x"
	done
	echo "${#files[@]}"
}
count_files
```
### 期末测试
#### 选择
1. 执行命令 cp -a MyFile /media/usb/的结果是
- A.把二级目录/media/usb 中的所有文件合并保存到普通文件 Myfile
- B. 把二级目录/media/usb/中的所有文件及其子目录复制到当前目录的子目录 Myfile 中
- <mark style="background: #FF5582A6;">C.在二级目录/media/usb/中创建三级目录 MyFile，并将当前目录的子目录 Myfile 中的所有文件及其子目录复制到三级目录 Myfile 中</mark>。
- D.把当前目录的子目录 Myfile 中的所有文件及其子目录复制到二级目录/media/usb/
2. 创建文件系统挂载点的命令是（）
- A. mkfs. 
- <mark style="background: #FF5582A6;">B. Mkdir</mark>
- C. Mount
- D. Touch
3. 如果工作目录是一个三级目录则将工作目录转换至二级目录 tmp 的子目录 mine, 可以使用的命令是
- A. cd /tmp/mine
- <mark style="background: #FF5582A6;">B. Cd ../..//tmp/mine</mark>
- C. Cd ~/tmp/mine
- D. Cd tmp/mine
4. 下列不属于 Linux 发行版的操作系统是 ()
- A. Ubuntu
- B. OpenSUSE
- C. Fedora
- <mark style="background: #FF5582A6;">D. FreeBSD</mark>
5. 下列关于 GPT 分区的表述中, 错误的选项是 ()
- A.GPT 分区可支持不超过 128 个分区的磁盘
- B.相对于 MBR 分区，GPT 分区能提供更可靠的数据存储。
- <mark style="background: #FF5582A6;">C.使用 BIOS 引引导启动的系统不能识别 GPT 分区</mark>
- D.GPT 分区支持超过 2 PB 大小的分区
6. 户账户配置文件中，默认不包含下列哪个选项的信息（）
- A.账户标识符
- B.用户登录后执行的第一条命令
- C.账户名称
- <mark style="background: #FF5582A6;">D.登录密码</mark>
7. 命令 reboot 是（）
- A.shell 内置命令
- B.普通的二进制可执行文件
- C.函数
- <mark style="background: #FF5582A6;">D.链接文件</mark>
8. 执行 apt 源更新命令的输出中包含下列信息： Get: 11 http://mirrors.tuna.tsinghua.edu.cn/ubuntu jammy-updates/main amd 64 Packages 说明软件包的发行版本是（）
- A. Ubuntu 20.04
- <mark style="background: #FF5582A6;">B. Ubuntu 22.04</mark> 
- C. Ubuntu 18.04 
- D. Ubuntu 22.10
9. 用户账户的主属组信息保存在文件（）
- A. /etc/gshadow
- B. /etc/shadow
- <mark style="background: #FF5582A6;">C./etc/passwd</mark>
- D. /etc/group
10. 下列不能用于 BRE 的元字符是
- A. .
- <mark style="background: #FF5582A6;">B. +</mark>
- C. *
- D. []
11. NOME 中, 程序窗口的活动状态显示在（）
- A.软件概览视图
- <mark style="background: #FF5582A6;">B.dash 面板</mark>
- C.顶部面板
- D. 任务栏
12. 下列 fdisk 的命令序列中，可创建 Linuxnative 分区的正确选项是（）。
- A. d-n-w
- B. N-t-w
- C. N-d-w
- <mark style="background: #FF5582A6;">D. P-n-w</mark>
13. 不显示命令 find/tmp-iname\*txt-print 的错误输出信息的命令是（）
- <mark style="background: #FF5582A6;">A. find /tmp -iname \*txt -print 2>/dev/null</mark>
- B. Find /tmp -iname \*. Txt -print >>/dev/null 
- C. find /tmp -iname \*txt -print &>/dev/null
- D. Find /tmp -iname \*. Txt -print |& /dev/null 
- 
14. 在 Linux 系统中，修改文件名称会改变文件的
- A.访问时间
- B.内容修改时间
- C.创建时间
- <mark style="background: #FF5582A6;">D.属性修改时间</mark>
15. 如果键入的命令没有正确执行,最不可能的原因是 ()
- A.在环境变量中没有保存命令文件的存储路径
- B.键入的命令存在拼写错误
- <mark style="background: #FF5582A6;">C.该命令是内部命令</mark>
- D.命令文件不是保存在当前目录中
16. 下列可暂停进程执行的信号是（)
- <mark style="background: #FF5582A6;">A. SIGCONT</mark>
- B. SIGTERM
- C. SIGSTOP
- D. SIGHUP
17. 列可能会导致 shell 进程终止的快捷键是（）
- A. Ctrl+O
- B. Ctrl+Z
- C. Ctrl+C
- <mark style="background: #FF5582A6;">D. Ctrl+D</mark>
18. 下列关于 root 账户，表述错误的是（）
- <mark style="background: #FF5582A6;">A.以 root 身份执行命令时需要在命令前使用 sudo 前缀</mark>
- B.root 账户登录后默认的命令提示符是井字符号（#)
- C.root 可以执行系统中所有具备执行权限的命令
- D.root 账户就是用户标识符为 0 的账户
19. Linux 发行版使用的软件许可证是（）
- A. BSD
- B. MPL
- <mark style="background: #FF5582A6;">C. GPL</mark>
- D. Apache License
20. PID PGID SID TTY TPGID STAT COMMAND 
	2841 2841 2841 pts/1 4213 Ss bash 
	4213 4213 2841 pts/1 4213 S+ man ps 
	4223 4213 2841 pts/1 4213 S+ pager 
	4243 4243 4243 pts/0 4250 Ss bash 
	4250 4250 4243 pts/0 4250 R+ ps j 
	有进程列表如上所示, 则上列进程中，前台进程的个数有（）
	<mark style="background: #FF5582A6;">A.3</mark> B. 2 C. 4 D.1
21. 列不能把管线中前一条命令输出的字符串转换为大写字母的命令是（）
- A. sed 's/[a-z]/\U&/g'
- B. Tr a-z A-Z
- <mark style="background: #FF5582A6;">C. Xargs echo $(BASH_ARGV^^)</mark>
- D. awk '{print toupper ($0)}'
22. 下列不会创建新的 shell 进程的命令是（）
- A. bash
- B. Su
- C. Login
- <mark style="background: #FF5582A6;">D. Exit</mark>
23. Ubuntu 中, 无需安装即可执行的软件包是 ()
- A. deb
- <mark style="background: #FF5582A6;">B. Appimage</mark>
- C. Flatpak
- D. Snap
24. 下列可以在 Ubuntu 22.04 x 86_64 系统中安装的软件是（）
- A. linuxqq_2.0.0-b 2-1089_arm 64. Deb
- B. Code_1.73.1-1667967334_x 86. Deb
- <mark style="background: #FF5582A6;">C. Baidunetdisk_4.15.5_amd 64. Deb</mark>
- D. Wps-office-11.1.0.11664-1. X 86_64. Rpm
25. 如果某个命令语法描述如下则下列命令格式错误的是（）zip [-aABcdD] [--longoption .] [-b path] [-t date] [zipfile [file .]
- A. zip -a --ws foo. Zip
- B. Zip -db /tmp -t 10072022 infomy foo
- C. <mark style="background: #FF5582A6;">Zip -bD /tmp foo. Zip foo 1 foo 2 </mark>
- D. Zip -ac foo. Zip foo 1 foo 2
#### 填空
1. 打开程序概览视图的快捷键是：<mark style="background: #FF5582A6;">Super+A</mark>
2. 把命令 Is-tFa 创建为别名 lt 的命令是: <mark style="background: #FF5582A6;">alias lt='ls -tFa'</mark>
3. 显示当前使用的终端设备名称的命令:<mark style="background: #FF5582A6;">tty</mark>
4. MBR 分区方案中 Linux 系统中，第一个磁盘上的第一个逻辑分区的设备名称是:<mark style="background: #FF5582A6;">/dev/sda 5</mark>
5. 使用 vi 编辑器时，在命令模式中，进入末行模式使用的按键是: <mark style="background: #FF5582A6;">冒号</mark>
6.  Apt 工具的主配置文件是：<mark style="background: #FF5582A6;">/etc/apt/source. List</mark>
7. 在 ubuntu 20.04 系统中，配置使用非官方 APT 源前，通常需要添加软件源的数字签名：包含数字签名的文件默认保存的目录是:<mark style="background: #FF5582A6;">/etc/apt/trusted. Gpg.D</mark>
8. Linux 系统中，以/dev/loopn (n 是整数）命名的设备被称为回环设备，以管理员身份将文件a.img 创建为包含分区信息回环设备的命令是: <mark style="background: #FF5582A6;">sudo losetup /dev/loop 10 a.img</mark>
9. Linux 内核发行采用的软件许可证是:<mark style="background: #FF5582A6;">GPL</mark>
10. 统在执行完磁盘的挂载后，会在挂载点中创建一个目录，该目录的名称是:<mark style="background: #FF5582A6;">lost+found</mark>
11. Linux 是一种旨在实现<mark style="background: #FF5582A6;"> POSIX</mark>和单一 Unix 规范的操作系统
12. 清除字符终端屏幕显示内容的快捷键是:<mark style="background: #FF5582A6;">Ctrl+L</mark>
13. Ubuntu 22.04 系统默认保存 Systemd 的目标 (target）单元文件的目录是:<mark style="background: #FF5582A6;">/usr/lib/systemd/system</mark>
14. Systemd 单元配置文件一般由多个节组成通用于所有单元的节有两个, 其中，定义启动信息的节是:<mark style="background: #FF5582A6;">Install</mark>
15. 在 Ubuntu desktop 系统中，默认启动显示管理器的虚拟控制台是:<mark style="background: #FF5582A6;"> tty 1</mark>
16. 如果某个用户设置的默认文件访问模式是 0644, 则回答下列问题执行命令 chmoda+x, o+t file 1. Txt.，文件 file. Txt 的权限是：<mark style="background: #FF5582A6;">rwxr-xr-t</mark>
17. 使能用于交换分区的文件时需要将文件的访问权限设置为 <mark style="background: #FF5582A6;">0600</mark>属主设置为 <mark style="background: #FF5582A6;">root</mark>
#### 判断
1. 使用 nano 编辑文本时, 可以同时按下 Esc 键和 6 键, 复制标记的文本----<mark style="background: #FF5582A6;">错</mark>
2. 在 Linux 操作系统中，设备文件的大小总是为 0 字节----<mark style="background: #FF5582A6;">对</mark>
3. 当 shell 提示显示为 user@srv :~ #时 ，命令会以 root 权限执行----<mark style="background: #FF5582A6;">错</mark>
4. 设置群组密码的作用是为了避免非群组用户在未授权的情况下获取该群组的访问权限----<mark style="background: #FF5582A6;">错</mark>
5. 在正则表达式中星号字符匹配任意多个字符组成的字符串----错
6. Dash 面板可以显示所有打开的窗口----错
7. 使用 nano 编辑文本时, 可以按下快捷键 M-P 显示空格的数量----<mark style="background: #FF5582A6;">对</mark>
8. 在 MBR 分区方案中, Linux native 分区只能是主分区----<mark style="background: #FF5582A6;">对</mark>
