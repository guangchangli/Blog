# Linux

## vim

```
ngg  跳转
gg   文件首行
G    文件最后一行
H 	 屏幕首行
M 	 屏幕中间
L		 屏幕最后
$    行尾
^ 	 行首
:n enter
:nu 行号  
:set nu 显示行号
:set nonu 不显示行号
永久行号
修改 
	/etc/.vimrc
	～/.vimrc set number
ctrl u 清除当前行
ctrl l 清屏
```

```
k 上移
j 下移
h 左移
l 右移
ctrl f 上一页
ctrl b 下一页
* 当前下一个
# 当前下一个
gg 
```

```
/str 正向搜索
n 	 跳转到下次
?str 反向搜索
N    跳转到上次
```

## iterm2

```
跳转
ctrl a 行首
ctrl e 行尾
command k/r 清屏 ctrl l
ctrl u 清除当前行
从光标处删到行首/尾 ctrl w/k
command alt / 最近打开记录
command option 矩形选中
command shift h 打开最近记录
command shift B 时间
command + t	新建标签
command + w	关闭标签
command + 数字 command + 左右方向键	切换标签
command + enter	切换全屏
command + f	查找
command + d	垂直分屏
command + shift + d	水平分屏
command + option + 方向键 command + [ 或 command + ]	切换屏幕
command + ;	查看历史命令
command + shift + h	查看剪贴板历史
ctrl + u	清除当前行
ctrl + l	清屏
ctrl + a	到行首
ctrl + e	到行尾
ctrl + f/b	前进后退
ctrl + p	上一条命令
ctrl + r	搜索命令历史
```

## basic Command

```
head 显示文件头 10 行
tail 显示文件尾部 10 行
diff {f1} {f2} 比较文件内容
wc 统计文件有多少行、多少单词、多少字母
stat {f} 显示文件详情
grep str {f} 匹配文件中字符
```

```
cd - 回到之前的目录 - 【==】 $OLDPWD
mkdir -p  级联创建
【下面的暂时没明白】
pushd 目录压榨并进入新目录
popd 弹出斌进入栈顶的目录
dirs 列出当前目录栈
	-v 每行显示一条记录，同时展示该记录在栈中的index
	-c 清空目录栈
	-p 每行显示一条记录
```

```
ssh user@host 远程登录
scp {f} user@host:path 拷贝文件到远程主机
scp user@host:path dest 从远程主机拷贝文件
uptime 查看系统启动时间
date 	显示时间
cal 显示日历
vmstat 显示内存和cpu使用情况
free -g/m 显示内存和交换区
sz 发送文件到终端 send
rz 接受终端发送来的文件
```



## search

```
find  path -iname  file ["str*"]
mdfind -name str
mdfind -onlyin path str 路径下所有
```

```
df [-g] 文件系统使用
du  当前文件
df -h
```

## port

```
lsof -i tcp:8080 
sudo lsof -i -P | grep -i "listen"
```

## link

```
ln –s 源文件 目标文件  软链接 生成映像创建快捷方式
ln 源文件 目标文件     硬链接 创建一个文件大小相同的文件 
软链接和硬链接文件同步变化
无论是修改源文件还是软连接还是硬链接【软连接也是可以修改的】,都会同步变化
硬链接通过索引节点进行连接，磁盘分区的文件都有一个索引节点号【Inode Index】,硬链接就是多个文件指向同一个索引节点
ls -li 可以看到硬链接和软连接的 Inode Index 是一样的
```

## grep

```
grep 【-v 不包含 -i 忽略大小写】 abc xxx.log 
```

## tail

```
tail -f file 监听文件变化
tail -n xxx file 输出文件的倒数第 xxx 行
```

## top

```
top  P cpu 排序 
top -p 进程号
```

## lsof

```
列出当前系统打开文件，访问网络连接
lsof -i:port
```

## nc

```
探测端口
扫描 ip:port 的 tcp 连接
nc -vz ip port
扫描 ip:port 的 udp 连接
nc -uz ip port
nc -vz www.baidu.com 443
```

## netstat

```
显示 ip tcp udp icmp 统计数据,检验端口网络连接
netstat -t | -u
```

## 磁盘占用

```
du -sh * | sort -nr 当前目录
lsof | grep delete 查看当前系统没有删除未释放的文件描述符
```

## 查找

```
find 查找目录 -name \*.后缀 文件
find / -name xxx -d 查找文件
locate \*.log 执行前 locate updatedb
```

## tar

```
tar -zcvf pack.tar.gz pack/  #打包压缩为一个.gz格式的压缩包
tar -jcvf pack.tar.bz2 pack/ #打包压缩为一个.bz2格式的压缩包
tar -Jcvf pack.tar.xz pack/  #打包压缩为一个.xz格式的压缩包
```

```
tar -zxvf pack.tar.gz /pack  #解包解压.gz格式的压缩包到pack文件夹
tar -jxvf pack.tar.bz2 /pack #解包解压.bz2格式的压缩包到pack文件夹
tar -Jxvf pack.tar.xz /pack  #解包解压.xz格式的压缩包到pack文件夹
```

```
tar
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。
-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
```

```
1、*.tar 用 tar -xvf 解压
2、*.gz 用 gzip -d或者gunzip 解压
3、*.tar.gz和*.tgz 用 tar -xzf 解压
4、*.bz2 用 bzip2 -d或者用bunzip2 解压
5、*.tar.bz2用tar -xjf 解压
6、*.Z 用 uncompress 解压
7、*.tar.Z 用tar -xZf 解压
8、*.rar 用 unrar e解压
9、*.zip 用 unzip 解压
```

## cut

```
cut -b list [-n] [file...]
cut -c list [file...]
cut -f list [-d delim] [-s] [file...]

-d 表示字节 byte
-c 表示字符 char
-f 表示字段 field
-d 表示分隔符 默认 tab delimeter
-n 具体数字 
list 表示范围 从 1 开始 -b1-3 从 1 到 3 字节
cut -d: -f1  /etc/passwd 分隔符为: 取出第一个字段
cut -c1-8 /etc/passwd 截取每行的第 1-8 个字
```

## awk

```

```

## 流量统计vnstat

```shell
vnstat 服务器流量统计
vnstat -d 统计天
vnstat -m 统计月
rx 接受流量 tx 发送流量
【install】
yum install epel-release -y && yum install -y vnstat  
1.创建监控数据库
	vnstat -u -i eth0
2.启动服务并设置开机启动
	service vnstat start
 	chkconfig vnstat on
 
```

##  实时流量iftop

```
yum install -y iftop
iftop -i eth0
查看特定ip 带宽访问情况 
iftop -i eth0 -B -F ip 单位是Byte
"TX"：从网卡发出的流量
"RX"：网卡接收流量
"TOTAL"：网卡发送接收总流量
"cum"：iftop开始运行到当前时间点的总流量
"peak"：网卡流量峰值
"rates"：分别表示最近2s、10s、40s 的平均流量
```

## vmstat

```
vmstat 2 1 第一个参数是采样的时间间隔，第二个参数是采样的次数
r 表示运行队列 进程队列
b 阻塞进程
swpd 虚拟内存使用大小 >0 表示无力内存不足
free 空闲的物理内存
buffer 缓存
cache 缓冲
sl 每秒从磁盘读入虚拟内存大小 
so
bl
bo
cs
us
sy
id
wt

```



## 系统显示

```
uname 
 -a  全部信息
 -m  架构名
 -n  网络主机名
 -r  操作系统内核发行号
 -s  操作系统名
hostname
	-s 短主机名
	-i ip
	-f 长主机名
	-d DNS
cat /etc/redhat-release 查看操作系统环境
cat /proc/cpuinfo  查看 cpu 配置信息  sublings 逻辑核心数 cpu cores 物理核心数
fdisk -l 查看所有分区
swapon -s 查看所有交换分区
route -n 查看路由表
netstat -lntp 查看所有监听的端口
netstat -antp 查看所有建立的连接
netstat -s 查看网络统计信息进程
ethtool [etho] 查看网卡带宽
```

## 用户相关

```
w 查看活动用户
id 查看周定用户信息
last 查看用户登陆日志
cut -d: -f1 /etc/passwd 所有用户名
```



## libc glibc glib

```
glibc libc 都是 linux 的 c 函数库‘
libc 是 ANSI c 函数库  标准库
glibc 是 GUN c 函数库  基于标准库实现，封装
libc 泛指符合实现了 c 标准规定的内容
查看版本
【ldd  --version】
```



## /etc/profile(global)

```
登录Linux时，首先启动 /etc/profile 文件
然后再启动用户目录下的 ~/.bash_profile、 ~/.bash_login 或 ~/.profile文件中的其中一个
执行的顺序为：
~/.bash_profile、
~/.bash_login、
~/.profile
如果 ~/.bash_profile文件存在的话，一般还会执行 ~/.bashrc文件，因为有下面的 shell 
```

```shell
if [ -f ~/.bashrc ] ; then
　. ./bashrc
　　　　　　　　　　　fi
　　~/.bashrc中，一般还会有以下代码：
if [ -f /etc/bashrc ] ; then
　. /etc/bashrc
fi
```

```
所以，~/.bashrc会调用 /etc/bashrc文件。最后，在退出shell时，还会执行 ~/.bash_logout文件
```

```
/etc/profile -> 
( ~/.bash_profile | ~/.bash_login | ~/.profile )-> 
~/.bashrc -> 　　　　　　　　　
/etc/bashrc -> 
~/.bash_logout
```

```
（1）/etc/profile： 此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行. 并从/etc/profile.d目录的配置文件中搜集shell的设置。

（2）/etc/bashrc: 为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取。

（3）~/.bash_profile: 每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。

（4）~/.bashrc: 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。

（5）~/.bash_logout: 当每次退出系统(退出bash shell)时,执行该文件. 另外,/etc/profile中设定的变量(全局)的可以作用于任何用户,而~/.bashrc等中设定的变量(局部)只能继承/etc /profile中的变量,他们是"父子"关系。

（6）~/.bash_profile 是交互式、login 方式进入 bash 运行的~/.bashrc 是交互式 non-login 方式进入 bash 运行的通常二者设置大致相同，所以通常前者会调用后者。
```

## /etc/rc.d/

## 文件类型

```
d 目录
- 文件
l 链接
b 表示为装置文件里面的可供存储的接口设备 可随机存取装置
c 表示为装置文件里面的串行端口设备
```

## 刷新dns

```
yum install -y nscd
service nscd restart
```

## scp-免密登陆

```
A scp -> B
1.A 服务器生成 公私密钥 
	ssh-keygen -t rsa -P ''
2.重命名避免冲突 id_rsa.pub 拷贝到 B 服务器 /root/.ssh/
3.将 A.pub 导入 authorized_keys cat A.pub >> authorized_keys
4.修改权限 chmod 600 authorized_keys
```

## 主机名

```
centos7
1.static hostname
	静态主机名【内核主机名】，系统启动时从 /etc/hostname 自动初始化的主机名
2.tansient hostname
	瞬态主机名【临时分配】,通过 DHCP 或 mDNS 服务器分配
3.pretty hostname
	别名主机，展示给终端用户
```

```
centos 7，hostnamectl 命令行工具，允许查看、修改与主机相关的配置
```

### 查看主机名

```
hostnamectl [status]							查看当前主机名
hostnamectl --[static|transient|pretty]		
```

### 修改主机名

#### 临时修改

```
hostname xxx 重启后恢复
```

#### 永久生效

```
1.hostnamectl set-hostname xxx
修改静态主机名
【hostnamectl --static set-hostname xxx】 不需要重启
删除 hostname
hostname set hostname "" --[static|pretty]
更新 /etc/hosts
127.0.0.1 xxx
::1 			xxx
```

## 防火墙

### 查看服务状态

```
systemctl status firewalld
```

### 查看 firewall 状态

```
firewall-cmd --state
```

### 开启、重启、关闭 firewalls.service 服务

```
【开启】
service firewalld start
【重启】
service firewalld restart
【关闭】
service firewalld stop
```

### 查看防火墙规则

```
firewall-cmd --list-all
firewall-cmd --zone=public --list-ports 
```

### 查询、开放、关闭端口

```
【查询端口是否开放】
	firewall-cmd --query-port=8080/tcp
【开放端口】
	firewall-cmd --permanent --add-port=80/tcp
【移除端口】
	firewall-cmd --permanent --remove-port=80/tcp
【重启防火墙】
	firewall-cmd --reload
【-----】
	firewall-cmd 是 linux 提供的操作 firewall 工具
	--permanent 表示持久，重启后有效
	--add-port 标识端口
```

## 日志 /var/log/

```
1)【/var/log/secure】：记录登录系统存取数据的文件;
			例如pop3，ssh，telnet，ftp等都会记录在此.
2)【/ar/log/wtmp】：记录登录这的信息记录，被编码过，所以必须以last解析;
3)【/var/log/messages】:session 记录;
4)【/var/log.boot.log】：记录一些开机或者关机启动的一些服务显示的启动或者关闭的信息;
5)【/var/log/maillog】：记录邮件的存取和往来;
6)【/var/log/cron】：用来记录crontab服务的内容;
8)【/var/log/acpid ,   ACPI - Advanced Configuration and Power Interface】，表示高级配置和电源管理接口。
			后面的 d 表示 deamon 。 acpid 也就是 the ACPI event daemon 。 也就是 acpi 的消息进程。用来控制、获取、管理 acpi 的状态的服务程序。
9)【/var/run/utmp】 记录着现在登录的用户;
10)【/var/log/lastlog】 记录每个用户最后的登录信息;
11)【/var/log/btmp】 记录错误的登录尝试;
12)【/var/log/dmesg】内核日志;
13)【/var/log/cpus CPU】的处理信息；
14)【/var/log/syslog】 事件记录监控程序日志；
15)【/var/log/auth.log】 用户认证日志；
16)【/var/log/daemon.log】 系统进程日志；
17)【/var/log/mail.err】 邮件错误信息；
18)【/var/log/mail.info】 邮件信息；
19)【/var/log/mail.warn】 邮件警告信息；
20)【/var/log/daemon.log】 系统监控程序产生的信息;
21)【/var/log/kern】 内核产生的信息;
22)【/var/log/lpr】   行打印机假脱机系统产生的信息;
```

## Crond 

**周期性处理任务的守护进程**

#### 系统任务调度

```
【/etc/crontab】
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin 
MAILTO=root #任务执行信息发送给 root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

```
星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。
```



#### 用户任务调度

```
【/etc/cron.deny】 禁用用户列表
【/var/spool/cron/】 用户名命名文件为用户自定义任务调度信息，一行一个任务

```

#### crond

```
service crond status
service crond start
service crond stop
service crond reload
service crond restart
chkconfig –level 35 crond on # 加入开机启动
```

#### crontab

```
crontab [-u user] file # 指定文件作为任务列表文件加入 crontab，不指定将接受输入参数作为文件
crontab [-u user] [ -e | -l | -r ]
```

