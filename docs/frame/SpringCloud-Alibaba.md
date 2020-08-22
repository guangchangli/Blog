# SpringCloud-Alibaba

## Nacos

- 服务发现和服务健康检测

```
Nacos 支持基于 DNS 和基于 RPC 的服务发现，服务提供者使用原生 SDK 、OpenApi 或独立的 A gent TODO 注册 service 后，服务消费者可以使用 DNS 或 HTTP&API 查找和发现服务
```

```
Nacos 提供对服务的实时健康检测，阻止向不健康的主机或的服务实例发送请求
Nacos 支持传输层(PING 或 TCP)和应用层（HTTP、MySQL、用户自定义）的健康检测
对于复杂的云环境和网络拓扑环境中（VPC，边缘网络）服务的健康检测
```

```
Nacos 提供了 agent 上报和服务端主动检测的两种检查模式，和统一的健康检查仪表盘
```

- 动态配置服务

```
外部化、动态化的方式高效快捷管理所有环境的应用
```

- 动态 DNS 服务
- 服务及其元数据管理

## Install

``单机/集群/多集群``

**ip:8848/nacos   nacos/nacos**

## API

```
服务列表
http://localhost:8848/nacos/v1/ns/service/list?pageNo=1&pageSize=10
curl -X PUT 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=example&ip=127.0.0.1&port=8801'
```

[Nacos API](https://nacos.io/zh-cn/docs/open-api.html)

