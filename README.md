# k8s-imgbuild

# 使用已构建的



| REPOSITORY              | TAG     |
| ----------------------- | :------ |
| kube-apiserver          | v1.18.0 |
| kube-controller-manager | v1.18.0 |
| kube-proxy              | v1.18.0 |
| kube-scheduler          | v1.18.0 |
| coredns                 | 1.6.7   |
| etcd                    | 3.4.3-0 |
| pause                   | 3.2     |



| REPOSITORY                | TAG     |
| ------------------------- | ------- |
| calico/cni                | v3.13.3 |
| calico/kube-controllers   | v3.13.3 |
| calico/node               | v3.13.3 |
| calico/pod2daemon-flexvol | v3.13.3 |



| REPOSITORY | TAG             |
| ---------- | --------------- |
| flannel    | v0.12.0-amd64   |
|            | v0.12.0-arm64   |
|            | v0.12.0-arm     |
|            | v0.12.0-ppc64le |
|            | v0.12.0-s390x   |





脚本下载所有镜像并改名：

```
k8s.gcr.io
```



# 如何自己构建

通过kubeadm部署k8s集群，在执行kubeadm init命令时，默认生成的manifests文件夹下yaml文件的镜像都是gcr.io上的，在国内由于被墙而不能正常下载，也就导致了集群不能正常安装。

**解决办法：** 购买vpn 或者通过阿里云的容器镜像服务镜像构建。

## Step 1：创建github仓库

首先在github创建一个repository，创建好后然后git clone到本地，并在本地创建所需下载的镜像dockerfile，这里本地的目录层级为 镜像名称->版本号->dockerfile。创建之后再把所有的文件push到github仓库。最后的结果如下：

![1588335100233](assets/1588335100233.png)



Dockerfile内容如下：

```bash
From k8s.gcr.io/kube-scheduler:v1.18.0
MAINTAINER Rotel <602616568@qq.com>
```



## Step 2：构建镜像

登录阿里云的容器镜像服务网址  https://www.aliyun.com/product/acr ，进入管理控制台，在容器镜像服务->镜像列表->创建镜像仓库，按照要求填写相关信息，示例如下

**进入阿里云容器镜像服务控制台：**

![1588294259382](assets/1588294259382.png)

**创建自己的命名空间：**

![1588294354322](assets/1588294354322.png)



**创建镜像仓库：**

![1588294478944](assets/1588294478944.png)

![1588512993185](assets/1588512993185.png)

![1588512955380](assets/1588512955380.png)



![1588294551294](assets/1588294551294.png)

**添加构建规则：**

**==注意：每个仓库最多能创建5条规则==**

![1588294880197](assets/1588294880197.png)

**立即构建：**

![1588513300597](assets/1588513300597.png)

构建日志：失败请查看日志

![1588513324900](assets/1588513324900.png)



## Step 3：拉取镜像

**服务器拉取**

1、登录阿里云Docker Registry（私有库需要登录；公有库直接拉取）

```bash
$ sudo docker login --username=xxx@qq.com registry.cn-beijing.aliyuncs.com
Password:阿里云开通服务时密码
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

2.、从Registry中拉取镜像

```bash
$ sudo docker pull registry.cn-beijing.aliyuncs.com/crazywjj/k8s.gcr.io:kube-apiserver-v1.18.0
```

3、 重命名镜像 

```bash
$ docker images
REPOSITORY                                             TAG                      IMAGE ID            CREATED             SIZE
registry.cn-beijing.aliyuncs.com/crazywjj/k8s.gcr.io   kube-apiserver-v1.18.0   1a02b28ffedd        About an hour ago   173MB

$ docker tag registry.cn-beijing.aliyuncs.com/crazywjj/k8s.gcr.io:kube-apiserver-v1.18.0 k8s.gcr.io/kube-controller-manager:v1.18.0

$ docker rmi -fregistry.cn-beijing.aliyuncs.com/crazywjj/k8s.gcr.io:kube-apiserver-v1.18.0 
```





