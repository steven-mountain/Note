# docker-compose.yml配置文件写法

比较来自：https://zhuanlan.zhihu.com/p/387840381

## 1、version

版本信息，定义关乎docker的兼容性



## 2、build

指定构建镜像的dockerfile的上下文路径，或详细配置对象

```dockerfile
version: "3.9"
services:
  webapp:
    build: ./dir #指定路径
```

详细写法

```dockerfile
version: "3.9"
services:
  webapp:
    build:
      context: ./dir
      dockerfile: Dockerfile-alternate
      args:
        buildno: 1
```

- **context** 上下文路径，可以是文件路径，也可以是到链接到 git 仓库的 url。当是相对路径时，它被解释为相对于 Compose 文件的位置。
- **dockerfile** 指定构建镜像的 Dockerfile 文件名
- **args** 构建参数，只能在构建过程中访问的环境变量
- **cache_from** 缓存解析镜像列表
- **labels** 设置构建镜像的元数据
- **network** 设置网络容器连接，`none` 表示在构建期间禁用网络
- **shm_size** 设置`/dev/shm`此构建容器的分区大小
- **target** 多阶段构建，可以指定构建哪一层



## 3、network

默认情况下，Compose为您的应用程序设置单个网络。**services** 服务的每个容器都加入默认网络，并且可以被该网络上的其他容器访问。

您的应用程序网络的名称基于“项目名称”，也就是其所在目录的名称。您可以使用 --project-name 命令行选项 或 COMPOSE_PROJECT_NAME 环境变量覆盖项目名称。

例如，假设您的应用程序是在一个名为`myapp`目录下，`docker-compose.yml`如下：

例如，假设您的应用程序是在一个名为`myapp`目录下，`docker-compose.yml`如下：

```text
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:8000"
  db:
    image: postgres
    ports:
      - "8001:5432"
```

运行`docker-compose up`，会发生以下情况：

1. 创建了一个名为 myapp_default 的网络。
2. 把`web`加入网络。
3. 把`db`加入网络。

上面例子还有一个注意点就是端口号，注意区分`HOST_PORT`和`CONTAINER_PORT`，以上面的db为例：

- `8001` 是宿主机的端口
- `5432`（postgres的默认端口） 是容器的端口

当容器之间通讯时 ， 是通过 `CONTAINER_PORT` 来连接的。

我们可以通过设置一级配置network自定义网络，创建更复杂的网络选项，也可以用来连接已经存在的网络（不是通过compose创建的）

每个`service` 配置下也可以指定`networks`配置，来指定一级配置的网络。

```bash
version: "3"
services:

  proxy:
    build: ./proxy
    networks:
      - frontend
  app:
    build: ./app
    networks:
      - frontend
      - backend
  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
    # Use a custom driver
    driver: custom-driver-1
  backend:
    # Use a custom driver which takes special options
    driver: custom-driver-2
    driver_opts:
      foo: "1"
      bar: "2"
```

一级配置`networks` 创建了自定义的网络 。这里配置了两个`frontend`和`backend` ，且自定义了网络类型。

每一个services下，`proxy` , `app` , `db`都定义了`networks`配置。

1. `proxy` 只加入到 `frontend`网络。
2. `db` 只加入到`backend`网络。
3. `app`同时加入到 `frontend`和`backend` 。
4. `db`和`proxy`不能通讯，因为不在一个网络中。
5. `app`和两个都能通讯，因为`app`在两个网络中都有配置。
6. `db`和`proxy`要通讯，只能通过`app`这个应用来连接。

同一网络上的其他容器可以使用服务名称或别名来连接到其他服务的容器

```text
services:
  some-service:
    networks:
      some-network:
        aliases:
          - alias1
          - alias3
      other-network:
        aliases:
          - alias2
```

加入网络时，还可以指定容器的静态 IP 地址。

```text
version: "3.9"

services:
  app:
    image: nginx:alpine
    networks:
      app_net:
        ipv4_address: 172.16.238.10
        ipv6_address: 2001:3984:3989::10

networks:
  app_net:
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"
        - subnet: "2001:3984:3989::/64"
```

**一级networks还有如下这些配置：**

- driver 指定该网络应使用哪个驱动程序。默认使用`bridge`单个主机上的网络，overlay代表跨多个节点的网络群

```text
driver: overlay
```

- host or none 使用主机的网络堆栈，或者不使用网络。相当于`docker run --net=host`或`docker run --net=none`。仅在使用`docker stack`命令时使用。如果您使用该`docker-compose`命令，请改用 network_mode。
- driver_opts 将选项列表指定为键值对以传递给此网络的驱动程序

```text
driver_opts:
  foo: "bar"
  baz: 1
```

- attachable 仅在`driver`设置为 `overlay`时可用。如果设置为`true`，那么除了服务之外，独立容器也可以连接到此网络。

```text
networks:
  mynet1:
    driver: overlay
    attachable: true
```

- enable_ipv6 在此网络上启用 IPv6 网络。
- ipam 自定义 IPAM （IP地址管理）配置。

```text
ipam:
  driver: default
  config:
    - subnet: 172.28.0.0/16
```

- internal 默认情况下，Docker 会将桥接网络连接到它提供外部连接。如果要创建外部隔离的覆盖网络，可以将此选项设置为true。
- labels 添加元数据
- external如果设置为`true`，则指定此网络是在 Compose 之外创建的。`docker-compose up`不会尝试创建它，如果它不存在，则会引发错误。在下面的例子中，`proxy`是通往外界的门户。

```text
version: "3.9"

services:
  proxy:
    build: ./proxy
    networks:
      - outside
      - default
  app:
    build: ./app
    networks:
      - default

networks:
  outside:
    external: true
```

- name为此网络设置自定义名称。

```text
version: "3.9"
networks:
  network1:
    name: my-app-net
```



## 四、cap_add, cap_drop

添加或删除容器功能。

```text
cap_add:
  - ALL

cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```



## 五、cgroup_parent

为容器指定一个可选的父 cgroup。

```text
cgroup_parent: m-executor-abcd
```



## 六、command

覆盖容器启动后默认执行的命令

```text
command: bundle exec thin -p 3000
command: ["bundle", "exec", "thin", "-p", "3000"]
```



## 七、configs

为每个服务赋予相应的configs访问权限

- 简短语法，指定配置名称即可。以下示例授予`redis`服务访问`my_config`和`my_other_config`configs 的权限。

```text
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    configs:
      - my_config
      - my_other_config
configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true
```

- 长语法

- - `source`：配置名称
  - `target`：要挂载文件的路径和名称
  - `uid`和`gid`：容器的数字 UID 或 GID
  - `mode`：挂载在服务的任务容器中的文件的权限，以八进制表示。例如，`0444` 代表可读。
  - 以下示例在容器内设置configs名称为`my_config ，路径为redis_config`，将模式设置为`0440`（组可读）并将用户和组设置为`103`。该`redis`服务无权访问`my_other_config`配置。

```bash
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    configs:
      - source: my_config
        target: /redis_config
        uid: '103'
        gid: '103'
        mode: 0440
configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true
```

**一级`configs`详细配置：**

- `file`: 使用指定路径中的文件内容创建配置。
- `external`: 如果设置为 true，则指定此配置已经创建。Docker 不会尝试创建它，如果它不存在， 会报错`config not found`。
- `name`: Docker 中配置对象的名称。此字段可用于引用包含特殊字符的配置。
- `driver`和`driver_opts`：自定义驱动程序的名称，以及作为键/值对传递的特定于驱动程序的选项。
- `template_driver`：要使用的模板驱动程序的名称，它控制是否以及如何将配置负载作为模板。如果未设置驱动程序，则不使用模板。当前支持的唯一驱动程序是`golang`，它使用`golang`。

在下面例子中，`my_first_config`是通过confif_data文件内容创建的（就像 `<stack_name>_my_first_config)`部署堆栈时一样，并且`my_second_config`已经创建过。

```text
configs:
  my_first_config:
    file: ./config_data
  my_second_config:
    external: true 
```

当 Docker 中的配置名称与服务中存在的名称不同时，可以使用name进行配置。

```text
configs:
  my_first_config:
    file: ./config_data
  my_second_config:
    external:
      name: redis_config
```



## 八、container_name

指定自定义容器名称，而不是生成的默认名称。由于 Docker 容器名称必须是唯一的，因此如果您指定了自定义名称，则不能将服务扩展到 1 个以上的容器。



## 九、credential_spec

为托管服务帐户配置凭据规范。此选项仅用于使用 Windows 容器的服务。在`credential_spec`上的配置列表格式为`file://<filename>`或`registry://<value-name>`



## 十、depends_on

表示服务之间的依赖关系。服务依赖会导致以下行为：

- `docker-compose up`按依赖顺序启动服务。在下面的例子中，`db`和`redis`在 `web`之前启动。
- `docker-compose up SERVICE`自动包含`SERVICE`的依赖项。在下面的示例中，`docker-compose up web`还创建并启动`db`和`redis`。
- `docker-compose stop`按依赖顺序停止服务。在以下示例中，`web`在`db`和`redis`之前停止。

```bash
version: "3.9"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```



## 十一、deploy

指定与服务的部署和运行有关的配置。只在 swarm 模式下才会有用。

- **endpoint_mode** 访问集群服务的方式。

1. vip ：Docker 集群服务一个对外的虚拟 ip。所有的请求都会通过这个虚拟 ip 到达集群服务内部的机器。
2. dnsrr ：DNS 轮询（DNSRR）。所有的请求会自动轮询获取到集群 ip 列表中的一个 ip 地址。

**labels** 在服务上设置标签。可以用容器上的 labels（跟 deploy 同级的配置） 覆盖 deploy 下的 labels。

**mode** 指定服务提供的模式

1. global：全局服务，服务将部署至集群的每个节点。
2. replicated：复制服务，复制指定服务到集群的机器上。

**placement** 指定约束和首选项的位置

```bash
version: "3.9"
services:
  db:
    image: postgres
    deploy:
      placement:
        constraints:
          - "node.role==manager"
          - "engine.labels.operatingsystem==ubuntu 18.04"
        preferences:
          - spread: node.labels.zone
```

- 您可以通过定义约束表达式来限制可以安排任务的节点集。约束表达式可以使用匹配(`==`) 或排除(`!=`) 规则。多个约束查找可以使用 AND 匹配。约束可以匹配节点或 Docker 引擎标签，如下所示：

| 节点属性           | 匹配                        | 例子                                        |
| ------------------ | --------------------------- | ------------------------------------------- |
| node.id            | 节点 ID                     | node.id==2ivku8v2gvtg4                      |
| node.hostname      | 节点主机名                  | node.hostname!=node-2                       |
| node.role          | 节点角色 ( manager/ worker) | node.role==manager                          |
| node.platform.os   | 节点操作系统                | node.platform.os==windows                   |
| node.platform.arch | 节点架构                    | node.platform.arch==x86_64                  |
| node.labels        | 用户定义的节点标签          | node.labels.security==high                  |
| engine.labels      | Docker 引擎的标签           | engine.labels.operatingsystem==ubuntu-14.04 |

- **max_replicas_per_node** 如果服务是`replicated`（默认值），则限制任何时间在节点上运行的副本数
- **replicas** 如果服务是`replicated`（默认值），指定在任何给定时间应运行的容器数量。
- **resources** 配置资源约束。在下面示例中，`redis`服务被限制为使用不超过 50M 的内存和`0.50`（单核的 50%）可用处理时间 (CPU)，并保留`20M`内存和`0.25`CPU 时间（始终可用）。

```text
version: "3.9"
services:
  redis:
    image: redis:alpine
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
        reservations:
          cpus: '0.25'
          memory: 20M
```

- **restart_policy** 配置是否以及如何在退出时重新启动容器。替换`restart`

1. condition: none, on-failure 或 any (默认: any) 之一。
2. delay：重新启动尝试之间等待的时间（默认值：5s）。
3. max_attempts：在放弃之前尝试重新启动容器的次数（默认值：永不放弃）。如果在配置的窗口（window）内重新启动未成功，则此尝试不计入配置max_attempts值。例如，如果 max_attempts 设置为“2”，并且第一次尝试重启失败，则可能会尝试两次以上的重启。
4. window：在决定重启是否成功之前等待多长时间（默认值：立即重启）。

**rollback_config** 在更新失败的情况下应如何回滚服务。

1. `parallelism`：一次回滚的容器数量。如果设置为 0，则所有容器同时回滚。
2. `delay`：每个容器组回滚之间等待的时间（默认为 0 秒）。
3. `failure_action`: 如果回滚失败怎么办。`continue`或者`pause`（默认`pause`）
4. `monitor`：每次任务更新后监控失败的持续时间`(ns|us|ms|s|m|h)`（默认 5s）**注意**：设置为 0 将使用默认 5s。
5. `max_failure_ratio`：回滚期间允许的故障率（默认为 0）。
6. `order`：回滚期间的操作顺序。`stop-first`（旧任务在开始新任务之前停止），或`start-first`（首先启动新任务，并且正在运行的任务短暂重叠）（默认`stop-first`）。

**update_config** 配置应如何更新服务。用于配置滚动更新。

1. `parallelism`：一次更新的容器数量。
2. `delay`：更新一组容器之间的等待时间。
3. `failure_action`: 如果更新失败怎么办。`continue`，`rollback`或者`pause` （默认：`pause`）。
4. `monitor`：每次任务更新后监控失败的持续时间`(ns|us|ms|s|m|h)`（默认 5s）**注意**：设置为 0 将使用默认 5s。
5. `max_failure_ratio`：更新期间可容忍的故障率。
6. `order`：更新期间的操作顺序。`stop-first`（旧任务在开始新任务之前停止），或`start-first`（新任务首先启动，并且正在运行的任务短暂重叠）（默认`stop-first`）



## 十二、devices

设备映射列表。使用与`--device`docker 客户端创建选项格式相同。

```text
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
```



## 十三、dns

自定义 DNS 服务器。可以是单个值或列表。

```text
dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9
```



## 十四、dns_search

自定义 DNS 搜索域。可以是单个值或列表。

```text
dns_search: example.com
dns_search:
  - dc1.example.com
  - dc2.example.com
```



## 十五、entrypoint

在 Dockerfile 中有一个指令叫做`ENTRYPOINT`指令，用于运行程序。在`docker-compose.yml`中可以定义覆盖 Dockerfile 中定义的 entrypoint：

```text
entrypoint: /code/entrypoint.sh
entrypoint: ["php", "-d", "memory_limit=-1", "vendor/bin/phpunit"]
```



## 十六、env_file

从文件添加环境变量。可以是单个值或列表。

如果您使用指定了 Compose 文件`docker-compose -f FILE`，则其中的路径 `env_file`相对于该文件所在的目录。

environment 声明的环境变量会覆盖这些值——即使这些值是空的或未定义的。

```text
env_file: .env
env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/runtime_opts.env
```



## 十七、environment

添加环境变量。您可以使用数组或字典。任何布尔值（true、false、yes、no）都需要用引号括起来，以确保它们不会被 YML 解析器转换为 True 或 False。

一般 arg 标签的变量仅用在构建过程中。而`environment`和 Dockerfile 中的`ENV`指令一样会把变量一直保存在镜像、容器中，类似`docker run -e`的效果

```text
environment:
  RACK_ENV: development
  SHOW: 'true'
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
```



## 十八、expose

暴露端口，但不映射到宿主机，只被连接的服务访问。这个标签与 Dockerfile 中的`EXPOSE`指令一样，用于指定暴露的端口，但是只是作为一种参考，实际上`docker-compose.yml`的端口映射还得`ports`这样的标签。



## 十九、external_links

链接到 docker-compose.yml 外部的容器，甚至 并非 Compose 项目文件管理的容器。

```bash
external_links:
  - redis_1
  - project_db_1:mysql
  - project_db_1:postgresql
```



## 二十、extra_hosts

添加主机名映射。使用与 docker 客户端`--add-host`类似。

```text
extra_hosts:
  - "somehost:162.242.195.82"
  - "otherhost:50.31.209.229"
```

会往`/etc/hosts`文件中添加一些记录，启动之后查看容器内部 hosts可以看到：

```text
162.242.195.82  somehost
50.31.209.229   otherhost
```



## 二十一、healthcheck

配置运行的检查以确定此服务的容器是否“健康”。

```text
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

`interval`，`timeout 和 start_period`都是持续时间。`test`必须是字符串或列表。如果是列表，则第一项必须是`NONE`,`CMD`或`CMD-SHELL`。如果是字符串，则相当于指定`CMD-SHELL`后跟该字符串。

```text
# Hit the local web app
test: ["CMD", "curl", "-f", "http://localhost"]
test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
test: curl -f https://localhost || exit 1
```

如果需要禁用镜像的所有检查项目，可以使用`disable:true`，相当于`test:["NONE"]`

```text
healthcheck:
  disable: true 
```



## 二十二、image

从指定的镜像中启动容器，可以是存储仓库、标签以及镜像 ID



## 二十三、init

在容器内运行一个 init 来转发信号和取得进程。将此选项设置`true`为服务启用此功能。

```text
version: "3.9"
services:
  web:
    image: alpine:latest
    init: true
```



## 二十四、isolation

指定容器的隔离技术。在 Linux 上，唯一支持的值是`default`。在 Windows 上，可接受的值为`default`、`process`和`hyperv`。



## 二十五、labels

使用 Docker 标签将元数据添加到容器，可以使用数组或字典。与 Dockerfile 中的`LABELS`类似：

```text
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"

labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
```



## 二十六、links

链接到另一个服务中的容器。指定服务名称和链接别名 (`"SERVICE:ALIAS"`)，或仅指定服务名称。

它们不需要启用服务进行通信 - 默认情况下，任何服务都可以以该服务的名称访问任何其他服务。在以下示例中，`web`可以访问`db，并且设置别名为database`：

```text
version: "3.9"
services:

  web:
    build: .
    links:
      - "db:database"
  db:
    image: postgres
```



## 二十七、logging

日志记录配置。

```text
version: "3.9"
services:
  some-service:
    image: some-service
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
network_mode
```



## 二十八、network_mode

网络模式。使用与 docker 客户端`--network`相同，可以使用特殊形式`service:[service name]`。

```bash
network_mode: "bridge"
network_mode: "host"
network_mode: "none"
network_mode: "service:[service name]"
network_mode: "container:[container name/id]"
```



## 二十九、pid

将 PID 模式设置为主机 PID 模式。这会在容器和主机操作系统之间共享 PID 地址空间。使用此标志启动的容器可以访问和操作裸机命名空间中的其他容器，反之亦然。

```text
pid: "host"
```



## 三十、ports

暴露端口。

**简短语法**

共有三种写法：

- 指定两个端口 ( `HOST:CONTAINER`)
- 仅指定容器端口（为主机端口选择了一个临时主机端口）。
- 指定要绑定到两个端口的主机 IP 地址（默认为 0.0.0.0，表示所有接口）：( `IPADDR:HOSTPORT:CONTAINERPORT`)。如果 HOSTPORT 为空（例如`127.0.0.1::80`），则会选择一个临时端口来绑定到主机上。

```text
ports:
  - "3000"
  - "3000-3005"
  - "8000:8000"
  - "9090-9091:8080-8081"
  - "49100:22"
  - "127.0.0.1:8001:8001"
  - "127.0.0.1:5000-5010:5000-5010"
  - "127.0.0.1::5000"
  - "6060:6060/udp"
  - "12400-12500:1240"
```

**长语法**

- `target`: 容器内的端口
- `published`: 公开的端口
- `protocol`：端口协议（`tcp`或`udp`）
- `mode`：`host`用于在每个节点上发布主机端口，或`ingress`用于负载平衡的群模式端口。

```text
ports:
  - target: 80
    published: 8080
    protocol: tcp
    mode: host
```



## 三十一、profiles

允许通过有选择地启用服务来针对各种用途和环境调整 Compose 应用程序模型。这是通过将每个服务分配给单个或多个配置文件来实现的。如果未分配，则始终启动该服务，但如果已分配，则仅在激活配置文件时才启动。

这允许人们在单个`docker-compose.yml`文件中定义额外的服务，这些服务应该只在特定场景中启动，例如用于调试或开发任务。

```text
profiles: ["frontend", "debug"]
profiles:
  - frontend
  - debug
```



## 三十二、restart

`no`是默认的重启策略，在任何情况下都不会重启容器。当`always`指定时，容器总是重新启动。`on-failure`如果退出代码指示失败错误，则该策略会重新启动容器。`unless-stopped`总是重新启动容器，除非容器停止（手动或其他方式）。

```text
restart: "no"
restart: always
restart: on-failure
restart: unless-stopped
```



## 三十三、secrets

为每个服务机密授予相应的访问权限

**简短语法**

简短的语法仅指定机密名称。

以下示例授予`redis`服务对`my_secret`和`my_other_secret`机密的访问权限。`./my_secret.txt`文件的内容被设置为 my_secret，`my_other_secret`被定义为外部机密，这意味着它已经在Docker中定义，无论是通过运行`docker secret create`命令还是通过另一个堆栈部署，都不会重新创建。如果外部机密不存在，堆栈部署将失败并显示`secret not found`错误。

```text
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - my_secret
      - my_other_secret
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

**长语法**

- `source`：定义机密标识符。
- `target`：要挂载在`/run/secrets/`服务的任务容器中的文件的名称，默认是 source。
- `uid`和`gid`：`/run/secrets/`在服务的任务容器中拥有文件的数字 UID 或 GID 。
- `mode`：要挂载在`/run/secrets/` 服务的任务容器中的文件的权限，以八进制表示法。例如，`0444` 代表可读。

下面的示例表示将my_secret 命名为`redis_secret`，模式为`0440`（组可读），和用户组为`103`。该`redis`服务无权访问该`my_other_secret`机密。

```text
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - source: my_secret
        target: redis_secret
        uid: '103'
        gid: '103'
        mode: 0440
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

**一级`secrets`详细配置：**

- `file`：使用指定路径中的文件内容创建机密。
- `external`：如果设置为 true，则指定此机密已创建。Docker 不会尝试创建它，如果它不存在， 会报错`secret not found`。
- `name`：Docker 中秘密对象的名称。此字段可用于引用包含特殊字符的机密。
- `template_driver`：要使用的模板驱动程序的名称，它控制是否以及如何将机密负载评估为模板。如果未设置驱动程序，则不使用模板。当前支持的唯一驱动程序是`golang`，它使用`golang`。

在本示例中，`my_first_secret`在`<stack_name>_my_first_secret` 部署堆栈时创建 ，并且`my_second_secret`已存在于 Docker 中。

```text
secrets:
  my_first_secret:
    file: ./secret_data
  my_second_secret:
    external: true 
```



## 三十四、security_opt

为每个容器覆盖默认的标签。简单说来就是管理全部服务的标签，比如设置全部服务的 user 标签值为`USER`

```text
security_opt:
  - label:user:USER
  - label:role:ROLE 
```



## 三十五、stop_grace_period

指定在尝试停止容器时等待多长时间。

在docker stop命令执行的时候，会先向容器中的进程发送系统信号SIGTERM，然后等待容器中的应用程序终止执行。如果等待时间达到设定的超时时间，或者默认的10秒，会继续发送SIGKILL的系统信号强行kill掉进程。

在容器中的应用程序，可以选择忽略和不处理SIGTERM信号，不过一旦达到超时时间，程序就会被系统强行kill掉，因为SIGKILL信号是直接发往系统内核的，应用程序没有机会去处理它。

```text
stop_grace_period: 1s
```

默认情况下，`stop`在发送 SIGKILL 之前等待容器退出 10 秒。



## 三十六、stop_signal

设置一个替代信号来停止容器。默认情况下`stop`使用 SIGTERM。使用`stop_signal`设置替代信号来`stop`。

```text
stop_signal: SIGUSR1
```



## 三十七、sysctls

在容器中设置的内核参数，可以为数组或字典

```text
sysctls:
  net.core.somaxconn: 1024
  net.ipv4.tcp_syncookies: 0
```



## 三十八、tmpfs

在容器内挂载一个临时文件系统。可以是单个值或列表。

```text
tmpfs: /run
tmpfs:
  - /run
  - /tmp
```



## 三十九、ulimits

设置当前进程以及其子进程的资源使用量，覆盖容器的默认限制，可以单一地将限制值设为一个整数，也可以将`soft/hard`限制指定为映射

```text
ulimits:
  nproc: 65535
  nofile:
    soft: 20000
    hard: 40000
```



## 四十、userns_mode

如果 Docker 守护程序配置了用户命名空间，则禁用此服务的用户命名空间。

```text
userns_mode: "host"
```



## 四十一、volumes

挂载一个目录或者一个已存在的数据卷容器，可以直接使用`HOST:CONTAINER`这样的格式，或者使用`HOST:CONTAINER:ro`这样的格式，后者对于容器来说，数据卷是只读的，这样可以有效保护宿主机的文件系统

您可以将主机路径挂载为单个服务定义的一部分，无需在一级`volumes`键中定义它。

但是，如果您想在多个服务中重用一个卷，则需要在一级volumes 中定义一个命名卷。

如下实例，web 服务使用命名卷 (`mydata`)，以及为单个服务定义的绑定安装（`db`service下的第一个路径`volumes`）。`db`服务还使用名为`dbdata`（`db`service下的第二个路径`volumes`）的命名卷，使用了旧字符串格式定义它以安装命名卷。命名卷必须列在顶级`volumes`键下。

```text
version: "3.9"
services:
  web:
    image: nginx:alpine
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
      - type: bind
        source: ./static
        target: /opt/app/static

  db:
    image: postgres:latest
    volumes:
      - "/var/run/postgres/postgres.sock:/var/run/postgres/postgres.sock"
      - "dbdata:/var/lib/postgresql/data"

volumes:
  mydata:
  dbdata:
```

**简短语法**

简短语法使用通用`[SOURCE:]TARGET[:MODE]`格式，其中 `SOURCE`可以是主机路径或卷名。`TARGET`是安装卷的容器路径。标准模式`ro`用于只读和`rw`读写（默认）。

您可以在主机上挂载一个相对路径，该路径相对于正在使用的 Compose 配置文件的目录展开。相对路径应始终以`.`或开头`..`。

```text
volumes:
  # Just specify a path and let the Engine create a volume
  - /var/lib/mysql

  # Specify an absolute path mapping
  - /opt/data:/var/lib/mysql

  # Path on the host, relative to the Compose file
  - ./cache:/tmp/cache

  # User-relative path
  - ~/configs:/etc/configs/:ro

  # 命名卷
  - datavolume:/var/lib/mysql
```

**长语法**

- `type`: 安装类型， `bind`,`tmpfs`或`npipe`
- `source`: 安装源、主机上用于绑定安装的路径或在顶级volumes 中定义的卷的名称 。不适用于 tmpfs 挂载。
- `target`：安装卷的容器中的路径
- `read_only`: 将卷设置为只读的标志
- `bind`: 配置额外的绑定选项

1. `propagation`：用于绑定的传播模式

`volume`: 配置额外的选项

1. `nocopy`: 创建卷时禁用从容器复制数据的标志

`tmpfs`: 配置额外的 tmpfs 选项

1. `size`：tmpfs 挂载的大小（以字节为单位）

```text
version: "3.9"
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
      - type: bind
        source: ./static
        target: /opt/app/static

networks:
  webnet:

volumes:
  mydata:
```



**一级 Volume 详细配置：**

- driver 指定该卷应使用哪个卷驱动程序
- driver_opts 将选项列表指定为键值对以传递给此卷的驱动程序

```text
volumes:
  example:
    driver_opts:
      type: "nfs"
      o: "addr=10.40.0.199,nolock,soft,rw"
      device: ":/docker/example"
```

- external 如果设置为true，则指定该卷是在 Compose 之外创建的
- labels 添加元数据
- name 为此卷设置自定义名称

```text
version: "3.9"
volumes:
  data:
    name: my-app-data
```



## 四十二、变量置换

你可以使用 **$VARIABLE** 或者 **${VARIABLE}** 来置换变量

- `${VARIABLE:-default}VARIABLE`在环境中未设置或为空时设置为default。
- `${VARIABLE-default}`仅当`VARIABLE`在环境中未设置时才设置为default。
- `${VARIABLE:?err}`退出并显示一条错误消息，其中包含环境中的`err`if `VARIABLE`未设置或为空。
- `${VARIABLE?err}`退出并显示一条错误消息，其中包含`err`if `VARIABLE`在环境中未设置。
- 如果想使用一个不被compose处理的变量，可用使用 $$



## docker-compose 常用指令

作者：Alio
链接：https://juejin.cn/post/7051057669896929288
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### 1、Docker-Compose命令格式

```bash
docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
```

命令选项如下： -f，–file FILE指定Compose模板文件，默认为docker-compose.yml，可以多次指定。 -p，–project-name NAME指定项目名称，默认将使用所在目录名称作为项目名。 -x-network-driver 使用Docker的可拔插网络后端特性（需要Docker 1.9+版本） -x-network-driver DRIVER指定网络后端的驱动，默认为bridge（需要Docker 1.9+版本） -verbose输出更多调试信息 -v，–version打印版本并退出

### 2、docker-compose up 

```bash
docker-compose up [options] [--scale SERVICE=NUM...] [SERVICE...]
```

选项包括： -d 在后台运行服务容器 –no-color 不使用颜色来区分不同的服务的控制输出 –no-deps 不启动服务所链接的容器 –force-recreate 强制重新创建容器，不能与–no-recreate同时使用 –no-recreate 如果容器已经存在，则不重新创建，不能与–force-recreate同时使用 –no-build 不自动构建缺失的服务镜像 –build 在启动容器前构建服务镜像 –abort-on-container-exit 停止所有容器，如果任何一个容器被停止，不能与-d同时使用 -t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒） –remove-orphans 删除服务中没有在compose文件中定义的容器 –scale SERVICE=NUM 设置服务运行容器的个数，将覆盖在compose中通过scale指定的参数 `docker-compose up` 启动所有服务 `docker-compose up -d` 在后台所有启动服务 -f 指定使用的Compose模板文件，默认为docker-compose.yml，可以多次指定。 `docker-compose -f docker-compose.yml up -d`

### 3、docker-compose ps

`docker-compose ps [options] [SERVICE...]` `docker-compose ps` 列出项目中目前的所有容器

### 4、docker-compose stop

`docker-compose stop [options] [SERVICE...]` 选项包括： -t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒） `docker-compose stop` 停止正在运行的容器，可以通过docker-compose start 再次启动

### 5、docker-compose -h

`docker-compose -h` 查看帮助

### 6、docker-compose down

`docker-compose down [options]` 停止和删除容器、网络、卷、镜像。 选项包括： –rmi type，删除镜像，类型必须是：all，删除compose文件中定义的所有镜像；local，删除镜像名为空的镜像 -v, –volumes，删除已经在compose文件中定义的和匿名的附在容器上的数据卷 –remove-orphans，删除服务中没有在compose中定义的容器 `docker-compose down` 停用移除所有容器以及网络相关

### 7、docker-compose logs

`docker-compose logs [options] [SERVICE...]` 查看服务容器的输出。默认情况下，docker-compose将对不同的服务输出使用不同的颜色来区分。可以通过–no-color来关闭颜色。 `docker-compose logs` 查看服务容器的输出

### 8、docker-compose build

`docker-compose build [options] [--build-arg key=val...] [SERVICE...]` 构建（重新构建）项目中的服务容器。 选项包括： –compress 通过gzip压缩构建上下环境 –force-rm 删除构建过程中的临时容器 –no-cache 构建镜像过程中不使用缓存 –pull 始终尝试通过拉取操作来获取更新版本的镜像 -m, –memory MEM为构建的容器设置内存大小 –build-arg key=val为服务设置build-time变量 服务容器一旦构建后，将会带上一个标记名。可以随时在项目目录下运行docker-compose build来重新构建服务

### 9、docker-compose pull

`docker-compose pull [options] [SERVICE...]` 拉取服务依赖的镜像。 选项包括： –ignore-pull-failures，忽略拉取镜像过程中的错误 –parallel，多个镜像同时拉取 –quiet，拉取镜像过程中不打印进度信息 `docker-compose pull` 拉取服务依赖的镜像

### 10、docker-compose restart

`docker-compose restart [options] [SERVICE...]` 重启项目中的服务。 选项包括： -t, –timeout TIMEOUT，指定重启前停止容器的超时（默认为10秒） `docker-compose restart` 重启项目中的服务

### 11、docker-compose rm

`docker-compose rm [options] [SERVICE...]` 删除所有（停止状态的）服务容器。 选项包括： –f, –force，强制直接删除，包括非停止状态的容器 -v，删除容器所挂载的数据卷 `docker-compose rm` 删除所有（停止状态的）服务容器。推荐先执行docker-compose stop命令来停止容器。

### 12、docker-compose start

`docker-compose start [SERVICE...]` `docker-compose start` 启动已经存在的服务容器。

### 13、docker-compose run

`docker-compose run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]` 在指定服务上执行一个命令。 `docker-compose run ubuntu ping www.baidu.com` 在指定容器上执行一个ping命令。

### 14、docker-compose scale

`docker-compose scale web=3 db=2` 设置指定服务运行的容器个数。通过service=num的参数来设置数量

### 15、docker-compose pause

`docker-compose pause [SERVICE...]` 暂停一个服务容器

### 16、docker-compose kill

```
docker-compose kill [options] [SERVICE...]` 通过发送SIGKILL信号来强制停止服务容器。 支持通过-s参数来指定发送的信号，例如通过如下指令发送SIGINT信号： `docker-compose kill -s SIGINT
```

### 17、dokcer-compose config

`docker-compose config [options]` 验证并查看compose文件配置。 选项包括： –resolve-image-digests 将镜像标签标记为摘要 -q, –quiet 只验证配置，不输出。 当配置正确时，不输出任何内容，当文件配置错误，输出错误信息 –services 打印服务名，一行一个 –volumes 打印数据卷名，一行一个

### 18、docker-compose create

`docker-compose create [options] [SERVICE...]` 为服务创建容器。 选项包括： –force-recreate：重新创建容器，即使配置和镜像没有改变，不兼容–no-recreate参数 –no-recreate：如果容器已经存在，不需要重新创建，不兼容–force-recreate参数 –no-build：不创建镜像，即使缺失 –build：创建容器前，生成镜像

### 19、docker-compose exec

`docker-compose exec [options] SERVICE COMMAND [ARGS...]` 选项包括： -d 分离模式，后台运行命令。 –privileged 获取特权。 –user USER 指定运行的用户。 -T 禁用分配TTY，默认docker-compose exec分配TTY。 –index=index，当一个服务拥有多个容器时，可通过该参数登陆到该服务下的任何服务，例如：docker-compose exec –index=1 web /bin/bash ，web服务中包含多个容器

### 20、docker-compose port

`docker-compose port [options] SERVICE PRIVATE_PORT` 显示某个容器端口所映射的公共端口。 选项包括： –protocol=proto，指定端口协议，TCP（默认值）或者UDP –index=index，如果同意服务存在多个容器，指定命令对象容器的序号（默认为1）

### 21、docker-compose push

`docker-compose push [options] [SERVICE...]` 推送服务依的镜像。 选项包括： –ignore-push-failures 忽略推送镜像过程中的错误

### 22、docker-compose stop

`docker-compose stop [options] [SERVICE...]` 显示各个容器运行的进程情况。

### 23、docker-compose unpause

`docker-compose unpause [SERVICE...]` 恢复处于暂停状态中的服务。

### 24、docker-compose version

`docker-compose version` 打印版本信息。



## 问题

### 1、关于docker-compose up容器运行后就直接退出的问题

 `Docker`镜像的缺省命令是bash，如果不加 `-it`，`bash`命令执行了自动会退出，加`-it`后`docker`命令会为容器分配一个伪终端，并接管其`stdin/stdout`支持交互操作，这时候`bash`命令不会自动退出 像不使用`docker-compose`，我们会执行类似如下的命令 `docker run -d -it mmm /bin/bash` 但`docker-compose`需要额外配置下 需要在`docker-compose.yml`中包含以下行:

```vbnet
stdin_open: true
tty: true
```

第一个对应于`docker run`中的 `-i` ,第二个对应于 `-t` .

作者：crazyjums
链接：https://juejin.cn/post/7085215549562716174
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

---



