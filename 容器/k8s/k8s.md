> k8s

Table of Contents
=================

* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [Rest设计规则](#rest%E8%AE%BE%E8%AE%A1%E8%A7%84%E5%88%99)
  * [资源（对象）](#%E8%B5%84%E6%BA%90%E5%AF%B9%E8%B1%A1)
  * [API Server](#api-server)
    * [说明](#%E8%AF%B4%E6%98%8E)
    * [swagger ui](#swagger-ui)
    * [查看所有API](#%E6%9F%A5%E7%9C%8B%E6%89%80%E6%9C%89api)
  * [Deployment](#deployment)
    * [Deployment的特性](#deployment%E7%9A%84%E7%89%B9%E6%80%A7)
    * [Deployment对比RC](#deployment%E5%AF%B9%E6%AF%94rc)
    * [Deployment的应用场景](#deployment%E7%9A%84%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)
  * [Service](#service)
    * [特性](#%E7%89%B9%E6%80%A7)
    * [总结](#%E6%80%BB%E7%BB%93)
  * [kube proxy](#kube-proxy)
    * [作用](#%E4%BD%9C%E7%94%A8)
    * [特性](#%E7%89%B9%E6%80%A7-1)
  * [Annotation](#annotation)
    * [特性](#%E7%89%B9%E6%80%A7-2)
    * [常见的annotation](#%E5%B8%B8%E8%A7%81%E7%9A%84annotation)
  * [Volumn](#volumn)
    * [分类](#%E5%88%86%E7%B1%BB)
* [架构](#%E6%9E%B6%E6%9E%84)
  * [master节点](#master%E8%8A%82%E7%82%B9)
    * [api server](#api-server-1)
    * [controller manager](#controller-manager)
    * [scheduler](#scheduler)
  * [node节点](#node%E8%8A%82%E7%82%B9)
    * [kubelet](#kubelet)
    * [kube proxy](#kube-proxy-1)
    * [docker](#docker)
  * [etcd](#etcd)
  * [k8s客户端](#k8s%E5%AE%A2%E6%88%B7%E7%AB%AF)
* [使用](#%E4%BD%BF%E7%94%A8)
  * [1\.nodePort的范围](#1nodeport%E7%9A%84%E8%8C%83%E5%9B%B4)
  * [2\.service相关命令行](#2service%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%E8%A1%8C)
  * [3\.查看k8s集群各组件状态](#3%E6%9F%A5%E7%9C%8Bk8s%E9%9B%86%E7%BE%A4%E5%90%84%E7%BB%84%E4%BB%B6%E7%8A%B6%E6%80%81)
  * [4\.查看nodes状态](#4%E6%9F%A5%E7%9C%8Bnodes%E7%8A%B6%E6%80%81)
  * [5\.查看node的标签](#5%E6%9F%A5%E7%9C%8Bnode%E7%9A%84%E6%A0%87%E7%AD%BE)
  * [6\.查看PodIP和container端口](#6%E6%9F%A5%E7%9C%8Bpodip%E5%92%8Ccontainer%E7%AB%AF%E5%8F%A3)
  * [7\.查看pod运行在哪个node](#7%E6%9F%A5%E7%9C%8Bpod%E8%BF%90%E8%A1%8C%E5%9C%A8%E5%93%AA%E4%B8%AAnode)
  * [8\.查看pod详细信息](#8%E6%9F%A5%E7%9C%8Bpod%E8%AF%A6%E7%BB%86%E4%BF%A1%E6%81%AF)
  * [9\.查看日志](#9%E6%9F%A5%E7%9C%8B%E6%97%A5%E5%BF%97)
  * [10\.交互式进入k8s](#10%E4%BA%A4%E4%BA%92%E5%BC%8F%E8%BF%9B%E5%85%A5k8s)
  * [11\.删除deployment](#11%E5%88%A0%E9%99%A4deployment)
  * [12\.创建service account](#12%E5%88%9B%E5%BB%BAservice-account)
  * [13\.创建clusterrolebinding](#13%E5%88%9B%E5%BB%BAclusterrolebinding)
  * [14\.创建令牌](#14%E5%88%9B%E5%BB%BA%E4%BB%A4%E7%89%8C)
  * [15\.解决镜像站点不能访问的问题](#15%E8%A7%A3%E5%86%B3%E9%95%9C%E5%83%8F%E7%AB%99%E7%82%B9%E4%B8%8D%E8%83%BD%E8%AE%BF%E9%97%AE%E7%9A%84%E9%97%AE%E9%A2%98)
* [示例](#%E7%A4%BA%E4%BE%8B)
  * [1\.在k8s上部署jenkins](#1%E5%9C%A8k8s%E4%B8%8A%E9%83%A8%E7%BD%B2jenkins)
    * [文档](#%E6%96%87%E6%A1%A3)
    * [步骤](#%E6%AD%A5%E9%AA%A4)
      * [1\.创建namespace](#1%E5%88%9B%E5%BB%BAnamespace)
      * [2\.定义并创建deployment](#2%E5%AE%9A%E4%B9%89%E5%B9%B6%E5%88%9B%E5%BB%BAdeployment)
      * [3\.定义并创建service](#3%E5%AE%9A%E4%B9%89%E5%B9%B6%E5%88%9B%E5%BB%BAservice)
      * [4\.访问jenkins](#4%E8%AE%BF%E9%97%AEjenkins)
    * [实现jenkins的持久化](#%E5%AE%9E%E7%8E%B0jenkins%E7%9A%84%E6%8C%81%E4%B9%85%E5%8C%96)
      * [1\.给运行jenkins的node打标签](#1%E7%BB%99%E8%BF%90%E8%A1%8Cjenkins%E7%9A%84node%E6%89%93%E6%A0%87%E7%AD%BE)
      * [2\.pod和node进行绑定](#2pod%E5%92%8Cnode%E8%BF%9B%E8%A1%8C%E7%BB%91%E5%AE%9A)
      * [3\.设置存储卷](#3%E8%AE%BE%E7%BD%AE%E5%AD%98%E5%82%A8%E5%8D%B7)
      * [4\.修改宿主机挂载目录的权限为jenkins user](#4%E4%BF%AE%E6%94%B9%E5%AE%BF%E4%B8%BB%E6%9C%BA%E6%8C%82%E8%BD%BD%E7%9B%AE%E5%BD%95%E7%9A%84%E6%9D%83%E9%99%90%E4%B8%BAjenkins-user)
      * [5\.删除pod并重新创建](#5%E5%88%A0%E9%99%A4pod%E5%B9%B6%E9%87%8D%E6%96%B0%E5%88%9B%E5%BB%BA)
      * [6\.进行验证](#6%E8%BF%9B%E8%A1%8C%E9%AA%8C%E8%AF%81)
      
# 基本概念
## Rest设计规则
Rest的设计规则：

1.所有事物抽象为资源

2.资源有唯一标识符（URI）

3.使用通用的接口对资源进行操作

4.对资源的操作不会影响URI

5.操作是无状态的

## 资源（对象）
1.资源有元数据

2.资源可以嵌套

## API Server
### 说明
1.API Sever支持Rest和非Rest风格

### swagger ui
api server提供了swagger api：
```
http://apiserver:8080/swagger-ui/
```

### 查看所有API
```
http://apiserver:8080/api/v1
```

## Deployment
### Deployment的特性
1.Deployment相当于RC的升级版

2.Deployment内部使用了Replica Set

### Deployment对比RC
Deployment对比RC，最大的一个升级是：能够随时了解当前Pod的部署状态。

### Deployment的应用场景
1.通过创建deployment对象来创建replica set对象，最终创建pod副本对象

2.通过检查deployment来查询部署进度

3.通过deployment来实现pod的更新和回滚（扩容，缩容）

## Service
### 特性
1.service定义了一个服务的访问入口

2.service相当于微服务架构中的一个微服务

3.service和后端pod集群交互是通过label selector实现的

### 总结
传统服务通过k8s的service实现了微服务，不同的service提供不同业务的服务，它们之间彼此独立，一起组成强大而灵活的微服务系统。

## kube proxy
### 作用
kube proxy将来自service的请求转发给后端的pod。

### 特性
1.service被创建，系统会分配给它一个独立的IP（cluster IP），这个IP是固定的

## Annotation
### 特性
1.中文为注解

2.用来定义资源对象的特殊信息

### 常见的annotation
1.build,release信息

2.docker信息

3.日志库，监控库等资源库的地址信息

## Volumn
### 分类
```
宿主机: emptyDir、HostPath
集群存储: configmap、secret
云存储: awsElasticBlockStore、gcePersistentDisk等
外部存储: glusterfs、Ceph等
```

# 架构
## master节点
master是k8s集群的控制节点，每个k8s集群都有一个master节点。

master节点负责管理和控制整个集群。

master节点由三个组件构成：api server,controller manager,scheduler。

### api server
api server是集群的控制入口，也是资源对象操作的唯一入口。提供了rest风格API供客户端调用。

### controller manager
controller manager是集群的控制中心。相当于资源对象的“管家”。

### scheduler
scheduler是集群的调度中心。负责Pod的调度。

## node节点
node节点时集群的工作节点。负责接收master节点的指令并在本地执行。

node节点由三个组件构成：kubelet,proxy,docker

### kubelet
kubelet负责Pod的创建，启动，销毁。

### kube proxy
kube proxy负责service资源对象的实现，提供Pod的负载均衡。

### docker 
docker负责容器的创建和管理。

## etcd
etcd是k8s集群的存储中心，负责存储k8s各种资源的信息。

## k8s客户端
用户通过k8s客户端和k8s集群进行交互。常见的客户端有：kubectl工具,编程语言的sdk。

# 使用
## 1.nodePort的范围
```
30000-32767
```

## 2.service相关命令行
```
#查看PodIP和container端口
kubectl get endpoints --namespace=jenkins
#查看service详细信息
kubectl get svc jenkins-service -o yaml --namespace=jenkins
#查看pod运行在哪个node
kubectl get pods -o wide --namespace=jenkins
#查看pod详细信息
kubectl describe pod jenkins-deployment-85bf948f97-vcd42 --namespace=jenkins
#查看日志
kubectl logs jenkins-deployment-85bf948f97-q86c6 --namespace=jenkins
(-f 表示流式打印日志)
#重启pod
kubectl get pod jenkins-deployment-85bf948f97-q86c6 -o yaml --namespace=jenkins|kubectl replace --force -f -
#交互式进入k8s
kubectl exec -it -n jenkins jenkins-deployment-85bf948f97-fzxtk /bin/bash
#删除deployment
kubectl delete deployment jenkins-deployment -n jenkins
```

## 3.查看k8s集群各组件状态
```
kubectl get componentstatuses
```

## 4.查看nodes状态
```
kubectl get nodes
```

## 5.查看node的标签
```
kubectl get nodes --show-labels
```

## 6.查看PodIP和container端口
```
kubectl get endpoints --namespace=jenkins
```

## 7.查看pod运行在哪个node
```
kubectl get pods -o wide --namespace=jenkins
```

## 8.查看pod详细信息
```
kubectl describe pod jenkins-deployment-85bf948f97-vcd42 --namespace=jenkins
```

## 9.查看日志
```
kubectl logs jenkins-deployment-85bf948f97-q86c6 --namespace=jenkins
(-f 表示流式打印日志)
```

## 10.交互式进入k8s
```
kubectl exec -it -n jenkins jenkins-deployment-85bf948f97-fzxtk /bin/bash
```

## 11.删除deployment
```
kubectl delete deployment jenkins-deployment -n jenkins
```

## 12.创建service account
```
kubectl create sa dashboard-admin -n kube-system
```

## 13.创建clusterrolebinding
```
kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
```

## 14.创建令牌
```
kubectl create sa dashboard-admin -n kube-system
kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin

ADMIN_SECRET=$(kubectl get secrets -n kube-system | grep dashboard-admin | awk '{print $1}')

DASHBOARD_LOGIN_TOKEN=$(kubectl describe secret -n kube-system ${ADMIN_SECRET} | grep -E '^token' | awk '{print $2}')

echo ${DASHBOARD_LOGIN_TOKEN}
```

## 15.解决镜像站点不能访问的问题
1.自己拉国内镜像的镜像，然后重新打tag
```
docker pull mirrorgooglecontainers/kube-apiserver:v1.15.0
docker tag mirrorgooglecontainers/kube-apiserver:v1.15.0 k8s.gcr.io/kube-apiserver:v1.15.0
```

# 示例
## 1.在k8s上部署jenkins
### 文档
https://devopscube.com/setup-jenkins-on-kubernetes-cluster/

### 步骤
#### 1.创建namespace
```
kubectl create ns jenkins
```

#### 2.定义并创建deployment
```
#cat jenkins-deployment.yml 
apiVersion: extensions/v1beta1
kind: Deployment
metadata: 
  name: jenkins-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins
        image: jenkins:2.60.3
        ports:
        - containerPort: 8080
```
```
kubectl create -f jenkins-deployment.yml --namespace=jenkins
```

#### 3.定义并创建service
```
#cat jenkins-service.yml 
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
spec:
  type: NodePort
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30000
  selector:
    app: jenkins
```
```
kubectl create -f jenkins-service.yml --namespace=jenkins
```

#### 4.访问jenkins
```
#查看pod运行在哪个node
kubectl get pods -o wide --namespace=jenkins
```
访问
```
http://<node-ip>:30000
```

jenkins的初始密码可以通过日志获得：
```
kubectl logs jenkins-deployment-85bf948f97-q86c6 --namespace=jenkins
```

### 实现jenkins的持久化
#### 1.给运行jenkins的node打标签
```
#给运行jenkins的node打标签
kubectl label nodes 10.186.60.107 app=jenkins
```

#### 2.pod和node进行绑定
```
#pod和node进行绑定
apiVersion: extensions/v1beta1
kind: Deployment
metadata: 
  name: jenkins-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins
        image: jenkins:2.60.3
        ports:
        - containerPort: 8080
      nodeSelector:
        app: jenkins
```

#### 3.设置存储卷
```
#设置存储卷
#cat jenkins-deployment.yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata: 
  name: jenkins-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins
        image: jenkins:2.60.3
        volumeMounts:
        - mountPath: /var/jenkins_home
          name: jenkins-home
        ports:
        - containerPort: 8080
      volumes:
      - name: jenkins-home
        hostPath:
          path: /volumes/jenkins_home
      nodeSelector:
        app: jenkins
```

#### 4.修改宿主机挂载目录的权限为jenkins user
```
#修改宿主机挂载目录的权限为jenkins user
chown -R 1000 /volumes/jenkins_home
```

查看jenkins uid的方式：
```
#kubectl exec -it jenkins-deployment-555c465c57-544z9 /bin/bash -n jenkins
jenkins@jenkins-deployment-555c465c57-544z9:/$ cat /etc/passwd|grep jenkins
jenkins:x:1000:1000::/var/jenkins_home:/bin/bash
```

#### 5.删除pod并重新创建
```
#重新创建pod
kubectl delete deployment jenkins-deployment -n jenkins
kubectl create -f jenkins-deployment.yml --namespace=jenkins
```

#### 6.进行验证
```
#重启pod进行验证
kubectl get pod jenkins-deployment-555c465c57-7l7x4 -o yaml --namespace=jenkins|kubectl replace --force -f -
```
