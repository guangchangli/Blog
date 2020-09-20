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

## Centos8

```
curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io- 1.2.63.3.fc30.x86_64.rpm

yum install docker-ce
systemctl start docker
vi /etc/docker/daemon.json
{"registry-mirrors": ["http://abcd1234.m.daocloud.io"],"graph": "/opt/docker_file"}
```

## 常用操作

```
启动 Docker
systemctl start docker

重新启动 Docker
systemctl retart docker

开机时自动启动 Docker
systemctl enable docker
[bash systemctl enable docker ¨G2G bash systemctl status docker] not valid
查看 docker 日志
tail -f /var/log/messages
```

## 加速

```
阿里云 https://cr.console.aliyun.com/
修改 daemon 配置文件 /etc/docker/daemon.json
{
  "registry-mirrors": ["https://oinh00fc.mirror.aliyuncs.com"]
}

#graph 指定 docker 文件地址
{
	"registry-mirrors": ["http://abcd1234.m.daocloud.io"],
	"graph": "/opt/docker_file"
	#私有仓库 信任ip
	"insecure-registries":["152.136.207.239:5000"]
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

# 1.镜像管理命令

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
docker pull xxx:latest
#统计时间
time docekr pull xxx:latest 
```

### 提交镜像

```
docker commit container mame[:tag]
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

### 镜像添加标签

```
docker tag busybox:latest busybox:test
```

# 2.容器控制命令

### 创建启动容器

```
docker run -it --name=容器名称 镜像名称:标签 /bin/bash #一次性交互 exit 退出容器
docker run -dt --name=容器名称 镜像名称:标签 cmd # detach 
docker run -d -p 8080:80 --name Nginx nginx
登陆后台启动容器
docker exit -it 容器名称 /bin/bash
```

```
run 									创建容器
-i										运行容器
-d 										detach以后台 daemon 的方式运行
--name      					指定一个容器的名字,后面操作系统通过这个名字你在定位容器
-t										分配一个伪终端，启动后会进入命令行 Allocate a pseudo-TTY
-p 										端口映射
	-P 随机映射
	-p 指定
		hostport:containerport
		ip:hostport:container:port
		ip::conntainerport
		hostport:containerport:|udp
-v 										挂载 volume
-net									指定容器的网络连接类型 bridge / host / none / contriner:<name|id>
ping 114.114.114.114	启动容器执行的命令
```

### 进入容器

```
docker exec -it container_name /bin/bash
```

### 查看运行中的容器

```
docker ps
```

### 查看容器

```
docker ps     	#查看正在运行的
docker ps -a 	  #查看所有
docker ps -l 	  #查看最后一次运行的容器
docker ps -f status=exited # 查看停止的容器
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
docker start container
```

### 重新启动容器

```
docker restart container
```

### 停止容器

```
docker stop container
```

### 杀死容器

```
docker kill container
```

### 删除运行中的容器

```
docker rm -f container
```

### 重命名容器

```
docker rename container container_new
```

### 执行容器中的命令

```
docker exec -it container command
更新容器启动项：docker container update --restart=always nginx
```

### 文件拷贝

```
【复制文件到容器】
docker cp 需要拷贝的文件/目录 容器名：容器目录
docker cp custom.conf Nginx:/etc/nginx/conf.d/
【从容器拷贝文件】
docker cp 容器名:容器目录 需要拷贝的文件或目录
```

### 目录挂载

```
可以通过修改宿主机的文件影响容器
docker run -di -v 宿主机目录:容器目录 --name=containner_name image_name
```

### 查看容器IP地址

```
#查看全部信息
docker inspect 容器名称｜id
 【Networks 结点 IPAddress 查看ip】
#格式化提取f
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_name
```

### 查看容器日志

```
docker logs -f container
```

# 3.镜像迁移备份

### 容器保存为镜像

```
保存新镜像
docker commit container_name new_image_name
```

### 镜像备份

```
docker save -o bak_name container
```



### 镜像恢复

```
docker load -i bak_name
```



# 4.DockerFile

```
vi DockerFile
常用指令

FROM 基础镜像
MAINTAINER 维护者信息
RUN 安装软件
ADD copy 文件，会自动解压
COPY sourcedir/file dest_dir/file 拷贝文件，不会解压
WORKDIR cd 切换工作目录
VOLUME 目录挂载
EXPOSE 内部服务端口
CMD 执行 DockFile 中的命令
ENV 设置环境变量
```

# 5.构建zookeeper

```
FROM centos:8
MAINTAINER gopher_lee


ADD jdk-11.0.8_linux-x64_bin.tar.gz /opt/
ADD zookeeper-3.4.14.tar.gz /opt/

ENV JAVA_HOME /opt/jdk-11.0.8
ENV CLASSPATH .:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH $JAVA_HOME/bin:$PATH

ENV ZK_HOME /opt/zookeeper-3.4.14

ENV CLASSPATH .:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$ZK_HOME/bin

RUN cp $ZK_HOME/conf/zoo_sample.cfg $ZK_HOME/conf/zoo.cfg
VOLUME ["/opt/docker_volumes"]
WORKDIR /opt/zookeeper-3.4.14


EXPOSE 2181

CMD $ZK_HOME/bin/zkServer.sh start-foreground
```

#### dockerfile构建 images

```
docker build -f docker_file_name -t="images_name"
docker build -f zk_file -t="gopher_lee_zk" .
```

#### 启动 zk

```
docker run -d --name zk -p 2181:2181 gopher_lee_zk
进入
docker exec -it zk /bin/bash
```



# 6.docker私有仓库

```
1.拉去私有仓库镜像
	dockeer pull registry
2.启动
	docker run -di --name=registry -p 5000:5000 registry
3.测试
	http://xxx:5000/v2/_catalog
4.修改 daemon.json,信任私有仓库
	"insecure-registries":[xxx:5000]
5.重启
6.地址
	ip:5000/v2/_catalog
```

## 标记上传

```
#标记为私有仓库镜像
docker tag image_name 127.0.0.1:5000/image_name
docker images # image id 一样
docker push 127.0.0.1:5000/image_tag_name
#上传完成就可以看到
ip:5000/v2/_catalog
```



# 7.异常

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

# 附录

## docker run --help

```
[root@gopher2 Dockerfiles]# docker run --help

Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print container ID
      --detach-keys string             Override the key sequence for detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to initialize before starting health-retries countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for unlimited)
      --platform string                Set platform if server is multi-platform capable
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```

