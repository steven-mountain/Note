<center>Docker 笔记实录</center>

### 1 Docker

#### 1.1 为什么要有不同的tag：

因为一个镜像要在不同的平台上运行，而因为不同平台内核不同，运行的镜像也就不同



#### 1.3 docker 关于镜像的操作

```powershell
# 关于镜像的命令
# 查看镜像
docker image ls
docker images

# 拉取镜像
docker pull imagename

# 也可以是run，run默认如果找不到就下载最新版镜像
docker run imagename

# 删除镜像
docker image rm imagenmae
```



#### 1.4 docker 关于容器的操作

```powershell
# 查看正在运行的容器
docker ps
# 查看所有容器
docker ps -a

# 运行镜像，生成容器，每次调用run都会生成一个容器
docker run -it imagename # i交互，t伪终端
docker run imagename ls # 打印 运行的所有指令
docker run --rm imagename # 要是想运行之后不保留容器，加--rm
docker run --name nickname imagename # 给生成的容器起别名

# 进入正在运行的容器, exec 需要两个参数：容器id 和 要执行的指令
docker exec -it containername/id /bin/bash
docker exec -it -u user id /bin/bash # 指定用户进入容器，不需要输入密码就可以直接进入

# 重新运行已经停掉的容器
docker start -ai container # 前提是生成此容器时用了 -it参数
docker start id # id可以只给前2-3个字符

# 删除容器
docker rm containername
```



#### 1.5 docker中的代理

+ 守护程序代理：执行docker pull，build等所需要的代理
+ docker容器代理：就是容器可以使用的代理



### 2 Linux 基本指令集合

#### 2.1 Linux 基本知识

```bash
root@2f...:/#
# root: 当前用户
# 2f..: 当前机子的名称
# /   : 当前目录
# #   : root用户最高权限
# $   : 普通用户

echo $0
# 可以查看当前程序的位置

whoami
# 查看当前是哪个用户

history
# 查看历史命令

!2
# 运行历史命令中的第二条
```



#### 2.2 Linux发行版：

Alpine：一个非常小的Linux发行版



#### 2.3 包管理

用的是ubuntu，所以就只讨论Ubuntu的包管理

```bash
apt # 在2014年之后出来的 
apt-get # Advanced Package Tool 一般需要root权限

# 列出本地数据库中的包
apt list

# 更新本地数据库
apt update

# 安装包(一般安装之前都要update一下)
apt install name

# 删除已经安装的包
apt remove package
```



#### 2.4 文件系统

+ Linux中的各个文件

  | 文件名 | 作用                                                         | 文件名 | 作用                             |
  | ------ | ------------------------------------------------------------ | ------ | -------------------------------- |
  | /      | 主目录                                                       | root   | root用户的home目录               |
  | bin    | 存放所使用的命令的地方，sbin为root命令存放的地方             | lib    | 共享库，相当于windows的dll       |
  | boot   | 启动文件                                                     | var    | 包含系统运行时要改变的数据       |
  | etc    | 来自法语：et cereta，and so on的意思，这是来源于Unix，本意时用来装些其他的乱七八糟的东西 | proc   | 包括系统进程和内核信息的动态视图 |
  | home   | 用户的宿主目录，用户登录后所在的位置就是/home                |        |                                  |

  

+ 关于文件系统的一些指令

  ```bash
  pwd # print working directory 查看当前工作目录
  
  # 列出所有文件
  ls # 默认不列出隐藏文件，
  ls -a # 隐藏文件 .bash 前面有个 .
  ls -1   # 列出所有文件，展示在一列上
  ls -l   # long listing 列出所有文件，并包含其详细信息
  ls path # path下的所有文件
  
  # 移动工作目录
  cd obsolute/relatepath
  cd ../.. # .. 为进入上级目录
  cd ~     # 进入用户的home目录
  ```

  

+ 关于目录的操作

  ```bash
  # 创建目录
  mkdir test 
  
  # 重命名或移动目录
  mv A B # A->B
  
  # 删除目录
  rm -r test # r recursion 递归
  ```



+ 关于文件的操作

  ```bash
  # 创建文件
  touch file.txt
  
  # 编辑文件，nano或者vim
  nano file.txt
  
  # 删除文件
  rm file.txt
  
  # 删除和创建文件目录都可跟多个
  ```

  

+ 查看文件内容

  ```bash
  cat file.txt # concatenate file and print 适合内容少，纯文本的文件
  more file.txt # 适合内容多长的文件，但是只能下滑
  less file.txt # 需要安装less，解决了more的问题
  
  tail -n 10 file.txt # 查看后10行
  head -n 10 file.txt # 查看前10行
  ```



+ 输入输出重定向

  ```bash
  cat file.txt > file1.txt # 把file的内容放入file1中
  cat file1.txt file2.txt > conbined.txt # 将两个文件的内容放在一起
  
  echo hello > hello.txt # 向hello.txt中写入hello，如果没有文件，则创建之
  echo hello >> hello.txt # 向目标文件追加
  ```



#### 2.5 搜索

+ 对于文本搜索，使用的是grep（global search regular expression and print out the line）

```bash
grep hello file.txt # 在file中搜索hello  大小写敏感

grep -i hello file.txt # 忽略大小写

grep -i -r hello . # 在当前目录中的所有文件中搜索 hello
grep -ir hello .
```



+ 文件查找

  ```bash
  find # 查看当前路径下的所有文件
  find path # 查看path下的文件
  
  find -type d # 目录
  find -type f # 文件
  
  find -type f -name "f*"  # 查找 f开头的文件 大小写敏感
  find -type f -iname "f*" #大小写不敏感
  ```



#### 2.6 命令链接

```bash
# 命令顺序执行
命令1 ; 命令2 ; 命令3

# 命令执行一个，某个为假后面就都不执行
命令1 && 命令2 && 命令3

# 两者取其一, 如果1失败则执行2，大号练废了，练小号
命令1 || 命令2

# 管道,前者输出为后者输入
ls /bin | less

# 多命令换行
mkdir hello; cd hello; echo done
# 多行形式
mkdir hello;\
cd hello;\
echo done
```



#### 2.7 环境变量

```bash
# 查看机器上的环境变量
printenv 
# 查看具体某一项,都是大小写敏感的
printenv PAHT
echo $PATH

# 设置环境变量
exprot DB_USER=BLAH
# 变量保存在了当前的session中，只有在当前终端中有效

# 在环境变量中不要保存敏感信息，因为最终都是以纯文本的形式保存
# 对.bashrc的修改会在下一次启动终端后生效 
source .bashrc # 在当前终端重新进加载 .bashrc
# .bashrc 文件是一个存在于 Linux 系统内，普通用户目录 (/home/username) 或 root 用户目录 (/root) 下的隐藏文件。
# 它用来存储并加载你的终端配置和环境变量
```



#### 2.8 进程管理

```bash
# 查看进程
ps

# 进程后台运行 加上&
sleep 3 &

# 杀进程
kill PID
```



#### 2.9 用户管理

```bash
# 添加用户
useradd
useradd -m username # 用户保存在配置文件中

# /etc/passwd 里面保存有用户信息
username : x(password) : userid : groupid :: userhome : shell
# 用户名称：密码：用户id：主组id：：home路径：所使用的shell默认时老shell

# 更改为bash
usermod -s /bin/bash username

# 用户密码保存在 /ect/shadow中，只对root用户开放
# useradd 和 adduser

# 指定主组
useradd -g groupname newuser

# 给用户设置密码，只有root权限的用户才能设置密码
sudo passwd newuser

# 修改用户
usermod

# 删除用户
userdel

```



#### 2.10 主管理

```bash
# 创建组
groupadd

# 修改组
groupmod

# 删除组
groupdel

# 把用户添加到组
usrmod -G 组名 用户名

# 查看用户属于哪些组
groups 用户名
```





### docker一些问题集合

#### 1 docker启动失败的问题

在windows 中docker desktop failed initial，是因为配置文件出问题了。

解决办法：将 `C:\Users\user\AppData\Roaming\Docker `的Docker文件删掉，然后再启动Docker desktop，让它自己生成一个就行

