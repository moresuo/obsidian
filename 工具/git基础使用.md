***
##  概述
### 简介
Git 是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过 Git 仓库来存储和管理这些文件，Git 仓库分为两种：
- 本地仓库：开发人员自己电脑上的仓库
- 远程仓库：远程服务器上的仓库，远程仓库可以和多个本地仓库进行连接，一般用于团队的共享仓库
![[Pasted image 20231120233021.png]]
### 下载与安装
下载地址：[https://git-scm.com/download](https://git-scm.com/download)
下载后点击安装文件
![[Pasted image 20231120233313.png]]
安装完成后在任意目录点击鼠标右键即可看到两个关于 git 的操作
![[Pasted image 20231120233443.png]]
Git GUI Here：打开 Git 图形界面(一般不怎么用，至少我不用)
Git Bash Here：打开 Git 命令行

> [!warning]- 注意
> - 如果你想将某个目录下的文件保存至仓库，那么就要在该目录下执行 git 命令
> - 如果想将文件保存至远程仓库，那么你必须先创建一个远程仓库，我们可以借助网上的的一些代码托管平台，如 gitHub, gitee 等，在这里就不多赘述了
## 常用命令
当安装 Git 后首先要做的事情是设置用户名称和 email 地址。这是非常重要的，因为每次 Git 提交都会使用该用户信息
<mark style="background: #ABF7F7A6;">设置用户信息：</mark>
- `git config --global user.name "moresuo"`
- `git config --global user.email "2118249053@qq.com"`

> [!info]- 提示
> User. Name 和 user. Email 可以任意设置

<mark style="background: #ABF7F7A6;">查看配置信息：</mark>
- `git config --list`
如果是第一次提交文件至仓库，必须先创建一个本地仓库，在你想提交的文件目录下执行如下命令：
- `git init`
如果在当前目录中看到.git 文件夹（此文件夹为隐藏文件夹）则说明 Git 仓库创建成功
![[Pasted image 20231120234920.png]]
### 工作区，暂存区，版本库
工作区：包含.git 文件夹的目录就是工作区，也称为工作目录，主要用于存放开发的代码
暂存区：.git 文件夹中有很多文件，其中有一个 index 文件就是暂存区，也可以叫做 stage。暂存区是一个临时保存修改文件的地方
版本库：前面看到的.git 隐藏文件夹就是版本库，版本库中存储了很多配置信息、日志信息和文件版本信息等
![[Pasted image 20231120235146.png]]
Git 工作区的文件存在两种状态：
- Untracked: 未被纳入版本控制
- Tracked: 已被纳入版本控制
	- Unmodified: 未修改状态
	- Modfied: 已修改状态
	- Staged: 已暂存状态
### 将本地文件提交至远程仓库
本地文件要先从工作区添加到暂存区，再由暂存区提交至版本库，最后由版本库推送给远程仓库
可以使用通配符一次提交多个文件，例如*
<mark style="background: #ABF7F7A6;">将文件加入暂存区：</mark>
-  `git add fileName`
![[Pasted image 20231121000523.png]]
<mark style="background: #ABF7F7A6;">取消暂存或切换到指定版本：</mark>
- `git reset fileName` 取消暂存
- `git reset --hard 版本号` 切换指定版本
![[Pasted image 20231121000537.png]]
![[Pasted image 20231121000542.png]]
<mark style="background: #ABF7F7A6;">查看版本号以及提交日志：</mark>
- `git log`
![[Pasted image 20231121000550.png]]
<mark style="background: #ABF7F7A6;">将暂存区文件提交至版本库：</mark>
- `git commit -m "这里填写提交信息" fileName`
![[Pasted image 20231121000601.png]]

> [!warning]- 注意
> 如果提交空文件夹会报错，error: pathspec 'temp' did not match any file (s) known to git
> 要在空文件下创建一个后缀名为 .gitkeep 的文件，该文件不会被提交

提交至远程仓库之前要先创建远程仓库
<mark style="background: #ABF7F7A6;">添加远程仓库：</mark>
-  `git remote add 简称 远程仓库地址`
![[Pasted image 20231121000819.png]]
<mark style="background: #ABF7F7A6;">将本地仓库内容推送到远程仓库：</mark>
- `git push 简称 分支名称`
![[Pasted image 20231121000916.png]]

> [!info]- 提示
> 如果提交的是 gitHub 的远程仓库记得关闭代理，否则提交会失败

在使用 git push 命令将本地文件推送至码云远程仓库时，如果是第一次操作，需要进行身份认证，认证通过才可以推送，如下：
![[Pasted image 20231121001105.png]]
注意：上面的用户名和密码对应的就是我们在码云上注册的用户名和密码，认证通过后会将用户名和密码保存到 windows 系统中（如下图），后续再推送则无需重复输入用户名和密码
### 远程仓库克隆至本地仓库
远程仓库克隆指的是本地并没有与远程仓库创建任何联系，只是单纯的想获取远程仓库中的文件与数据，并且建立联系
<mark style="background: #ABF7F7A6;">克隆仓库的命令格式：</mark>
- `git clone 远程仓库地址`
![[Pasted image 20231121001619.png]]
### 更新本地仓库与远程仓库一致
从远程仓库获取最新版本并合并到本地仓库：
-  `git pull 远程仓库简称 分支名称`

> [!warning]- 注意
> 如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库，并且仓库中存在文件，此时再从远程仓库拉取文件的时候会报错（fatal: refusing to merge unrelated histories ），解决此问题可以在 git pull 命令后加入参数--allow-unrelated-histories

### 分支操作
分支是 Git 使用过程中非常重要的概念。使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线
本地仓库和远程仓库中都有分支，同一个仓库可以有多个分支，各个分支相互独立，互不干扰，一般在团队中，一个人就是一个分支，最后每个人提交的代码汇总到主分支
通过 git init 命令创建本地仓库时默认会创建一个 master 分支
<mark style="background: #ABF7F7A6;">查看分支：</mark>
- `git branch` 查看所有本地分支
- `git branch -r` 查看所有远程分支
- `git branch -a` 查看所有本地分支和远程分支
![[Pasted image 20231121002503.png]]
<mark style="background: #ABF7F7A6;">创建分支：</mark>
- `git branch 分支名称`
![[Pasted image 20231121002550.png]]
<mark style="background: #ABF7F7A6;">切换分支：</mark>
- `git checkout 分支名称`
![[Pasted image 20231121002702.png]]
<mark style="background: #ABF7F7A6;">合并分支：</mark>
- `git merge 分支名称`
注意：分支合并时需注意合并的方向，如上图所示，在 Master 分支执行操作，结果就是将 b1分支合并到 Master 分支。
## 在 IDEA 中使用 git
在IDEA中使用Git，本质上还是使用的本地安装的Git软件，所以需要提前安装好Git并在IDEA中配置Git
![[Pasted image 20231121004544.png]]
<mark style="background: #ABF7F7A6;">初始化本地仓库：</mark>
依次选择菜单【VCS】---【Import into Version Control】---【Create Git Repository】
![[Pasted image 20231121005014.png]]
<mark style="background: #ABF7F7A6;">在弹出的【Create Git Repository】对话框中选择当前项目根目录，点击【OK】按钮：</mark>
![[Pasted image 20231121005028.png]]
<mark style="background: #ABF7F7A6;">操作完成后可以看到当前项目根目录下出现了.git 隐藏目录：</mark>
![[Pasted image 20231121005042.png]]
<mark style="background: #ABF7F7A6;">操作完成后可以在IDEA的工具栏中看到Git的相关操作图标：</mark>
![[Pasted image 20231121005136.png]]
### 将本地代码提交至远程仓库
<mark style="background: #ABF7F7A6;">将文件加入暂存区：</mark>
![[Pasted image 20231121005347.png]]
<mark style="background: #ABF7F7A6;">将暂存区文件提交至版本库：</mark>
![[Pasted image 20231121005521.png]]
![[Pasted image 20231121005642.png]]
<mark style="background: #ABF7F7A6;">添加到指定远程仓库：</mark>
由于推送至远程仓库操作属于高频操作，所以可以通过IDEA工具栏中的提交快捷按钮同时完成提交和推送
![[Pasted image 20231121005919.png]]
<mark style="background: #ABF7F7A6;">查看远程仓库：</mark>
![[Pasted image 20231121095058.png]]

### 从远程仓库克隆和拉取
![[Pasted image 20231121122939.png]]
![[Pasted image 20231121122952.png]]
<mark style="background: #ABF7F7A6;">产看远程仓库：</mark>
![[Pasted image 20231121123117.png]]
![[Pasted image 20231121123123.png]]
<mark style="background: #ABF7F7A6;">从远程仓库上拉取：</mark>
![[Pasted image 20231121123257.png]]
### 分支操作
<mark style="background: #ABF7F7A6;">查看分支：</mark>
![[Pasted image 20231121123433.png]]
<mark style="background: #ABF7F7A6;">在弹出的窗口中可以看到本地分支和远程分支：</mark>
![[Pasted image 20231121123451.png]]
<mark style="background: #ABF7F7A6;">由于分支操作属于高频操作，所以在 IDEA 的状态栏中提供了分支操作的快捷按钮：</mark>
![[Pasted image 20231121124022.png]]
<mark style="background: #ABF7F7A6;">点击【master】快捷按钮即可弹出【Git Branches】分支窗口：</mark>
![[Pasted image 20231121124037.png]]
<mark style="background: #ABF7F7A6;">切换分支：</mark>
![[Pasted image 20231121124105.png]]
<mark style="background: #ABF7F7A6;">将分支推送值远程仓库：</mark>
![[Pasted image 20231121124132.png]]
<mark style="background: #ABF7F7A6;">合并分支：</mark>
![[Pasted image 20231121124145.png]]