> gitlab

Table of Contents
=================

* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [gitlab](#gitlab)
* [配置免密钥](#%E9%85%8D%E7%BD%AE%E5%85%8D%E5%AF%86%E9%92%A5)
  * [环境说明](#%E7%8E%AF%E5%A2%83%E8%AF%B4%E6%98%8E)
  * [1\.初始化git仓库](#1%E5%88%9D%E5%A7%8B%E5%8C%96git%E4%BB%93%E5%BA%93)
  * [2\.生成密钥对](#2%E7%94%9F%E6%88%90%E5%AF%86%E9%92%A5%E5%AF%B9)
  * [3\.创建一个gitlab项目](#3%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AAgitlab%E9%A1%B9%E7%9B%AE)
  * [4\.复制密钥到gitlab](#4%E5%A4%8D%E5%88%B6%E5%AF%86%E9%92%A5%E5%88%B0gitlab)
  * [5\.推送本地仓库到gitlab](#5%E6%8E%A8%E9%80%81%E6%9C%AC%E5%9C%B0%E4%BB%93%E5%BA%93%E5%88%B0gitlab)
* [jenkins的使用](#jenkins%E7%9A%84%E4%BD%BF%E7%94%A8)
  * [安装插件](#%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6)
  * [配置](#%E9%85%8D%E7%BD%AE)
* [ssh原理](#ssh%E5%8E%9F%E7%90%86)
* [webhook](#webhook)
  * [1\.开启webhook](#1%E5%BC%80%E5%90%AFwebhook)
  * [2\.gitlab配置](#2gitlab%E9%85%8D%E7%BD%AE)
  * [3\.jenkins配置](#3jenkins%E9%85%8D%E7%BD%AE)
    * [全局安全配置](#%E5%85%A8%E5%B1%80%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)
    * [job中配置](#job%E4%B8%AD%E9%85%8D%E7%BD%AE)
* [git基本使用](#git%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8)
  * [查看远程分支](#%E6%9F%A5%E7%9C%8B%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF)
  * [推送本地分支master到远程分支dev](#%E6%8E%A8%E9%80%81%E6%9C%AC%E5%9C%B0%E5%88%86%E6%94%AFmaster%E5%88%B0%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AFdev)
* [其他](#%E5%85%B6%E4%BB%96)
  * [修改gitlab域名](#%E4%BF%AE%E6%94%B9gitlab%E5%9F%9F%E5%90%8D)

# 基本概念
## gitlab
gitlab是一个流行的代码仓库。

# 配置免密钥
## 环境说明
```
jenkins节点   node01
本地仓库    node02
gitlab节点  master
```

## 1.初始化git仓库
node02上执行：
```
mkdir -p /apps/test/git-demo
cd /apps/test/git-demo
git init
git config --global user.name node02
git config --global user.email
node02@node02.com
cat >> README.md << EOF
# a git demo node02 
- #01 
EOF
git add .
git commit -m "#01"
```

## 2.生成密钥对
node02上执行：
```
ssh-keygen -t rsa -C "node02"
```

## 3.创建一个gitlab项目
在web端创建用户 `ky` ，然后创建一个项目 `node02`

## 4.复制密钥到gitlab
在web端操作，`ky` 用户登录，Settings -> SSH Keys，复制node02的公钥到gitlab

## 5.推送本地仓库到gitlab
node02节点，在/etc/hosts添加gitlab服务器的域名解析：
```
vim /etc/hosts
gitlab_ip git.leo.com
```

推送
```
git remote add ssh-origin ssh://git@git.leo.com:2222/ky/node02.git
git push -u ssh-origin master
# 第一次加上 -u 选项，后面可以不加
```
如果node02第一次和gitlab服务器建立ssh连接，会要求确认，输入 `yes` 即可。

# jenkins的使用
## 安装插件
```
Gitlab
```

## 配置
web端，凭据 -> 全局凭据 -> 添加凭据 -> 

`SSH Username with private key` 

`Username` 随便填

`Private Key` **填写jenkins的私钥** 

将jenkins的公钥添加到gitlab

这里需要说明的是，其实只要任意节点的公钥添加到了gitlab，`Private Key` 就可以填写该节点的私钥。但是推荐只填写 `jenkins` 的私钥。

# ssh原理
比如A节点 ssh无密码 B节点

1.将A的公钥复制到B的 `authorized_keys` 

2.A会在自己的 `known_hosts` 中添加B节点的记录

注意：第一次A连接B会要求确认，确认之后A会在自己的 `known_hosts` 中添加B节点的记录。

# webhook
## 1.开启webhook
管理员登录web -> Admin Area -> Settings -> Network -> Outbound requests -> Allow requests to the local network from hooks and services打钩

## 2.gitlab配置
普通用户登录 -> 项目 -> Settings -> Integrations 

URL:
```
jenkins_url/job/job_name/build?token=xxx
```
token是在job中随便填写的。

提示：`test` -> `Push events` 可以测试webhook是否生效。

## 3.jenkins配置
### 全局安全配置
cors关闭

允许匿名用户访问


### job中配置
`Trigger builds remotely` -> token随便写

pipeline代码：
```
node {
    stage('git clone'){
        echo 'begin to git pull...'
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'aa508664-9cee-44ac-8fdf-a6587ce48eab', url: 'ssh://git@git.leo.com:2222/ky/node02.git']]])
    }
}
```

之后，在本地仓库每次 `push` 代码，都会自动触发jenkins中代码的构建。

# git基本使用
## 查看远程分支
```
git branch -r
```

## 推送本地分支master到远程分支dev
```
git push ssh-origin master:dev
```

# 其他
## 修改gitlab域名
```
vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
production: &base
  #              
  # 1. GitLab app settings
  # ==========================
                 
  ## GitLab settings
  gitlab:        
    ## Web server settings (note: host is the FQDN, do not include http://)
    host: test.gitlab.com                                                                                                                                   
    port: 80  
    https: false
```
重启
```
gitlab-ctl restart
```