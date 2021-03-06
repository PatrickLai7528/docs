# 常见报错解决方案

安装过程中的错误主要集中在硬件和软件配置上。

* 首先检查Global节点是否满足**8核16G内存，100G系统盘**的要求。
* 其次仔细检查每个节点的硬件和软件需求：[installation requirements](../../chan-pin-bu-shu-zhi-nan/bu-shu-huan-jing-yao-qiu.md)

### 密码安装报错

错误情况：使用密码安装Global集群报 ssh:unable to authenticate 错误。

解决方案：将Global集群节点/etc/ssh/sshd\_config配置文件中的PasswordAuthentication设为yes，重启sshd服务。

注：建议配置SSH key的方式安装Global集群。

## 如何重新部署集群

### 继续安装

若安装报错后，请先排障，再登录到 Installer 节点执行如下命令后，重新打开 [http://\[tke-installer-IP\]:8080/index.html](http://[tke-installer-IP]:8080/index.html) 安装控制台。

```text
docker restart tke-installer
```

### 重新安装

安装报错后，请先排障，再登录到 Installer 节点执行如下命令后，重新打开 [http://\[tke-installer-IP\]:8080/index.html](http://[tke-installer-IP]:8080/index.html) 安装控制台。

```text
rm -rf /opt/tke-installer/data && docker restart tke-installer
```

### 彻底清除所有安装文件，重新部署TKEStack。

想要彻底清理TKEStack，请对installer和所有Global Cluster节点执行下方脚本。

```text
curl -s https://tke-release-1251707795.cos.ap-guangzhou.myqcloud.com/tools/clean.sh | sh
```

或者使用如下脚本：

```text
#!/bin/bash

rm -rf /etc/kubernetes

systemctl stop kubelet 2>/dev/null

docker rm -f $(docker ps -aq) 2>/dev/null
systemctl stop docker 2>/dev/null

ip link del cni0 2>/etc/null

for port in 80 2379 6443 8086 {10249..10259} ; do
    fuser -k -9 ${port}/tcp
done

rm -rfv /etc/kubernetes
rm -rfv /etc/docker
rm -fv /root/.kube/config
rm -rfv /var/lib/kubelet
rm -rfv /var/lib/cni
rm -rfv /etc/cni
rm -rfv /var/lib/etcd
rm -rfv /var/lib/postgresql /etc/core/token /var/lib/redis /storage /chart_storage

systemctl start docker 2>/dev/null
```

注：如有混合部署其他业务，请基于实际情况评估目录内数据是否可删除。

