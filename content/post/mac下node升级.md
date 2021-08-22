---
title: "mac下node升级"
date: 2021-07-21
description: mac下node升级
tags:
    - 网站
categories:
    - 网站
---


```shell
# 清除nodejs的cache
sudo npm cache clean -f 
# 由于您可能已经拥有node，最简单的安装方式n是npm：
sudo npm install -g n
# node所有版本
npm view node versions
#  升级到最新版本
sudo n latest
# 升级到稳定版本
sudo n stable
# 升级到具体版本号
sudo n xx.xx 
```

