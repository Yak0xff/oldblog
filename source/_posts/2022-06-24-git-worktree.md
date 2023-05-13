---
layout: post
author: Robin
title: 或许是频繁切换git分支的救星--git worktree
tags: 开发知识
categories:
- 开发知识
cover: /images/cover/2022-06-24-git-worktree.jpg
---

在实际的开发过程中，你是否也需要经常来回切换分支，如果是，那么这篇文章介绍的方法或者正合适你。

## 频繁切换分支的情况

* **场景1：协助同事**

第一种场景是你正在自己的分支`feature-my`上做着功能的开发，这时候你的同事给你发信息说，帮忙看一个问题，分支是：`feature-abc`,通常的情况，你会采用以下的步骤：

1. 通过 `git stash --all` 保存你的修改，或者通过 `git commit -m a "Temp"` 做一个临时提交
2. 通过vscode，或者命令行：`git checkout -b feature-abc origin/feature-abc` 或者 `git swith feature-abc` 切换分支
3. 等待分支切换完成
4. 接着就可以在`feature-abc`正常修改，提交，推送更新，完成之后就可以切回到`feature-my`继续之前的工作

这些都是比较常规的步骤，其中感觉比较痛苦的是第三步，有的时候需要长时间地等待。

* **场景2：修改bug**
  
你已经完成了一个功能的开发，代码都已经提交并推送到服务器了，你已经在做另一个功能了，但是发现上一个功能有一个bug，急需要修复，这个时候，你又要通过上面说的步骤进行分支的来回切换

* **场景3：同时做着两个功能的开发**

同时做着多个功能的开发，本身就不符合常规的开发流程，我们不论这个流程的问题，就说这种场景，例如，如果你的工作会经常去优化可持续集成的构建代码，确保本地的修改是否符合要求，就需要去CI系统中进行测试验证 使用过CI的同事就会知道，CI分支通常的提交信息就像下面这个：


<!-- more -->

![](/images/git-worktree/upload_f3f58ac624e1d5c10a0ff2d8e000a50f)

每一个提交都是一个小小的改动，需要推送到服务器，等待CI构建的完成。根据CI构建环境的不同，需要等待反馈结果的时间也不同。

在等待的过程中，我们还可以做着需求功能开发，于是就会切换到功能分支上进行开发，待CI构建结果出来后，又返回到CI分支上，去调整相关的修改，再次推送CI构建系统进行验证，接着有重复着上面的切换分支的流程，如此往复，是否觉得繁琐呢？

## 同时在多个分支上开发

* **方案1：再clone一次仓库**

同时在多个分支上进行开发，最原始的方式就是在另一个文件夹clone该项目，这样就可以在不同的clone下打开不同的分支。


![](/images/git-worktree/upload_7b7c83250dd4c19bee9c818c5e0d5f15)

但是这种方式也有几个弊端；

1. **重复的文件**，上图所示，每一个文件夹下都有.git文件夹，包含了项目的所有修改记录，但是他们的内容是一样的
2. **重复的更新操作**，由于是在两个不同的文件夹，如果需要更新项目的时候，例如：git fetch或者git pull，必须重复这种操作
3. **不能共享本地的分支**，比如，在test_one分支上修改了文件内容，不能在另一个文件夹的项目里看到，除非将这个更改同步到服务器端，然后再另一个文件夹的项目中进行更新操作，如此也就显得有点复杂了

* **方案2：git worktree**

方案2就是使用**git worktree**, 与方案1非常类似，但是不存在上述的缺点，接下就展示如何通过**git worktree**，实现同时在两个分支上进行工作。

> 关于git worktree的相关使用帮助，可参考：[Git 官方文档](https://git-scm.com/docs/git-worktree)。

### 通过git worktree 实现同时多分支开发

git本身就有**工作树**的概念，就像是你在一个目录下打开一个分支后看到的那些文件（当然不包括.git 文件夹），当你切换另一个分支时，git就会更新这个分支下的所有文件。一个仓库中可以有很多分支，但是只有一个当前分支，你可以在上面修改文件。

**git worktree**允许你同时checkout多个分支。每一个工作树都属于不同的文件夹，和多个克隆有点类似。但是不同的是，多个工作树都是链接到同一个仓库，接下来将简单地解释一下这个概念。

#### 通过git worktree管理工作树

有很多种方式使用git worktree，这里我们只展示一些最基本的使用场景。

**通过一个现有的分支创建工作树**

在上面提到的场景中，你正在自己的分支上做着新功能的开发，这个时候你的同事需要你在他的分支上，帮他看一个问题，但是你又不像暂存你的更改，此时，你可以创建一个工作树。假如你同事的分支为other_feature，你可以在的项目根目录下，执行以下的命令：

```
git worktree add ../test_demo2 feature-test
```

将会看到如下的输出信息：

```
Preparing worktree (new branch 'feature-test')
Branch 'feature-test' set up to track remote branch 'feature-test' from 'origin'.
HEAD is now at 22aa823 delete some unused files
```

git worktree 有三个参数：

* **add** 表示创建一个新的工作树
* **../test_demo2** 就是我们新创建的工作树的目录。由于我们是在仓库的根目录下创建的工作树，因此会创建目录test_demo2与原始仓库目录平级

![](/images/git-worktree/upload_81f664f59816ab30ceaae8beb461a66c)

* **other_feature** 就是要在新工作树中checkout的分支

运行完命令之后，将看到如下的目录结构：

![](/images/git-worktree/upload_a124c11e551d31517b592339e53bf9e8)

可以看到在test_demo2中没有.git文件夹，但是有一个.git文件，该文件就是指向原始的仓库，意味着你在test_demo2文件夹中所有的更改，同样的也会在test_demo中发生更改。

如果不再需要other_feature工作树的话，可以进入到test_demo目录，通过下面的命令删除

```
git worktree remove ../test_demo2
```

将会删除test_demo2整个目录，但不会影响test_feature这个分支，只是不再会checkout feature-test分支了。

如果要删除的工作树中还有未提交的更改，git将会阻止你删除，但是你可以通过--force命令参数，强制进行删除。

**从新的分支中创建工作树**

第二种场景，假设你需要改一个着急的bug，但是你还没有有一个分支。git worktree命令有一个 **-b** 命令参数，可以用来创建一个新的分支关联到新的工作树中：

```
git worktree add ../test_demo2 origin/main -b bug_fix
```

通过远程origin/main分支，创建了一个新的分支bug_fix，并在目录test_demo2进行了checkout操作，同样，也可以通过一下命令进行删除：


```
git worktree remove ../test_demo2
```

## 小结

不得不说git为代码的管理带来了不可估量的高效率，几乎每一种开发中遇到的代码管理问题都能在官方文档中找到解决方式。本文也是简单的记录下git worktree的基本使用，更多详细的使用方式，可参考[Git 官方文档](https://git-scm.com/docs/git-worktree)，如果英文阅读比较费劲的话，可参考[Git 中文参考](https://static.kancloud.cn/apachecn/git-doc-zh/1945506)。