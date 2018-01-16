---
title: 不能不懂的git命令
date: 2017-04-07 11:31:31
categories:
- coding
- git
tags:
- git
- coding
comments: false
toc: true
---
> 不能不懂的git命令
<!-- more -->

## 创建相关
### 1. Git 全局设置

```
git config --global user.name "lqclester"
git config --global user.email "lqclester@163.com"
```


### 2. 创建 git 仓库:
```
mkdir sport_record
cd sport_record
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin url(git@github.com:lqclester/sportrecord.git)
git push -u origin master
```

### 3. 已有项目?
```
cd existing_git_repo
git remote add origin git@git.oschina.net:lqclester/sport_record.git
git push -u origin master
```

### 4. 生成key
```
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"  

# Generating public/private rsa key pair...
# 三次回车即可生成 ssh key

cat ~/.ssh/id_rsa.pub | clip
```

## 开发经常使用
### 查看分支(git branch)
```
git branch
```
### 创建分支(git branch name)
```
git branch name
```

### 切换分支(git checkout name)
```
git checkout name
```

### 创建+切换分支
```
git checkout -b name
```

### 推送到远程仓库
```
 git push origin name
```
### 更新并推送代码
```
git push -u origin name
```
### 更新本地暂存区
```
git pull
```
### 删除本地分支
```
git branch -d 分支名
```
###删除远程仓库
```
git  push origin :name
```
### 还原版本分支
```
git reset --hard
```
### 清除所有新增文件
```
git clean -fd
```
### 移除索引
```
git rm --cached --force src/main/resources/filename
```
