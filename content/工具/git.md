# Git
Git是一种用于管理**文本文件**的版本控制系统，无法追踪二进制文件的变化。

## 创建本地版本库
1. 创建一个文件夹。
2. 通过`git init`命令把这个目录变成Git可以管理的仓库，当前的目录下就多了一个隐藏的.git目录，这个目录是Git用来跟踪管理版本库的。
3. 把文件添加到仓库：
    - 用命令`git add`告诉Git，把文件添加到暂存区。`$ git add readme.txt`。
    - 用命令`git commit`告诉Git，把文件提交到仓库。`$ git commit -m "write a readme file" `。`-m`输入的是本次提交的说明，内容要有意义，这样就可以方便的从历史记录里找到改动记录了。由于`git commit`可以一次提交很多文件，所以可以多次`git add`不同的文件，所以Git添加文件需要`git add&git commit`两步,比如：

```shell
    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files"
```

## 关联远程版本库

- 在github网站上创建一个空的repository(版本仓库)，然后与本地版本库关联，本地版本库可以通过以下命令关联多个远程版本库：`$ git remote add origin git@github.com:cxuerui/blog.git`
- 添加后，远程库的名字就是`origin`，这是Git默认的叫法。之后就可以把本地库中的所有内容推送到远程库上：`$ git push -u origin master`，把本地master分支推送到远程。由于远程库是空的，第一次push本地master分支时，加上了`-u`参数，git不但会把**本地的master分支**内容推送到**远程的master分支**，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。之后就可以通过`git push origin master`直接把本地的master分支上的最新修改推送到远程库。

## Commit管理
Commit的版本号`commit id`是一个SHA1计算出来的非常长的数字（防止重复）。每提交一个版本，git就会自动把它们串成一条线。在git中，用`HEAD`表示当前版本，也就是最新的commit，上一个版本就是`HEAD^`，上100个版本就是`HEAD~100`

- 把当前版本回退到上一个版本:`$ git reset --hard HEAD^`，之后再用`git log`命令就找不到之后的commit记录了，不过只要知道之前的commit_id，就能恢复。
- `git checkout -- file`：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用这个命令，把缓存区的文件复制到工作区。
- `git reset HEAD file`：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用此命令就回到了场景1，第二部按场景1操作。
- `git reset --hard HEAD^`：已经提交了不合适的修改到版本库时，想要撤销本次提交，就用此命令进行版本回退。
- `git rm test.txt` 从版本库中删除该文件，并且之后再用`git commit -m "remove test.txt"`提交，文件就从版本库中被删除了。

## 创建SSH Key
在用户目录下("C:\Users\chen")执行以下命令，创建好后有一个.ssh目录

```shell
$ ssh-keygen -t rsa -C "bandery@mail.com"
```

把公钥的内容上传到github等网站，私钥要保存到本地，不能泄露，这样就可以本机到服务器间的通讯了。

## 分支管理

- 查看当前分支：`git branch`
- 创建新分支：`git branch branch_name`
- 切换到指定分支：`git checkout branch_name`
- 创建+切换到分支：`git checkout -b branch_name`
- 合并指定分支到当前分支分支：`git merge branch_name`
- 删除分支：`git branch -d branch_name`
- 删除没有合并过的分支：`git branch -D branch_name`

### 分支合并冲突
当俩个分支的相同文件的相同代码块不一致时，git就不能自动合并了，称为冲突(Confilct)需要手动解决冲突后提交（把冲突的文件选择一个分支的版本，然后Commit），就能合并了。
修改冲突文件，然后：

```shell
    $ git add readme.txt
    $ git commit -m "confict fixed"
```
最后删除不需要的分支`git branch -d gh-pages`

### 远程分支
远程分支（remote branch）是对远程仓库中的分支的索引。它们是一些无法移动的本地分支；只有在Git进行网络交互时才会更新。远程分支就像是书签，提醒着你上次连接远程仓库时上面各分支的位置。
![origin/master](https://git-scm.com/figures/18333fig0322-tn.png "Optional title")

可以运行`git fetch origin`来同步远程服务器上的数据到本地，然后把`origin/master`的指针移到它最新的位置上，不过只要不和远程仓库进行通讯，那么`origin/master`指针仍然保持原位不会移动，其实从远程仓库clone到本地时，本地`master`分支就是作为本地`origin/master`分支的一个跟踪分支（跟踪分支是一种和某个远程分支有直接联系的本地分支，执行`git push`时，会自动推断应该向哪个服务器的哪个分支推送数据），当然也可以再建立其他的跟踪分支通过命令：

```shell
git checkout -b [分枝名] [远程名]/[分枝名]
```
![origin/master](https://git-scm.com/figures/18333fig0324-tn.png "Optional title")

## 标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像，但是分支可以移动，标签不能移动），tag相比commit是一个更有意义的名字，跟某个commit绑定在一起。

- 创建默认标签：首先切换到需要打标签的分支上，然后`git tag tag_name`创建新标签（默认指向最新的commit）
- 在指定的commit创建标签：`git tag tag_name commit_id`(如`git tag v0.9 6224937`)
- 带有说明的标签：`git tag -a v0.1 -m "version 0.1 released" 3628164` `-a`指定标签名，`-m`指定说明文字
- 查看所有标签：`git tag`
- 查看标签信息：`git show tag_name`

## 忽略文件

- 忽略某些文件时，需要便携`.gitgnore`
- `.gitgnore`文件本身要放到版本库里，并且可以对`.gitgnore`做版本管理
- [各种.gitnore模板](https://github.com/github/gitignore)

## 其他命令

- `git status`：可以让我们时刻掌握仓库当前的状态，可以告诉文件有修改过
- `git diff readme.txt`：查看文件具体修改了什么内容
- `git log`：查看commit的历史记录（版本回退时，确定回到哪个版本）
- `git reflog`：查看所有的命令记录（确定回到未来的哪个版本）