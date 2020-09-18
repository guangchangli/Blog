# Redis

## Install

```
centos 8 ,redis 6
yum install -y gcc
yum install -y tcl
tar x
cd redis-6*
make MALLOC=libc  //避免提示找不到 jemalloc/jemalloc.h
make test 				//\o/ All tests passed without errors!
```

```
make /opt/redis6
cd /opt/redis6
/opt/redis6 mkdir bin
cp /source/redis-6*/src/redis-cli .
cp /source/redis-6*/src/redis-server .
c
/opt/redis6 mkdir conf
cp /source/redis-6*/redis.conf
```

```
vi redis.conf
daemonize yes
maxmemory 128MB 

/bin/redis-server redis.conf
netstat -anp | grep 6379
```

## 配置Service 启动

```
vim /lib/systemd/system/redis.service
< == > /etc/systemd/system/multi-user.target.wants/redis.service 【ln -s】
[Unit]
Description=Redis 	 #描述服务
After=network.target #描述服务在哪些基础服务启动后再启动 网络服务启动之后再启动

[Service]
Type=forking			   #是最简单和速度最快的选择
ExecStart=/opt/redis6/bin/redis-server /opt/redis6/bin/redis.conf #为启动服务的具体运行命令
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/opt/redis6/bin/redis-cli -h 127.0.0.1 -p 6379 shutdown  #停止命令
PrivateTmp=true																										#表示给服务分配独立的临时空间
[Install]
WantedBy=multi-user.target																				
#运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3 0-6

```

```
systemctl daemon-reload # 【重载系统服务】
启动
systemctl start redis    
查看状态
systemctl status redis
使开机启动
systemctl enable redis
查看是否开机启动
systemctl list-unit-files|grep redis
```

## 配置

```
redis.conf
1. protected-mode no #默认 yes 内网访问
2. bind 	# 默认 bind 127.0.0.1
3. daemonize yes # 默认 no 
4. requirepass xxx 设置密码
```



## 常用命令

```java
info all|default
info replication 查看状态
bin/redis-server -v 查看版本
```

### 服务器信息

```java
server : 一般 Redis 服务器信息，包含以下域：

redis_version : Redis 服务器版本
redis_git_sha1 : Git SHA1
redis_git_dirty : Git dirty flag
os : Redis 服务器的宿主操作系统
arch_bits : 架构（32 或 64 位）
multiplexing_api : Redis 所使用的事件处理机制
gcc_version : 编译 Redis 时所使用的 GCC 版本
process_id : 服务器进程的 PID
run_id : Redis 服务器的随机标识符（用于 Sentinel 和集群）
tcp_port : TCP/IP 监听端口
uptime_in_seconds : 自 Redis 服务器启动以来，经过的秒数
uptime_in_days : 自 Redis 服务器启动以来，经过的天数
lru_clock : 以分钟为单位进行自增的时钟，用于 LRU 管理
clients : 已连接客户端信息，包含以下域：

connected_clients : 已连接客户端的数量（不包括通过从属服务器连接的客户端）
client_longest_output_list : 当前连接的客户端当中，最长的输出列表
client_longest_input_buf : 当前连接的客户端当中，最大输入缓存
blocked_clients : 正在等待阻塞命令（BLPOP、BRPOP、BRPOPLPUSH）的客户端的数量
memory : 内存信息，包含以下域：

```

```java
used_memory : 由 Redis 分配器分配的内存总量，以字节（byte）为单位
used_memory_human : 以人类可读的格式返回 Redis 分配的内存总量
used_memory_rss : 从操作系统的角度，返回 Redis 已分配的内存总量（俗称常驻集大小）。这个值和 top 、 ps 等命令的输出一致。
used_memory_peak : Redis 的内存消耗峰值（以字节为单位）
used_memory_peak_human : 以人类可读的格式返回 Redis 的内存消耗峰值
used_memory_lua : Lua 引擎所使用的内存大小（以字节为单位）
mem_fragmentation_ratio : used_memory_rss 和 used_memory 之间的比率
mem_allocator : 在编译时指定的， Redis 所使用的内存分配器。可以是 libc 、 jemalloc 或者 tcmalloc 。
```



```java
在理想情况下， used_memory_rss 的值应该只比 used_memory 稍微高一点儿。

当 rss > used ，且两者的值相差较大时，表示存在（内部或外部的）内存碎片。

内存碎片的比率可以通过 mem_fragmentation_ratio 的值看出。

当 used > rss 时，表示 Redis 的部分内存被操作系统换出到交换空间了，在这种情况下，操作可能会产生明显的延迟。

当 Redis 释放内存时，分配器可能会，也可能不会，将内存返还给操作系统。

如果 Redis 释放了内存，却没有将内存返还给操作系统，那么 used_memory 的值可能和操作系统显示的 Redis 内存占用并不一致。

查看 used_memory_peak 的值可以验证这种情况是否发生。
```



```java
persistence : RDB 和 AOF 的相关信息

stats : 一般统计信息

replication : 主/从复制信息

cpu : CPU 计算量统计信息

commandstats : Redis 命令统计信息

cluster : Redis 集群信息

keyspace : 数据库相关的统计信息
```

```
> info server

# Server
redis_version:3.0.6 --redis服务版本
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:3edbb92b537d6123
redis_mode:cluster --运行模式，分为:Cluster,Sentinel,Standalone
os:Linux 3.10.0-327.el7.x86_64 x86_64 --Redis所在机器的操作系统
arch_bits:64 --架构(32或64位)
multiplexing_api:epoll --redis所使用的事件处理机制
gcc_version:4.1.2 --编译Redis时所使用的GCC版本
process_id:13902 --Redis服务进程的PID
run_id:4bd3b3cde098b2b8e952f5ba77ee0322d69a9eb2 --服务的标识符
tcp_port:8029 --监听端口
uptime_in_seconds:24321169 --自Redis服务启动以来，运行的秒数
uptime_in_days:281 --自Reids服务启动以来，运行的天数
hz:10 --serverCron 每秒运行次数
lru_clock:7513980 --以分钟为单位进行自增的时钟，用于LRU管理
config_file:/opt/app/redisCluster/8029/./redis_cluster/8029/redis-8029.conf --Redis的配置文件


> info clients
# Clients
connected_clients:1375 --当前客户端连接数
client_longest_output_list:0 --当前所有缓冲区中队列对象个数的最大值
client_biggest_input_buf:2 --当前所有输入缓冲区中占用的最大容量
blocked_clients:0 --正在等待阻塞命令(例如BLPOP等)的客户端数量

> info memory
# Memory
used_memory:3349912688 --Redis分配器分配的内存总量，也就是内部存储的所有数据内存占有量
used_memory_human:3.12G  --以可读的格式返回used_memory
used_memory_rss:6814093312 --从操作系统的角度，Redis进程占用的物理内存总量
used_memory_peak:5727005064 --内存使用的最大值，表示used_memory的峰值
used_memory_peak_human:5.33G --以可读的格式返回used_memory_peak
used_memory_lua:36864 --Lua引擎所消耗的内存大小
mem_fragmentation_ratio:2.03 --used_memory_rss/used_memory比值，表示内存碎片率
mem_allocator:jemalloc-3.6.0 --Redis所使用的内存分配器。默认为:jemalloc

> info persistence
# Persistence
loading:0  --是否在加载持久化文件。0否，1是
rdb_changes_since_last_save:1224395935 --自上次RDB，Redis数据改动条数
rdb_bgsave_in_progress:0 --标识RDB的bgsave操作是否进行中。0否，1是
rdb_last_save_time:1493145644 --上次bgsave操作的时间戳
rdb_last_bgsave_status:ok --上次bgsave操作状态
rdb_last_bgsave_time_sec:29 --上次bgsave使用的时间
rdb_current_bgsave_time_sec:-1 --如何bgsave操作正在进行，则记录当前bgsave操作使用的时间(单位是秒)
aof_enabled:0 --是否开启了AOF功能。0否，1是
aof_rewrite_in_progress:0  
aof_rewrite_scheduled:0 --标识是否将要在RDB的bgsave操作结束后执行AOF rewrite操作
aof_last_rewrite_time_sec:-1 --上次AOF rewrite操作使用的时间(单位是秒)
aof_current_rewrite_time_sec:-1 --如果rewrite操作正在进行，则记录当前AOF rewrite所使用的时间(单位是秒)
aof_last_bgrewrite_status:ok --上次AOF重写操作的状态
aof_last_write_status:ok --上次AOF写磁盘的结果


> info stats
# Stats
total_connections_received:4393241 --连接过的客户端总数
total_commands_processed:1756721337 --执行过的命令总数
instantaneous_ops_per_sec:13 --每秒处理命令条数
total_net_input_bytes:636866047148 --输入总网络流量(以字节为单位)
total_net_output_bytes:1558439303474 --输出总网络流量(以字节为单位)
instantaneous_input_kbps:5.42 --每秒输入字节数
instantaneous_output_kbps:9.89 --每秒输出字节数
rejected_connections:0 --拒绝的连接个数
sync_full:2 --主从完全同步成功次数
sync_partial_ok:3 --主从部分同步成功次数
sync_partial_err:0 --主从部分同步失败次数
expired_keys:64979568 --过期的key数量
evicted_keys:0 --剔除(超过了maxmemory后)的key数量
keyspace_hits:8573735 --命中次数
keyspace_misses:19578062 --不命中次数
pubsub_channels:1 --当前使用中的频道数量
pubsub_patterns:0 --当前使用中的模式数量
latest_fork_usec:63895 --最近一次fork操作消耗的时间(微秒)
migrate_cached_sockets:0 --记录当前Redis正在进行migrate操作的目标Redis个数。例如RedisA 分别向Redis B和C执行migrate操作，那么这个值就是2


> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=172.16.35.76,port=8028,state=online,offset=597361953364,lag=0
slave1:ip=172.16.35.218,port=8010,state=online,offset=597361951155,lag=1
master_repl_offset:597361953577 --主节点偏移量
repl_backlog_active:1 --复制缓冲区状态
repl_backlog_size:67108864 --复制缓冲区尺寸(单位:字节)
repl_backlog_first_byte_offset:597294844714 --复制缓冲区起始偏移量，标识当前缓冲区可用范围
repl_backlog_histlen:67108864 --标识复制缓冲区已存有效数据长度

> info cpu
# CPU
used_cpu_sys:91573.59 --Redis主进程在内核态所占用的CPU时钟总和
used_cpu_user:129416.68 --Redis主进程在用户态所占用的CPU时钟总和
used_cpu_sys_children:5.00 --Redis子进程在内核态所占用的CPU时钟总和
used_cpu_user_children:17.58 --Redis子进程在用户态所占用的CPU时钟总和

> info Commandstats
# Commandstats
cmdstat_get:calls=2,usec=14,usec_per_call=7.00 --get 命令调用总次数，总耗时，平均耗时(单位是毫秒)
cmdstat_set:calls=167609,usec=5659321,usec_per_call=33.77 --set 命令总次数，总耗时，平均耗时(单位是毫秒)

> info cluster
# Cluster
cluster_enabled:1 --节点是否为cluster模式 。1是0否

> info keyspace
# Keyspace
db0:keys=4868218,expires=1265463,avg_ttl=265690817 --当前数据库key总数，带有过期时间的key总数，平均存活时间
```

