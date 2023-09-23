### windows 桌面快捷键

ctrl + win + D : 新建桌面

ctrl + win + 左右：切换桌面

ctrl + win + F4 : 关闭当前桌面



+ 图像频域的理解

  + 图像的傅里叶变换，将图像的灰度分布函数变换为图像的频率分布函数

  + 低频对应轮廓，高频对应细节
  + 频谱中，亮度大就证明梯度大

+ github代理设置

  ```c
  #只对github.com 
  
  # 找到自己的代理的port的4个数字的端口就行，不一定是1080口的 
  git config --global http.https://github.com.proxy socks5://127.0.0.1:1080 
  
  #上面是别人的，如果你的代理是http类型的，如下设置： 
  git config --global http.https://github.com.proxy 'http://127.0.0.1:代理的port' 
  
  #取消代理 
  git config --global --unset http.https://github.com.proxy
  ```




#### git提交规范

```c++
// commit message 格式
<type>:<subject>
// 空一行
<body>
// 空一行
<footer>
```

类型说明

| 标识符      | 描述                                               |
| ----------- | -------------------------------------------------- |
| feat        | 新功能(feature)                                    |
| fix         | 修复bug                                            |
| docs        | 文档变更、文档注释                                 |
| style       | 不影响代码内容的修改（格式修改、比如空格、分号等） |
| refactor    | 重构（既不是新增功能，也不是修改bug的代码变动）    |
| improvement | 对当前功能的改进                                   |
| perf        | 提高性能的代码修改                                 |
| test        | 添加测试或修正现有的测试                           |
| chore       | 构建过程或辅助工具的变动                           |
| ci          | ci配置文件和脚本的改动                             |
| revert      | 回滚先前提交                                       |
| delete      | 删除（代码、文档等）                               |

+ subject（必须项）

  是commit目的的简短描述、不超过50个字符

+ body

  是对本次commit的详细描述，可分成多行描述

+ footer

  一般用于关闭issue



#### python 常用命令

```c++
	 //  查找pip的配置文件
pip config -v list
    
    // 修改镜像源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

	// 查找python 路径
where python
    
    // 查找pip安装的库路径
pip show package

```

+ pycharm 镜像源：

在 File -> settings->project:项目名称->project interpretery页面点击右边的“+”号，然后点击“Manager Repositories”



#### 网页不让复制？

直接win+r 然后read://



#### F12抓包文件

F12 network media



#### git冲突处理

+ 1、编辑冲突文件，决定要保留的内容，然后删掉三行分割线
+ 2、`git add` 将冲突文件添加到暂存区
+ 3、`git commit`提交
+ git log 查看提交记录
+ `git status` 查看冲突文件退出合并的中间状态：`git merge --abort`
+ `git status` 查看冲突文件
+ 退出合并的中间状态：`git merge --abort`

#### git查看状态
```powershell
git status 查看改变的文件列表
git status -s 查看改变的文件列表简化版
git diff或者git diff filename查看不在缓冲区的文件发生的改变
git diff --cached或者git diff --staged查看缓冲区的文件发生的改变
git diff HEAD是git diff和git diff --cached的合并

#### powershell 奇淫巧计

+ 计算hash，文件验证，可以使用 Certutil
+ 判断两个字符串是否相等， -eq



#### 好用软件合集

+ 压缩加密：7-zip
+ 生成gif：screenToGif
+ 卸载软件：geek
+ windows小工具包：powertoys
+ 文件分类：dropit
+ 鼠标精灵：tinytask
+ 文件查找：everything



#### python 获取到的正则表达式的某一项

+ re
+ r'(.+?)sljdf‘
+ .group() 获取值，加数字就是第几个



#### pandas 读取csv

用pandas因为方便点

+ pd.readcsv("cvs文件路径")
+ array=data.values[0::,0::]  *#读取全部行，全部列*， 左开右闭
+ data_csv.to_csv("./Student.csv", index=False, sep=',') *# 将新增的列数据，增加到原始数据中*



#### python打包

```python
pyinstaller 
Pyinstaller -F -w -i chengzi.ico py_word.py
```



#### python 运行程序加参数

```python
import sys
username = sys.argv[1]
password = sys.argv[2]
```



### Latex 更新

首先下载安装 update-tlmgr-latest，即tlmgr

tlmgr操作集合

```powershell
# 更换镜像
tlmgr option repository https://mirrors.aliyun.com/CTAN/systems/texlive/tlnet

# 安装宏包
tlmgr install <packagename>

# 移除
tlmgr remove <packagename>

#查看所有更新的宏包指令：
tlmgr update --list

#更新所有需要更新的宏包
tlmgr update --self --all

# 如果遇到更新宏包过程中，某一个宏包更新失败，可以使用指令继续更新：
tlmgr update --reinstall-forcibly-removed --all
```

