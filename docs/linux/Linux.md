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

