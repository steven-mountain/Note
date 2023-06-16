samba映射共享文件

+ 安装samba

  ```bash
  apt update
  apt install samba
  apt install cifs-utils
  apt install samba-common
  ```

  

+ 创建共享目录

  ```bash
  mkdir -p /home/user/samba
  chmod 777 -R /home/user/samba
  ```



+ 修改samba配置

  ```bash
  nano /etc/samba/smb.conf
  
  -----
  # global 下添加
  security = user
  
  # 文末添加
  [share]
  comment=share for users # 自定义描述
  path=/home/user/samba   # 共享目录
  browseable=yes          # 可浏览
  available=yes           #
  writable=yes            # 可写
  public=yes              # 公共
  ```



+ linux启动、停止、重启 samba共享

  ```bash
  /etc/init.d/ samba start
  /etc/init.d/ samba stop
  /etc/init.d/ samba restart
  
  # 或者
  /usr/sbin/ samba start
  /usr/sbin/ samba stop
  /usr/sbin/ samba restart
  ```

  