# Git

​       **git安装后-指定名称和邮箱:**

```csharp
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

* ## **配置命令**

  >`git config -l` 查看当前git环境详细配置
  >
  >`git config --system --list`  查看系统config
  >
  >`git config --global --list`  查看当前用户配置
  >
  >`git config --local --list`  查看当前仓库配置信息
  >
  >`git config [--local][--global][--system] section.key value` 修改git配置
  >
  >`git config --local user.name huhuhu` 设置当前项目的用户名
  >
  >`git config --global core.quotepath false` 配置当前用户的编码项，可以解决中文编码问题
  >
  >`git config --local core.ignorecase false` 配置当前项目不忽略文件大小写，git默认忽略文件名的大小写，这点值得注意

  

`HEAD` 当前版本的指针，当切换本地版本的时候会快速指向指定版本文件

`master` git为我们创建主分支

`origin` 远程仓库的名称

* ## git文件四种状态：

* `Untracked: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.`

* `Unmodify: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified. 如果使用git rm移出版本库, 则成为Untracked文件`

* `Modified: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过, 返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改`

* `Staged: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存, 文件状态为Modified`

  ---

  

* ## **常用命令**

  ```yacas
  git init //初始化 在工作路径上创建主分支
  git clone 地址 //克隆远程仓库
  git clone -b 分支名 地址 //克隆分支的代码到本地
  git status //查看状态
  git add 文件名 //将某个文件存入暂存区
  git checkout -- file //撤销工作区的修改 例如git checkout -- readMe.txt 将本次readMe.txt在工作区的修改撤销掉
  git add b c //把b和c存入暂存区
  git add . //将所有文件提交到暂存区
  git add -p 文件名 //一个文件分多次提交
  git stash -u -k //提交部分文件内容 到仓库 例如本地有3个文件 a b c 只想提交a b到远程仓库 git add a b 然后 git stash -u -k 再然后git commit -m "备注信息" 然后再push push之后 git stash pop 把之前放入堆栈的c拿出来 继续下一波操作
  git commit -m "提交的备注信息"  //提交到仓库
  若已经有若干文件放入仓库，再次提交可以不用git add和git commit -m "备注信息" 这2步， 直接用
  git commit -am "备注信息" //将内容放至仓库 也可用git commit -a -m "备注信息"
  * git commit中的备注信息尽量完善 养成良好提交习惯 例如 git commit -m "变更(范围)：变更的内容"
  ```

  

* ## **创建版本库**

  ```kotlin
  $ mkdir learngit    //创建
  $ cd learngit   //使用
  $ pwd   //查看当前目录
  $ git init  //初始化，生成.git文件(若该文件隐藏，则使用ls -ah)
  ```

* ## **添加add和提交commit**

  ```csharp
  $ git add test.txt  //添加
  $ git commit -m "wrote a test file" //提交
  $ git commit -m "add 3 files."      //一次性提交多个文件
  注意：必须在当前版本库和当前目录下
  ```

* ## **版本控制**

  ```cpp
  $ git log   //查看提交历史记录，从最近到最远，可以看到3次
  $ git log --pretty=oneline  //加参，简洁查看
  $ git reflog    //查看每一次修改历史
  $ cat test.txt  //查看文件内容
  $ git status    //查看工作区中文件当前状态
  $ git reset --hard HEAD^（HEAD~100）（commit id）   //回退版本
  $ git checkout -- test.txt  //丢弃工作区的修改，即撤销修改
  $ git reset HEAD test.txt   //丢弃暂存区的修改（若已提交，则回退）
  ```

* ## **删除文件**

  ```cpp
  $ rm test.txt
  //直接删除
  $ git rm test.txt
  $ git commit -m "remove test.txt"
  //删错了，恢复
  $ git checkout -- test.txt
  ```

* ## **远程仓库**

  ```dart
  $ ssh-keygen -t rsa -C "youremail@example.com"  //创建SSH Key
  $ git remote add origin git@github.com:Daisy/AKgit.git  //关联
  $ git push -u origin master //将本地内容推送到远程仓库（第一次）
  $ git push origin master    //将本地内容推送到远程仓库（之后）
  $ git remote -v        //查看远程仓库信息
  $ git remote rm origin  //删除远程仓库（解绑）
  $ git clone git@github.com: Daisy/AKgit.git //克隆远程仓库
  //克隆之后使用和查看
  $ cd gitskills
  $ ls
  $ git remote    //查看远程库的信息
  $ git remote -v //查看远程库的详细信息
  ```

* ## **多人协作**

  ```cpp
  $ git checkout -b dev   //创建并切换到分支dev
  //创建并切换到分支dev，同上
  $ git branch dev    //创建
  $ git checkout dev  //切换
  //新版本
  $ git switch -c dev //创建并切换到分支dev
  $ git switch master //直接切换分支
  $ git branch        //查看当前分支
  $ git merge dev （--no-ff）(-m)//合并，把dev分支的工作成果合并到master分支上
  $ git branch -d dev //删除dev分支 
  
  $ git stash //将现场储藏起来
  $ git stash list    //查看储存的工作现场
  //恢复和删除
  $ git stash apply
  $ git stash drop
  //恢复并删除
  $ git stash pop
  $ git cherry-pick 4c805e2   //复制修改
  
  $ git push origin master（dev）   //推送分支
  $ git checkout -b dev origin/dev    //创建远程origin的dev分支到本地
  $ git pull  //抓取分支（解决冲突）
  $ git branch --set-upstream-to=origin/dev dev//指定本地与远程dev的链接
  $ git rebase    //把本地未push的分叉提交历史整理成直线
  ```

* ## **标签管理**

  ```cpp
  $ git tag v1.0  //打标签
  $ git tag -a v0.1 -m "version 0.1 released" 1094adb //指定标签名和说明文字
  $ git tag   //查看所有标签
  //若是忘记打，则查找历史提交commit id ，再打上
  $ git log --pretty=oneline --abbrev-commit
  $ git tag v0.9 f52c633
  $ git show v0.9     //查看标签详细信息
  $ git tag -d v0.1   //删除标签
  $ git push origin v1.0  //推送标签到远程
  $ git push origin –tags //一次性推送全部本地标签
  //删除标签，（若已推送到远程，先从本地删除，从远程删除）
  $ git tag -d v0.9
  $ git push origin :refs/tags/v0.9 
  ```

* ## **自定义git**

  ```csharp
  $ git config --global color.ui true //让git显示颜色
  
  $ git config --global alias.st status   //配置别名
  $ git config --global alias.unstage 'reset HEAD'  //配置操作别名
  $ git config --global alias.last 'log -1'   //显示最后一次提交信息
  $ git last  //显示最近一次的提交
  $git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"  //颜色
  $ cat .git/config //查看每个仓库的git配置文件
  $ cat .gitconfig  //查看当前用户的git配置文件
  ```

* ## **文件内常用命令**

  ```csharp
  O或功能键[Home] 移动到这一行的最前面
  
  $或功能键[enc] 移动到这一行的最后面字符处
  
  H 光标移动到这个屏幕的最上方那一行的第一个字符
  
  M 光标移动到这个屏幕的中央那一行的第一个字符
  
  L 光标移动到这个屏幕的最下方那一行的第一个字符
  
  G 光标移动到这个文件的最后一
  
  nG n为数字。删除光标所在的向下n行
  
  d1G 删除光标所在到最后一行的所有数据
  
  yy 复制游标所在的那一行
  
  nyy n为数字。复制光标所在的向下n行
  
  y1G 复制游标所在行到第一行的所有数据
  
  yG 复制游标所在行到最后一行的所有数据
  
  p 为将已复制的数据在光标下一行贴上
  ```

  

  
