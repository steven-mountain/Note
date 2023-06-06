<center>ubuntu开放端口</center>

http://t.csdn.cn/85tB2

### 使用ufw

UFW 是标准 [Ubuntu](https://so.csdn.net/so/search?q=Ubuntu&spm=1001.2101.3001.7020) 20.04 安装过程中的一部分，它应该已经在系统上存在。



```bash
# 如果没有安装
sudo apt update
sudo apt install ufw

# 安装过程不会自动激活防火墙，以避免服务器被锁住。可以检查 UFW 的状态，输入：
sudo ufw status verbose

# ufw命令
sudo ufw allow 9200 允许外部访问9200端口(tcp/udp)
sudo ufw allow 3690 允许外部访问3690端口(svn)
sudo ufw allow from 192.168.25.125 允许此IP访问所有的本机端口
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 22 允许指定的IP段访问特定端口
sudo ufw delete allow smtp 删除上面建立的某条规则，比如删除svn端口就是 sudo ufw delete allow 3690 
```

