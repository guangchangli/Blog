# Docker

[TOC]

## install

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



## 组件｜元素

```
Docker Client：用户和 Docker 守护进程进行通信的接口，也就是 docker 命令。
Docker 守护进程：宿主机上用于用户应答用户请求的服务。
Docker Index：用户进行用户的私有、公有 Docker 容器镜像托管，也就是 Docker 仓库。

Docker 容器：用于运行应用程序的容器，包含操作系统、用户文件和元数据。
Docker 镜像：只读的 Docker 容器模板，简言之就是系统镜像文件。
DockerFile：进行镜像创建的指令文件。
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