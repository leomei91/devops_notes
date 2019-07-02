[toc]
# 项目说明
devops_notes项目是个人平时运维工作的笔记。

# Github项目规范
## 1.GitHub上添加公钥
github - Settings - SSH and GPG keys - New SSH key

## 2.创建项目
```
mkdir devops_notes
cd devops_notes
git init 
git config --global user.name leomei91
git config --global user.email leo_mp@163.com
git remote add ssh-origin ssh://git@github.com/leomei91/devops_notes.git
```

## 3.Github上创建一个仓库
Your repositories - New

## 4.本地拉取和推送
```
git pull ssh-origin master
git push -u ssh-origin master # 第一次加上-u选项
```