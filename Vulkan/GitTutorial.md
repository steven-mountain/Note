### git教程

+ 将remote仓库复制到本地(local)

  ```c
  git clone https://blah....
  ```

  + 本地：
    + local git：里面有所有告诉git的信息
    + disk：本地磁盘，源文件真正在磁盘里的样子，用编辑器打开的样子

+ 修改代码的时候

  + 建立一个新的feature branch
    + `git checkout -b my-freature`， 复制当前branch 到新branch上，my-reature就是你的feature的名字
    + 使用checkout命令之后，git会把branch上的所有源文件同步给硬盘
  + 修改之后，硬盘上的文件是有变化的
    + `git diff`， 查看硬盘和本地 git 的区别
    + `git add<changed_file>`将修改文件告知git，会把文件放在暂存区
    + `git commit`， 将修改放入git里，local git 就会新增commit
  + 将local git 变化告知github（remote）
    + `git push origin my-featue`

+ main branch上要是也有更新
  + 将main branch 上的更新同步到 my-feature上
  + 首先更新local branch
  + 切换到 mian branch  `git checkout main`
  + `git pull origin master`，将远端的main 同步到本地的main
  + `git checkout my-feature` 回到my-feature branch
  + 同步main的变化`git rebase main`
    + 先不管你的修改，把main最新的修改拿过来，在这个基础上将你的commit尝试弄回去
    + 如果出现 rebase conflic，就需要手动选择
    + rebase之后相当于是在最新的main branch上做了修改
  + 更新完之后，`git push -f origin my-feature`, 将local git push到github上 -f force
+ 将更新的代码合并到main branch上 `pull request`
  + 形式上认为，main属于项目，feature属于个人
  + pull request是 request 这个项目的主人，把我这个心的分支的改变，pull到项目上去
  +  `squash and merge` ，把这一分支上的所有改变合并成一个改变，把这个commit放到main branch上，是main branch commit history 尽可能简洁
  + 在merge之后一般会把远端的myfeature branch 删掉，但是本地还有
  + 切换到main branch 上 然后`git branch -D my-feature`，删掉本地分支
  + 再使用`git pull origin master`，将最新的更新拉到本地