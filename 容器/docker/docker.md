> docker

Table of Contents
=================

* [历史背景](#%E5%8E%86%E5%8F%B2%E8%83%8C%E6%99%AF)
* [概念](#%E6%A6%82%E5%BF%B5)
  * [OCI](#oci)
  * [OCF](#ocf)
  * [docker镜像](#docker%E9%95%9C%E5%83%8F)
  * [docker引擎](#docker%E5%BC%95%E6%93%8E)
  * [存储驱动](#%E5%AD%98%E5%82%A8%E9%A9%B1%E5%8A%A8)
  * [docker插件](#docker%E6%8F%92%E4%BB%B6)
* [架构](#%E6%9E%B6%E6%9E%84)
  * [docker client](#docker-client)
  * [docker host](#docker-host)
  * [docker image registry](#docker-image-registry)
  * [docker网络](#docker%E7%BD%91%E7%BB%9C)
* [部署](#%E9%83%A8%E7%BD%B2)
* [使用](#%E4%BD%BF%E7%94%A8)
  * [限制内存资源](#%E9%99%90%E5%88%B6%E5%86%85%E5%AD%98%E8%B5%84%E6%BA%90)
  * [限制CPU资源](#%E9%99%90%E5%88%B6cpu%E8%B5%84%E6%BA%90)
  * [限制IO资源](#%E9%99%90%E5%88%B6io%E8%B5%84%E6%BA%90)
    * [限制 bps 和 iops](#%E9%99%90%E5%88%B6-bps-%E5%92%8C-iops)
* [命令行](#%E5%91%BD%E4%BB%A4%E8%A1%8C)
  * [查看docker当前状态](#%E6%9F%A5%E7%9C%8Bdocker%E5%BD%93%E5%89%8D%E7%8A%B6%E6%80%81)
  * [查看docker版本信息](#%E6%9F%A5%E7%9C%8Bdocker%E7%89%88%E6%9C%AC%E4%BF%A1%E6%81%AF)
  * [拉取镜像](#%E6%8B%89%E5%8F%96%E9%95%9C%E5%83%8F)
  * [删除镜像](#%E5%88%A0%E9%99%A4%E9%95%9C%E5%83%8F)
  * [删除容器](#%E5%88%A0%E9%99%A4%E5%AE%B9%E5%99%A8)
  * [查看镜像列表](#%E6%9F%A5%E7%9C%8B%E9%95%9C%E5%83%8F%E5%88%97%E8%A1%A8)
  * [创建并启动容器](#%E5%88%9B%E5%BB%BA%E5%B9%B6%E5%90%AF%E5%8A%A8%E5%AE%B9%E5%99%A8)
  * [查看容器详细信息](#%E6%9F%A5%E7%9C%8B%E5%AE%B9%E5%99%A8%E8%AF%A6%E7%BB%86%E4%BF%A1%E6%81%AF)
  * [查看容器列表](#%E6%9F%A5%E7%9C%8B%E5%AE%B9%E5%99%A8%E5%88%97%E8%A1%A8)
  * [在外部执行容器内命令](#%E5%9C%A8%E5%A4%96%E9%83%A8%E6%89%A7%E8%A1%8C%E5%AE%B9%E5%99%A8%E5%86%85%E5%91%BD%E4%BB%A4)
  * [一张图搞定docker命令](#%E4%B8%80%E5%BC%A0%E5%9B%BE%E6%90%9E%E5%AE%9Adocker%E5%91%BD%E4%BB%A4)
* [文档](#%E6%96%87%E6%A1%A3)

# 历史背景
Linux为了实现资源隔离，搞出了namespace和cgroup。

基于namespace和cgroup，又诞生了LXC，这个是早期的容器。

LXC使用起来还是太繁琐，功能过于简单，于是docker横空出世，极大降低了容器的使用门槛，功能上更加强大，火遍全球。

此时，谷歌按捺不住了！因为类似docker容器的技术，在谷歌内部，其实使用了十几年！

谷歌想阻住docker的风头，奈何容器大势已定。于是，谷歌开始在其他方面发力，这就是容器编排领域。

docker容器如果没有很好的编排工具支持，无法大规模使用，发展会严重受阻。

于是，各类编排工具应时而生，比较流行的有：

swarm+compose

mesos+marathon

k8s

而站在谷歌borg系统肩上的k8s，可以说一出世便势如破竹，如今几乎称霸了容器编排领域。

谷歌还牵头搞出了一个CNCF组织，云原生得以茁壮成长。

这就是容器的历史背景。

（docker是商业版本，社区版改名成了moby，万恶的资本主义啊！）

# 概念
## OCI
由Linux基金会于2015年创立。

旨在围绕容器格式和容器运行时制定一个开放的工业标准。

## OCF
开发容器格式。RunC容器引擎就是基于OCF格式的一个具体实现。

## docker镜像
镜像和容器的关系，好比程序和进程的关系。

镜像是分层的，顶层是可写的，叫做容器层。其余是只读层。

docker使用的是系统的内核。镜像中不包含内核。

docker镜像是联合挂载的。

## docker引擎
docker引擎由最初的LXC，到后来的libcontainer，到现在的runContainer，发展也逐渐成熟。

## 存储驱动
docker存储驱动使用的是overlay2。

## docker插件
docker是插件式的，像存储卷，网络和日志组件都是插件式部署的。

# 架构
docker由客户端，服务器和镜像仓库构成。

## docker client
docker客户端。比如docker命令工具，各种SDK。

## docker host
docker host包含docker daemon，containers和images三个部分。

## docker image registry
docker的镜像仓库

## docker网络
https://www.cnblogs.com/CloudMan6/p/7066851.html

# 部署
```
yum install docker-ce -y > /dev/null

# 启用daocloud加速
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://9f1c3784.m.daocloud.io > /dev/null

# 修改配置文件
cp /etc/docker/daemon.json{,.bak}
`sed -i s/,}/}/g /etc/docker/daemon.json`

# 启动
systemctl restart docker

docker version

echo "docker is installed."
```

# 使用
## 限制内存资源
```
-m xxxM
--memory xxxM
```

限制内存+swap资源
```
--memory-swap xxxM
```
注意是内存和swap的总量。

如果在启动容器时只指定 -m 而不指定 --memory-swap，那么 --memory-swap 默认为 -m 的两倍。

## 限制CPU资源
Docker 可以通过 -c 或 --cpu-shares 设置容器使用 CPU 的权重。如果不指定，默认值为 1024。
```
-c 1024
-c 512
```
需要特别注意的是，这种按权重分配 CPU 只会发生在 CPU 资源紧张的情况下。如果 container_A 处于空闲状态，这时，为了充分利用 CPU 资源，container_B 也可以分配到全部可用的 CPU。

## 限制IO资源
默认情况下，所有容器能平等地读写磁盘，可以通过设置 --blkio-weight 参数来改变容器 block IO 的优先级。

--blkio-weight 与 --cpu-shares 类似，设置的是相对权重值，默认为 500。

### 限制 bps 和 iops
bps 是 byte per second，每秒读写的数据量。

iops 是 io per second，每秒 IO 的次数。

可通过以下参数控制容器的 bps 和 iops：
```
--device-read-bps，限制读某个设备的 bps。
--device-write-bps，限制写某个设备的 bps。
--device-read-iops，限制读某个设备的 iops。
--device-write-iops，限制写某个设备的 iops。
```

下面这个例子限制容器写 /dev/sda 的速率为 30 MB/s
```
docker run -it --device-write-bps /dev/sda:30MB ubuntu
```

# 命令行
## 查看docker当前状态
```
docker info
```

## 查看docker版本信息
```
docker version
```

## 拉取镜像
```
docker [image] pull
```

## 删除镜像
```
docker rmi image_id
```

## 删除容器
```
docker rm container_id
```

## 查看镜像列表
```
docker image ls
```

## 创建并启动容器
```
docker run [option] image
```

## 查看容器详细信息
```
docker inspect contanier_name
```

## 查看容器列表
```
docker ps -a
```

## 在外部执行容器内命令
```
docker exec [option] container_name cmd
```

## 一张图搞定docker命令
![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1557556679/k8s/upload-ueditor-image-20170608-1496889671778038814.png)

# 文档
https://www.cnblogs.com/CloudMan6/tag/%E5%AE%B9%E5%99%A8/default.html?page=8
