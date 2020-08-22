# RocketMQ

## 操作

### 命令

```java
bin/
1.启动 nameServer
nohup sh mqnamesrv &
  查看日志
  tail -f ~/logs/rocketmqlogs/namesrv.log
2.启动 broker
nohup sh mqbroker -n localhost:9876
  查看日志
  tail -f ~/logs/rocketmqlogs/broker.log
jvm参数调整
runbroker.sh
runserver.sh
关闭
 sh mqshutdown namesrv
 sh mqshutdown broker
```



