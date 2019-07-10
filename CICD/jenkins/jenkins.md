> jenkins

Table of Contents
=================

* [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  * [jenkins](#jenkins)
  * [目录结构](#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84)
* [文档](#%E6%96%87%E6%A1%A3)
* [pipeline](#pipeline)
  * [环境说明](#%E7%8E%AF%E5%A2%83%E8%AF%B4%E6%98%8E)
  * [jenkins必须安装的插件](#jenkins%E5%BF%85%E9%A1%BB%E5%AE%89%E8%A3%85%E7%9A%84%E6%8F%92%E4%BB%B6)
  * [jenkinsfile编辑器推荐](#jenkinsfile%E7%BC%96%E8%BE%91%E5%99%A8%E6%8E%A8%E8%8D%90)
  * [jenkins换主题](#jenkins%E6%8D%A2%E4%B8%BB%E9%A2%98)
  * [步骤](#%E6%AD%A5%E9%AA%A4)
    * [1\.gitlab创建一个测试用户test](#1gitlab%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%B5%8B%E8%AF%95%E7%94%A8%E6%88%B7test)
    * [2\.test用户创建一个springcloud项目（勾选初始化README\.md）](#2test%E7%94%A8%E6%88%B7%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AAspringcloud%E9%A1%B9%E7%9B%AE%E5%8B%BE%E9%80%89%E5%88%9D%E5%A7%8B%E5%8C%96readmemd)
    * [3\.添加git客户端的SSH Key](#3%E6%B7%BB%E5%8A%A0git%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%9A%84ssh-key)
    * [4\.git客户端上操作](#4git%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%B8%8A%E6%93%8D%E4%BD%9C)
    * [5\.jenkins配置gitlab地址](#5jenkins%E9%85%8D%E7%BD%AEgitlab%E5%9C%B0%E5%9D%80)
  * [6\.构建项目](#6%E6%9E%84%E5%BB%BA%E9%A1%B9%E7%9B%AE)
  * [全局变量](#%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F)
    * [docker](#docker)
  * [配置邮件](#%E9%85%8D%E7%BD%AE%E9%82%AE%E4%BB%B6)
  * [多分支pipeline](#%E5%A4%9A%E5%88%86%E6%94%AFpipeline)
  * [共享库](#%E5%85%B1%E4%BA%AB%E5%BA%93)
  * [最佳实践](#%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5)
    * [1\.](#1)
    * [2\.](#2)
    * [3\.](#3)
* [二次开发](#%E4%BA%8C%E6%AC%A1%E5%BC%80%E5%8F%91)
  * [golang实现的jenkins api](#golang%E5%AE%9E%E7%8E%B0%E7%9A%84jenkins-api)

# 基本概念
## jenkins
jenkins是一个CICD工具，主要作用是代码的构建和发布。

## 目录结构
目录 | 说明
---|---
jobs | 创建的job及相关数据都会放在该目录
users | 本地数据库的方式下，用户文件存放点
war | jenkins.war包会解压到该目录，winstone.jar文件的存放点
secrets | 初始密码就存放在这里,initialAdminPassword

# 文档
https://jenkins.io/zh/doc/book/

# pipeline
## 环境说明
服务器
```
jenkins server: 10.186.63.9:8080
gitlab server: 10.186.63.77
应用服务器: 10.186.63.76
git客户端: 10.186.63.9
```

账号
```
gitlab用户: test
密码: test123456
```

## jenkins必须安装的插件
```
Pipeline	
GitLab
Git Parameter
Extended Choice Parameter
Email Extension
Workspace Cleanup
```

## jenkinsfile编辑器推荐
推荐使用vscode，需要安装插件：
```
jenkins-pipeline-linter-connector
```
## jenkins换主题
1.安装`Simple Theme`插件

2.到GitHub搜索`jenkins theme`

这里使用 http://afonsof.com/jenkins-material-theme/，可以自己制作图标和主题，然后把主题下载到本地，之后上传css文件到网盘，直接在系统设置中配置css文件的URL即可！

## 步骤
### 1.gitlab创建一个测试用户test

### 2.test用户创建一个springcloud项目（勾选初始化README.md）

### 3.添加git客户端的SSH Key

### 4.git客户端上操作
初始化仓库
```
cd /mnt/git
git init
```

上传项目到仓库

初始化git
```
git config --global user.name jenkins_server
git config --global user.email jenkins_server@test.com
```

提交
```
git add spring-cloud-config-server/
git commit -m "springcloud config-server"
```

添加解析
```
vim /etc/hosts
10.186.63.77 test.gitlab.com
```

推送到gitlab server
```
git remote add ssh-origin ssh://git@test.gitlab.com/test/springcloud.git
git pull ssh-origin master
git push -u ssh-origin master #仅仅第一次加上-u选项
```

gitlab创建test分支，之后可以推送代码到远程test分支
```
git push ssh-origin master:dev
```

### 5.jenkins配置gitlab地址
1.首先在 凭据->系统->全局凭据 创建两个账号
```
GitLab API token (gitlab-token-01)
test/****** (test-username-password)
```
注意：需要先在gitlab->Settings->access tokens中创建一个personal token，scope选择api。

2.在 系统管理->系统设置->Gitlab中配置仓库地址

3.在项目中直接使用即可

4.创建一个list视图 `springcloud-framework`

5.在视图中创建一个job`config-server`

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560243835/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190611151905.png)

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560243835/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190611152122.png)

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560243967/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190611170542.png)

注意：	Script Path就是jenkinsfile的名称，默认是`Jenkinsfile`，如果不一致记得修改！

jenkinsfile
```
node {
    stage('检出代码') {
        git branch: '${branch_name}', credentialsId: 'test-username-password', url: 'http://test.gitlab.com/test/springcloud.git', changelog: true
    }
    stage('Maven构建') {
        println "service: ${service_name}"
        dir('./') {
             sh """
            pwd
            ls
            """
        }
    }
    stage('部署项目到远程服务器') {
        dir('/data/build') {
            sh '/data/build/test.sh'
        }
    }
}
```

## 6.构建项目
之后点击参数化构建即可。

## 全局变量
### docker
用来访问和docker相关的函数。

## 配置邮件
在 Jenkins 的主界面，进入系统管理，系统设置，然后找到 Extended E-mail Notification，添加与邮件通知类似的配置:

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560499884/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190614160907.png)

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560499885/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190614160936.png)

![](https://res.cloudinary.com/dkkg9pm0i/image/upload/v1560499885/%E5%8F%91%E5%B8%83%E7%B3%BB%E7%BB%9F/QQ%E6%88%AA%E5%9B%BE20190614160959.png)

## 多分支pipeline
不同的分支发布到不同的环境，比如说合并到test分支，就自动构建到测试环境，合并到releas就上预发布，master就上线了

每个分支的Jenkinsfile都可以不同，比如说不同的环境可能要做一些特殊的操作啥的

## 共享库
pipeline提供了一种共享库的机制，可以将一些通用的代码放到gitlab，之后任何项目都可以导入共享库，并且使用这些通用的代码。

1.首先得将共享库提交到gitlab
```
.
├── README.md
└── vars
    └── test.groovy
```

```
test.groovy
def call() {
  echo '完成!'
}

def ok(a) {
  echo 'ok!'
  echo "args: ${a}"
}
```

2.在jenkins配置该共享库

配置->系统配置->全局共享库

3.在项目中使用共享库
```
@Library('test-library') _

node {
    stage('测试') {
        test()
        test.ok('hehe')
    }
}
```

## 最佳实践
### 1.
```
node {
    try {
        stage('拉取代码') {
            git branch: '${branch_name}', credentialsId: 'test-username-password', url: 'http://test.gitlab.com/test/springcloud.git'
        }
        stage('项目构建') {
            echo currentBuild.JOB_NAME
            dir('./') {
                sh """
                echo "开始构建..."
                echo "构建完成"
                """
            }
        }
        stage('部署项目') {
            echo "开始部署项目 "
        }
    } catch (e) {
        echo "Failed: ${e}"
        echo "result: ${currentBuild.result}"
        emailext(
            subject: "构建结果通知【失败】",
            body: "构建项目:${BUILD_URL}\r\n构建失败，\r\n错误消息：${e.toString()}",
            to: '${EMAIL}'
        )
        throw e
    } finally {
		// 清空工作空间
        cleanWs notFailBuild: true
	}
}
```

### 2.
```
node {
    try {
        stage('拉取代码') {
            git branch: '${branch_name}', credentialsId: 'test-username-password', url: 'http://test.gitlab.com/test/${project_name}.git'
        }
        stage('项目构建') {
            dir('./') {
                sh """
                cd ${service_name}
                echo "开始构建..."
                /usr/local/maven/bin/mvn clean package -Dmaven.test.skip=true
                echo "构建完成"
                cp target/*.jar /data/build
                """
            }
        }
	   	stage('存档') {//注释
	       	def jar = getShEchoResult("find ./ -name '*.jar'")
	       	echo jar
	       	def artifactsDir="artifacts"//存放产物的文件夹
	        ///usr/local/jenkins/jobs/demo/builds/xx/archive/artifacts
	        sh "mkdir ${artifactsDir}"
	       	sh "mv ${jar} ${artifactsDir}"
	       	archiveArtifacts "${artifactsDir}/*"
	   	}
        stage('部署项目') {
            dir('/data/build') {
                sh '/data/build/test.sh'
                echo "传输代码到远程应用服务器..."
                sh 'scp *.jar 10.186.63.76:/tmp'
                //执行远程脚本 
                sh """
                ssh 10.186.63.76 "cd /usr/local/scripts/deploy;sh run_java.sh"
                """
            }
        }
    } catch (e) {
        echo "Failed: ${e}"
        echo "result: ${currentBuild.result}"
        emailext(
            subject: "构建结果通知【失败】",
            body: "构建项目:${BUILD_URL}\r\n构建失败，\r\n错误消息：${e.toString()}",
            to: '${EMAIL}'
        )
        throw e
    } finally {
		// 清空工作空间
        cleanWs notFailBuild: true
	}
}

// 获取 shell 命令输出内容
def getShEchoResult(cmd) {
    def getShEchoResultCmd = "ECHO_RESULT=`${cmd}`\necho \${ECHO_RESULT}"
    return sh (
        script: getShEchoResultCmd,
        returnStdout: true
    ).trim()
}
```

### 3.
```
node {
    try {
        stage('拉取代码') {
            git branch: '${branch_name}', credentialsId: 'test-username-password', url: 'http://test.gitlab.com/test/${project_name}.git'
        }
        stage('项目构建') {
            dir('./') {
                sh """
                cd ${service_name}
                echo "开始构建..."
                /usr/local/maven/bin/mvn clean package -Dmaven.test.skip=true
                echo "构建完成"
				mkdir /data/build/${service_name}
                cp target/*.jar /data/build/${service_name}
                """
            }
        }
        stage('部署项目') {
            dir('/data/build') {
                echo "传输代码到远程应用服务器..."
                sh 'scp ${service_name}/*.jar 10.186.63.76:/opt'
                //执行远程脚本 
                sh """
                ssh 10.186.63.76 "cd /usr/local/scripts/deploy;sh run_java.sh ${service_name}"
                """
            }
        }
    } catch (e) {
        echo "Failed: ${e}"
        echo "result: ${currentBuild.result}"
        emailext(
            subject: "构建结果通知【失败】",
            body: "构建项目:${BUILD_URL}\r\n构建失败，\r\n错误消息：${e.toString()}",
            to: '${EMAIL}'
        )
        throw e
    } finally {
		// 清空工作空间
        cleanWs notFailBuild: true
	}
}
```

# 二次开发
## golang实现的jenkins api
```
package main

import (
	"fmt"

	"github.com/bndr/gojenkins"
)

func main() {
	jenkins := gojenkins.CreateJenkins(nil, "http://10.186.63.9:8080/", "Admin", "864160c05c3344d3bb7905750f4eb946")
	// Provide CA certificate if server is using self-signed certificate
	// caCert, _ := ioutil.ReadFile("/tmp/ca.crt")
	// jenkins.Requester.CACert = caCert
	_, err := jenkins.Init()

	if err != nil {
		fmt.Println("err: ", err)
		panic("Something Went Wrong")
	}

	build, err := jenkins.GetJob("config-server")
	if err != nil {
		panic("Job Does Not Exist")
	}
	lastSuccessBuild, err := build.GetLastSuccessfulBuild()
	if err != nil {
		panic("Last SuccessBuild does not exist")
	}

	output := lastSuccessBuild.GetConsoleOutput()
	fmt.Println("console output: ", output)

}
```
