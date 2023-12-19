***
## 购买服务器
1. 我这里使用的阿里云服务器，大学生有 300 元的劵, 推荐 99 元/年的，[云服务器2核2G 99元/1年 (aliyun.com)](https://www.aliyun.com/lowcode/promotion/allinaliyun/99program?spm=5176.8789780.J_3965641470.1.4c4039fb9rjxiy)
2. 进入控制台页面，选择云服务器 ECS, 找到实例
![[Pasted image 20231128094729.png]]
3. 点击远程连接
![[Pasted image 20231128094807.png]]
4. 修改密码
![[Pasted image 20231128142317.png]]

5. 输入密码进入 shell 界面 (也可通过本地远程连接工具根据公网 IP 进行连接，也需要输入密码和用户名)
![[Pasted image 20231128094842.png]]
## 安装宝塔
宝塔地址：[宝塔Linux面板安装教程 - 2023年5月11日更新 - 7.9.10正式版 - Linux面板 - 宝塔面板论坛 (bt.cn)](https://www.bt.cn/bbs/thread-19376-1-1.html)

> [!NOTE] CentOS 安装命令
```shell
>yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_6.0.sh && sh install.sh 12f2c1d72
```

> [!warning]- 特别注意
> 服务器绑定弹性公网 IP 一定要选和服务器所在地一致的，否则都是泪😭

1. 安装完成，复制外网地址
![[Pasted image 20231128155337.png]]

2. 将端口号 13466 加入安全组，就是关闭防火墙，[阿里云如何关闭防火墙?_阿里云web应用防火墙 如何关闭-CSDN博客](https://blog.csdn.net/jisuyunzzc/article/details/109400136)
3. 登录宝塔，输入用户名和密码
4. 宝塔用户名和密码不用记，忘了输入 `bt default`
![[Pasted image 20231128160553.png]]
<mark style="background: #ABF7F7A6;">可以修改密码为熟悉的密码</mark>
5. 选择 <mark style="background: #FF5582A6;">LNMP </mark>极速安装
![[Pasted image 20231128160733.png]]

## 安装 Docker
Docker 安装官网：[Install Docker Engine on CentOS | Docker Docs](https://docs.docker.com/engine/install/centos/)
1. 卸载旧版本 Docker, 没有安装就不用管
```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
2. 设置 Docker 仓库
```shell
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
3. 安装 Docker
```shell
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
4. 启动 Docker
```shell
sudo systemctl start docker
```
5. 拉取一个 hello world 镜像测试
```shell
sudo docker run hello-world
```
## 安装 Halo 博客
1. 创建一个博客工作目录, 不用创建也可以，后面安装配置文件时会自动创建
```shell
mkdir ~/.halo && cd ~/.halo
```
2. 下载配置文件到工作目录
```shell
curl -o ~/.halo/application.yaml --create-dirs https://dl.halo.run/config/application-template.yaml
```
3. 拉取 Docker 镜像
```shell
sudo docker pull halohub/halo
```
4. 创建容器
```shell
docker run -it -d --name halo -p 8090:8090 -v ~/.halo:/root/.halo --restart=always halohub/halo
```
- `-it`: 开启输入功能并连接伪中断，就是进入到 docker 容器后允许向 shell 控制台输入
- `-d`: 后台运行容器
- `--name`: 为容器指定名称
- `-p`: 端口映射，服务器端口映射到 docker 镜像容器端口
- `-v`: 工作目录映射，相当于挂载文件，在容器外修改文件就相当于在容器内修改一样
安装成功后会有一段编码
![[Pasted image 20231128210911.png]]
5. 打开防火墙 8090 端口
![[Pasted image 20231128211408.png]]
6. 填写注册信息，进入后台，如果是最新版进入后台不再是在地址栏端口号后面输入 admin 而是输入 console
7. 升级博客
- 删除原有容器
```shell
docker stop halo
docker rm -f halo
```
- 拉取最新镜像
```shell
docker pull halohub/halo
```
- 运行容器
```shell
docker run -it -d --name halo -p 8090:8090 -v ~/.halo:/root/.halo --restart=always halohub/halo
```

## 注册域名
1. 我买的时腾讯云的域名，购买域名之后进入域名注册面板
![[Pasted image 20231128213130.png]]
2. 点击解析
3. 点击添加记录，记录值就是要解析的公网 ip
![[Pasted image 20231128213245.png]]
## 配置反向代理
1. 到宝塔添加一个站点，就是刚才的域名
2. 设置 SSL 证书，当前博客网站是不安全的 http, 我们要把他设置为 https
3. 如果在宝塔上申请失败就到域名购买的地方进行申请，比如我在腾讯云上购买的域名，我就在腾讯云上申请，最后将证书文件中的 key 与 pem 文件分别全部复制粘贴到当前证书中
![[Pasted image 20231128213700.png]]
各大服务器厂商都有免费的 SSL 证书申请
4. 点击反向代理，进行添加反向代理
![[Pasted image 20231202125033.png]]
![[Pasted image 20231202125100.png]]
这样就可以通过域名访问网站了

> [!warning]- 注意
> 如果是国内服务器 (不包括香港), 必须等备案下来才可以访问网站

## 安装主题

	

