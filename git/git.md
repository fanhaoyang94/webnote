# Git

## 合理的使用 Git 进行版本管理

在使用 git 进行版本管理的过程中，经常经常遇到一些让人很懊恼的问题。eg：

1. 当我正在开发一个功能的时候，突然有一个 bug 需要紧急处理，当前的工作又没处理完，无法提交，当我要修复另一个模块 bug 的时候如何提交？
   > 通过暂存区，暂存 bug 修改的文件分离正在开发的功能代码，然后 commit 暂存区中修复 bug 的代码
   > 保证 commit 的原子性，方便回滚、版本管理
2. 当正在开发一个功能时，线上出现 bug 需要从某一个固定版本进行修复，本地又没处理完，不可提交，如何回滚版本进行修复
   > 线上稳定版本，保持使用一个稳定的分支（master）进行维护，当开发分支通过测试，发布后，一定将其代码合并入稳定分支（master），线上出先问题则从稳定分支（master）检出从而达到与新功能代码隔离，修复后分别合并入稳定分支（master）和开发分支
3. 当你修改了很多文件，做好了很多功能后 commit 的信息却不知如何侧重，如和更清晰的描述此次修改
   > 这时你可以分两次提交。先把一部分加到暂存区，提交一次，剩下的部分再提交一次，这样当回头看提交历史时会更清晰。
4. 当开发功能过程中，需要切换到稳定分支，修复问题，切换分支时收到报错”error: Your local changes to the following files would be overwritten by checkout:
   index.md“
   > 此时将工作区中所有更改，commit 提交到本地仓库即可切换分支，随后可以使用[--amend](#amend)或者[rebase](#rebase)覆盖

以上问题可通过[git-flow](./git-flow.md)以及[git commit 规范](./git-commit-message.md)进行辅助解决

## git 工作中存在的几个分区

1. Workspace（工作区）
2. Stage（暂存区）commit 是原子性操作，在 commit 时难以选择要提交哪些文件，因此在添加到暂存区之前，选择想要提交的文件，尽量保证是一次完整的功能提交，一起添加到暂存区后，进行 commit，方便回溯，出现问题回退到固定版本
3. Repository（本地仓库）
4. Remote（远程仓库）

## git 常用命令

1. 新建代码库

```
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史,第二个参数定义项目目录名
$ git clone [url][project-name]
```

2. Git 配置

```
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"

# 生成git密钥
$ ssh-keygen -t rsa -C "这里换上你的邮箱"

```

3. 增加文件

```
# 添加指定文件|目录（包括子目录）到暂存区,结尾可以使用“.“ 表示添加当前目录所有问价到暂存区
$ git add [file1 | dir] [file2] ...

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p
```

4. 代码提交
   <a name="amend"></a>

```
# 提交暂存区到仓库区
$ git commit -m [message]

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]
```

5. 分支操作

```
# 列出所有本地分支 后跟 `-r` 列出所有远程分支，`-a` 列出所有本地和远程分支
$ git branch

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

6. 标签

```
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags
```

7. 查看信息

```
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"
```

8. 远程操作

```
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]
```

<a name="rebase"></a> 9. git rebase 不要通过 rebase 对任何已经提交到公共仓库中的 commit 进行修改（你自己一个人玩的分支除外）参考[1](#link1)

- 合并多个 commit 为一个完整 commit

```
$ git rebase -i  [startpoint]  [endpoint]
```

- 将某一段 commit 粘贴到另一个分支上

```
$ git rebase   [startpoint]   [endpoint]  --onto  [branchName]
```

## 参考链接

<span name="link1">[1]</span> [rebase 用法小结](https://www.jianshu.com/p/4a8f4af4e803)  
<span name="link2">[2]</span> [常用 Git 命令清单](https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
