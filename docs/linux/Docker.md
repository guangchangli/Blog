# Docker

[TOC]

## install-1

```
https://download.docker.com/linux/static/stable/x86_64/
tar -zxvf 
cp docker/* /usr/bin/
/etc/systemd/system/ docker.service docker 注册服务 
chmod +x /etc/systemd/system/docker.service 
systemctl daemon-reload  重新加载文件
systemctl start docker 启动
systemctl enable docker.service 开机启动
systemctl status docker 查看状态
```

```
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
  
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd --selinux-enabled=false --insecure-registry=ip
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
  
[Install]
WantedBy=multi-user.target
```

## install-2

```
su root
1.安装必要系统工具
	yum install -y yum-utils device-mapper-persistent-data lvm2
2.添加软件源信息
	yum-config-manager --add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
3.更新、安装 CE
	yum makecache fast
	yum -y install docker-ce
```

## 常用操作

```
启动 Docker
systemctl start docker

重新启动 Docker
systemctl retart docker

开机时自动启动 Docker
bash systemctl enable docker ¨G2G bash systemctl status docker
```

## 加速

```
阿里云 https://cr.console.aliyun.com/
修改 daemon 配置文件 /etc/docker/daemon.json
{
  "registry-mirrors": ["https://oinh00fc.mirror.aliyuncs.com"]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```



## 组件｜元素

```
Docker Client：用户和 Docker 守护进程进行通信的接口，也就是 docker 命令。
Docker 守护进程：宿主机上用于用户应答用户请求的服务。
Docker Index：用户进行用户的私有、公有 Docker 容器镜像托管，也就是 Docker 仓库。

Docker 镜像：只读的 Docker 容器模板，简言之就是系统镜像文件，提供运行环境
Docker 容器：用于运行隔离应用程序的容器，包含操作系统、用户文件和元数据。
DockerFile：进行镜像创建的指令文件。
【容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响】
【镜像本身是只读的，容器从镜像启动时，Docker 在镜像的上层创建一个可写层，镜像本身不变】
Docker 仓库，存放镜像文件
```



### 1.image

```
docker images
docker pull <image name>
```



### 2.container

```
docker ps 【-a】
docker exec -it 4  (exit)
docker [container] stop CONTAINER_ID
docker rm CONTAINER_ID
```





### 3.repository





docker exec -it b0 bash  b0是容器的唯一id  bash 表示要在容器中执行的命令。

启动的容器

docker ps



docker stop id



Docker restart

## 镜像管理命令

### **docker images**

```
repostory 镜像来自哪个仓库
tag 			镜像的标签信息，版本信息
image id  镜像创建时的 id
created	  镜像创建的时间
size	  	镜像文件大小
```

### 搜索镜像

```
docker search imageName
name 			  		仓库名称
description 		镜像描述
stars						用户评价
official 				是否官方
automated				自动构建，Docker Hub 自动构建流程创建
```



### 下载镜像

```
docker  xxx:latest
```

### 导出镜像

```
docker save xxx > xxx.tar
```

### 删除镜像

```
docker rmi xxx:latest
```

### 导入镜像

```
docker load < xxx.tar
```

### 更改镜像名

```
docker tag busybox:latest busybox:test
```

## 容器管理命令

### 运行容器

```
docker run -d --name=busybox busybox:latest ping 114.114.114.114
```

```
run 									启动容器
-d 										以后台 daemon 的方式运行
--name      					指定一个容器的名字,后面操作系统通过这个名字你在定位容器
busybix:latest 				容器所使用的镜像名字
ping 114.114.114.114	启动容器执行的命令
```

### 查看运行中的命令

```
docker ps
```

### 查看所有容器

```
docker ps -a
```

```
container id 			容器启动的id
image						  使用的镜像
command 					启动容器的命令
created						创建时间
status				    启动时间
ports							容器映射到宿主机的端口
names							容器启动的名字
```

### 启动容器

```
docker start busybox
```

### 重新启动容器

```
docker restart busybox
```

### 停止容器

```
docker stop busybox
```

### 杀死容器

```
docker kill busybox
```

### 删除运行中的容器

```
docker rm -f busybox
```

### 执行容器中的命令

```
docker exec -it busybox ls
```

### 复制容器内文件

```
docker cp busybox:/etc/hosts hosts
```

### 查看容器日志

```
docker logs -f busybox
```

# 异常

## IPV4

```
WARNING: IPv4 forwarding is disabled. Networking will not work.
```

```
# vi /etc/sysctl.conf
或者
# vi /usr/lib/sysctl.d/00-system.conf
添加如下代码：
net.ipv4.ip_forward=1

重启network服务
# systemctl restart network

查看是否修改成功
# sysctl net.ipv4.ip_forward
```

