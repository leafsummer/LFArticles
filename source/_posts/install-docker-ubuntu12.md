---
title: ubuntu12.04 下安装Docker
categories:
  - 技术实践
abbrlink: 2100280121
date: 2015-04-24 20:52:11
tags:
---

***ubuntu12.04 下安装Docker***

1. 检测Linux内核版本
通过 uname -a 命令查看当前系统linux内核版本，如果版本低于3.8，需要升级linux的内核（docker在linux的kernel3.8上运行最佳，ubuntu12.04内置的内核一般是3.2版本）
安装内核
```bash
sudo apt-get update
sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring
```

重启
```bash
sudo reboot
```

2. 检测apt系统的https兼容性，如果不是最新版或者/usr/lib/apt/methods/https文件不存在，请安装apt-transport-https包，因为这是获取docker安装文件的重要步骤
```bash
sudo apt-get install apt-transport-https
```

3. 添加docker库的密钥
```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
```

4. 添加docker库的源
```bash
sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
sudo apt-get update
```

5. 安装docker
```bash
sudo apt-get install lxc-docker
```
后台运行服务的命令是sodu docker -d &,在最新版的docker，已经不需要再额外执行，安装好之后，docker的服务会自动启动。

6. 安装成功后，下载ubuntu镜像并启动镜像来验证安装是否正常（会需要点时间）
```bash
sudo docker run -i -t ubuntu /bin/bash
```
如果请求超时, 请更换docker的镜像源, 最好是换成国内的, 例如: 阿里的. 后面会说到怎么替换镜像源

7. 成功运行后，退出容器环境
```bash
exit
```

***安装遇到的问题：***
1. docker 和 ufw（如果你安装了ufw，并启动了它）
Dockers是用桥接的方式管理容器的网络，默认情况下，如果你安装了UFW防火墙，他会过滤掉所有的转发，所以你需要允许UFW转发
```bash
sudo nano /etc/default/ufw
----
# Change:
# DEFAULT_FORWARD_POLICY="DROP"
# to
DEFAULT_FORWARD_POLICY="ACCEPT"
```
然后刷新UFW
```bash
sudo ufw reload
```
当然你也可以只放行Docker容器允许的端口4243
```bash
sudo ufw allow 4243/tcp
```

2. docker和网络延迟
ping get.docker.io,看下延迟。如果访问不了或者访问延迟很高，可以在网上搜索docker的镜像网站，来替代get.docker.io。
例如使用Yandex的镜像来替代get.docker.io,Yandex是一个俄罗斯的镜像站点，每6小时更新一次
用http://mirror.yandex.ru/mirrors/docker/  替代 http://get.docker.io/ubuntu
```bash
sudo sh -c "echo deb http://mirror.yandex.ru/mirrors/docker/ docker main\
> /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install lxc-docker
```

***Red Hat Enterprise Linux安装docker教程***
顺便简单说下redhat怎么装docker
注意事项是redhat是社区贡献的所以这个不需要我多说了，人家建议用ubuntu
安装步骤如下:
```bash
#安装包
sudo yum -y install docker-io

#升级安装包
sudo yum -y update docker-io

#启动docker
sudo service docker start

#开机启动，加入3,5就可以了
sudo chkconfig docker on

#然后运行吧--比较坑的就是fedora
sudo docker run -i -t fedora /bin/bash
```