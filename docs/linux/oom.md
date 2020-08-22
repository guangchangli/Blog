# 线上故障排查

## Linux version

```
内核信息
uname －a 
版本信息
cat /proc/version 
系统发行版信息
cat /etc/redhat-release
cat /etc/issue
cpu信息
cat /proc/cpuinfo
查看位数
getconf LONG_BIT  
版本信息
lsb_release -a
```



## cpu

```
ps 找到 pid  jps -l
top -H -p pid
将占用最高的 pid 转换为 16 进制 printf '%x\n' pid 得到 nid
查看堆栈信息
jstack pid | grep 'nid' -C5 -color
```

## GC

```
jstat -gc pid 1000
S0C/S1C、S0U/S1U、EC/EU、OC/OU、MC/MU 
分别代表
两个 Survivor 区、Eden 区、老年代、元数据区的容量和使用量。

YGC/YGT、FGC/FGCT、GCT 
则代表 YoungGc、FullGc 的耗时和次数以及总耗时
```

## 上下文切换

```
vmstat 
cs (context switch) 上下文切换次数
pidstat -w pid
cswch 和 nvcswch 表示自愿及非自愿切换
```

## 磁盘

```
df -hl 磁盘利用率
du -sh * 查看文件夹大小
iostat -d -k -x
rrqpm/s wrqm/s 读写速度
%util 每块磁盘读写程度
哪个进程在进行读写 iotop 拿到 tid 
tid 转换 pid 
readlink -f /proc/*/task/tid/../..t
cat /proc/pid/io 查看进程读写情况

lsof -p pid
```

## 内存

```
free [-m]
内存增长 jcmd  
需要启动参数 -XX:NativeMemoryTracking=summary 或者 
					 -XX:NativeMemoryTracking=detail
```

## Dump

```
jmap -dump:format=b,file=dump.hprof pid
pstree -p pid | wc -l  总体线程
ls -l /proc/pid/task |wc -pt  线程数量
pmap -x pid |sort -rn -k3 |head -30 查看 pid 倒序前30大的内存段

```

```
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
查看 time-wait 和 close_wait 数量
```

