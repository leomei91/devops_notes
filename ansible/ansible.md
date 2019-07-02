* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [说明](#%E8%AF%B4%E6%98%8E)
  * [工作模式](#%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F)
  * [ansible的幂等性](#ansible%E7%9A%84%E5%B9%82%E7%AD%89%E6%80%A7)
  * [ansible role](#ansible-role)
  * [ansible tower](#ansible-tower)
  * [ansible galaxy](#ansible-galaxy)
  * [ansible的模板引擎](#ansible%E7%9A%84%E6%A8%A1%E6%9D%BF%E5%BC%95%E6%93%8E)
* [原理](#%E5%8E%9F%E7%90%86)
  * [ansible执行过程](#ansible%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B)
* [部署](#%E9%83%A8%E7%BD%B2)
  * [环境说明](#%E7%8E%AF%E5%A2%83%E8%AF%B4%E6%98%8E)
  * [安装](#%E5%AE%89%E8%A3%85)
  * [推荐配置](#%E6%8E%A8%E8%8D%90%E9%85%8D%E7%BD%AE)
* [使用](#%E4%BD%BF%E7%94%A8)
  * [yes和True](#yes%E5%92%8Ctrue)
  * [常见模块](#%E5%B8%B8%E8%A7%81%E6%A8%A1%E5%9D%97)
    * [shell](#shell)
    * [script](#script)
    * [setup](#setup)
  * [playbook](#playbook)
    * [目录结构](#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84)
    * [执行方式](#%E6%89%A7%E8%A1%8C%E6%96%B9%E5%BC%8F)
    * [增加并行能力](#%E5%A2%9E%E5%8A%A0%E5%B9%B6%E8%A1%8C%E8%83%BD%E5%8A%9B)
    * [输出分析](#%E8%BE%93%E5%87%BA%E5%88%86%E6%9E%90)
* [注意事项](#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
  * [1\.ansible命令行不要使用命令的别名，可能无法识别](#1ansible%E5%91%BD%E4%BB%A4%E8%A1%8C%E4%B8%8D%E8%A6%81%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E7%9A%84%E5%88%AB%E5%90%8D%E5%8F%AF%E8%83%BD%E6%97%A0%E6%B3%95%E8%AF%86%E5%88%AB)
* [文档](#%E6%96%87%E6%A1%A3)
* [例子](#%E4%BE%8B%E5%AD%90)
  * [复制文件](#%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6)
  * [下载文件](#%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6)
  * [yum安装](#yum%E5%AE%89%E8%A3%85)
  * [重启服务](#%E9%87%8D%E5%90%AF%E6%9C%8D%E5%8A%A1)
  * [查看主机组下所有主机](#%E6%9F%A5%E7%9C%8B%E4%B8%BB%E6%9C%BA%E7%BB%84%E4%B8%8B%E6%89%80%E6%9C%89%E4%B8%BB%E6%9C%BA)
    * [同时执行多个主机组](#%E5%90%8C%E6%97%B6%E6%89%A7%E8%A1%8C%E5%A4%9A%E4%B8%AA%E4%B8%BB%E6%9C%BA%E7%BB%84)
  * [ansible基于role部署apache](#ansible%E5%9F%BA%E4%BA%8Erole%E9%83%A8%E7%BD%B2apache)
    * [准备工作](#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C)
    * [目录结构](#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84-1)
    * [执行](#%E6%89%A7%E8%A1%8C)
    * [验证](#%E9%AA%8C%E8%AF%81)

# 基本概念
## 说明
1.对于每一个任务，ansible在每个主机之间是并行执行的

2.在开始执行下一个任务之前，ansible会等待当前任务在所有主机上执行完成

3.ansible是按照顺序来执行任务的

## 工作模式
ansible是采取主动推送的工作模式，可以立即执行任务；ansible也支持被动拉取模式（ansible-pull）。

## ansible的幂等性
ansible的模块是幂等的，如果用户不存在则创建，创建则不做操作。

同一台主机上的同一个任务，只会执行一次。

## ansible role
role用来实现更为复杂的任务，可以在不同系统中使用，可以复用。

## ansible tower
ansible的web管理后台。

## ansible galaxy
一个role的在线仓库。

## ansible的模板引擎
ansible 采用jinjia2作为模板引擎。

# 原理
## ansible执行过程
1.加载配置文件/etc/ansible/ansible.cfg

2.加载模块文件

3.通过ansible将模块和命令生成为py文件，将文件传输至目标主机的$HOME/.ansible/tmp目录下

4.指定py文件

5.删除py文件，退出

# 部署
## 环境说明
```
centos7
```

## 安装
控制节点安装ansible
```
yum install ansible -y
```

配置
```
vim /etc/ansible/hosts
[nodes]
node01
node02
node03
```

免密钥
```
# 控制节点 node01 也可以用 ansible 管理
ssh-copy-id root@node01
ssh-copy-id root@node02
ssh-copy-id root@node03
```

## 推荐配置
```
vim /etc/ansible/ansible.cfg
...
#不检查known_hosts文件
host_key_checking = False
#开启日志
log_path = /var/log/ansible.log 
...
```

# 使用
## yes和True
向模块传递参数时，使用`yes`；在剧本其他地方使用`True`。

## 常见模块
### shell
ansible默认使用 `command` 模块，但是很多时候并不能满足我们的需求。比如涉及到管道操作时，`command` 模块就无能为力了。

这个时候就需要用到 `shell` 模块了！

1.过滤进程
```
ansible gb_test -m shell -a 'ps -ef|grep -v grep|grep cron'
```

### script
script模块用来在所有主机执行本地shell脚本
```
ansible 'hosts' -m script -a 'test.sh'
```

### setup
我们可以通过setup模块查看主机的相关信息。

## playbook
但通常情况下，有很多任务需要执行，这个时候该怎么办呢？

难道把命令行全都放在一个脚本文件中？这种方式当然可行，但是明显不够完美。

ansible其实提供了一个剧本，让我们可以将多个任务放在一个剧本中，然后执行这个剧本就可以了！

- 剧本
```
# yml
- hosts: all # 指定执行节点
  tasks:
    - name: "ifconfig eht1"
      shell: "ifconfig eth1"

```

- 执行
```
$ ansible-playbook -i /etc/ansible/hosts asb_pb_test.yml 
```

### 目录结构
```
playbooks/
    hosts
    files/
    templates
```
hosts文件用来存放主机清单文件，files用来存放文件，templates用来存放jinjia2模板文件。

### 执行方式
```
ansible-playbook foo.yml
```
```
vim test.yml
#!/usr/bin/env ansible-playbook

./test.yml(可执行权限)
```

### 增加并行能力
```
vim ansible.cfg
[defaults]
forks = 20
```

### 输出分析
`GATHERING FACTS` ansible初始化时会去搜集主机的基本信息，这个就是在搜集中。

可以配置关闭这个搜集功能。
```
gather_facts: False
```
或
```
[defaults]
gathering=explicit
```

`changed` 主机状态和模块状态不一致，执行完后会出现`changed`；不改变状态为`ok`。

# 注意事项
## 1.ansible命令行不要使用命令的别名，可能无法识别
ansible命令行不要使用命令的别名，可能无法识别，比如：
```
ll
要写成
ls -l
```

# 文档
https://docs.ansible.com/

# 例子
## 复制文件
```
ansible nodes -m copy -a "src=/mnt/install_docker.sh dest=/mnt/install_docker.sh mode=755"
```

## 下载文件
```
ansible webservers -m get_url -a "url=http://www.baidu.com dest=/tmp/index.html  mode=0755 force=yes"
```

## yum安装
```
ansible webservers -m yum -a "name=httpd state=latest"
```

## 重启服务
```
ansible webservers -m service -a "name=httpd state=restarted"
```

## 查看主机组下所有主机
```
ansible hostgroup --list-hosts
```

### 同时执行多个主机组
```
ansible 'h1:h2' -a '...'
```

## ansible基于role部署apache
### 准备工作
配置域名解析
```
[root@vnode01 opt]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.186.63.76 vnode01
10.186.63.182 vnode02
10.186.63.9 vnode03
```

配置文件
```
/etc/ansible/hosts
...
[vnodes]
vnode01
vnode02
vnode03
```

ssh初始化

在vnode01执行：
```
ssh vnode01
ssh vnode02
ssh vnode03
```

### 目录结构
```
.
└── role
    ├── apache
    │   ├── files
    │   │   └── index.html
    │   ├── handlers
    │   │   └── main.yaml
    │   ├── tasks
    │   │   └── main.yaml
    │   ├── templates
    │   │   └── httpd.conf.j2
    │   └── vars
    │       └── main.yaml
    └── site.yaml

7 directories, 6 files
```

### 执行
```
ansible-playbook -vv role/site.yaml
```

### 验证
```
[root@vnode02 ~]# curl -vvv 127.0.0.1:880
* About to connect() to 127.0.0.1 port 880 (#0)
*   Trying 127.0.0.1...
* Connected to 127.0.0.1 (127.0.0.1) port 880 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.29.0
> Host: 127.0.0.1:880
> Accept: */*
> 
< HTTP/1.1 200 OK
< Date: Tue, 02 Jul 2019 02:20:51 GMT
< Server: Apache/2.4.6 (CentOS)
< Last-Modified: Tue, 02 Jul 2019 02:20:14 GMT
< ETag: "13-58ca962926d6a"
< Accept-Ranges: bytes
< Content-Length: 19
< Content-Type: text/html; charset=UTF-8
< 
welcome to apache!
* Connection #0 to host 127.0.0.1 left intact
```