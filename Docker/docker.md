<center>Docker 教程</center>

### 在WSL2中使用docker，自行百度

### Docker 命令集合

+ 数据卷挂载

  ```dockerfile
  docker run -it -v  /宿主机绝对路径目录:  /容器内目录  镜像名
  ```



#### 关于镜像的基本操作

```dock
docker search centos # 查看centos镜像是否存在
docker pull centos    # 利用pull命令获取镜像
docker images    # 查看当前系统中的images信息
docker run -it centos:latest /bin/bash    # 启动一个容器
exit # 推出容器
docker ps -a
# 将容器转化为镜像
docker commit -m "centos with git" -a "qixianhu" 72f1a8a0e394 xianhu/centos:git
-m指定说明信息；
-a指定用户信息；
72f1a8a0e394代表容器的id；
xianhu/centos:git指定目标镜像的用户名、仓库名和 tag 信息。注意这里的用户名xianhu，后边会用到。
```



#### 利用Dockerfile创建镜像

+ dockerfile， 一种配置文件，告诉dockerbuild命令应该执行哪些操作

  ```dockerfile
  # 说明该镜像以哪个镜像为基础
  FROM centos:latest
  
  # 构建者的基本信息
  MAINTAINER xianhu
  
  # 在build这个镜像时执行的操作
  RUN yum update
  RUN yum install -y git
  
  # 拷贝本地文件到镜像中
  COPY ./* /usr/share/gitdir/
  ```

+ 利用build命令构建镜像

  ```dockerfile
   docker build -t="xianhu/centos:gitdir" 
   其中-t用来指定新镜像的用户信息、tag等。
   最后的点表示在当前目录寻找Dockerfile。
  ```

+ 删除镜像和容器

  ```dockerfile
  docker rm container_name/container_id
  docker rmi image_name/image_id
  删除镜像前必须先删除以此镜像为基础的容器。
  ```

+ 镜像其它命令

  ```dockerfile
  docker save -o centos.tar xianhu/centos:git    # 保存镜像, -o也可以是--output
  docker load -i centos.tar    # 加载镜像, -i也可以是--input
  ```



#### docker 容器的基本操作

```dockerfile
docker run -it centos:latest /bin/bash # 启动容器
docker run -d centos:latest /bin/bash -c "while true; do echo hello; sleep 1; done" -d # 使容器后台运行

# 启动、停止、重启容器命令：
docker start container_name/container_id
docker stop container_name/container_id
docker restart container_name/container_id

# 后台启动容器 要进入此容器
docker attach container_name/container_id
```



#### docker 仓库的基本操作

```dockerfile
docker login # 登陆docker hub
docker push xianhu/centos:git    # 将镜像推送到DockerHub上

```

