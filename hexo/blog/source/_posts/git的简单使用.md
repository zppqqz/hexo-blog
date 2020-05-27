git的简单使用

参考：https://www.bootcss.com/p/git-guide/

#### 1.安装   

下载链接  https://git-scm.com/

#### 2.创建新仓库  git init

#### 3.创建仓库的克隆版本   

​    本地： git clone /path/to/repository

​    远程： git clone username@host:/path/to/repository



#### 4.提交到本地

了解几个概念

工作目录（实际目录）------add----->  缓存区(临时保存改动) ------commit--------->head（最近一次提交结果）

git add <filename>     git  add *

git commit -m "代码提交信息"    // 执行之后还没提交到远程仓库  



#### 5.提交到远程

你的改动现在已经在本地仓库的 **HEAD** 中了。执行如下命令以将这些改动提交到远端仓库：
`git push origin master`
可以把 *master* 换成你想要推送的任何分支。

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：
`git remote add origin `
如此你就能够将你的改动推送到所添加的服务器上去了。

#### 6.分支

创建一个叫做“feature_x”的分支，并切换过去：
`git checkout -b feature_x`

切换回主分支：
`git checkout master`

再把新建的分支删掉：
`git branch -d feature_x`

除非你将分支推送到远端仓库，不然该分支就是 *不为他人所见的*：
`git push origin `



#### 7.合并分支

要更新你的本地仓库至最新改动，执行：
`git pull`
以在你的工作目录中 *获取（fetch）* 并 *合并（merge）* 远端的改动。
要合并其他分支到你的当前分支（例如 master），执行：
`git merge `
两种情况下，git 都会尝试去自动合并改动。不幸的是，自动合并并非次次都能成功，并可能导致 *冲突（conflicts）*。 这时候就需要你修改这些文件来人肉合并这些 *冲突（conflicts）* 了。改完之后，你需要执行如下命令以将它们标记为合并成功：
`git add `
在合并改动之前，也可以使用如下命令查看：
`git diff  `



#### 8.标签

软件发布时创建标签，是被推荐的。这是个旧有概念，在 SVN 中也有。可以执行如下命令以创建一个叫做 *1.0.0* 的标签：
`git tag 1.0.0 1b2e1d63ff`
*1b2e1d63ff* 是你想要标记的提交 ID 的前 10 位字符。使用如下命令获取提交 ID：
`git log`
你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。



#### 9.丢弃本地改动

假如你做错事（自然，这是不可能的），你可以使用如下命令替换掉本地改动：
`git checkout -- `
此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到缓存区的改动，以及新文件，都不受影响。

假如你想要丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：
`git fetch origin`
`git reset --hard origin/master`



#### 10.小贴士

内建的图形化 git：
`gitk`
彩色的 git 输出：
`git config color.ui true`
显示历史记录时，只显示一行注释信息：
`git config format.pretty oneline`
交互地添加文件至缓存区：
`git add -i`