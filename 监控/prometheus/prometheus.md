> prometheus

Table of Contents
=================

* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [prometheus](#prometheus)
  * [exporter](#exporter)
* [架构](#%E6%9E%B6%E6%9E%84)
  * [prometheus server](#prometheus-server)
  * [client libraries](#client-libraries)
  * [push gateway](#push-gateway)
  * [exporter](#exporter-1)
  * [alertmanager](#alertmanager)
* [原理](#%E5%8E%9F%E7%90%86)
* [部署](#%E9%83%A8%E7%BD%B2)
  * [prometheus](#prometheus-1)
    * [部署脚本](#%E9%83%A8%E7%BD%B2%E8%84%9A%E6%9C%AC)
  * [node\_exporter](#node_exporter)
    * [部署脚本](#%E9%83%A8%E7%BD%B2%E8%84%9A%E6%9C%AC-1)
    * [制作成service](#%E5%88%B6%E4%BD%9C%E6%88%90service)
* [文档](#%E6%96%87%E6%A1%A3)

# 基本概念
## prometheus
prometheus是一个监控告警工具。

## exporter
对于不同的监控对象，prometheus通过各种exporter来解决。比如node exporter，提供对磁盘，CPU，内存，网络等资源的监控。

# 架构
## prometheus server
主要的Prometheus服务器，用于存储时间序列数据

## client libraries
用于检测应用程序代码的客户端库

## push gateway
用于支持短期工作的推送网关

## exporter
针对HAProxy，StatsD，Graphite等服务的专用出口商

## alertmanager
一个alertmanager处理警报

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1553145936/ops/architecture.png)

# 原理
Prometheus直接或通过中间推送网关从仪表工作中删除指标，用于短期工作。它在本地存储所有已删除的样本，并对此数据运行规则，以汇总和记录现有数据的新时间序列或生成警报。Grafana或其他API使用者可用于可视化收集的数据。

# 部署
## prometheus
### 部署脚本
```
prometheus_install(){
INSTALL prometheus
cd $SRC
mkd $BASEDIR/prometheus
tar xf prometheus-2.10.0.linux-amd64.tar.gz --strip 1 -C $BASEDIR/prometheus
$BASEDIR/prometheus/prometheus --version
}
```
```
vim prometheus.yml
scrape_configs:
  - job_name: "node"
    scrape_interval: "15s"
    static_configs:  
```
```
#启动
nohup ./prometheus > prometheus.log 2>&1 &
#访问
ip:9090
#
ip:9090/consoles/node.html
```

## node_exporter
### 部署脚本
```
node_exporter_install(){
INSTALL node_exporter
cd $SRC
mkd $BASEDIR/prometheus/node_exporter
tar xf node_exporter-0.18.1.linux-amd64.tar.gz --strip 1 -C $BASEDIR/prometheus/node_exporter
}
```

### 制作成service
```
vim /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter

[Service]
User=prometheus
ExecStart=/usr/local/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target

systemctl daemon-reload
groupadd prometheus
useradd prometheus -g prometheus -s /sbin/nologin -M
chown -R prometheus /usr/local/prometheus
```
```
#启动
systemctl start node_exporter
#测试
curl ip:9100/metrics
```

# 文档
https://github.com/prometheus

https://prometheus.io/docs/visualization/grafana/