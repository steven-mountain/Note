<center>Linux docker</center>

###  查看linux系统信息

```bash
cat /etc/os-release
```



### 安装docker

来自于知乎雪梦科技 -- 如何在 Ubuntu 20.04 上安装和使用 Docker

https://zhuanlan.zhihu.com/p/143156163

  ```bash
  # 更新软件包索引，并且安装必要的依赖软件，来添加一个新的 HTTPS 软件源
  apt update
  # 安装必要的证书并允许 apt 包管理器使用以下命令通过 HTTPS 使用存储库
  apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  
  # 添加 Docker 的官方 GPG 密钥
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  
  # Docker 官方库
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  
  # 安装docker 最新版本
  apt update
  apt install docker-ce docker-ce-cli containerd.io
  
  # 安装指定版
  pt update
  list -a docker-ce
  apt install docker-ce=<VERSION> docker-ce-cli=<VERSION> containerd.io
  
  # 验证 docker服务安装完成就会自动启动
  sudo systemctl start docker
  # 使 Docker 服务在每次重启时自动启动
  sudo systemctl enable docker
  systemctl status docker
  
  # 更新docker
  apt update
  apt upgrade
  
  # 阻止docker更新
  apt-mark hold docker-ce
  ```



### 使用国内镜像加速

知乎 水木十三--- docker国内镜像加速

镜像列表

```bash
# docker中国区官方镜像加速：
https://registry.docker-cn.com

# 网易镜像加速：
http://hub-mirror.c.163.com

# 中国科技大学镜像加速：
https://docker.mirrors.ustc.edu.cn

# 腾讯云镜像加速：
https://mirror.ccs.tencentyun.com

# 阿里云镜像加速：
https://ung2thfc.mirror.aliyuncs.com
```



添加镜像加速，ubuntu为例

```bash
vim /etc/docker/daemon.json
```

添加如下内容

```json
{
    "registry-mirrors": [
        "https://ung2thfc.mirror.aliyuncs.com ",
        "https://registry.docker-cn.com",
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn"
    ]
}
```



加载重启docker

```bash
systemctl daemon-reload
systemctl restart docker
```





### 以非root用户身份执行docker

默认情况下，只有root或者sudo权限的用户可以执行docker命令

将用户添加到Docker用户组，该用户组在Docker CE软件包安装过程中被创建

```bash
usermod -aG docker &USER
-G 修改用户所属的附加群组
-g 修改主组
-a 追加
```

退出重新登陆，刷新权限



### 验证安装

```bash
# 验证docker是否被安装
docker run hello-world
```



### 卸载docker

```bash
# 停止所有正在运行的容器，并且移除所有的 docker 对象
docker stop $(docker container ls -aq)
docker system prune -a --volumes

# 卸载docker
apt purge docker-ce
apt autoremove
```





### docker中使用nvidia显卡 需要配置

```bash
# 设置包存储库和 GPG 密钥
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

# 下载安装 nvidia-docker2
sudo apt-get update
sudo apt-get install -y nvidia-docker2

# 重启 Docker 守护进程并测试
sudo systemctl restart docker
sudo docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
```



### docker 修改镜像源

+ 创建或修改 /etc/docker/daemon.json 

```json
{
    "registry-mirrors": [
        "https://registry.hub.docker.com",
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://registry.docker-cn.com"
    ]
}
```

+ 重启docker

```bash
systemctl restart docker
```

+ 检查

```ba
docker info
