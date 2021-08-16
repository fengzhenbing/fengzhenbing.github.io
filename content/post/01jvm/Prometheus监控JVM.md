---
title: Prometheus监控JVM
date: 2021-08-15T14:16:25+06:00
description: Prometheus监控JVM,grafana展示
categories:                                 
    - JVM
    - 中间件
    - 运维
tags:
    - JVM
    - 中间件
    - 运维
---

## 1. jmx_exporter

### 下载jmx_exporter

```shell
ubuntu:/# mkdir -p /usr/local/prometheus/jmx_exporter 
ubuntu:/# cd /usr/local/prometheus/jmx_exporter
ubuntu:/# wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.16.1/jmx_prometheus_javaagent-0.16.1.jar
```

### 配置文件jmx_exporter

jmx_exporter.yml

```
 vim /usr/local/prometheus/jmx_exporter/jmx_exporter.yml
---
rules:
- pattern: ".*"
```

### java agent运行jmx_exporter

以java agent的方式启动你的一个java应用

```shell
java -javaagent:/usr/local/prometheus/jmx_prometheus_javaagent-0.16.1.jar=3010:/usr/local/prometheus/jmx_exporter.yml  -jar  xxx-web-0.1-SNAPSHOT.jar
```



   ##  2. prometheus    

### docker方式下载运行                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      

```shell
# docker pull prom/prometheus    #下载docker镜像
# mkdir -p /etc/prometheus
# vim /etc/prometheus/prometheus.yml  #配置
# docker run -d \
    -p 192.168.3.13:9090:9090 \
    -v /etc/prometheus:/etc/prometheus \
    prom/prometheus;
```

### prometheus中配置上步的jmx的metrics

```yml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s
alerting:
  alertmanagers:
  - follow_redirects: true
    scheme: http
    timeout: 10s
    api_version: v2
    static_configs:
    - targets: []
scrape_configs:
- job_name: prometheus
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  follow_redirects: true
  static_configs:
  - targets:
    - 192.168.3.13:9090
  ### 以下为jmx_exporter地址：需改为你实际的
- job_name: 'jmx'
  static_configs:
  scrape_interval: 15s
  - targets: ['192.168.3.14:3010']
```

### 验证

访问http://192.168.3.13:9090 可看到读取的jmx_exporter的metrics  

![image-20210815150732316](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210815150732316.png)



## 3. Grafana连接Prometheus



```
docker pull grafana/grafana
docker run -d --name=grafana -p 3000:3000 grafana/grafana

```

访问http://192.168.3.13:3000  使用admin/admin即可登录

### 配置prometheus数据源

![image-20210815153009091](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210815153009091.png)



### 配置dashboard

找一个合适的dashboard导入。如https://grafana.com/grafana/dashboards/3457

![image-20210815160530374](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210815160530374.png)

### 效果

![image-20210815160638492](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210815160638492.png)

## 参考

* prometheus安装 https://prometheus.io/docs/prometheus/latest/installation/

- JMX Exporter 项目地址: https://github.com/prometheus/jmx_exporter
- JVM 监控面板: https://grafana.com/grafana/dashboards/3457
- Grafana(https://grafana.com/)

