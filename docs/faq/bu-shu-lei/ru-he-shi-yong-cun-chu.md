# 如何使用存储

TKEStack 没有提供存储服务，Global集群中的镜像仓库、ETCD、InfluxDB等数据组件，均使用本地磁盘存储数据。如果您需要使用存储服务，建议使用[ROOK](https://rook.io/)或者[chubaoFS](https://chubao.io/)，部署一套容器化的分布式存储服务。

