---
title: docker命令学习
categories:
  - 技术总结
abbrlink: 1737205767
date: 2015-04-26 21:32:19
tags:
---

1. 后台启动docker服务
```bash
sudo docker -d &
```

2. 下载一个ubuntu镜像
```bash
sudo docker pull ubuntu
```

3. 查看下载的所有docker镜像
```bash
sudo docker images （显示精简镜像ID，带参数-notrunc=true，显示完整镜像ID）
```

4. 查看某个镜像的详细信息
```bash
sudo docker inspect 精简ID
```

5. 显示镜像的层次结构
```bash
sudo docker images -tree
```

6. 通过指定文件（Dockerfile）来创建一个新的容器
```bash
sudo docker build -t vieux/apache:2.0
sudo docker build github.com/creack/docker-firefox
```

7. 提交一个容器
```bash
sudo docker ps -l
sudo docker commit c3f279d17e0a  SvenDowideit/testimage:version3
```

8. 将容器的文件和目录复制到主机中
```bash
sudo docker cp 7bb0e258aefe:/etc/debian_version .
sudo docker cp blue_frog:/etc/hosts .
```

9.  列出更改的容器文件系统文件和目录
```bash
sudo docker diff 7bb0e258aefe
```

10. 从服务器获取实时事件信息
```bash
sudo docker events（监听事件）
```

11. 导出容器系统作为一个tar文档发送到stdout
```bash
sudo docker export red_panda > latest.tar
```

12.创建一个空容器系统镜像和引用压缩文件的内容
```bash
sudo docker import http://example.com/exampleimage.tgz
cat exampleimage.tgz | sudo docker import - exampleimagelocal:new （引用本地文件）
```

13. 显示镜像的历史操作
```bash
sudo docker history docker
```

14. 显示整个容器系统信息
```bash
sudo docker info
```

15. 通过url给镜像容器增加文件
```bash
sudo docker insert 8283e18b24bc https://raw.github.com/metalivedev/django/master/postinstall /tmp/postinstall.sh
06fd35556d7b
```

16. 查看容器底层信息
```bash
sudo docker inspect 60734
sudo docker inspect -format='{{.NetworkSettings.IPAddress}}' $INSTANCE_ID（获取一个容器ip地址）
```

17. 杀死一个正在运行的容器（发送终止信号）
```bash
Usage: docker kill CONTAINER [CONTAINER...]
```

18. 从输入流中加载一个压缩包，恢复包含的所有镜像和tag
```bash
Usage: docker load < ubuntu.tar
```

19. 登陆你在docker服务器注册的账号 index.docker.io
```bash
Usage: docker login [OPTIONS] [SERVER]
docker login localhost:8080
```

20.取出容器的log日志
```bash
Usage: docker logs [OPTIONS] CONTAINER
```

21. 查找私有地址转换到共有地址端口
```bash
Usage: docker port [OPTIONS] CONTAINER PRIVATE_PORT
```

22. 查看正在运行的镜像
```bash
Usage: docker ps [OPTIONS]
-a=false: 查看所有的镜像 默认情况下只查看正在运行的镜像
```

23. 从远程仓库中获取镜像
```bash
Usage: docker pull NAME
```

24. 推送本地镜像到远程仓库
```bash
Usage: docker push NAME
```

25. 重启正在运行的远程仓库
```bash
Usage: docker restart [OPTIONS] NAME
```

26. 删除一个或者所有的容器
```bash
Usage: docker rm [OPTIONS] CONTAINER
```

27. 删除一个或者多个镜像
```bash
Usage: docker rmi IMAGE [IMAGE...]
sudo docker rmi fd484f19954f
sudo docker rmi test2
```

28. run

```bash
Usage: docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]

Run a command in a new container

  -a=map[]: 附加标准输入、输出或者错误输出
  -c=0: 共享CPU格式（相对重要）
  -cidfile="": 将容器的ID标识写入文件
  -d=false: 分离模式，在后台运行容器，并且打印出容器ID
  -e=[]: 设置环境变量
  -h="": 容器的主机名称
  -i=false: 保持输入流开放即使没有附加输入流
  -privileged=false: 给容器扩展的权限
  -m="": 内存限制 (格式: <number><optional unit>, unit单位 = b, k, m or g)
  -n=true: 允许镜像使用网络
  -p=[]: 匹配镜像内的网络端口号
  -rm=false:当容器退出时自动删除容器 (不能跟 -d一起使用)
  -t=false: 分配一个伪造的终端输入
  -u="": 用户名或者ID
  -dns=[]: 自定义容器的DNS服务器
  -v=[]: 创建一个挂载绑定：[host-dir]:[container-dir]:[rw|ro]. 如果容器目录丢失，docker会创建一个新的卷
  -volumes-from="": 挂载容器所有的卷
  -entrypoint="": 覆盖镜像设置默认的入口点
  -w="": 工作目录内的容器
  -lxc-conf=[]: 添加自定义 -lxc-conf="lxc.cgroup.cpuset.cpus = 0,1"
  -sig-proxy=true: 代理接收所有进程信号 (even in non-tty mode)
  -expose=[]: 让你主机没有开放的端口
  -link="": 连接到另一个容器 (name:alias)
  -name="": 分配容器的名称，如果没有指定就会随机生成一个
  -P=false: Publish all exposed ports to the host interfaces 公布所有显示的端口主机接口
```

docker会先创建一个新的可写的容器层来指定镜像，通过指定命令来启动他, docker run相当于运行API/containers/create在/container/(id)/start之前
docker run命令可以用结合docker commit来改变正在运行的容器！

29. 通过标准输出流存储镜像包含所有父层的、tag、version
```bash
Usage: docker save image > repository.tar
```

30. 搜索docker 镜像
```bash
Usage: docker search TERM
```

31. 启动一个停止的容器
```bash
Usage: docker start [OPTIONS] CONTAINER
```

32. 关闭一个容器
```bash
Usage: docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

33. 给镜像仓库添加标签
```bash
Usage: docker tag [OPTIONS] IMAGE REPOSITORY[:TAG]
```

34. 查看容器内运行的进程
```bash
Usage: docker top CONTAINER [ps OPTIONS]
```

35. 显示docker的版本和最后更新版本信息
```bash
sudo docker version
```

36. 等待容器停止，打印出退出编码
```bash
Usage: docker wait [OPTIONS] NAME
```