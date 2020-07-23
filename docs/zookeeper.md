

# Zookeeper

## 数据模型

``存储处理数据结构``

#### 配置文件

```java
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
  
服务启动
bin/zkServer.sh start
客户端连接
bin/zkCli.sh -server 127.0.0.1:2181

```

## Znode节点类型

```java
持久节点
一旦将节点创建为持久节点，该数据节点会一直存储在 Zookeeper 服务器上
删除持久节点，显式调用 delete 函数
```

```java
临时节点
创建该临时节点的客户端绘画因超时或者发生异常而关闭时，节点被删除
```

```java
有序节点
创建有序节点的时候，zookeeper 服务器会自动使用一个单调递增的数字作为后缀，追加到创建节点的后边
```

3.5.3版本之后新增

```
容器节点
当容器节点下的最后一个字节点被删除时，容器节点就会被自动删除
```

```
TTL节点
针对持久化节点或者持久化有序节点，可以设置一个存活时间
如果在存活时间之内该节点没有任何修改并且没有任何子节点，他就会被自动删除
```

**同一层级目录下吗，节点的名称必须是唯一的**

## 节点特性

```java
节点维护数据
二进制数组
  存储节点的数据，ACL 访问控制信息，子节点数据
  临时节点不允许有子节点，子节点字段为 nul
  每个数据节点还有一个记录自身状态信息的字段 stat /locks
  stat 包含数据变化的时间和版本
```

### 节点状态结构

| 状态属性       | 说明                                   |
| -------------- | -------------------------------------- |
| czxid          | 数据节点被创建时的事物id               |
| mzxid          | 节点最后一次被更新时的事务             |
| pzxid          | 节点的子节点列表最后一次被修改的事务id |
| ctime          | 节点的创建时间                         |
| mtime          | 节点最后被更新的时间                   |
| version        | 数据节点的版本号                       |
| cversion       | 子节点的版本号                         |
| aversion       | 节点的ACL版本号                        |
| ephemeralOwner | 创建该临时节点的会话 sessionID         |
| dataLength     | 数据内容的长度                         |
| numChildren    | 节点的子节点个数                       |

### 版本信息

```java
每个节点有三种类型的版本信息，对数据节点的任何更新操作都会引起版本号的变化
Zookeeper 的版本信息表示的是对节点数据内容、子节点信息或者是 ACL 信息的修改次数
```

## 实现锁

```java
悲观锁
java 创建临时节点（避免锁不释放）
```

```
乐观锁
认为进程对临界资源的竞争不会总是出现，加锁不激烈
在数据进行提交更新的时候，对数据冲突进行检测
发现冲突，拒绝操作

读取 -> 校验 -> 写入

```

**为什么zookeeper 使用绝对路径？**

```java
内部使用 Hash 存储 绝对路径作为 key
```

## Watcher机制

```
zookeeper 提供了一种针对 Znode 的订阅通知机制，当ZNode节点状态发生变化时或者客户端链接状态发生变化时，会出发事件通知
```

- getData()  获取指定节点 value 信息，可以注册监听，当监听的节点进行了创建、修改、删除操作时，会触发相应的事件通知
- getChildren() 用于获取指定节点的所有子节点，并且允许注册监听，当监听节点的子节点进行创建、修改、删除操作时，触发通知
- Exists() 用于判断指定节点是否存在，同样可以注册针对指定节点的监听

```
watcher 事件的触发都是一次性的，客户端必须在收到的事件回调中再次注册事件
```

## 分布式锁

```
分布式解决进程对资源的争抢，临时节点
```

- 获得锁

```
所有客户端去同一个节点下创建一个临时节点，基于同级节点的唯一性，会保证所有客户端中只有一个客户端能够创建成功
只有一个能够获得锁，其他客户端就要通过 watcher 机制监听节点下子节点的变更作出反应
```

- 释放锁

```
1.获得锁的客户端断开和服务端的链接，基于临时节点的特性，节点会被自动删除
2.获得锁的客户端主动删除创建的节点
```

## master选举

```
同级下节点唯一性
同一级节点不能重复创建一个已经存在的节点
如果集群中有三个节点，选举出一个master,这三个节点同时去Zookeeper 服务器上创建一个临时节点 /master-election
只会有一个创建成功，创建成功的节点就成为了master节点
其他没有创建成功的节点针对该节点注册watcher事件，用于监控当前的master机器是否存活
master节点挂了，节点被删除了，其他节点会重新发起matser选举
```

```
临时有序节点

```

## 注册中心原理

**持久化节点-consumer/provider + 临时节点-URL**

## 服务动态上下线感知

```
Dubbo 服务启动时，回去 zookeeper 服务器上 /dubbo/xxx/providers 目录下创建当前服务的 URL
xxx 是发布服务的接口全路径名称
providers 表示服务提供者的类型
dubbo://ip:port 表示该服务器发布的协议类型及访问地址
URL 是临时节点，其他都是持久化节点
如果服务下线了，这个服务器的 URL 地址就会从 Zookeeper 服务器上被移除
```

```
/duboo
	com.xxx.xxx
		/providers
				dubbo://ip:port
				dubbo://ip:port
		/consumers
				dubbo://ip:port
				dubbo://ip:port
				
```

```
服务消费者启动，会对 /dubbo/xxx/providers 节点下的子节点注册 watcher 监听，就可以感知到服务提供方的节点上下线变化，防止请求发送到已经下线的服务器造成访问失败
同时，会在 /dubbo/xxx/consumers 下写下自己的 URL，可以在监控平台上看到某个 dubbo 服务正在被哪些服务调用
服务消费者会对要消费的服务获取到所有可用的 URL 列表进行负载均衡
```

## Dubbo

```
基于临时节点的特性，当服务提供者宕机或者下线时，注册中心会自动删除该服务提供者的信息
注册中心重启时，dubbo 能够自动恢复注册数据以及订阅请求
为了保证节点操作的安全性， Zookeeper 提供了 ACL 权限控制,在 Dubbo可以通过 dubbo.registry.username/dubbo.registry.password 设置节点的验证信息
注册中心默认的跟节点是 /dubbo ,如果需要根据不同的环境设置不同的根节点，可以使用 dubbo.registry.group 修改跟节点名称
```

## 数据恢复

```

```

## 日志

``集群进行大量数据读写会产生大量的事务日志``

```
事务日志 dataLogDir
快照日志 dataLog
log4j 日志
```

```
事务日志 二进制文件
zookeeper系统在正常运行过程中，针对所有的更新操作
在返回客户端“更新成功”的响应前，zookeeper会保证已经将本次更新操作的事务日志已经写到磁盘上
只有这样，整个更新操作才会生效。
```

```
zookeeper快照文件的命名规则为snapshot.**，其中**表示zookeeper触发快照的那个瞬间，提交的最后一个事务的ID。
```

```
在zookeeper 3.4.0以后
zookeeper提供了自动清理snapshot和事务日志功能
通过配置zoo.cfg下的autopurge.snapRetainCount和autopurge.purgeInterval这两个参数实现日志文件的定时清理
autopurge.snapRetainCount这个参数指定了需要保留的文件数目，默认保留3个；
autopurge.purgeInterval这个参数指定了清理频率，单位是小时，需要填写一个1或者更大的数据，默认0表示不开启自动清理功能。
```

## Zoo.cfg

```
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/var/lib/zookeeper
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
```

- titkTime

```
客户端与服务器或者服务器与服务器之间维持心跳，也就是每个tickTime时间就会发送一次心跳
通过心跳不仅能够用来监听机器的工作状态，还可以通过心跳来控制Flower跟Leader的通信时间，默认情况下FL的会话时常是心跳间隔的两倍。
```

- initLimit

```
集群中的follower服务器(F)与leader服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量）
```

- syncLimit

```
集群中flower服务器（F）跟leader（L）服务器之间的请求和答应最多能容忍的心跳数 
```

- 在zoo.cfg这个文件中，配置集群信息是存在一定的格式：service.N =YYY： A：B

```
N：代表服务器编号（也就是myid里面的值）
YYY：服务器地址
A：表示 Flower 跟 Leader的通信端口，简称服务端内部通信的端口（默认2888）
B：表示 是选举端口（默认是3888）
eg
server.1=hadoop05:2888:3888
server.2=hadoop06:2888:3888
server.3=hadoop07:2888:3888
```

- clientPort

```
客户端连接的接口，客户端连接zookeeper服务器的端口，zookeeper会监听这个端口，接收客户端的请求访问！这个端口默认是2181
```

## myId

## 复制模式

## 集群模式