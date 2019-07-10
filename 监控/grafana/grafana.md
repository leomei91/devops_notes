> grafana

Table of Contents
=================

* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [Data Source](#data-source)
  * [Organization](#organization)
  * [User](#user)
  * [Row](#row)
  * [Panel](#panel)
  * [Query Editor](#query-editor)
  * [Dashboard](#dashboard)
* [部署](#%E9%83%A8%E7%BD%B2)
  * [部署脚本](#%E9%83%A8%E7%BD%B2%E8%84%9A%E6%9C%AC)
  * [制作成service](#%E5%88%B6%E4%BD%9C%E6%88%90service)
* [使用](#%E4%BD%BF%E7%94%A8)
  * [grafana和prometheus的组合](#grafana%E5%92%8Cprometheus%E7%9A%84%E7%BB%84%E5%90%88)
    * [导入第三方模板](#%E5%AF%BC%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E6%A8%A1%E6%9D%BF)
  * [grafana配置邮件功能](#grafana%E9%85%8D%E7%BD%AE%E9%82%AE%E4%BB%B6%E5%8A%9F%E8%83%BD)
  * [安装插件](#%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6)
* [文档](#%E6%96%87%E6%A1%A3)

# 基本概念
## Data Source
Grafana为您的时间序列数据（数据源）支持许多不同的存储后端。每个数据源都有一个特定的查询编辑器，该编辑器针对特定数据源公开的特性和功能进行了自定义。

官方支持以下数据源：Graphite，InfluxDB，OpenTSDB，Prometheus，Elasticsearch，CloudWatch。

每个数据源的查询语言和功能显然非常不同。您可以将来自多个数据源的数据组合到单个仪表板上，但每个Panel都绑定到属于特定组织的特定数据源。

## Organization
Grafana支持多个组织，以支持各种部署模型，包括使用单个Grafana实例为多个可能不受信任的组织提供服务。

在许多情况下，Grafana将部署在一个组织中。

每个组织可以拥有一个或多个数据源。

所有仪表板都归特定组织所有。

## User
用户是Grafana中的指定帐户。用户可以属于一个或多个组织，可以通过角色为其分配不同级别的权限。

Grafana支持各种内部和外部方式，供用户进行身份验证。这些包括来自其自己的集成数据库，来自外部SQL服务器或来自外部LDAP服务器。

## Row
A Row是仪表板中的逻辑分隔符，用于将Panel组合在一起

## Panel
该小组是Grafana的基本可视化构建块。每个Panel提供一个查询编辑器（取决于面板中选择的数据源），允许您使用查询编辑器提取要在Panel上显示的完美可视化

每个面板都有各种各样的样式和格式选项，可以让您创建完美的图片。

可以在仪表板上拖放面板并重新排列面板。它们也可以调整大小。

目前有五种Panel类型：Graph，Singlestat，Dashlist，Table和Text。

## Query Editor
查询编辑器公开数据源的功能，并允许您查询它包含的指标。

使用查询编辑器在时间序列数据库中构建一个或多个查询（对于一个或多个系列）。该面板将立即更新，允许您实时有效地探索您的数据，并为该特定面板构建一个完美的查询。

您可以在查询编辑器中使用查询编辑器中的模板变量。这提供了一种基于仪表板上选择的模板变量动态探索数据的强大方法。

Grafana允许您通过查询编辑器中的行来引用查询。如果向图表添加第二个查询，则只需键入#A即可引用第一个查询。这提供了一种简单方便的方法来构建复合查询。

## Dashboard
仪表板是它们汇集在一起​​的地方。仪表板可以被认为是一组一个或多个面板组织并排列成一个或多个行。

仪表板的时间段可以通过仪表板右上角的仪表板时间选择器进行控制。

仪表板可以利用模板化使其更具动态性和交互性。

仪表板可以利用Annotations在Panel中显示事件数据。这有助于将Panel中的时间序列数据与其他事件相关联。

可以通过各种方式轻松共享仪表板（或特定面板）。您可以向登录Grafana的人发送链接。您可以使用快照功能将当前正在查看的所有数据编码为静态和交互式JSON文档; 它比通过电子邮件截图更好！

可以标记仪表板，仪表板选择器提供对特定组织中所有仪表板的快速，可搜索的访问。


# 部署
## 部署脚本
```
grafana_install(){
INSTALL grafana
cd $SRC
mkd $BASEDIR/grafana
tar xf grafana-6.2.2.linux-amd64.tar.gz --strip 1 -C $BASEDIR/grafana
}
```

## 制作成service
```
vim /etc/sysconfig/grafana-server
GRAFANA_USER=grafana

GRAFANA_GROUP=grafana

GRAFANA_HOME=/usr/local/grafana

LOG_DIR=/usr/local/grafana/data/log

DATA_DIR=/usr/local/grafana/data

MAX_OPEN_FILES=10000

CONF_DIR=/usr/local/grafana/conf

CONF_FILE=/usr/local/grafana/conf/defaults.ini

RESTART_ON_UPGRADE=true

PLUGINS_DIR=/usr/local/grafana/data/plugins

PROVISIONING_CFG_DIR=/usr/local/grafana/conf/provisioning

# Only used on systemd systems
PID_FILE_DIR=/usr/local/grafana
```
```
vim /usr/lib/systemd/system/grafana-server.service
[Unit]
Description=Grafana instance
Documentation=http://docs.grafana.org
Wants=network-online.target
After=network-online.target
After=postgresql.service mariadb.service mysql.service

[Service]
EnvironmentFile=/etc/sysconfig/grafana-server
User=grafana
Group=grafana
Type=notify
Restart=on-failure
WorkingDirectory=/usr/local/grafana
RuntimeDirectory=grafana
RuntimeDirectoryMode=0750
ExecStart=/usr/local/grafana/bin/grafana-server                                                  \
                            --config=${CONF_FILE}                                   \
                            --pidfile=${PID_FILE_DIR}/grafana-server.pid            \
                            cfg:default.paths.logs=${LOG_DIR}                       \
                            cfg:default.paths.data=${DATA_DIR}                      \
                            cfg:default.paths.plugins=${PLUGINS_DIR}                \
                            cfg:default.paths.provisioning=${PROVISIONING_CFG_DIR}  

LimitNOFILE=10000
TimeoutStopSec=20

[Install]
WantedBy=multi-user.target

systemctl daemon-reload
```
```
vim conf/defaults.ini
socket = /usr/local/grafana/grafana.sock

mkdir -p data/{log,plugins}
groupadd grafana
useradd grafana -g grafana -s /sbin/nologin -M
chown -R grafana /usr/local/grafana/
#启动
systemctl start grafana-server
```
```
#访问web
ip:3000
```
默认账号
```
admin/admin
```
第一次登陆需要自己修改密码

# 使用
## grafana和prometheus的组合
https://prometheus.io/docs/visualization/grafana/#installing

### 导入第三方模板
自己动手写模板太费时了!这个时候就可以使用模板导入功能！

https://grafana.com/docs/reference/export_import/

## grafana配置邮件功能
```
grafana.ini
[smtp]
enabled = true
host = smtp.163.com:465
user = 邮箱前缀@163.com
# If the password contains # or ; you have to wrap it with trippel quotes. Ex """#password;"""
password = 客户端授权密码
skip_verify = true
from_address = 邮箱前缀@163.com
from_name = Grafana	
```
## 安装插件
```
bin/grafana-cli --pluginsDir data/plugins/ plugins install alexanderzobnin-zabbix-app
systemctl restart grafana
```


# 文档
https://grafana.com/docs/installation/configuration/