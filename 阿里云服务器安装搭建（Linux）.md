# 阿里云CentOS7服务器安装 #



## 主要步骤： ##
一、检查并备份更新yum源

二、安装编译环境软件---openssl-devel gcc gcc-c++

三、安装服务支撑软件---haproxy redis keepalived

四、安装维护工具软件--tigervnc-server tcpdump mlocate

五、安装Docker

六、安装Emqx---三种方式：Docker方式、YUM方式、安装包方式


## 详细步骤： ##

**一、检查并备份更新yum源**

```
cat /etc/yum.repos.d/CentOS-Base.repo
```

备份

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```
 下载新的 CentOS-Base.repo 到 /etc/yum.repos.d/

```
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```
运行 yum makecache 生成缓存

**二、安装编译环境软件**

```
yum -y install openssl-devel gcc gcc-c++
```

**三、安装服务支撑软件**

1、安装haproxy

```
rpm -qa | grep haproxy   ----查找是否安装
yum install haproxy    ----安装
```

自启动服务：

```
systemctl  enable  haproxy.service
systemctl  start  haproxy.service
```
配置文件位置

```
/etc/haproxy/haproxy.cfg
```

2、安装Redis：（自动启用）

```
yum install redis
```

自启动服务：

```
systemctl  enable  redis.service
systemctl  start  redis.service
```
3、安装keepalived

```
yum -y install keepalived
```
自启动服务：

```
systemctl  enable  keepalived.service
systemctl  start  keepalived.service
```

**四、安装维护工具软件**

1、安装vnc

```
rpm -q tigervnc tigervnc-server   --检查是否安装过vnct
yum install tigervnc-server -y       --安装vnc
```

2、安装tcpdump

```
yum -y install tcpdump
```

测试：

```
tcpdump -i eth0 -nn host 224.1.100.88
```

3、安装mlocate

```
yum  -y install mlocate
```

更新查询索引

```
updatedb
```

**五、安装Docker**

Step 1: 安装必要的一些系统工具

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

Step 2: 添加软件源信息

```
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

Step 3

```
sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

Step 4: 更新并安装Docker-CE

```
sudo yum makecache fast
sudo yum -y install docker-ce
```

Step 5: 开启Docker服务

```
sudo service docker start
```
自启动服务：

```
systemctl  enable  docker.service
systemctl  start  docker.service
```

Docker镜像默认目录：

```
/var/lib/docker
```

修改默认目录系统软链接到其他目录：

```
ln -s /home/docker /var/lib/docker
```

配置镜像加速器（可选）

针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器
以下https://XXXXXX.mirror.aliyuncs.com 请使用在阿里上申请的实际地址。

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://XXXXXX.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

**六、安装Emqx**

**Docker方式：**

1、获取 Docker 镜像

```
docker pull emqx/emqx:5.0.10
```
2、启动 Docker 容器
```
docker run -d --name emqx -p 1883:1883 -p 8083:8083 -p 8084:8084 -p 8883:8883 -p 18083:18083 emqx/emqx:5.0.10
```

**YUM方式：**

1、配置 EMQX Yum 源

```
curl -s https://assets.emqx.com/scripts/install-emqx-rpm.sh | sudo bash
```

2、安装 EMQX

```
sudo yum install emqx
```

3、启动 EMQX

```
sudo systemctl start emqx
```

**安装包方式：**

CentOS7 amd64 / rpm

1、下载 [emqx-5.0.10-otp24.2.1-1-el7-amd64.rpm](https://www.emqx.com/zh/downloads/broker/5.0.10/emqx-5.0.10-otp24.2.1-1-el7-amd64.rpm)   [SHA256](https://www.emqx.com/zh/downloads/broker/5.0.10/emqx-5.0.10-otp24.2.1-1-el7-amd64.rpm.sha256)

```
wget https://www.emqx.com/zh/downloads/broker/5.0.10/emqx-5.0.10-otp24.2.1-1-el7-amd64.rpm
```

2、安装 EMQX

```
sudo yum install emqx-5.0.10-otp24.2.1-1-el7-amd64.rpm
```

3、启动 EMQX

```
sudo systemctl start emqx
```



