# Git

**Git**



工作区

WorkSpace

暂存区

Stage

仓库

Repository



git add 把文件修改添加到暂存区

git commit 提交修改，把暂存区的所有内容**提交到当前分支**

**简单理解：需要提交的文件全部存放到暂存区，然后一次性把暂存区的文件提交到本地仓库**

**git add 命令实际上就是把要提交的所有修改放到暂存区 Stage**

**git commit 一次性把暂存区的所有修改提交到分支**

**git status 查看 git 状态** 

![431664243973_.pic.jpg](blob:file:///6499d2a6-136e-45da-adb9-15c78098f677)

WorkSpace	Working Directory

Stage

Local Repository

Remote Repository



版本仓库 Repository

1.创建一个合适的空目录

mkdir learnGit

cd learnGit

pwd

 

2.git init

git init 命令把这个目录变成 Git 可以管理的仓库

.git 目录是 Git 用来跟踪管理版本仓库的

如果没有看到 .git 目录，是因为这个目录默认是隐藏的，使用 ls -ah 命令



3.把文件添加到版本库

所有的版本控制系统，只能追踪文本文件的改动，例如 .txt 文件(Windows)，网页，程序代码

一定要放到 learnGit 目录下，因为这是一个 Git 仓库，放到其他地方 Git 再厉害也找不到这个文件。



4.git add		git commit -m “message”

git commit 命令执行成功后会告诉，1 file changed：1个文件被改动	2 insertions：插入两行内容 

把文件放到 Git 仓库

![Pasted Graphic 1.png](blob:file:///6fc22397-a384-4eab-8a5d-94a2def087ad)

Git 命令必须在 git 仓库内执行，在仓库外面执行是没有任何意义的

![Pasted Graphic 2.png](blob:file:///fda7dbe9-27b1-4f59-9a3a-614c3e32cbb3)

总结：

git init

git add <file>

git commit -m <message>



5.git status

随时掌握

WorkSpace

Stage

Local Repository

Remote Repository

等状态

![Pasted Graphic 3.png](blob:file:///9ecdf294-2dc1-4dc2-a172-a6694fb96f50)

Git status 命令可以让我们掌握仓库当前的状态

![Pasted Graphic 4.png](blob:file:///fcb2619c-f782-4e37-9e6b-f33bb94010e7)



6.git diff

查看具体修改的内容，git diff

查看 difference 的地方

![Pasted Graphic 5.png](blob:file:///e83c1355-3a79-40a1-9b56-27ece3993920)





当前没有需要提交的修改，工作目录是干净的（working tree clean）

![Pasted Graphic 6.png](blob:file:///490aaca4-0469-451d-934b-b12f1990aa3a)



git status 被修改的文件

git diff 查看修改的内容



7.git log

历史记录

**git log —pretty=oneline**

![Pasted Graphic 7.png](blob:file:///e1eac2d2-20b5-4b33-b39b-bf53ad3aab1b)

commit-id



8.HEAD 指针

表示当前版本



9.**git reset —hard HEAD^（commit id）**

版本回退

Git 的版本回退速度非常快，因为 Git 内部有个指向当前版本的指针 HEAD，进行版本回退时，Git 仅仅是把 HEAD 指针的指向进行了更改。

![Pasted Graphic 8.png](blob:file:///ccfec83f-3dc4-4083-99a7-703f3e5354e3)

**本质上是指针的回退，不进行文件的修改**



想恢复到某个版本，必须知道 commit id



**git reset HEAD^**

![Pasted Graphic_1.png](blob:file:///cddd23a1-6ff1-4804-b169-be648325d90c)



10.git reflog

记录每一次的命令

![Pasted Graphic 9.png](blob:file:///99aac3e9-bf6d-40bc-836f-de1a42bec3d8)

查看 HEAD 指针的更改



总结：

HEAD 指针指向当前的节点

版本回退 git reset —hard <commit id>

git log 查看提交历史，确定要回退的版本

git reflog 查看历史命令，确定回到未来的版本



11.git 相比于其他版本控制系统：跟踪的是修改而非文件

Git 跟踪修改，每次修改，git add 之后放到 Stage 暂存区，使用 git commit 命令将暂存区的所有内容放到 Repository 本地仓库



12.撤销修改

代码修改后

git add 提交修改到 Stage 暂存区

git restore <file> 撤销修改，丢弃工作区的修改，撤销工作区的修改



git add

git restore <file>

![Pasted Graphic 10.png](blob:file:///90ec092f-5597-4992-9cb2-405c4b4cb344)



git commit -m “message”

git restore —stage <file>		

​	to unstage 从暂存区Stage撤销修改到工作区 WorkSpace。把暂存区的修改撤销掉 unstage

![Pasted Graphic 11.png](blob:file:///5ee1832a-aacb-4f04-a02b-57ff1f60dbcf)



本地仓库：版本回退 git reset —hard

一旦提交远端仓库，使用 git revoke



13.git rm

使用 rm 命令删除文件，使用 git rm 从版本库中删除文件，并提交



Git 是分布式版本控制系统，同一个 Git 仓库可以分布到不同的机器上。

实际情况：设置一台电脑充当服务器的角色，24小时开机，其余开发者从这个“服务器”仓库 clone 一份到自己的电脑上，开发者将自己的提交推送到服务器仓库里，也从服务器仓库拉去别人的提交。

远端仓库

Remote Repository

14.Add SSH Key 添加公钥

GitHub 允许添加多个 Key，如果使用多台电脑办公，只要把每台电脑的 Key 添加到 GitHub 上，就可以在每台电脑上进行代码推送

GitHub 上免费托管的 Git 仓库，任何人都可以看到，不要把敏感信息放进去



15.添加远程仓库

Create a new repo 创建一个新的仓库

关联远端仓库：**git remote add origin git@github.com:LYabstract/graduate-program.git**

关联远程仓库：**git remote add origin git@server-name:path/repo-name.git** 

第一次推送master分支的所有内容：**git push -u origin master**

推送修改：**git push origin master**

![Pasted Graphic_2.png](blob:file:///0fda78e4-090a-4e29-a776-5090f7c3ceb5)

![Pasted Graphic_3.png](blob:file:///ebdc2999-b896-439b-a593-72e12b517843)



16.git push origin master



17.git clone git@github.com:gtb-2022-liu-haodong/assignment-todo-java-console-app.git

实际上，Git 支持多种协议，默认的 git:// 使用SSH，但是也可以使用 https 等协议。

使用 https 速度较慢，每次输入都必须输入口令





分支管理：团队开发

18.主分支 master

HEAD 严格上来说不是指向提交，而是指向 master，master 指向提交，HEAD 指向的是当前分支

![Pasted Graphic 12.png](blob:file:///a3bb527d-6219-4a9b-8761-03c7a508b1cc)

master 指向提交，HEAD 指向 master，所以，HEAD 指向的是当前分支

![Pasted Graphic 13.png](blob:file:///49ba21d0-c06e-4f5f-b719-0a56e995780e)

创建新的分支 dev，Git 创建一个新的指针 dev，指向和 master 相同的提交，并且把 HEAD 指针指向 dev，表示当前分支在 dev 上

Git 创建分支很快，仅仅是增加 dev 分支的指针，修改 HEAD 的指向，工作区的文件没有任何变化。

![Pasted Graphic 14.png](blob:file:///f23e092a-bef7-4e20-ad52-fa1cf8767818)

创建分支之后，对工作区的修改会提交到 dev 分支，dev 指针向前移动，master 指针不改变

![Pasted Graphic 15.png](blob:file:///a57487dc-c71c-4237-8e66-315df4b5caa6)

完成 dev 分支的工作后，就可以把 dev 合并到 master 上。直接把 master 的指针指向 dev 的当前提交，就完成了合并

Git 进行分支合并，仅仅是修改指针，工作区内容不变

![Pasted Graphic 16.png](blob:file:///99a5dce2-5e22-4f8e-ba31-dd6948057194)

分支合并完成后，可以删除 dev 分支。删除 dev 分支就是把 dev 的指针删除，这样只剩下 master 分支



创建分支

git branch <branchName>



切换分支

git switch <branchName>



查看分支

git branch

​	git branch 会列出所有分支，HEAD 指针指向的当前分支会有一个 * 号



代码提交

git add .

git commit -m “message”



分支合并

0.查看分支

git branch

1.切换回 master 分支

git switch master

2.分支合并 把 dev 分支合并到 master 分支上

git merge dev

默认的合并模式“快进模式”，直接把 master 指向 dev 的当前提交，所以合并速度非常快

3.删除分支

git branch -d dev



因为创建、合并和删除分支非常快，鼓励使用 git 分支完成某个任务，合并分支后删除分支，和直接在 master 分支上的效果一样，过程更加安全。

![Pasted Graphic 17.png](blob:file:///4fe742eb-29d6-4e96-b478-11b6e8903084)



解决冲突

在不同分支上进行开发

![Pasted Graphic 18.png](blob:file:///a925cce9-774f-4b89-9b3e-f018ed837fe9)

19.git 无法执行快速合并，需要手动的进行代码合并

![Pasted Graphic 19.png](blob:file:///abcbd03c-5658-43ed-b8e6-c243da362328)



20.查看 git 日志，也可以看到分支合并情况

git log



21.分支合并情况图

git log --graph --pretty=oneline --abbrev-commit



22.实际开发时分支管理-开发团队合作开发

![Pasted Graphic 20.png](blob:file:///25f8cddc-cc98-4767-914d-b59a5f456751)

1.master 分支功能稳定，用来进行版本发布 release，不在 master 分支上编写代码

2.创建 dev 分支，dev 分支不稳定，版本发布的时候把 dev 分支合并到 master 分支上，在 master 分支上进行版本发布 release

3.开发者在 dev 分支上工作，每个人有自己命名的分支，时不时地向 dev 分支上合并即可



23.Bug 分支

修复BUG

1.定位 BUG 所在分支，切换到该分支

2.同时创建一个 BUG 分支进行修复

3.切换到原分支，进行合并 merge 操作



24.保存工作现场

git stash

回到工作现场

git stash pop



25.Feature 分支



26.强行删除

git branch -D <branchName>



27.多人协作



28.查看远程仓库信息

git remote

git remote -v



29.推送分支

git push origin master

git push origin <branchName>

git push

把该分支的所有本地提交推送到远程仓库



本地分支可以和远端仓库的分支不同

master 是主分支，需要时刻与远程同步

dev 是开发分支，团队成员合作开发，需要与远程同步

Bug 分支、feature 分支只用于本地开发，无需推到远端



30.从远端仓库拉取最新的提交，在本地进行分支合并，解决代码冲突，然后在提交

git pull

![Pasted Graphic 21.png](blob:file:///f3b7882e-f746-4c39-b062-29323ff88520)

0.git add -p		git commit -m “message”

1.git push 向远端仓库推送自己的修改

2.推送失败，git pull 拉取线上最新的代码

3.在本地合并冲突，解决冲突后重新 git push，提交代码



31.git cherry-pick <commit_id>		将指定的提交应用于其他分支，类似于 git merge (git merge )

多分支的代码库，将代码从一个分支转移到另一个分支是常见的需求

这时有两种方案：

1. 需要分支的所有代码改动，采用 git merge <branch_name>
2. 需要部分代码变动（某几个提交），采用 git cherry-pick <commit_id>



git cherry-pick <commit_id>	

将指定的 commit 应用于当前所在分支(代码合并)，会在当前分支产生一个**新的提交** 

<link>https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html





**Rebase**

为什么 Git 的提交历史不能是一条干净的直线？

**HEAD -> master**

**origin/master**

标识出当前分支的 HEAD 和远程 origin 的位置



git rebase

将原本分叉的提交变成了一条直线。

**原理：Git 将本地的提交“挪动”了位置，改变了位置，这样整个提交历史变成了一条直线**



Rebase 操作的特点：把分叉的提交历史“整理”成一条直线，看上去更美观，缺点是本地的分支提交已经被修改过

通过 git log 查看效果

git log --graph —pretty=oneline

远程仓库的分支提交历史也变成一条直线

![Pasted Graphic 22.png](blob:file:///77a66418-2263-4216-ae1c-b862ae89e283)



**git reset & git revert 区别**

<link>https://towardsdatascience.com/mastering-git-reset-v-revert-12701108a451

git reset 直接舍弃 commit 节点

git revert 重新提交一个 commit 节点，新的commit删除了当时所做的修改

![Pasted Graphic 9_1.png](blob:file:///d04bc1c4-c3a5-4ddc-b1ff-bc4f65d6319f)

csdn：<link>[https://blog.csdn.net/yxlshk/article/details/79944535?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166815494816782414954664%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166815494816782414954664&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-79944535-null-null.142^v63^control_1,201^v3^add_ask,213^v2^t3_esquery_v3&utm_term=git%20revert%20%E5%92%8C%20git%20%20reset&spm=1018.2226.3001.4187](https://blog.csdn.net/yxlshk/article/details/79944535?ops_request_misc=%7B%22request%5Fid%22%3A%22166815494816782414954664%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166815494816782414954664&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-79944535-null-null.142^v63^control_1,201^v3^add_ask,213^v2^t3_esquery_v3&utm_term=git revert 和 git  reset&spm=1018.2226.3001.4187)





实际开发情况

1.merge 合并冲突

保留修改的代码

版本回退，git reset --hard <commit id> 回退到产生分支的节点，指针 HEAD -> 分叉的节点，git pull 拉取线上最新的代码，选择 rebase（整理为一条直线）目前所在节点为头结点 HEAD，进行代码提交的时候不会产生冲突，将修改内容 git push 到远端仓库

![Pasted Graphic 23.png](blob:file:///75b0355b-f7cb-4ef6-99d1-8be6ced5018a)



2.上班时 git pull 拉取线上最新的代码，选择 rebase 整理为一条直线，减少分支合并的过程





**Git**

**1.两条指令：**

​	**git log —pretty=oneline**

​	**git reset —hard <commit id>**

**2.HEAD** 指针

**3.Git 只是记录修改，不是记录文件**





**Tips**

**1.合并冲突**

​	1.1 保留修改的代码

​	1.2 版本回退，git reset --hard <commit id> 回退到产生分支的节点，指针 HEAD -> 分叉的节点

​	1.3 git pull 拉取线上最新的代码，选择 rebase（整理为一条直线）目前所在节点为头结点 HEAD，进行代码提交的时候不会产生冲突

​	1.4 将修改内容 git push 到远端仓库



**2.****上班时** **git pull** **拉取线上最新的代码，选择** **rebase** **整理为一条直线，减少分支合并的过程**