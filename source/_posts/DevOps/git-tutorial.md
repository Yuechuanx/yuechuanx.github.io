---
title: Git 基础
date: 2018-01-01 18:38:54
toc: true
categories:
  - DevOps
tags:
  - git

---

## 获取 Git 仓库

有两种取得 Git 项目仓库的方法。

1. 从当前项目初始化: 

   ~~~bash
   git init
   ~~~

2. 从现有仓库克隆:

   ~~~bash
   git clone [URL]
   git clone [URL] DIR_NAME
   ~~~

## 记录每次更新到仓库

1. **检测当前文件状态** :

    ~~~bash
   git status
   ~~~

   ![img](https://blog-1252790741.cos.ap-shanghai.myqcloud.com/imgs/git-file-status-lifecycle.png)						 	 	

2. **提出更改（把它们添加到暂存区**）：

   ~~~sh
   git add filename  # 针对特定文件
   git add *  # 所有文件
   git add *.txt  # 支持通配符，所有 .txt 文件
   ~~~

3. **忽略文件**：

   `.gitignore` 

4. **提交更新:** 

   ~~~bash
   git commit -m COMMIT_MSG  #提交暂存区的代码
   ~~~

5. **跳过使用暂存区域更新的方式** :

   ~~~bash
   git commit -a -m COMMIT_MSG	 #可跳过 git add
   ~~~

6. **移除文件** ：

   ~~~bash
   git rm filename		# 从暂存区域移除，然后提交。
   ~~~

7. **对文件重命名** ：

   ~~~bash
   `git mv README.md README`(这个命令相当于`mv README.md README`、`git rm README.md`、`git add README` 这三条命令的集合)
   ~~~

## 推送改动到远程仓库

- 将你的仓库连接到某个远程仓库

  `git remote add origin <server>`

  比如我们要让本地的一个仓库和 Github 上创建的一个仓库关联可以这样`git remote add origin https://github.com/Snailclimb/test.git` 

- 将这些改动提交到远端仓库

  ~~~bash
  git push origin master	# master可以替换为其他分支
  ~~~
  

## 远程仓库的移除与重命名

- 将 test 重命名位 test1：

  ~~~bash
  git remote rename test test1
  ~~~

- 移除远程仓库 test1:

  ~~~bash
  git remote rm test1
  ~~~

## 查看提交历史

在提交了若干更新，又或者克隆了某个项目之后，你也许想回顾下提交历史。 完成这个任务最简单而又有效的工具是 `git log` 命令。

`git log` 会按提交时间列出所有的更新，最近的更新排在最上面。

**可以添加一些参数来查看自己希望看到的内容：**

只看某个人的提交记录：

~~~bash
git log --author=bob
~~~

## 撤销操作

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令尝试重新提交：

```bash
git commit --amend
```

取消暂存的文件

```bash
git reset filename
```

撤消对文件的修改:

```bash
git checkout -- filename
```

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

```bash
git fetch origin
git reset --hard origin/master
```

## 分支

分支是用来将特性开发绝缘开来的。在你创建仓库的时候，*master* 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上。

我们通常在开发新功能、修复一个紧急 bug 等等时候会选择创建分支。单分支开发好还是多分支开发好，还是要看具体场景来说。

创建一个名字叫做 test 的分支

```sh
git branch test
```

切换当前分支到 test（当你切换分支的时候，Git 会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。 Git 会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样）

```sh
git checkout test
```

你也可以直接这样创建分支并切换过去(上面两条命令的合写)

```sh
git checkout -b feature_x
```

切换到主分支

```shell
git checkout master
```

合并分支(可能会有冲突)

```bash
git merge test
```

把新建的分支删掉

```bash
git branch -d feature_x
```

将分支推送到远端仓库（推送成功后其他人可见）：

```bash
git push origin 
```



## 推荐阅读

- [Git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
- [图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html)
- [猴子都能懂得Git入门](https://backlog.com/git-tutorial/cn/intro/intro1_1.html)
- [Pro Git](https://git-scm.com/book/en/v2)
