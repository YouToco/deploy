# 前提准备

在节点上部署NFS服务器，创建共享目录`/data/kubernetes`，基于该NFS服务器在Kubernetes集群中创建名为nfs-storage的默认动态存储类。

centos/openeuler/rocky Linux/RHEL：

```sh
# mkdir /data/kubernetes -p
# chmod 777 /data/kubernetes
# yum install nfs-utils -y
# vim /etc/exports
/data/kubernetes *(rw,no_root_squash)
# exportfs -r     #重新载入配置
# exportfs -s     #显示所有共享目录
/data/kubernetes  *(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,no_root_squash,no_all_squash)
# systemctl enable nfs-server.service --now
```

# 部署方式

```sh
curl -LO https://ghfast.top/https://github.com/YouToco/deploy/raw/refs/heads/main/nfs-storage/nfs-storageclass.yaml

sed -i "s#10.88.0.10#<your_nfs_server_ip>#" nfs-storageclass.yaml

kubectl create -f nfs-storageclass.yaml
```



# 部署后使用

`pvc-example.yaml`:

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysql-pvc
spec:
  storageClassName: nfs-storage
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 20G
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: wordpress-pvc
spec:
  storageClassName: nfs-storage
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 20G
```

```
kubectl create -f pvc-example.yaml
```

