---
title: "k8s Kubectl命令"
date: 2021-06-10
description: k8s相关命令记录
tags:
    - 微服务
    - 云原生
    - K8S
categories:
    - 云原生
    - 微服务
    - K8S
---

## kubelet日志

```shell
journalctl -fu kubelet

kubectl  -h

kubectl get namespaces


kubectl get pods -A

kubectl logs -f --tail=200  -l app=account -n bookstore-microservices 
```


## 常用命令

```shell
#查看端口映射
kubectl get svc -n kube-system
#查看 secret
kubectl get secret -n kube-system 
#查看 token
kubectl describe secret kubernetes-dashboard --namespace=kube-system
#k8s 无法启动，查看日志，查找Failed
journalctl -xefu kubelet
#查看pod错误日志
kubectl logs kubernetes-dashboard-8556c848b7-4kpzd --namespace=kube-system
#对资源进行配置
kubectl apply -f kubernetes-dashboard.yaml
kubectl delete -f kubernetes-dashboard.ya
```



## YAML配置文件管理对象

```shell
对象管理：
# 创建deployment资源
kubectl create -f nginx-deployment.yaml
# 查看deployment
kubectl get deploy
# 查看ReplicaSet
kubectl get rs
# 查看pods所有标签
kubectl get pods --show-labels
# 根据标签查看pods
kubectl get pods -l app=nginx
# 滚动更新镜像
kubectl set image deployment/nginx-deployment nginx=nginx:1.11
或者
kubectl edit deployment/nginx-deployment
或者
kubectl apply -f nginx-deployment.yaml
# 实时观察发布状态：
kubectl rollout status deployment/nginx-deployment
# 查看deployment历史修订版本
kubectl rollout history deployment/nginx-deployment
kubectl rollout history deployment/nginx-deployment --revision=3
# 回滚到以前版本
kubectl rollout undo deployment/nginx-deployment
kubectl rollout undo deployment/nginx-deployment --to-revision=3
# 扩容deployment的Pod副本数量
kubectl scale deployment nginx-deployment --replicas=10
# 设置启动扩容/缩容
kubectl autoscale deployment nginx-deployment --min=10 --max=15 --cpu-percent=80
```
