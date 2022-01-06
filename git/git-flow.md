# Git flow 工作流

Git 工作流的一种，可以规范团队成员 git 操作，合理的管理分支和代码，优雅的解决工作中 bug 处理以及紧急需求的处理方案

## Git Flow 流程图

![Git Flow](./img/gitflow.png)

## Branches 介绍

1. Master

- **master 分支上存放的是最稳定的正式版本**，并且该分支的代码应该随时可在生产环境中使用的代码（Production Ready state）。当一个版本开发完毕后，产生了一份新的稳定的可供发布的代码时，master 分支上的代码要被更新。同时，每一次更新，都需要在 master 上打上对应的版本号（tag）
- 任何人不允许在 master 上进行代码的直接提交，只接受其他分支的合入。原则上 master 上的代码必须是合并自经过多轮测试且已经发布一段时间且线上稳定的 release 分支（预发分支）

2. Develop

- **develop 分支是主开发分支，该分支更新的代码始终反应着下一个发布版本需要交付的新功能**。当 develop 分支到达一个稳定的点并准备好发布时，应该从该点拉取一个 release 分支并附上发布版本号。也有人称 develop 分支为 “integration branch”，因为会基于该分支和持续集成工具做自动化的 Nightly builds
- develop 分支接受其他 Supporting branches 分支的合入，最常见的就是 feature 分支，开发一个新功能时拉取新的 feature 分支，开发完成后再并入 develop 分支。需要注意的是，**合入 develop 的分支必须保证功能完整，不影响 develop 分支的正常运行**。

3. Feature

- feature 分支又叫做功能分支，一般命名为 feature/xxx，**用于开发即将发布版本或未来版本的新功能或者探索新功能**。该分支通常存在于开发人员的本地代码库而不要求提交到远程代码库上，除非几个人合作在同一个 feature 分支开发。关于这点，ThoughtWorks 洞见上有一篇文章 [1](#link1) 做了非常有意思的阐述，文章下的评论也异常激烈。也许该文章的名字可能有失偏颇，但文章的本意以及评论传达了一个观点：**feature 分支要求足够细粒度以避免成为 long-lived branch，应当小步小步 merge 而不是一次 merge 大量代码**
- feature 分支只能拉取自 develop 分支，开发完成后要么合并回 develop 分支，要么因为新功能的尝试不如人意而直接丢弃

4. Release

- release 分支又叫做预发分支，一般命名为 release/1.2（后面是版本号），**该分支专为测试—发布新的版本而开辟，允许做小量级的 Bug 修复和准备发布版本的元数据信息（版本号、编译时间等）**。通过创建 release 分支，使得 develop 分支得以空闲出来接受下一个版本的新的 feature 分支的合入
- release 分支需要提交到服务器上，交由 QA 进行测试，并由 Dev 修复 Bug。同时根据该分支的特性我们可以部署自动化测试以及生产环境代码的自动化更新和部署
- release 分支只能拉取自 develop 分支，合并回 develop 和 master 分支

5. Hotfix

- hotfix 分支又叫热修复分支，一般命名为 hotfix/1.2.1（后面是版本号），**当生产环境的代码（master 上代码）遇到了严重到必须立即修复的缺陷时，就需要从 master 分支上指定的 tag 版本（比如 1.2）拉取 hotfix 分支进行代码的紧急修复，并附上版本号（比如 1.2.1）**。这样做的好处是不会打断正在进行的 develop 分支的开发工作，能够让团队中负责 feature 开发的人与负责 hotfix 的人并行、独立的开展工作
- hotfix 分支只能从 master 上拉取，测试通过后合并回 master 分支和 develop 分支

## 工具

1.  使用 sourcetree 中 gitflow 工作流 (windows 推荐)
2.  使用 gitflow 终端工具

```
 npm i -g gitflow # Mac and Linux users only, will fail on windows
```

## 工具使用

1. **初始化** 执行初始化命令会自动创建并切换到 develop 分支，然后确认各分支前缀

- 初始化会自动生成 `master` 和 `develop` 分支
- 确认发布 `production` 和 `release` 分支
- 确认各个分支创建时的前缀

```
  $ git flow init

  # No branches exist yet. Base branches must be created now.
  # Branch name for production releases: [master]
  # Branch name for "next release" development: [develop]

  # How to name your supporting branch prefixes?
  # Feature branches? [feature/]
  # Bugfix branches? [bugfix/]
  # Release branches? [release/]
  # Hotfix branches? [hotfix/]
  # Support branches? [support/]
  # Version tag prefix? []
  # Hooks and filters directory? [D:/fhy/download/my/gitflow-test/.git/hooks]
```

2. **开发新功能**

- 从 `develop` 分支检出并切换到一个名为 `feature/hello-feature` 的 `feature` 分支

```
$ git flow feature start hello-feature

# 以下为输出
# Switched to a new branch 'feature/hello-feature'
#
# Summary of actions:
# - A new branch 'feature/hello-feature' was created, based on 'develop'
# - You are now on branch 'feature/hello-feature'
#
# Now, start committing on your feature. When done, use:
#
#      git flow feature finish hello-feature
```

3. **完成功能**

- `feature/hello-feature` 分支已经被 `merge` 到了 `develop` 分支。
- 本地 `feature/hello-feature` 被删除。
- 切换分支到 `develop`。
- 记得手动 `push` 到远程仓库

```
$ git flow feature finish hello-feature

# 以下为输出
# Switched to branch 'develop'
# Updating 36b1fdf..b035ac5
# Fast-forward
#  index.md | 2 ++
#  1 file changed, 2 insertions(+)
# Deleted branch feature/hello-feature (was b035ac5).
#
# Summary of actions:
# - The feature branch 'feature/hello-feature' was merged into 'develop'
# - Feature branch 'feature/hello-feature' has been locally deleted
# - You are now on branch 'develop'
```

4. **Release**

- 从 `develop` 分支检出并切换到一个名为 `release/v1.0.0` 的 `release` 分支

```
$ git flow release start v1.0.0

# 以下为输出
# Switched to a new branch 'release/v1.0.0'
#
# Summary of actions:
# - A new branch 'release/v1.0.0' was created, based on 'develop'
# - You are now on branch 'release/v1.0.0'
#
# Follow-up actions:
# - Bump the version number now!
# - Start committing last-minute fixes in preparing your release
# - When done, run:
#
#      git flow release finish 'v1.0.0'
```

5. **完成 Release**

- `release/v1.0.0` 分支已经被 `merge` 到了 `master` 分支。
- 版本被打上 tag `v1.0.0`。
- `release/v1.0.0` 被 `merge` 到了 `develop` 分支。
- 本地 `release/v1.0.0` 被删除。
- 切换分支到 `develop`。

```
$ git flow release finish v1.0.0

# 会跳出vi编辑界面要求输入版本号
# 1. 按下 i 键开启编辑模式，方向键调整至末尾，换行后输入版本号
# 2. 按下 Esc 键退出输入编辑模式
# 3. 按下 ：键进入vi命令模式 输入 wq 再次按下换行键，保存退出

# Write a message for tag:
#   v1.0.0
# Lines starting with '#' will be ignored.

# 最后输出如下
# Switched to branch 'master'
# Merge made by the 'recursive' strategy.
#  index.md | 4 ++++
#  1 file changed, 4 insertions(+)
# Already on 'master'
# Switched to branch 'develop'
#                             Merge made by the 'recursive' strategy.
#                                                                     index.md | 2 ++
#                                                                                     1 file changed, 2 insertions(+)
#                                                                                                                    Deleted branch release/v1.0.0 (was 1c63f25).
#
# Summary of actions:
# - Release branch 'release/v1.0.0' has been merged into 'master'
# - The release was tagged 'v1.0.0'
# - Release tag 'v1.0.0' has been back-merged into 'develop'
# - Release branch 'release/v1.0.0' has been locally deleted
# - You are now on branch 'develop'

```

<span name="push"></span>  
6. **推送到远程**

```
$ git push origin master
$ git push origin develop
$ git push --tags
```

7. **Hotfix**

- 从 `master` 分支检出并切换到一个名为 `hotfix/1.0.1` 的 `hotfix` 分支

```
$ git flow hotfix start 1.0.1

# 以下是输出
# Switched to a new branch 'hotfix/1.0.1'
#
# Summary of actions:
# - A new branch 'hotfix/1.0.1' was created, based on 'master'
# - You are now on branch 'hotfix/1.0.1'
#
# Follow-up actions:
# - Start committing your hot fixes
# - Bump the version number now!
# - When done, run:
#
#      git flow hotfix finish '1.0.1'

```

8. **完成 Hotfix**

- `hotfix/1.0.1` 分支已经被 `merge` 到了 `master` 分支。
- 版本被打上 tag `v1.0.1`。
- `hotfix/1.0.1` 被 `merge` 到了 `develop` 分支。
- 本地 `hotfix/1.0.1` 被删除。
- 切换分支到 `develop`。

```
$ git flow hotfix finish '1.0.1'

# 此处会出现vi编辑界面，要求输入版本号参照release finish

# 最后输出如下
# Switched to branch 'master'
# Merge made by the 'recursive' strategy.
#  index.md | 2 ++
#  1 file changed, 2 insertions(+)
# Switched to branch 'develop'
#                             Merge made by the 'recursive' strategy.
#                                                                     index.md | 2 ++
#                                                                                     1 file changed, 2 insertions(+)
#                                                                                                                    Deleted branch hotfix/1.0.1 (was b53ca79).
#
# Summary of actions:
# - Hotfix branch 'hotfix/1.0.1' has been merged into 'master'
# - The hotfix was tagged '1.0.1'
# - Hotfix tag '1.0.1' has been back-merged into 'develop'
# - Hotfix branch 'hotfix/1.0.1' has been locally deleted
# - You are now on branch 'develop'

```

记得推送到远程参照 [第六步](#push)

## 参考链接

<span name="link1">[1]</span> [Git Flow 有害论 —— ThoughtWorks 洞见](https://insights.thoughtworks.cn/gitflow-consider-harmful/)  
<span name="link2">[2]</span> [Git Flow 模型以及工具的使用](https://zhuanlan.zhihu.com/p/43844470)  
<span name="link3">[3]</span> [git flow 的使用](https://www.jianshu.com/p/36292d36e41d)
