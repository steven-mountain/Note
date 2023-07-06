## mosh git教程

### git 入门

#### 不痛不痒的简介

+ 可以回溯
+ 可以多人协同
+ 中心化和分布式
+ Global Information Tracker（git）

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



---

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



---

### 查看提交历史

#### 获取库

Getting a Repository



#### 检查历史记录

Viewing the History

```bash
# 查看git历史记录
git log

# 查看每个提交里哪些文件被修改了
git log --oneline --stat
# 详情
git log --stat 

# 查看具体哪些地方被修改
# patch：补丁，用来显示两个文件不同的地方
git log --oneline --patch
```



#### 过滤历史记录

Filtering the History

```bash
## 查看最近三个commit
git log --oneline -3

## 按照作者过滤
git log --oneline --author="Mosh"

## 按照日期过滤
git log --oneline --before="2020-08-17"
git log --oneline --after="2020-08-17"
git log --oneline --after="yesterday"
git log --oneline --after="one week ago" # two day ago/ one month ago

## 按照message过滤
# 大小写敏感
git log --oneline --grep="GUI"

## 按照内容过滤
git log --oneline -S"OBJECT" # 所有添加或删除过OBJECT的commit
# 查看具体修改
git log --oneline -S"OBJECT" --patch

## 查看某个区间里的commit
git log --oneline fb0d184..edb3594 # 查看这两个ID之间包含这两者的commit

## 按照文件过滤
git log --oneline toc .txt
# 如果文件和文件夹同名，需要指定文件
git log --oneline -- toc .txt
# 查看详情 --patch 必须在文件名之前
git log --oneline --patch -- toc.txt
```



#### 格式化日志输出

Formatting the Log Output

```bash
# author name
git log --pretty=format:"hello %an" # %an author name

# commit id
git log --pretty=format:"%an commited %H" # %H hash, commit id, H显示全id
git log --pretty=format:"%an commited %h" # h显示部分id

# commit date
git log --pretty=format:"%an commited %h on %cd" # cd commit date

# color
# %Cgreen 修改之后的颜色为绿色
# %Creset 对之后的结果重置
git log --pretty=format:"%Cgreen%an%Creset commited %h on %cd" 
```



#### 取别名

Creating Aliases

```bash
# 给常用的指令取别名, alias.别名, 内部有双引号的变为单引号
git config --global alias.lg "--pretty=format:'%Cgreen%an%Creset commited %h on %cd'"

# 也可以在配置文件里加
git config --global -e
# 在 [alias]后面加

# unstage, 恢复staging area中的内容
git config --global alias.unstage "restore --staged ."
```



#### 查看commit

viewing a commit

```bash
# 只想查看在某个commit中哪些文件被修改
git show HEAD~2 --name-only

# 查看在某个commit中的文件被增删改查
git show HEAD~2 --name-status
```



#### 查看commit之间的改变

Viewing the Changes Across Commits

```bash
# 查看前三个的change
git diff HEAD~2 HEAD

# 查看某一个文件的修改
git diff HEAD~2 HEAD audience.txt

# 只看文件名
git diff HEAD~2 HEAD --name-only
# 查看修改类型 
git diff HEAD~2 HEAD --name-status

```



#### 检查commit

check out a commit

```bash
# 有时候想要查看在某一个时间的snapshot
git checkout commitid

# 一个 warning
You are in 'detached Head' state.
```

git中的Master与Head一般指向最新的节点

<img src=".\figure\detach11.png" style="zoom:50%;" />



`git checkout commitid`，head 跳转到对应commitid的节点

<img src="figure\detach2.png" style="zoom:50%;" />



而此时如果commit的话，会在这之后新创建一个节点

<img src="figure\detach3.png" style="zoom:50%;" />



当head回到master节点，之前先创建的节点便无法访问

<img src="figure\detach4.png" style="zoom:50%;" />

因此`git checkout commitid`或者说处在`detached head`状态，都只能查看，不能新建分支节点

```bash
# HEAD跳转到某一commit，工作区就会变更为这一commit的snapshot
git checkout commitid

# 查看HEAD及之前的commit
git log --oneline

# 查看所有commit
git log --oneline --all

# 回到master
git checkout master
```



#### 通过二分查找找bug

Finding Bugs Using Bisect

```bash
# 通过bisect找bug
# 需要设定一个 good 和 bad commit
# 开始
git bisect

# 将最新的也就是master设定为bad（因为这儿有问题）
git bisect bad

# 设定一个good commit，这里将第一个commit设定为good
git log --oneline
git bisect good firstCommitID
# 此时的head指向good和bad中间的节点
# 运行程序，看问题是否存在，如果存在name 设为bad否为good
# 查到追踪的结果
git bisect bad

# 退出
git bisect reset
```



#### 通过shortlog查找贡献者

Finding Contributors Using Shortlog

```bash
# 找出对项目做出贡献的人
git shortlog

# 根据每一个贡献者的提交数排序
git shortlog -n/ --numbered

# 只显示数量
git shortlog -s/ --summary

# 显示每一个contributor的email
git shortlog -e

# 可以组合
git shortlog -nse

# 在某一时间的贡献
git shortlog -nse --before="" --after=""
```



#### 查看某一文件的历史记录

```bash
# 查看某一文件的历史记录
git log toc.txt

# 简述
git log --oneline toc.txt

# 每一个commit中该文件的状态
git log --oneline --stat toc.txt

# 每一个commit对该文件所做的修改
git log --oneline --patch toc.txt
```



#### 恢复一个被删除的文件

Restoring a Deleted File

```bash
# 从历史记录中恢复一个文件
# 删除一个文件并提交
git rm toc.txt
git commit -m "Remove toc.txt"

# 查看该文件的历史记录
git log toc.txt
# 从文件被删节点的父节点 中 恢复被删文件
# !!!注意这里没有跳转，并且恢复的文件就自动在staging area里了
git checkout parentID toc.txt

# 提交结果
git commit -m "restore toc.txt"
```



#### Blaming(查看哪些人修改了文件)

```bash
# 查看哪些人对某个文件做了修改
git blame audience.txt

# 带上邮箱
git blame -e audience.txt

# 只想查看前三个
git blame -e -L 1,3 audience.txt

```



#### Tagging

有时候想要对特定的历史提交做标记

```bash
# 给某一个commit打上标记
git tag v1.0 commitid

# 跳转到某一id
git checkout v1.0 # tag等同于id这里

# 查看所有的tag
git tag

# 自定义tag
# -a alternate
# 这里是给目前所指向的节点打tag
git tag -a v1.1 -m "alternate tag 1.1"

# 查看tag以及其message
# 如果没有指定message，那么默认就是commit的messsage
git tag -n

# 删除tag
git tag -d v1.1
```



#### 使用vscode查看历史记录

GitLens





---

### 分支管理

分支允许我们从mainland中分叉出来一个，然后独立工作。相当于一个独立于主分支的workspace。

MASTER(main)，尽可能保存稳定的版本。在不同分支之间切换时，git会将工作区设置为对应分支最新的snapshot。

HEAD指向当前分支的commit。



#### 使用branch

```bash
# 创建bugfix分支
git branch bugfix
# -C create
git switch -C branch

# 查看所有分支
git branch

# 切换分支 也可以checkout
git switch bugfix

# 重命名分支
git branch -m bugfix bugfix/signup-form

# 关于git log，会显示 HEAD及其之前的commit
git log --oneline

# 删除分支，在合并玩之后就可以删除掉这个分支了
git branch -d bugfix/signup-form
# 强制删除
git branch -D bugfix/signup-form
```



#### 对比分支

comparing branch

```bash
# 在merge分支之前，查看 master和当前分支 之间的commit
git log master..bugfix/signup-form

# 查看做了这些commit做了哪些修改
git diff master..bugfix/signup-form

# 对比bugfix/signup-form和当前所处分支
git diff bugfix/signup-form

# 查看哪些文件被修改
git diff --name-only bugfix/signup-form
git diff --name-status bugfix/signup-form
```



#### stashing（存储）

适用场景：当你正在进行项目中某一部分的工作，里面的东西处于一个比较杂乱的状态，而你想转到其他分支上进行一些工作。问题是，你不想提交进行了一半的工作，否则以后你无法回到这个工作点。

```bash
# 保存当前的工作
git stash push -m "message"

# 但是stash不能保存新建的文件，因为它们是untracked
echo hello > newfile.txt
git stash push -a/--all -m "message"
git stash push -am "message"

# 查看stash
git stash list

# 查看stash里做了哪些更改
git stash show 1/stash@{1}

# 将stash应用（恢复到所属分支）
git stash apply 1

# 引用完之后就可以删除掉stash
git stash drop 1
# 清除所有stash
git stash clear

```



#### 合并分支

Merging，有两种merge的方法

+ Fast-forward merges

  当master没有做修改的时候，只需要将master移动到最新分支即可

  <img src="figure\fastforwardmerge.png" style="zoom: 33%;" />



```bash
# 查看带有分支信息的commit
git log --oneline --all --graph

# 将分支合并到master
# 首先要切换到master分支
git switch master
git merge bugfix/signup-form

# 有时候不希望使用fast-forward
# 推荐这样用，因为这样可以使提交记录结构更清晰
git merge --no-ff bugfix/signup-form

# 有时候忘掉使用 --no-ff
# 配置禁用fast-foward一劳永逸
# 对当前用户所有仓库禁用ff
git config --global ff no # 这会报错
git config --global merge.ff no
```



+ 3-way merges

  当主分支master也有修改的时候，就需要3way，创建一个新的commit来合并这两个分支，3指的是初始分叉节点，和分叉之后的节点

  <img src="figure\3waymerge.png" style="zoom: 25%;" />



#### 查看合并的分支

Viewing the Merged Branches

```bash
# 查看哪些分支被合并的，哪些没有
# 首先切换到master分支
# 查看哪些分支合并到了当前分支
git branch --merged
# 没有合并的
git branch --no-merged
```



#### 合并冲突

mergeing conflicts

```bash
# 不同的branch对同一个文件做了不同的更改时，会出现冲突
# 此时merge，会卡在merge的中间状态
git merge branch1
git status

# 打开有冲突的文件
open confictfile
# 会有如下的信息
<<<<<<< HEAD # HEAD 指向当前所在分支，手动合并删除此行
Change in mater branch # 当前分支中的变化
======= # 分隔符，手动合并，删除此行
Change in conflic branch # 要合并分支中的变化
>>>>>>> conflict #要合并的分支，手动合并删除此行
# 在冲突文件中，不应该再加入出冲突内容之外加入新的内容，方便之后查看

# 处理完之后将此文件添加到staging area中
git add conflictfile
git commit # 使用默认信息

```



#### 冲突处理工具

+ Kdiff
+ P4Merge
+ WinMerge(Windows Only)

```bash
# 配置冲突处理工具，P4Merge
git config --global merge.tool p4merge
git config --global mergetool.p4merge.path "路径"
# 在遇到冲突时使用
git mergetool
```



#### Aborting merge

处理冲突的时候用，如果你不太确定能处理这些冲突的时候，那就打断施法。退出合并，不处理

```bash
# 打断merge，回到merge之前的状态
git merge --abort
```



#### Undoing a Faulty Merge

回滚merge，当merge出现问题时，回到merge之前的状态，

```bash
# rewrite the history，删除掉当前的commit，修改时间线相当于是，需要特别注意
# 修改时间线，在本地可以这么做，但是多人协同，这么做就不好了
# 回退merge
# soft，只是改变head指向上一个snapshot
# mixed，同上，并且将新的snapshot指向staging area
# hard，同上，并将staging area中的东西放入working dir
git reset --hard HEAD~1 # 将当前分支回退到上一个状态

# 上面回退的commit依然存在，可以通过id恢复
git reset commitId

# 回滚 revert
# 并不会删除当前commit，而是在当前分支之后创建新的commit，用这个commit恢复之前的状态
# 推荐使用
git revert -m 1 HEAD
# -m 指定前面的第几个commit，这里是第一个
# HEAD 回滚的目标节点
```



#### Squash Merging

因为如果merge那么被merge的分支commit会成为master的一部分。

但是有些分支上的commit我们并不想让他们并入主分支。这时候就用squash merge

<img src=".\figure\squashmerge1.png" style="zoom:33%;" />



相当于是将bugfix分支所做的左右修改合成一个commit并入master，但是又不是真的merge。

因为这个新的commit只有一个父分支。

<img src=".\figure\squashmerge2.png" style="zoom:33%;" />

squash之后的分支

<img src=".\figure\squashmerge3.png" style="zoom:33%;" />

那些短时间内需要使用feature或者bugfix的时候使用squash很有效

```bash
# 并不是真正的merge 
git merge --squash bugfix/photo-upload

# squash merge之后，所有bugfix上的修改会写入master的staging area中
# 需要commit才会创建新的节点
git commit

# git branch --merged，是不会显示被squash merge的分支
# 也就是说这个分支是unmerged的状态，
# git branch --no-merged
# 直接删除 -d 会报错，需要强制删除 -D
```



#### Rebasing

会改写时间线，只能是本地自己使用的时候用。

开始状态

<img src="figure\rebase1.png" style="zoom:33%;" />

rebase改变了当前分支的base commit

<img src="figure\rebase2.png" style="zoom:33%;" />

这里就改变了主分支的时间线。所以一般是子分支rebase主分支。

<img src="figure\rebase3.png" style="zoom:33%;" />

```bash
# 进入分支
git switch branch1
# 做一些修改，然后add commit

# 回到master
# 也做一些修改
git switch master

# 再次回到分支
git switch branch1
# 以master分支最新的commit的基础，修正branch1
git rebase master

# 处理rebase中的冲突
# git mergetool 或者打开冲突的文件，处理冲突
git rebase --continue # 继续rebase
git rebase --skip # 跳过当前commit，移动到下一个commit
git rebase --abort # 终止rebase
```



#### cherry picking

将子分支前面的commit加入到master中，但是不merge

<img src="figure\cherrypick.png" style="zoom:33%;" />

```bash
git cherry-pick commitid
```



#### picking files from another branch

将其他分支的文件，加入到当前分支的工作区

```bash
git restore --source=featrue/send-email -- toc.txt
# -- toc.txt 指定为文件
# 将文件加入到当前分支的工作区
```



#### vscode中的如何使用分支

略
