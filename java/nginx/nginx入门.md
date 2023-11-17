## Nginx 概述
- Nginx 是一款轻量级的 web 服务器，反向代理服务器，电子邮件代理服务器。
- 内存占用少，并发性高
- 官网：[https://nginx.org/](https://nginx.org/)
### 在 linux 下安装 nginx
#### 普通安装
Nginx 是 c 语言编写的，所以要先安装 c 语言编译环境
```shell
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
```
下载 nginx 安装包
```shell
yum install wget
wget https://nginx.org/download/nginx-1.16.1.tar.gz
```
解压 nginx 压缩包
```shell
tar -zxvf nginx-1.16.1.tar.gz
```
配置编译环境
```shell
cd nginx-1.16.1
./configure --prefix=/usr/local/nginx
```
编译安装
```shell
make & make install
```
#### Docker 容器安装
拉取 nginx 的镜像
```shell
docker pull nginx
```
![[image-20210731155856199.png]]
查看拉取到的镜像
```shell
docker images
```
创建并运行 nginx 容器
```shell
docker run --name mn -p 80:80 -d nginx
```
进入 nginx 容器
```shell
docker exec -it mn bash
```
![[image-20210731164159811.png]]
进入 nginx 目录
```shell
cd /usr/share/nginx
```

## Nginx 命令
### 常用命令

<mark style="background: #ADCCFFA6;">Nginx 中，我们的二进制可执行文件(nginx)存放在 sbin 目录下，虽然只有一个可执行文件，但是我们可以通过该指令配合不同的参数达到更加强大的功能。接下来，我们就演示一下 Nginx 常见指令, 在执行下面的指令时,都需要在/usr/local/nginx/sbin/目录下执行。</mark>
查看版本
```shell
./nginx -v
```
检查配置文件是否有误
```shell
./nginx -t
```
启动 nginx 服务
```shell
./nginx
```
Nginx 默认端口号为 80
根据服务器 ip 地址加访问端口查看浏览器是否启动成功

> [!warning]- 注意
> 想要正常访问 nginx，需要关闭防火墙或打开指定端口
> - 关闭防火墙：systemctl stop firewall
> - 开放 80 端口：firewall-cmd --add-port=80/tcp --permanent, firewall-cmd --reload
>
![[Pasted image 20231116125246.png]]

停止服务
```shell
./nginx -s stop
```
当修改 nginx 配置文件之后，需要重新加载才会生效
```shell
./nginx -s reload
```

### 配置环境变量
<mark style="background: #ABF7F7A6;">上述 nginx 命令只能在 sbin 的目录下执行，每次想执行时都要进入 sbin 目录，是在麻烦
所以将 nginx 设置为环境变量，这样就可以在任意目录下执行了</mark>
通过 vim 编辑器，打开/etc/profile 文件, 在 PATH 环境变量中增加 nginx 的 sbin 目录
```shell
vim /etc/profile
```
![[Pasted image 20231116130320.png]]
修改完配置文件之后，需要执行<mark style="background: #FF5582A6;"> source /etc/profile </mark>使文件生效
![[Pasted image 20231116130352.png]]

## Nginx 应用
### 配置文件结构
Nginx 配置文件分为 3 部分：
- 全局块：配置运行的全局配置
- Events 块：配置网络连接相关配置
- Http 块：配置代理、缓存、日志信息和虚拟主机等信息
![[Pasted image 20231116144713.png]]
Nginx 部署静态资源只需要将文件复制到 html 文件夹下
```properties
server {
    listen 80;				#监听端口	
    server_name localhost;	#服务器名称
    location / {			#匹配客户端请求url
        root html;			#指定静态资源根目录
        index index.html;	#指定默认首页
    }
}
```
<mark style="background: #ABF7F7A6;">修改配置文件之后要使用 reload 命令重新加载才会生效</mark>

### 反向代理
**正向代理**：正向代理服务器是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。
正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。
正向代理一般是**在客户端设置代理服务器**，通过代理服务器转发请求，最终访问到目标服务器。
![[Pasted image 20231116150736.png]]
正向代理对于用户而言，用户是知道代理服务器的存在，知道请求访问的节点
**反向代理**：对于用户而言，用户不知道目标服务的地址，把代理服务器当做目标服务器，用户真正想获取的目标服务器资源是由反向代理服务器请求转发获取的，反向代理服务器对于用户是无感知的
![[Pasted image 20231116151241.png]]
<mark style="background: #ABF7F7A6;">为一个服务器设置反向代理，只需要在反向代理服务器中配置相关信息，监听端口号及目标服务器地址</mark>

![[Pasted image 20231116160332.png]]
```properties
server {
    listen 82;
    server_name localhost;
    location / {
        proxy_pass http://192.168.188.131:8080; 	#反向代理配置，将请求转发到指定服务
    }
}
```
监听端口号为 82，如果有请求到达这个端口，那么就请求转发到 http://192.168.188.131:8080
### 负载均衡
早期的网站流量和业务功能都比较简单，单台服务器就可以满足基本需求，但是随着互联网的发展，业务流量越来越大并且业务逻辑也越来越复杂，单台服务器的性能及单点故障问题就凸显出来了，因此需要多台服务器组成应用集群，进行性能的水平扩展以及避免单点故障出现。
一台服务器挂掉，我们还可以访问另一台服务器，他们的功能是相同的，这就是服务器集群

<mark style="background: #FFF3A3A6;">那么在高并发的请求访问下，我们该如何分配这些请求的访问服务器</mark>
- 可以使用 nginx 的负载均衡机制，将用户请求根据对应的负载均衡算法分发到应用集群中的一台服务器进行处理
![[Pasted image 20231116154100.png]]

<mark style="background: #FFF3A3A6;">如何在 nginx 中配置负载均衡</mark>
- 明确服务器集群中的每个服务器的地址及端口号
- 在 nginx.conf 中配置分流器，upstream: 指定一组服务器进行负载均衡
```properties
#upstream 分流器 指令可以定义一组服务器
upstream targetserver{	
    server 192.168.200.201:8080;
    server 192.168.200.201:8081;
}
server {
    listen       8080;
    server_name  localhost;
    location / {
        proxy_pass http://targetserver;
    }
}
```
- Targetserver 为分流器名称，可以变，但必须保持一致
- 当有请求到达 nginx 服务器且端口号为 8080 时，就会采用负载均衡策略，请求转发到集群的一个服务器
配置完以后别忘了重新加载配置文件
```shell
nginx -s reload
```
测试时,我们直接访问 nginx 的8080端口([http://192.168.200.200:8080](http://192.168.200.200:8080)), 此时 nginx 会根据负载均衡策略,将请求转发到后面的两台服务器。
![[Pasted image 20231116154932.png]]
两次相同的请求，但是目标服务器地址不一致，nginx 的负载均衡默认采用轮询机制

#### 负载均衡策略
- 轮询：默认方式
- 权重方式：权重越大，服务器分配到请求的概率越大
- 根据 ip 地址分配：根据客户端请求的 IP 地址计算 hash 值，根据 hash 值来分发请求, 同一个 IP 发起的请求, 会发转发到同一个服务器上
- 最少连接分配：哪个服务器当前连接数最少，就请求哪个服务器
- 根据 url 分配：根据客户端请求 url 的 hash 值，来分发请求, 同一个 url 请求, 会发转发到同一个服务器上
- 根据响应时间分配：哪个服务器处理请求时间短，就将请求分配给哪个服务器
