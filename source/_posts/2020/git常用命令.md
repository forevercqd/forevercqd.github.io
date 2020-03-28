---
title: git常用命令
catalog: true
date: 2020-03-28 22:10:20
subtitle:
header-img:
tags:
- Git
---







# git常用命令
## git bash 中文乱码
Mac termianl 中 如果使用git进行命令操作，如果文件名是中文，会显示形乱码：
```
➜  2020 git:(blogSource) ✗ git status
On branch blogSource
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   ../../../db.json
	modified:   "2019-06-29-vim\346\255\243\345\210\231\350\241\250\350\276\276\345\274\217.md"
	modified:   "git\345\270\270\347\224\250\345\221\275\344\273\244.md"
```

解决方案：
```
git config --global core.quotepath false
```

解释：
core.quotepath设为false的话，就不会对0x80以上的字符进行quote


## 显示各分支及各commit关系
```
git log --graph --all --oneline
```
注: * 表示一次提交，直线表示 branch 间关系,　右上的斜线表示拉出了新的分支，左上的斜线表示合并分支;

## 二分回退代码
```
git bisect start startCommitID endCommitID
```

## 查找包含某commit 的所有分支
```
git branch --contains $COMMIT_ID
```

## git tag
在对应的分支上打印了的标签，该分支被删除后，该tag其实仍然存在，即tag相当于是新建了一个分支

### 查看标签
```
git tag // 查看所有标签:
git tag -l “3” // 查过包含有3的标签;
```

### 创建标签
* 轻量型标签:
```
git tag v1.0 // 默认给当前 commit id 创建标签:
git tag v1.0 commitID // 给指定的commit id 创建标签:
```

* 附注标签, 通过附注标签可以查看到添加该标签的作者，时间及邮箱等信息;
```
git tag　-a v1.0 -m “my version v1.0”
```

### 推送标签至远程仓库:
```
git push origin tagName // 将指定标签推送至远程仓库
git push origin --tags // 一次性将远程标签全部推送至远程仓库;
```

### 删除 tag
```
git tag -d v0.9 // 从本地删除tag
git push origin :refs/tags/v0.9 // 删除远程的tag
或者　git push origin :v0.9
```

## git 别名
在　 ~/.gitconfig 文件中添加以下
```
[alias]
	st = status -s
	ci = commit
	l = log --oneline --decorate -12 --color
	ll = log --oneline --decorate --color
	lc = log --graph --color
	co = checkout
	br = branch
	rb = rebase
	dci = dcommit
	sbi = submodule init
	sbu = submodule update
	sbp = submodule foreach git pull
	sbc = submodule foreach git co master
```

## git submodule
### 更新各分支
```
git submodule init
git submodule update --recursive
```
–recursive:递归方式遍历当前文件夹及所有子文件夹

###　拉取各分支
```
git submodule init
git submodule foreach --recursive git pull origin master:master
```
foreach: 逐个submodule仓库中执行后面的git命令;

仅更新某个分支
```
git submodule update sources/native/src
```
sources/native/src　是指通过 “git submodule” 查看得到的 submodule　名称;


## git fetch与git pull
* git fetch
将远程主机的所有分支的最新内容存拉到本地,但是并不会合并，故会看到不少分支存在一个本地分支，一个远程分支,当前分支的最新提交的commit id　将存在于 FETCH_HEAD 中;可能通过 git log --all --graph --oneline 来查看;
git fetch origin master: 将远程分支 master 的内容拉至本地，并将最新提交的 commit id 保存至　FETCH_HEAD 中;

* git pull
将远程主机的最新内容拉下来后直接合并，即：git pull = git fetch + git merge，这样可能会产生冲突，需要手动解决。

## git rebase 用法
### 指定的一段commit粘贴到另一个分支上
* 将(063e9fde, 76b152d0]间的提交追加到 master 分支
此时的分支状态 当前HEAD处于游离状态,即不属于任何已有的分支,HEAD 指向　fcf3224
```
➜  gitTest git:(master) ✗ git rebase 063e9fde 76b152d0 --onto master
First, rewinding head to replay your work on top of it...
Applying: test
Applying: 1. add f.txt on feature 3;
Applying: 1. add e.txt on feature2;
```

* 将master所指向的提交id设置为当前HEAD所指向的提交id就可以了
即执行　git checkout master 与　git reset --hard fcf3224
```
➜  gitTest git:(fcf3224) ✗ git checkout master
Warning: you are leaving 3 commits behind, not connected to
any of your branches:

  fcf3224 1. add e.txt on feature2;
  150a619 1. add f.txt on feature 3;
  b336019 test

If you want to keep them by creating a new branch, this may be a good time
to do so with:

 git branch <new-branch-name> fcf3224

Switched to branch 'master'
Your branch is up to date with 'origin/master'.
➜  gitTest git:(master) ✗ git reset --hard fcf3224
HEAD is now at fcf3224 1. add e.txt on feature2;
```

### 同一分支中合并连续的commit
变基操作 git rebase
假设 featur3 从 featur1 中拉出分支;
featur3 在 “Aug 3 20:20:10“ 新增一条提交 “feature3 修改 a.txt”;
featur1 在 “Aug 3 20:23:43” 新增一条提交 “feature1 modify b.txt”
在 featur3 上 rebase featur1, 其实是找出 featur3 与 feature1 最近的相同基点 commit_common, 然后将在 feature3 上执行 git format-patch commit_common 得取所有从该共同基点的patch, 并再所有的 patch 追加到 featur1 上的结果所有 featur3 的最终结果，其实 feature1 并未有记录的变化;

```
git checkout feature3
git rebase feature1
```
在 feature1 的最后一次提交之后，再追加(feature3 与 feature1 最近一次分叉后的 feature3 的所有提交)
git rebase 后的提交　commit id　会被修改，但是commit中的内容不会被修改;

## git init 与 git init --bare
### 首先目录不同
git init: 初始化的仓库根目录会在仓库中生成一个.git 文件夹，在该文件夹中存在git相关的相关文件;
git init --bare: 仓库根目录下没有.git 文件夹, 但是根目录下的内容与　git init　初始化的仓库的.git目录中内容文件。
### git push 时差异
git init:　该种方法创建的仓库,　如果要将本地的分支推至仓库中已有的分支时，会出现下述问题:
```
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
remote: is denied, because it will make the index and work tree inconsistent
remote: with what you pushed, and will require 'git reset --hard' to match
remote: the work tree to HEAD.
```
解决办法是: .git/config 文件中增加下面两条语句
```
[receive]
     denyCurrentBranch = ignore
```
如果是将本地分支推至仓库中新建的分支时，不会存在问题。

git init --bare:　不存在上述问题
## git 中　HEAD 用法
用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
