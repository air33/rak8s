# 目的

使用ansible来部署基于N1的k8s集群

略微修改原作者的[rak8s](https://github.com/rak8s/rak8s)脚本

## 硬件

* 斐讯N1(PHICOMM N1)

    至少3个?

## 软件

* 刷好`Rambian`
    笔者的系统是`ARMBIAN 5.77`, 不知道怎么做的看看[恩山](https://www.right.com.cn/forum/forum-158-1.html)

* 设置静态IP

* 可科学上网

* 建议设置ssh自动登陆

    ```
    # 生成key
    ssh-kegen
    ssh-copy-id root@your-n1-ip
    ```

* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) 2.2 or higher

* [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 应该安装在你的管理机上。如果使用PC或笔记本来管理集群，就需要安装在你的PC或笔记本上

# 步骤

## Clone 仓库

```
git clone git@github.com:air33/rak8s.git
```

## 修改 ansible.cfg 和 inventory

inventry:

    设置集群的IP

ansible.cfg:

    修改`remote_user`, 缺省是`root`

## 试试 ansible

```
ansible -m ping all
```

## 部署

部署集群

```
ansible-playbook cluster.yml
```

安装 dashboard

```
ansible-playbook dashboard.yml
```

## 清除设置


```
ansible-playbook cleanup.yml
```

可能并没有把所以更改都复原

# 部署后

## CLI

看看能否从笔记本上执行

```
kubectl get nodes
```

输出

```
NAME    STATUS   ROLES    AGE   VERSION
aml01   Ready    master   30m   v1.14.1
aml02   Ready    <none>   29m   v1.14.1
aml03   Ready    <none>   29m   v1.14.1
```

## Dashboard

启动

```
kubectl proxy
```

浏览
[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)


如果要求输入token的话，这样找到

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```
