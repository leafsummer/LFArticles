---
title: Centos7 下安装docker-ce
categories:
  - 技术实践
abbrlink: 427468595
date: 2018-06-14 20:40:45
tags:
---

**Centos7 下安装docker-ce**

目前docker版本都更新到了docker-ce, 对各个系统的支持也越来越好. 这里以Centos7为基础, 来说明下如何安装docker-ce

一. 安装依赖库
```bash
yum install -y yum-utils device-mapper-persistent-data lvm2 epel-release lrzsz vim net-tools bash-completion wget bridge-utils
```

二. 由于docker.com 被国内屏蔽了，所以按照如下的方法进行安装，可能行不通：
```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 换成aliyun的(http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo)
# (yum-config-manager --add-repo ./docker-ce.repo)
yum --enablerepo=docker-ce-stable clean metadata
yum install docker-ce
```

三. 如果步骤1通过docker的源无法安装, 也可以下载到本地安装
```bash
yum install ./docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm
```

查看是否安装成功
```bash
yum list docker-ce --showduplicates | sort -r
```

四. 安装好docker后，通过systemd来管理
```bash
systemctl enable docker
systemctl start docker
```

五. 运行hello-world镜像
```bash
docker run hello-world
```

会自动下载镜像，输出hello world
查看下载的镜像 docker image ls
查看运行容器 docker ps

六. 安装并运行最小 ubuntu的镜像，需要挂代理
```bash
docker run --rm -t -i phusion/baseimage:v0.10.1 /sbin/my_init -- bash -l
```

七. 后续内容 

参考文档 https://yeasy.gitbooks.io/docker_practice/install/centos.html
docker info 命令来查看 docker的信息

推荐使用DaoCloud的镜像来加速

```bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
```

在/etc/docker/daemon.json 文件中加入

```json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```

docker search centos
如果报错, 修改dns为8.8.8.8

八. 扩展工具

安装监控工具 
安装ctop来,对docker所有运行的容器进行监控

```bash
wget https://github.com/bcicen/ctop/releases/download/v0.7.1/ctop-0.7.1-linux-amd64 -O /usr/local/bin/ctop
chmod +x /usr/local/bin/ctop
```

安装compos工具

```bash
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-Linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
curl -L https://raw.githubusercontent.com/docker/compose/1.22.0/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

后续可能需要docker-slim 工具 对docker进行瘦身
docker-gc 进行收集垃圾容器和镜像
watchtower 用来监控镜像

九. 问题和命令举例

register cache 配置，配置成了cache，将不能push

```bash
Meanwhile thats possible:
https://blog.docker.com/2015/10/registry-proxy-cache-docker-open-source/
https://docs.docker.com/registry/recipes/mirror/
But Pushing to such a registry is not supported:
https://docs.docker.com/registry/configuration/#proxy
```

docker 的日志 目前 输入到了 /var/log/message 中了

查看网桥

```bash
brctl show 
```

在运行的容器中执行命令

```bash
docker exec -i -t  mynginx /bin/bash
```

查看镜像所占的空间
```bash
docker system df 
```

保存和加载镜像
```bash
docker save alpine | gzip > alpine-latest.tar.gz
docker load -i alpine-latest.tar.gz
docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'
```

docker run 执行的动作逻辑

1. 检查本地是否存在指定的镜像，不存在就从公有仓库下载
2. 利用镜像创建并启动一个容器
3. 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
4. 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
5. 从地址池配置一个 ip 地址给容器
6. 执行用户指定的应用程序
7. 执行完毕后容器被终止

导出和导入容器快照

```bash
docker export 7691a814370e > ubuntu.tar
cat ubuntu.tar | docker import - test/ubuntu:v1.0
```

管理敏感数据

```bash
openssl rand -base64 20 | docker secret create mysql_password -
docker secret ls
docker service create --name mysql --replicas 1 --network mysql_private --mount type=volume,source=mydata,destination=/var/lib/mysql --secret source=mysql_root_password,target=mysql_root_password --secret source=mysql_password,target=mysql_password -e MYSQL_ROOT_PASSWORD_FILE="/run/secrets/mysql_root_password" -e MYSQL_PASSWORD_FILE="/run/secrets/mysql_password" -e MYSQL_USER="wordpress" -e MYSQL_DATABASE="wordpress" mysql:latest
```

查看docker 容器或镜像的具体信息
```bash
docker image ls --format "{{.ID}}: {{.Size}}"| sort -n | uniq -c
docker inspect -f "{{.Config.Cmd}}" 3929
```

查找docker的进程号:
```bash
docker inspect -f '{{.State.Pid}}' <containerid>
```

查看连接

```bash
sudo nsenter -t <pid> -n netstat | grep ESTABLISHED
```

要解决docker宿主机和容器之间时间不一致的情况,有三种解决方法:

1.  共享主机的localtime. 创建容器的时候指定启动参数，挂载localtime文件到容器内，保证两者所采用的时区是一致的。

```bash
docker run --name <name> -v /etc/localtime:/etc/localtime:ro  ....
```

2. 复制主机的localtime

```bash
docker cp /etc/localtime:【容器ID或者NAME】/etc/localtime
```

在完成后，再通过date命令进行查看当前时间。 
但是，在容器中运行的程序的时间不一定能更新过来，比如在容器运行的mysql服务，在更新时间后，通过sql查看mysql的时间
select now() from dual;
可以发现，时间并没有更改过来。 
这时候必须要重启mysql服务或者重启docker容器，mysql才能读取到更改过后的时间。

3. 通过dockerfile来修改时间

创建自定义的docker文件, 自定义了镜像的时间格式及时区

```bash
FROM redis
FROM tomcat
ENV CATALINA_HOME /usr/local/tomcat
#设置时区
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo 'Asia/Shanghai' >/etc/timezone \
```