<center>nas 服务器挂载</center>

http://t.csdn.cn/4xWgV

# mount到本地

```bash
# 更新软件
sudo apt upgrade 
sudo apt update 
sudo apt dist-upgrade

# 安装cifs-utils
sudo apt install cifs-utils
```



### 访问nas

```bash
# 创建挂载目录
cd /mnt/
sudo mkdir nas

# 挂载操作
mount -t cifs //192.168.6.19/share /share -o username=test,password='test@2.',domain=DOMAIN,vers=2.0

# 参数解释
uid：最关键，可以指定所有者，解决只能root权限的问题，这里=Ubuntu用户名
username：在nas上的用户名
password：nas上用户名对应的密码，可以不写，回车后会有密码输入行
iocharset：路径中如有中文则添加此项，支持中文路径
nas地址：如"//192.168.0.1/share"
本地地址：如"/mnt/nas"
```



### 开机自动挂载

```bash
sudo vim /etc/fstab

nas地址 本地挂载地址 cifs uid=***,username=***,password=***,iocharset=utf8 0 0

//10.15.22.36/共享资料 /mnt/nas cifs uid=hermesjang,username=J716,password=nasj716,vers=2.0,iocharset=utf8 0 0

sudo mount -a
```



### 如不能开机自动挂载

```bash
# 开启服务
systemctl start remote-fs.target
systemctl enable remote-fs.target

# 提升用户权限为root和sudo
sudo vi /etc/sudoers

#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults	env_reset
Defaults	mail_badpass
Defaults	secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root	ALL=(ALL:ALL) ALL
# add user
[username] ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo	ALL=(ALL:ALL) ALL
# add user
[username] ALL=(ALL:ALL) NOPASSWD:ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
# 添加两处[# add user]，[username]为当前Ubuntu用户名称。

# 开机执行命令

cd && vi .bashrc
sudo mount -a
# 保存退出。
```

