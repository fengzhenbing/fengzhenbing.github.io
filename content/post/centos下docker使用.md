---
draft: true
---


```shell
## 脚本自动安装docker
curl -fsSL https://get.docker.com | bash -s docker --mirror aliyun
## 阿里源
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo;
## 运行    
sudo systemctl start docker
sudo systemctl stop docker
## 安装nginx
docker search nginx
docker pull nginx
docker run --name my-nginx --privileged=true  -it -p 80:80 \
-v /home/docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf:rw \
-v /home/docker/nginx/conf/conf.d:/etc/nginx/conf.d:rw \
-v /home/docker/nginx/html:/usr/share/nginx/html:rw \
-v /home/docker/nginx/logs:/var/log/nginx -d nginx;

## 文件挂载
docker exec -it 0a57986f95041fa415a3ed4dcd9fa07642b5cd93e3c5e5810a9a2613f36f25e1 /bin/bash
docker cp  0a57986f95041fa415a3ed4dcd9fa07642b5cd93e3c5e5810a9a2613f36f25e1:/etc/nginx/conf.d   /home/docker/nginx/conf/  
docker cp  0a57986f95041fa415a3ed4dcd9fa07642b5cd93e3c5e5810a9a2613f36f25e1:/etc/nginx/nginx.conf  /home/docker/nginx/conf/  
```

