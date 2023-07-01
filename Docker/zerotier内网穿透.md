zerotier，ubuntu使用



```bash
# 直接下载
curl -s https://install.zerotier.com | sudo bash

# 如果安装了GPG密钥验证
curl -s 'https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg' | gpg --import && \
if z=$(curl -s 'https://install.zerotier.com/' | gpg); then echo "$z" | sudo bash; fi

# 启动zerotier
systemctl start zerotier-one.service

# 设置开机启动
systemctl enable zerotier-one.service

# 加入zerotier
zerotier-cli join 83048a0632246d2c
```

创建网络的教程网上有就略过了