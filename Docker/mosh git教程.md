## mosh git教程

### git 入门

#### 不痛不痒的简介

+ 可以回溯
+ 可以多人协同
+ 中心化和分布式

#### 如何使用git

+ 命令行
+ IDE也有，例如vscode上的gitlens
+ 图形界面：gitkraken，sourcetree

+ `git --version` 查看git版本



#### git 配置

需要配置以下选项：

+ name
+ email
+ default editor
+ line ending



可以在三个不同的层级进行配置

+ system 所有用户
+ global 当前用户的所有仓库
+ local 当前仓库

```bash
git config --global user.name "blash l"
git config --global user.emai 123@test.com
# linux 设置nano
git config --global core.editor "nano"
#windows 设置vscode
git config --global core.editor "code -w"

# 打开默认编辑器，编辑全局设置
git config --global -e

# git 管理end of line
# windows 中 abc\r\n 回车换行
# macos/linux abc\n
# core.autocrlf carriage return line of life
# windows
git config --global core.autocrlf true
# mac/linux
git config --global core.autocrlf input
```



### 创建快照

Creating Snapshots

#### 仓库

```bash
# 创建一个空的仓库
git init
# 删掉 .git文件夹之后，这个仓库也就被删掉了
```

ps: 显示git分支插件，mac：Zsh with git plugin; windows: posh-git



#### 工作流 workflow

git仓库保存的是full context，项目的完整快照

```bash
git add file # 将对file的修改加入staging
git commit -m "message" # 将staging中的内容，快照放入仓库
```



#### Staging Files

```bash
# 查看工作目录的staging area
# 如果在git add之后又对文件进行了修改，那么再git add一次
git status

# 提交
git commit -m "message"
# 在提交之后，work director应该和stagging area一致，这样working tree 就干净了
```



#### Committing Best Practices

commit不应该太大或者太小，

+ 不应该每次更新文件都commit一次

+ 太大，不利于回滚



#### Skipping the staging area

跳过staging area，直接commit

```bash
git commit -am "blah"
# -a：所有被修改的文件
# -m: 信息，messsage
```



#### Removing files

  如果只是在工作区删除了某个文件，那么这个文件依旧存在于staging area

```bash
# 查看staging area中的文件
git ls-files

# 删除一个文件要同时在工作区和staging area中都删除
rm file # 工作区
git add file #缓冲区
# 最后提交
git commit -m "delete file"

# 因为这是一个常用的命令，git提供了一个常用的指令
git rm file
# 这个指令会删除工作区和缓冲区的文件
```



#### 重命名或者移动文件

Renaming or Moving Files

```bash
# 从命名一个文件
mv file1.txt newfile.txt

# 上面的操作可以看成，删除一个文件，然后创建一个文件
# 在git中每次创建一个文件，都得要add一下，这样git才会追踪这个文件
# 因此包含两步操作
git add file1.txt
git add newfile.txt

# 同样常用命令
git mv file1.txt newfile.txt

```



#### Ignoring Files

忽略文件，因为工作区并非所有文件都需要提交

例如隐藏文件，二进制文件，这些文件只会增加仓库的大小，但对其他人没用

有些文件也不能跟其他人共享

将不需要共享共享的文件放入 `.gitignore`

`.gitignore`添加到staging里，最后commit

ps: 有这样一个场景，有一个bin文件，并且这个文件已经被提交了，也就是说它已经在staging area里了，之后再将这个文件放入.gitignore里。这里就有一个问题，那就是git仍然在追踪这个文件。我们需要将bin这个文件从staging area中删掉。

```bash
# 将文件添加到 .gitignore

git rm --cached fileName
git rm --cached -r file # 递归

# 记得commit
git commit -m "balh"
```



#### Short Status

`git status`指令给出的结果太多了，我们有时候只想要简单的结果

`git status -s`

```bash
 M file2/hello.txt
?? simple.txt
左边两栏，第一栏是staging里的，第二栏是工作区的
这里表示file2/hello.txt文件在工作区被修改，而staging没有变化
第二行两个？表示这是一个新的文件

# git add 之后
git add file2/hello.txt
M  file2/hello.txt
?? simple.txt
# M绿色在左边，表示工作区的修改已经添加到了staging中了

# 添加simple.txt
git add simple.txt
M  file2/hello.txt
A  simple.txt
# M和A都是绿色，说明这两个文件的修改都被添加到了staging中了
```



#### 查看 staged & unstaged 改变

在commit之前，review一下你所做的修改

```bash
# 查看staging中的修改
git diff --staged

# 比较这两个文件a/file2/hello.txt b/file2/hello.txt；a为old，b为new
diff --git a/file2/hello.txt b/file2/hello.txt
index 31e0fce..5ad28e2 100644
--- a/file2/hello.txt # old的变化
+++ b/file2/hello.txt # new的变化
@@ -1 +1 @@ # 有些标志，表示哪些文件被改变了，每个改变都有一个这个header
-helloworld
+haha
diff --git a/simple.txt b/simple.txt
new file mode 100644
index 0000000..e69de29


# 查看工作区的修改
git diff
```



#### visual diff tools 

+ KDiff3
+ P4Merge
+ WinMerge(windows Only)
+ VSCode

```bash
git config --gloabl diff.tool vscode
git config --global difftool.vscode.cmd "code --wait --diff &lLOCAL &REMOTE"

# 使用
git difftool
git difftool --staged

```



#### view the History

```bash
git log # 查看提交的历史记录
git log --oneline # 关于commits的一个简短的总结
git log --oneline --reverse # 反转
```



#### view the commit

```bash
git log --oneline

git show # 上面查看到的id
git show HEAD~1 # HEAD 往下一个
git show HEAD~1:bin/app.bin # 查看这个commit中的app.bin中的内容
git show ls-tree HEAD~1 # 查看这个commit中的文件结构，文件为blob，文件夹为tree
git show 上面的id # 可以查看对应文件夹内的文件

```



#### unstaging file

撤销 git add 命令

```bash
git restore --staged file1.txt
# 这样做了之后，会撤销之前 git add 的结果，
# 并从仓库中获取最新的snapshot，并把它放入staging area
```



#### discarding local changes

撤销本地修改

```bash
git restore file1.txt 
# 从staging中恢复之前的结果
# 但是对于新建的文件不生效，因为staging就没有新建的文件，
# 没有跟踪（untracked）也就无从谈起恢复
git clean # 清除掉所有untracked的文件，默认拒绝，因为无从恢复
git clean -fd # f强制清除,清除文件夹
```



#### Restoring a File to an Earlier Version

```bash
# 从工作区和staging中删除文件
git rm file1.txt

# 从仓库的commit中恢复文件，从上一个版本中恢复
git log # 获取历史commit
git restore --source=HEAD~1 file1.txt # 从之前的版本中恢复文件
```



#### Creating Snapshots Using VSCode/GitKraken

