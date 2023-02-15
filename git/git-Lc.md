# Git

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
  git init // 初始化 在工作路径上创建主分支
  git clone 地址 // 克隆远程仓库
  git clone -b 分支名 地址 // 克隆分支的代码到本地
  git status // 查看状态
  git add 文件名 // 将某个文件存入暂存区
  git checkout -- file // 撤销工作区的修改 例如git checkout -- readMe.txt 将本次readMe.txt在工作区的修改撤销掉
  git add b c //把b和c存入暂存区
  git add . // 将所有文件提交到暂存区
  git add -p 文件名 // 一个文件分多次提交
  git stash -u -k // 提交部分文件内容 到仓库 例如本地有3个文件 a b c 只想提交a b到远程仓库 git add a b 然后 git stash -u -k 再然后git commit -m "备注信息" 然后再push push之后 git stash pop 把之前放入堆栈的c拿出来 继续下一波操作
  git commit -m "提交的备注信息"  // 提交到仓库
  若已经有若干文件放入仓库，再次提交可以不用git add和git commit -m "备注信息" 这2步， 直接用
  git commit -am "备注信息" // 将内容放至仓库 也可用git commit -a -m "备注信息"
  * git commit中的备注信息尽量完善 养成良好提交习惯 例如 git commit -m "变更(范围)：变更的内容"
  ```

  

