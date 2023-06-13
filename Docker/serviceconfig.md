<center>windows docker 服务器配置</center>

### docker 映射宿主机端口，启用ssh服务

```ba
docker run -it -d -p 50003:22 ubuntu
```

```bash
apt update
apt install passwd openssl openssh-service openssh-clients
```

修改`/etc/ssh/sshd_config`文件
将 `PermitRootLogin without-password` 改为 `PermitRootLogin yes`

重启ssh服务

```bash
service ssh restart
```



### windows 开放端口

控制面板 --》系统和安全 --》windows defender防火墙 --》高级设置 --》添加入站和出战规则

！！！ 注意

`Docker Desktop Backend` 都得要开启，不然无法远程连接