<center>miniconda ubuntu</center>

ubuntu上安装miniconda

```bash
# 下载安装文件
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py38_4.9.2-Linux-x86_64.sh

# 给下载的文件添加执行权限
chmod u+x Miniconda3-py38_4.9.2-Linux-x86_64.sh

# 运行安装脚本
sudo ./Miniconda3-latest-Linux-x86_64.sh

# 将miniconda加入PATH路径
nano ~/.bashrc
export PATH="/opt/miniconda3/bin:$PATH"

```

