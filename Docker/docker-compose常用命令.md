docker-compose 常用命令



### 常用的 Docker Compose 命令和说明：

```text
-  `docker-compose up` : 从 Docker Compose 文件构建并启动容器
-  `docker-compose down` : 停止并删除由 Docker Compose 文件定义的容器、网络和卷
-  `docker-compose ps` : 查看由 Docker Compose 文件定义的正在运行的容器信息
-  `docker-compose logs` : 查看由 Docker Compose 文件定义的容器的日志
-  `docker-compose build` : 仅构建 Docker Compose 文件定义的服务的镜像
-  `docker-compose pull` : 仅从 Docker Compose 文件定义的服务所需的镜像仓库中拉取镜像
-  `docker-compose restart` : 重启由 Docker Compose 文件定义的容器
-  `docker-compose stop` : 停止由 Docker Compose 文件定义的容器
-  `docker-compose start` : 启动由 Docker Compose 文件定义的容器
-  `docker-compose exec` : 在正在运行的容器中运行命令
```

以上是常用的 Docker Compose 命令。您可以在命令行中输入 `docker-compose` 并按下回车键，以查看完整的命令列表。 若要获取有关特定命令的详细信息，请输入 `docker-compose [command] --help` 。

### 1.docker-compose up

命令用于构建并启动由 Docker Compose 文件定义的服务。它支持以下参数：

```text
 -  `-d, --detach` : 后台运行服务容器，即“守护态”模式。
-  `--build` : 在启动容器前构建 Docker 镜像。如果您已经构建了 Docker 镜像，则可以使用此选项跳过此步骤，并直接启动容器。
-  `--force-recreate` : 无条件重新创建容器并启动它们。即使容器当前已经处在运行状态，也会重建容器并将其替换为新创建的容器。
-  `--no-deps` : 如果您的服务依赖于其他服务，使用此选项启动的时候将不会启动依赖服务。
-  `--scale SERVICE=NUM` : 通过指定  `SERVICE`  和  `NUM`  参数来扩展服务实例的数量。例如， `docker-compose up --scale web=3`  将启动三个  `web`  服务容器。
-  `SERVICE` : 指定要启动的服务。如果省略此参数，则将启动 docker-compose.yml 文件中定义的所有服务。
```

这些是 `docker-compose up` 命令常用的选项和参数。您可以在命令行中输入 `docker-compose up --help` 获取更多详细信息。

### 2.docker-compose down

命令用于停止并删除由 Docker Compose 文件定义的容器、网络和卷。它支持以下参数：

```text
-  `-v, --volumes` : 删除挂载的数据卷。
-  `-t, --timeout TIMEOUT` : 在杀死容器之前等待的秒数。默认为 10 秒。
```

这些是 `docker-compose down` 命令常用的选项和参数。您可以在命令行中输入 `docker-compose down --help` 获取更多详细信息。

### 3.docker-compose logs

命令用于查看由 Docker Compose 文件定义的服务容器的日志。它支持以下参数：

```text
-  `-f, --follow` : 跟随日志输出的变化。即，实时输出最新日志。
-  `--tail NUM` : 从日志末尾开始显示最后 N 行日志。默认是 all，表示显示所有日志。
-  `SERVICE` : 指定要查看日志的服务。如果省略此参数，则将显示所有服务的日志。
```

这些是 `docker-compose logs` 命令常用的选项和参数。您可以在命令行中输入 `docker-compose logs --help` 获取更多详细信息。

### 4.docker-compose exec

命令用于在正在运行的 Docker Compose 服务中的容器上执行命令。它支持以下参数：

```text
-  `-d, --detach` : 在后台运行命令并立即返回。
-  `--privileged` : 以特权模式运行容器。
-  `USER` : 指定要使用的用户名称或 UID。
-  `SERVICE` : 指定要在其中执行命令的服务。
-  `COMMAND` : 指定要在容器中运行的命令。
```

这些是 `docker-compose exec` 命令常用的选项和参数。您可以在命令行中输入 `docker-compose exec --help` 获取更多详细信息。

### 5.docker-compose build

命令用于构建 Docker Compose 文件中定义的服务容器。它支持以下参数:

```text
-  `--no-cache` : 构建时不使用缓存。
-  `--pull` : 在构建之前始终拉取最新的镜像。
-  `--parallel` : 同时构建多个服务（多个容器的构建过程并行执行）。
-  `--force-rm` : 在构建之前强制删除已存在的容器。
-  `SERVICE` : 指定要构建的服务。如果省略此参数，则将构建 docker-compose.yml 文件中定义的所有服务。
```

这些是 `docker-compose build` 命令的常用选项和参数。您可以在命令行中输入 `docker-compose build --help` 获取更多详细信息。

### 6.docker-compose pull

命令用于拉取 Docker Compose 文件中定义的所有服务的镜像。它支持以下参数:

```text
-  `SERVICE` : 指定要拉取镜像的服务。如果省略此参数，则将拉取 docker-compose.yml 文件中定义的所有服务的镜像。
-  `--ignore-pull-failures` : 如果拉取任何镜像失败，则继续拉取其他镜像。
```

这些是 `docker-compose pull` 命令的常用选项和参数。您可以在命令行中输入 `docker-compose pull --help` 获取更多详细信息。



### docker-compose 编排容器一直处于restarting 状态

docker-compose编排容器踩坑之容器一直处于Restarting状态
问题：
在用docker-compose编排容器的时候发现执行up命令后，发现容器的状态一直是Restarting状态。

原因：
`docker-compose.yml`文件中的`command`块不是以`/bin/bash`结束，导致`docker-compose`进程无法正常退出（`docker-compose up` 命令和`command`里面的命令是在同一个进程中完成的），所以一直导致`Restarting`状态。————————————————
版权声明：本文为CSDN博主「Nathan_Z」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/snqfyyzs/article/details/119813498



当容器通过“docker-compose up -d”启动时,容器立即终止。

您需要一个名为“tty: true”的选项来保持容器运行。

“tty”似乎与 Linux 命令相同。

“tty”命令用于从命令行获取连接终端的设备文件名。

伪终端(也称为 tty 或 pts )将用户的“终端”与标准输入和标准输出流连接起来,通常(但不一定)通过诸如 bash 之类的外壳。



一个示例：

```yaml
version: '3.3'
services:
  ml-neilfpp-main:
    image: ubuntu:20.04  
    container_name: ml-neilfpp-main
    restart: always
    volumes:
      - Z:/722/hermesjang/paper:/outData
    tty: true
```



### docker-compose 使用 GPU

```yaml
services:
  test:
    image: tensorflow/tensorflow:latest-gpu
    command: python -c "import tensorflow as tf;tf.test.gpu_device_name()"
    deploy:
      resources:
        reservations:
          devices:
          - driver: nvidia
            # device_ids: ['0', '3']
            count: all
            capabilities: [gpu]
```

