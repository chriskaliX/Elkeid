[![License](https://img.shields.io/badge/License-Apache%20v2-blue.svg)](https://github.com/bytedance/Elkeid/blob/main/agent/LICENSE)
[![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)

[English](README.md) | 简体中文
## 后台架构图

<img src="docs/server.png"/>

## 概述
Elkeid 后台大体包含4个模块：
1. AgentCenter(AC)，负责与Agent进行通信，采集Agent数据并简单处理后汇总到消息队列集群，同时也负责对Agent进行管理包括Agent的升级，配置修改，任务下发等。同时AC也对外提供HTTP接口，Manager通过这些HTTP接口实现对AC和Agent的管理和监控。
2. ServiceDiscovery(SD)，后台中的各个服务模块都需要向SD中心定时注册、同步服务信息，从而保证各个服务模块中的实例相互可见，便于直接通信。由于SD维护了各个注册服务的状态信息，所以当服务使用方在请求服务发现时，SD会进行负载均衡。比如Agent请求获取AC实例列表，SD直接返回负载压力最小的AC实例。
3. Manager，负责对整个后台进行管理并提供相关的查询、管理接口。包括管理AC集群，监控AC状态，控制AC服务相关参数，并通过AC管理所有的Agent，收集Agent运行状态，往Agent下发任务，同时manager也管理实时和离线计算集群。
4. 实时/离线计算模块，消费AC采集到消息队列中的数据，进行实时和离线的分析与检测。（此部分还未开源）

简单来说就是AgentCenter收集Agent数据，实时/离线计算模块对这些数据进行分析和检测，Manager管理着AgentCenter和这些计算模块，ServiceDiscovery把这些所有的服务、节点都串联了起来。

## 功能特点
- 百万级Agent的后台架构解决方案
- 分布式，去中心化，集群高可用
- 部署简单，依赖少，便于维护

## 部署文档
- [快速部署文档](docs/quick-start-zh_CN.md)
- [docker体验文档](docs/docker-install-zh_CN.md)

## API接口文档
Manger API通过token做鉴权，所有接口调用前都需要先用 /api/v1/user/login 接口，获取到token。
```
curl --location --request POST 'http://127.0.0.1:6701/api/v1/user/login' \
--data-raw '{
    "username": "hids_test",
    "password": "hids_test"
}'


{
    "code": 0,
    "msg": "success",
    "data": {
        "token": "xxxxxxxxxxx"
    }
}
```
再将登陆token加到每次请求的header中。
```
curl --location --request GET 'http://127.0.0.1:6701/api/v1/agent/getStatus'  -H "token:xxxxxxxxxxx"
```
详情请参考[API接口文档](https://documenter.getpostman.com/view/9865152/TzCTZ5Do#intro)

## QA
- [Q&A](docs/qa.md)

## License
Elkeid Server are distributed under the Apache-2.0 license.
