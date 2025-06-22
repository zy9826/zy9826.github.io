# Docker用法


<!--more-->
## 镜像管理
1. 搜索官方仓库镜像：`docker search centos`
2. 拉取镜像：`docker pull centos`
3. 拉去第三方镜像：`docker pull index.tenxcloud.com/tenxcloud/httpd`
4. 查看当前主机镜像列表：`docker image list`
5. 删除镜像：`docker image rm centos:latest` `docker image rm [IMAGE ID]`
6. 导出镜像：`docker image save centos > docker-centos.tar.gz`
7. 导入镜像：`docker image load -i docker-centos.tar.gz `
8. 查看镜像详细信息：`docker image inspect centos`


## 容器管理
1. 运行容器：`docker run nginx`
2. 查看正在运行的容器：`docker container ls` `docker ps` 
3. 查看所有容器：`docker ps -a`
4. 查看容器详细信息：`docker container inspect 容器名称/id`
5. 容器start/stop/kill：`docker start/stop/kill 容器名称/id`
6. 删除容器：`docker rm 容器名称/id`
7. 删除所有容器: ``` docker rm -f `docker ps -a -q` ```


## 容器启动
1. 启动时交互：`docker run -it nginx:latest /bin/bash`
2. 退出容器：ctrl+p & ctrl+q
3. attach进入容器：`docker attach 1bf0f43c4d2f`
4. -p端口映射：`docker run -d -p 8888:80 nginx:latest #-p hostPort:containerPort -d detach`
5. 挂载时创建卷：`docker run -d -p 80:80 -v /data:/usr/share/nginx/html nginx:latest


## volume管理
1. 创建卷：`docker volume create [volume name]`
2. 查看卷列表：`docker volume ls`
3. 查看卷信息：`docker volumn inspect [volumn_name]`
4. 使用卷创建：`docker run -d -p 9000:80 -v clsn:/usr/share/nginx/html nginx:latest `


## 配置容器镜像
``` shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```


---

> 作者: Yu  
> URL: https://blog.zy9826.site/posts/df6cd2b0b2ad/  

