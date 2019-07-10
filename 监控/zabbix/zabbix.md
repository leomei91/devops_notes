> zabbix

Table of Contents
=================

* [架构](#%E6%9E%B6%E6%9E%84)
* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
* [数据采集方式](#%E6%95%B0%E6%8D%AE%E9%87%87%E9%9B%86%E6%96%B9%E5%BC%8F)
  * [Zabbix agent](#zabbix-agent)
  * [Zabbix agent(active)](#zabbix-agentactive)
  * [简单检查（Simple check）](#%E7%AE%80%E5%8D%95%E6%A3%80%E6%9F%A5simple-check)
  * [SNMP设备监控](#snmp%E8%AE%BE%E5%A4%87%E7%9B%91%E6%8E%A7)
    * [SNMP介绍](#snmp%E4%BB%8B%E7%BB%8D)
    * [管理信息库（MIB）](#%E7%AE%A1%E7%90%86%E4%BF%A1%E6%81%AF%E5%BA%93mib)
    * [对象标识符（OID）](#%E5%AF%B9%E8%B1%A1%E6%A0%87%E8%AF%86%E7%AC%A6oid)
      * [OID的形式](#oid%E7%9A%84%E5%BD%A2%E5%BC%8F)
      * [OID名称标识](#oid%E5%90%8D%E7%A7%B0%E6%A0%87%E8%AF%86)
      * [zabbix中使用的OID](#zabbix%E4%B8%AD%E4%BD%BF%E7%94%A8%E7%9A%84oid)
    * [SNMPv1](#snmpv1)
    * [SNMPv2](#snmpv2)
    * [SNMPv3](#snmpv3)
    * [SNMP原理](#snmp%E5%8E%9F%E7%90%86)
    * [SNMP trap](#snmp-trap)
    * [SNMPD配置](#snmpd%E9%85%8D%E7%BD%AE)
    * [snmpwalk](#snmpwalk)
      * [选项说明](#%E9%80%89%E9%A1%B9%E8%AF%B4%E6%98%8E)
    * [zabbix的动态索引](#zabbix%E7%9A%84%E5%8A%A8%E6%80%81%E7%B4%A2%E5%BC%95)
      * [动态索引的语法](#%E5%8A%A8%E6%80%81%E7%B4%A2%E5%BC%95%E7%9A%84%E8%AF%AD%E6%B3%95)
      * [动态索引的原理](#%E5%8A%A8%E6%80%81%E7%B4%A2%E5%BC%95%E7%9A%84%E5%8E%9F%E7%90%86)
  * [zabbix系统内部数据采集](#zabbix%E7%B3%BB%E7%BB%9F%E5%86%85%E9%83%A8%E6%95%B0%E6%8D%AE%E9%87%87%E9%9B%86)
  * [Zabbix trap](#zabbix-trap)
  * [Zabbix aggregate](#zabbix-aggregate)
  * [External check](#external-check)
  * [Database monitor](#database-monitor)
  * [IPMI agent](#ipmi-agent)
  * [SSH agent](#ssh-agent)
  * [TELNET agent](#telnet-agent)
  * [JMX agent](#jmx-agent)
    * [原理](#%E5%8E%9F%E7%90%86)
    * [配置JMX服务](#%E9%85%8D%E7%BD%AEjmx%E6%9C%8D%E5%8A%A1)
    * [zabbix服务配置](#zabbix%E6%9C%8D%E5%8A%A1%E9%85%8D%E7%BD%AE)
    * [zabbix java gateway配置](#zabbix-java-gateway%E9%85%8D%E7%BD%AE)
    * [jmx监控项配置](#jmx%E7%9B%91%E6%8E%A7%E9%A1%B9%E9%85%8D%E7%BD%AE)
  * [Calculated](#calculated)
* [zabbix server的进程](#zabbix-server%E7%9A%84%E8%BF%9B%E7%A8%8B)
  * [db watchdog](#db-watchdog)
  * [houserkeeper](#houserkeeper)
  * [alerter](#alerter)
  * [icmp poller](#icmp-poller)
  * [ipmi poller](#ipmi-poller)
  * [java poller](#java-poller)
  * [node watcher](#node-watcher)
  * [history syncer](#history-syncer)
  * [httppoller](#httppoller)
  * [poller](#poller)
  * [proxy poller](#proxy-poller)
  * [discovery](#discovery)
  * [trapper](#trapper)
  * [self\-monitoring](#self-monitoring)
  * [unreachable poller](#unreachable-poller)
  * [vmware collector](#vmware-collector)
  * [configuration syncer](#configuration-syncer)
  * [timer](#timer)
  * [escalator](#escalator)
* [web配置](#web%E9%85%8D%E7%BD%AE)
  * [unsupported items的检测时间间隔](#unsupported-items%E7%9A%84%E6%A3%80%E6%B5%8B%E6%97%B6%E9%97%B4%E9%97%B4%E9%9A%94)
* [zabbix调优](#zabbix%E8%B0%83%E4%BC%98)
  * [调优原则](#%E8%B0%83%E4%BC%98%E5%8E%9F%E5%88%99)
  * [操作系统层面的优化](#%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E5%B1%82%E9%9D%A2%E7%9A%84%E4%BC%98%E5%8C%96)
* [低级自动发现 \- LLD](#%E4%BD%8E%E7%BA%A7%E8%87%AA%E5%8A%A8%E5%8F%91%E7%8E%B0---lld)
  * [流程](#%E6%B5%81%E7%A8%8B)
  * [支持的自动发现](#%E6%94%AF%E6%8C%81%E7%9A%84%E8%87%AA%E5%8A%A8%E5%8F%91%E7%8E%B0)
* [zabbix中的正则表达式](#zabbix%E4%B8%AD%E7%9A%84%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)
  * [配置](#%E9%85%8D%E7%BD%AE)
  * [说明](#%E8%AF%B4%E6%98%8E)
  * [用法](#%E7%94%A8%E6%B3%95)
  * [Expression type](#expression-type)
    * [Character string included](#character-string-included)
    * [Any character string included](#any-character-string-included)
    * [Character string included](#character-string-included-1)
    * [Result is TRUE](#result-is-true)
    * [Result is FALSE](#result-is-false)

# 架构
zabbix架构图：

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1548299387/ops/zabbix_arch.png)

# 基本概念
- 主机（host）

被监控的网络设备。

- 监控项（item）

被监控的对象。

# 数据采集方式
## Zabbix agent
agent的被动模式。zabbix服务器进程（默认监听10051端口）按照监控项所设置的时间间隔，定期发送采集数据的请求给agent进程（默认监听10050端口），agent开始采集监控数据并将采集到的数据返回给zabbix服务器。

## Zabbix agent(active)
agent的主动模式。agent从配置文件（zabbix_agentd.conf）中读取ServerActive（zabbix服务器的IP）配置项的内容，连接到zabbix服务器获取需要采集的监控项列表以及其配置信息（监控项列表及其配置信息的读取频率是由RefreshActiveChecks配置项指定，默认120秒），根据监控项配置信息定期采集监控数据，在采集到数据之后立即发送监控数据给zabbix服务器。

zabbix服务器和agent数据传输使用的是JSON格式。

## 简单检查（Simple check）
https://www.zabbix.com/documentation/3.4/zh/manual/config/items/itemtypes/simple_checks

## SNMP设备监控
### SNMP介绍
简单网络管理协议。广泛应用于各种网络设备，比如交换机，路由器，打印机，存储设备，超融合设备，防火墙等。

### 管理信息库（MIB）
基于SNMP协议管理的监控对象的集合，就是MIB。比如CPU负载，磁盘状态等监控项的集合。

### 对象标识符（OID）
MIB的组织结构是分层的，树状的，给每个监控对象设置一个标识符，这就是OID。通常使用OID来标识MIB中的对象。

#### OID的形式
从根开始到指定对象所经过的各层次对象的位置符，用点号（.）从左至右连接起来，所形成的的字符串就是OID。比如".1.3.6.1.2.1.2.1"就是ifnumber的OID。

#### OID名称标识
将各层次对象的名称用点号从左至右连接起来，这就是OID的名称标识，比如".iso.org.dod.internet.mgmt.mib-2.interface.ifnumber"。

#### zabbix中使用的OID
zabbix中使用最多的OID类似于"SNMPv2-MIB::sysName.0"这种形式，这是OID的另一种标识方法，标识SNMPv2信息库中sysName对象下第一节点的对象，即设备的系统全称。

### SNMPv1
SNMPv1是SNMP协议的第一个版本，它指定了四种核心协议数据单元（PDU），分别是GET，用来获取指定对象的信息；GETNEXT，用来连续获取指定的多个对象的信息；SET，用来设置一个管理对象的信息；TRAP，当管理对象的信息发送变化时，主动向管理站（通常就是zabbix服务器）报告这种变化。

SNMPv1只使用了一种安全策略，就是团体名。团体名和密码类似，只有正确提供团体名的管理站才能查询或设置SNMP代理。需要注意的是，SNMPv1，管理站和SNMP代理之前发送的PDU是明文的，非常不安全。

### SNMPv2
SNMPv2是SNMPv1的进化版本，在性能和安全上进行了升级。另外还增加了GetBulk和Inform两种PDU，当一次get多个值时，即使某些值不存在，也不会报错。

### SNMPv3
SNMPv3在SNMPv2基础上，增強了远程配置和安全性。放弃了之前依赖团体名认证的方式，提供了用户和密码认证方式；增加了数据包加密。

### SNMP原理
SNMPv1, SNMPv2, SNMPv3都是zabbix服务器根据监控项配置，定期通过SNMP协议查询被监控SNMP代理的信息，是一种被动的数据采集模式。

Zabbix服务器根据用户配置的监控项中的OID，定期调用net-snmp包所提供的接口函数，查询被监控主机上的MIB库中指定的OID所对应对象的数据。只需要被监控SNMP设备开启SNMP服务，并针对zabbix服务器开放查询权限，然后在配置监控项时指定好监控项目的OID值，zabbix就会按照我们的需求，定时去被监控主机上采集所需要的监控数据，不需要额外编写脚本。

### SNMP trap
SNMP trap是指当SNMP代理发生某个事件时，根据自身配置，向指定地址和端口发送相应的SNMP信息。是一种主动的采集方式。这种方式所能采集的监控数据有限。

### SNMPD配置
snmpd.conf
```
# 配置一条SNMPv3的用户信息，用户名snmpuser
# 这里配置完了之后，还需要使用net-snmp-config工具来创建SNMPv3用户
# net-snmp-config --create-snmpv3-user -ro -A A_STRING -a MD5|SHA snmpuser
# -A 用户密码
# -a 加密方法
rouser snmpuser

# 127.0.0.1和192.168.55.0网段的主机允许访问该SNMP服务器，需要使用相应的团体名
# 只适用于SNMPv1和SNMPv2
rocommunity community_name 192.168.55.0/24
rocommunity c_name 127.0.0.1

# 将所有没有使用团体名的，使用了SNMPv1和SNMPv2访问的归为notConfigGroup组
group notConfigGroup v1 notConfigUser
group notConfigGroup v2c notConfigUser

# 定义一个视图
view systemview included .1.3.6.1.2.1.1

# 所有属于notConfigGroup组的访问都会被拒绝
access notConfigGroup "" any noauth exact all none none

# 配置SNMPv1 trap，指定团体名为public，接受的服务器IP为192.168.11.22
trap1 192.168.11.22:162 public
# 配置SNMPv2 trap，指定团体名为trap2，接受的服务器IP为192.168.33.12
trap2 192.168.33.12:162 trap2
# 注意这里的团体名要和管理站上的snmptrapd.conf中所配置的团体名保持一致

```

### snmpwalk
snmpwalk是snmp客户端，用来采集snmp设备上的信息。

#### 选项说明
```
# -O n,-O用来控制数据输出的形式，n表示OID以数值的形式输出
snmpwalk -v2c -c cname -O n ip oid
```

### zabbix的动态索引
在监控snmp设备时，采用动态索引，可以极大提高监控的工作效率。

#### 动态索引的语法
```
<OID of Data>["index","<base OID of index>","<string to search>"]
```
OID if Data，监控对象的主OID，比如ifOutOctets

base OID of index，用于查找索引的基础OID，比如ifDescr

string to search，指定要匹配的字符串，比如GigabitEthernet0/1

#### 动态索引的原理
1.根据`base OID if index`和`string to search`获取具体的OID，然后取最后一个数值

2.将该数值和OID of Data进行拼接，得到另一个OID

3.使用这个OID去获取该监控对象的数据


## zabbix系统内部数据采集
用于采集和监控zabbix服务器自身的性能数据，不需要安装任何客户端

## Zabbix trap
使用zabbix_sender工具，主动向zabbix服务器发送监控数据，无需安装agent

使用这种方式需要注意的是：

因为将web配置信息同步到内存cache是按照周期来的（又CacheUpdateFrequency配置项决定，默认60秒），所以可能需要等待一段时间

## Zabbix aggregate
在已有的数据上进行组合，得到更多的数据。无需安装任何工具

## External check
通过脚本采集数据称之为外部检查。

zabbix服务器根据配置信息，周期的执行ExternalScripts配置项指定的脚本目录下的脚本，然后把脚本的输出结果当做监控项的值（即使脚本执行出错，也会将错误信息当做监控项的值），对zabbix性能影响较大。

key的格式：
```
script_name.sh[arg1,arg2]
# 比如echo_data.sh[{HOST.HOST}]
```

## Database monitor
通过ODBC进行数据库的监控

## IPMI agent
IPMI，智能平台管理接口，用于监控和采集某些硬件设备的信息，比如CPU温度等。IPMI的优势在于，它独立于CPU和操作系统，在关机状态下，也可以监控，还能远程开启硬件设备。

IPMI的网络接口，不同厂商有不同的叫法，HP叫iLO，DELL叫DRAC。

可以通过ipmitool工具配置IPMI网络和用户参数，也可以现场直接修改服务器的BIOS来设置这些信息。

## SSH agent
https://www.zabbix.com/documentation/4.0/zh/manual/config/items/itemtypes/ssh_checks

## TELNET agent
https://www.zabbix.com/documentation/4.0/zh/manual/config/items/itemtypes/telnet_checks

## JMX agent
JMX，JAVA 管理拓展，是一个在不对现有应用做修改的情况下植入到现有应用的框架，用来对应用的运行状态进行监控和管理。

### 原理
1.zabbix服务器根据监控项的配置，定期发送查询请求给zabbix java gateway进程

2.zabbix java gateway进程将请求打包成JMX格式的请求，发给被监控主机的JMX服务端口

3.zabbix java gateway进程接受来自被监控主机的响应包，之后将响应发给zabbix服务进程

4.zabbix服务进程将返回的监控数据保存到数据库相应的监控项里

### 配置JMX服务
java程序
```
java \
-Dcom.sun.management.jmxremote \
-Dcom.sun.management.jmxremote.port=12345 \
-Dcom.sun.management.jmxremote.authenticate=false \
-Dcom.sun.management.jmxremote.ssl=false\
```

java容器(以tomcat为例)
```
vim bin/catalina.sh
CATALINA_OPTS="-Dcom.sun.management.jmxremote.port=12345 \
-Dcom.sun.management.jmxremote.ssl=false \
-Dcom.sun.management.jmxremote.authenticate=true \
-Dcom.sun.management.jmxremote.password.file=jmxremote.password \
-Dcom.sun.management.jmxremote.access.file=jxmremote.access \
-Djava.rmi.server.hostname=ip"
```
password.file默认是当前目录下的jmxremote.password文件，port是JMX服务监听的端口，hostname限制连入的客户端

jmxremote.password(600)
```
monitor monitor@
admin admin@
```

jmxremote.access(600)
```
monitor readonly
admin readwrite
```
可以通过jconsole工具连入JMX服务

### zabbix服务配置
```
vim zabbix_server.conf
# 指定JavaGateway的IP
JavaGateway=IP|Hostname
# zabbix服务端和java gateway通信的端口
JavaGatewayPort=Port
# 启动多少个进程和java gateway进程通信
StartJavaPollers=2
```

### zabbix java gateway配置
```
vim zabbix_java_gateway.conf
LISTEN_IP="0.0.0.0" # java gateway监听的IP

LISTEN_PORT=10052 # java gateway监听的端口

START_POLLERS=5 # 启动多少个java gateway进程，注意要大于对应的zabbix服务进程

TIMEOUT=3 # 指定超时时间
```

### jmx监控项配置
```
Key: jmx[<object_name>,<attribute_name>]
```
这里的object_name可以通过jconsole查看，比如java.lang:type=classLoading，attribute_name是classLoading的属性。

## Calculated
https://www.zabbix.com/documentation/4.0/zh/manual/config/items/itemtypes/calculated

# zabbix server的进程
## db watchdog
用于监控zabbix系统数据库状态，当不可用时会发送告警信息

## houserkeeper
管家，定期清理过期的历史数据

## alerter
用来发送告警通知

## icmp poller
定期进行ICMP PING检查

## ipmi poller
定期进行ipmi类型监控项的检查

## java poller
用于轮询java类项目监控

## node watcher
用于在分布式节点中发送历史数据和配置信息

## history syncer
用于写入历史数据

## httppoller
监控web加载时间

## poller
用于被动监控项的轮询

## proxy poller
拥有服务器代理被动轮询

## discovery
自动发现新的主机

## trapper
用于处理主动采集、陷入以及分布式节点间或服务器代理

## self-monitoring
用于搜集zabbix内部的监控数据

## unreachable poller
用于轮询不可到达的设备

## vmware collector
用于从vmware服务进程中搜集数据

## configuration syncer
将配置文件中的配置信息同步到内存缓存(cache)中

## timer
用于处理触发器中与时间相关的函数和维护模式的进程

## escalator
处理action中的步骤升级

# web配置
##  unsupported items的检测时间间隔
zabbix服务器检测不支持key的时间间隔

Administrator -> General -> Other -> Refresh unsupported items (in sec)
默认600秒

# zabbix调优
## 调优原则
1.尽可能单独部署各个组件

比如数据库采用配置最好的服务器进行部署，web前端采用一般配置的服务器

2.尽可能采用分布式部署

3.重点关注数据库服务器

4.尽可能统一监控项类型，关闭不需要的进程

5.合理的配置数据采集的时间间隔

6.规划好模板

7.及时处理不支持的监控项和触发器

8.合理的使用函数

9.合理的选择监控项的数据类型

数值类比文本类的性能要好得多

## 操作系统层面的优化

# 低级自动发现 - LLD
## 流程
1.创建模板

2.基于模板创建自动发现规则

3.基于自动发现规则创建item

4.主机关联该模板

## 支持的自动发现
https://www.zabbix.com/documentation/4.0/zh/manual/discovery/low_level_discovery

# zabbix中的正则表达式
## 配置
![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1550134093/ops/regex01.png)

Administrator->General->Select

## 说明
zabbix中的正则相当于一个过滤的功能，比如有30个项目，符合正则的就20个，那么最后只保存这20个。

## 用法
```
@regex
```

## Expression type
### Character string included
Expression必须和目标字符串一模一样，结果才为TRUE

### Any character string included
对目标字符串使用指定分隔符分割，分割的对象中有一个匹配Expression，结果为TRUE

### Character string included
不解释

### Result is TRUE
这个才是通常意义上的正则匹配！只要目标字符串匹配Expression，结果为TRUE。
比如：Expression为[abc]，目标字符串为apple，结果为TRUE

### Result is FALSE
不解释