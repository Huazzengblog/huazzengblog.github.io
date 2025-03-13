---
title: cloud native 
date: 2025-03-13 00:45:00 +/-TTTT
categories: 25spring
tags: [25spring]
---
# 云原生
## docker
* how to use docker
  * [FDUCSLG.Dokerguide](https://comp101.fducslg.com/tools/docker)
* docker compose
  在 `docker-compose.yml` 文件中，常见的元素和字段有很多，下面是一些主要的元素和字段的列举：

  1. version
  - 用于指定 Docker Compose 文件的版本。

  2. services
  - 定义容器服务。

  3. volumes
  - 定义 Docker Compose 中使用的卷（Volumes）。

  4. networks
  - 定义 Docker Compose 中的网络配置。

  5. build
  - 指定服务的构建配置。

  6. image
  - 用于指定要使用的 Docker 镜像。

  7. ports
  - 定义容器与主机之间的端口映射。

  8. environment
  - 用于设置环境变量。

  9. command
  - 用于覆盖容器的默认命令。

  10. depends_on
  - 用于指定服务之间的依赖关系。

  11. restart
  - 用于指定容器崩溃后的重启策略。

  12. logs
  - 定义日志选项。

  13. extra_hosts
  - 为容器添加额外的主机名映射。


  14.  secrets
  - 用于存储敏感数据。

  15.  healthcheck
  - 用于定义健康检查。
## k8s
![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/cloud/k8s.png)
### 控制平面组件
> 控制平面组件会为集群做出全局决策，比如资源的调度。 以及检测和响应集群事件

