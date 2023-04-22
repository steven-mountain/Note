<center>conda 常用命令</center>

+ 创建环境

  ```python
  conda create -n test python=3.7
  ```

  

+ activate

  能将我们带入到Anaconda设定的虚拟环境中，如果你后边不带入参数，那么会进入到Anaconda的默认虚拟环境base中。

+ 查看虚拟环境

  ```python
  conda env list
  ```

  

+ 删除虚拟环境

  ```python
  conda remove -n test --all
  ```

+ 命令合集

  ```python
  activate // 切换到base环境
  ​
  activate test // 切换到test环境
  ​
  conda create -n test python=3.7 // 创建一个名为test的环境并指定python版本为3.7(的最新版本)
  ​
  conda env list // 列出conda管理的所有环境
  ​
  conda list // 列出当前环境的所有包
  ​
  conda remove -n test --all // 删除test环境及下属所有包
  ​
  conda env export > environment.yaml // 导出当前环境的包信息
  ​
  conda env create -f environment.yaml // 用配置文件创建新的虚拟环境
  ```

+ 包管理

  ```python
  # 1.安装第三模块 以requests为例子
  conda install requests
  
  # 2.删除第三方模块
  conda remove requests 或者 conda uninstall requests
  ```

  

+ 1.修改为清华源
  直接打开cmd输入以下命令

  ```python
  conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  
  conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  
  conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud//pytorch/
  
  conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  
  conda config --set show_channel_urls yes
  ```

  2.移除清华源
  输入：

  ```python
  conda config --remove channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  ```

  

  这个命令是为了移除之前conda config --show channels显示的清华源。
  