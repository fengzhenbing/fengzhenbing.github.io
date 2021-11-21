---
title: "test"
date: 2021-07-21
description: test
draft: true
tags:
    - test
categories:
    - test


---

centos  7	

https://blog.csdn.net/lanmei618/article/details/80096761s		

yum -y install git 

 

1 建一个版本库

mkdir vue-sports-museum.git

git --bare init

chown -R git:git vue-sports-museum.git/

2 推送文件

mkdir vue-sports-museum

  cd vue-sports-museum  

 git init

git remote add origin git@106.14.76.240:/home/git/repository/vue-sports-museum.git

git pull origin master

sd090)()mengmengww

新建分支

 git checkout -b dev_zb

git log

git status

git log -oneline

git reset --hard 51b86858ac5a38feaa1f8649c5dfd4fc651067a2

git reset --hard HEAD^