---
title: git初步使用
date: 2018-10-09 21:00:10
tags:
---

## git安装后进行一些设置
`git config --global user.name [你的英文名]`
`git config --global user.email [你的邮箱]`
`git config --global push.default [参数] //参数值为matching、simple。`
1.  ‘matching’ 参数是 Git 1.x 的默认行为，其意是如果你执行 git push 但没有指定分支，它将 push 所有你本地的分支到远程仓库中对应匹配的分支。
2.  而 Git 2.x 默认的是 simple，意味着执行 git push 没有指定分支时，只有当前分支会被 push 到你使用 git pull 获取的代码。
3.  >  **例：**比如当前你在一个新需求分支feature-1正在开发新的需求，但是还没有完成，突然发现线上代码有个bug,需要切过去修复bug,修复完了推送到服务器，这时候只推送bug修复分支，之前的feature-1分支还没完成，是不允许推到服务器的，这就是优势。simple的优势

`git config --global core.quotepath false  //防止文件名是中文的文件出现乱码。`
`git config --global core.editor "vim"  //设置git的编辑器为vim`

## 创建一个仓库
*  新建一个目录 【imitation-git】
*  目录下打开git bash。
*   执行`git init`、生成一个隐藏文件夹，.git。
*   完

## 将文件放入仓库
*  你得有个文件。在imitation-git目录下创建一个文件。【readme.txt】。
    *  readme.txt文件中写入一些东西。
    >  Git is a version control system.
      Git is free software.
*  用命令`git add`告诉Git，把文件添加到仓库。
    >  $ git add readme.txt
*  用命令`git commit`告诉Git，把文件提交到仓库
    >  $ git commit -m "wrote a readme file"，`-m`后面输入的是本次提交的说明

 ![微信图片_20180929114334.png](https://upload-images.jianshu.io/upload_images/14229531-c1071c9289ca17f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 查看状态和修改
*  我们已经成功地添加并提交了一个readme.txt文件，现在，我们继续修改readme.txt文件
    >  Git is a distributed version control system.
        Git is free software.

*  运行`git status`，查看状态
  ![2.png](https://upload-images.jianshu.io/upload_images/14229531-a4ee4775570312ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
`git status`命令可以让我们时刻掌握仓库当前的状态

*  Git还能让我们知道做了什么更改，使用`git diff`命令，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别
![3.png](https://upload-images.jianshu.io/upload_images/14229531-78b0d9756a82f860.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*  知道修改了什么就可以放心的提交了，重复【将文件放入仓库】的操作。即可将修改提交到仓库

* `git log`可以查看我们提交的版本。
![4.png](https://upload-images.jianshu.io/upload_images/14229531-6d8eb160e33a5e45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 版本回退
*  再次修改readme.txt文件，然后提交，`git commit -m "append GPL"`。
>  Git is a distributed version control system.
Git is free software distributed under the GPL.

*  此时有三个版本，现在版本是最后一次提交的append GPL。回退到 add distributed。
>  使用 `git reset --hard HEAD^`回退上一版本。HEAD^^表示上上版本、HEAD~100表示上一百个版本。
运行后工作区中的代码已经回到add distributed版本。

*  再次`git log`发现append GPL版本不见了。
![5.png](https://upload-images.jianshu.io/upload_images/14229531-52717c157eb0f63c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*  能否再次回去append GPL版本呢？是可以的
> 我们只需要知道`commit id`即可。使用`git reflog`可以查看。下图中红色部分就是commit id

![6.png](https://upload-images.jianshu.io/upload_images/14229531-5808006ce0f038ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>  执行`git reset --hard commit_id`。上图可看出我的append GPL的id为**ee4cbc2**。所以执行 `git reset --hard ee4cbc2`就可以回退。

## git reset  --mixed / --soft / --hard之间的区别
>  git reset --mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和add信息

>  git reset --soft：回退到某个版本，`git status`查看，可发现暂存区是有东西在里面的。此时使用`git commit -m "提交说明"`，即可回到**回退版本**的上一版本。
例如：现在处在**append GPL**版本，添加一个lisen.txt文件（内容随便写），使用`git add lisen.txt`添加到暂存区。然后使用`git reset --soft HEAD^`，回退到**add distributed**版本。`git status`查看状态，发现lisen.txt并没有被回退。如果使用`git reset --hard HEAD^`，再`git status`，发现On branch master nothing to commit, working tree clean。

>  git reset  --hard：彻底回退到某个版本，工作区和暂存区的内容都会被回退，慎用！
分别用`git reset --soft HEAD^`和`git reset --hard HEAD^`，然后使用`git status`命令即可知道两者的差别
---------------------

## 工作区和暂存区
*  工作区
> 就是你在电脑里能看到的目录，比如我的imitation-git文件夹就是一个工作区。
*  版本库
> 工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的**暂存区**，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

#### 把文件往Git版本库里添加的时候，是分两步执行的.
> 第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到**暂存区**；
第二步是用`git commit`提交更改，实际上就是把**暂存区**的所有内容提交到**当前分支**。
因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，`git commit`就是往`master`分支上提交更改。
你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

## 撤销修改
*  撤销工作区中的修改。还没有加入到暂存区。
你写了很多代码，最后又不想要了，还没有加入到暂存区。
`git checkout -- file`可以丢弃工作区的修改.
例：你修改了readme.txt文件。使用`git checkout -- readme.txt`。有两种情况：
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。

*  撤销暂存区中的修改
你写了一些东西，并且`git add`到了暂存区。现在想撤销。
用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区
git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

## 删除文件
1.  先添加一个新文件test.txt到Git并且提交。
2.  直接手动删除工作区中的内容。这时`git status`将告诉你，你的文件被删除。然后是下面两种选择
2.1.  确定要删除版本库中的文件，那就用命令`git rm <file>`删掉，并且`git commit`。
2.2.  如果是误删了，`git checkout`可以撤销操作。

## 远程仓库GitHub
1. 创建SSH Key，打开git bash， 输入`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`，`your_email@example.com`改成你的邮箱地址。如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
2.  登录GitHub,打开“Settings”——SSH and GPG keys。点击New SSH Key，title随便写，将id_rsa.pub里的内容复制到key里。
3.  跟着GitHub提示走，就可以创建远程仓库了。
4.  使用`git clone`可以将远程仓库clone到本地。

## 分支管理
1.创建分支
```
 git branch [分支名称]  //创建分支  git checkout [分支名称]  //切换到某分支
 git checkout -b [分支名称]  //创建并切换  git branch //显示分支  *为当前分支

```

2.  合并分支
> 切换到master分支。`git checkout master`。`git merge [需合并的分支名]`。`git branch -d [需删除的分支名]`

3.  解决冲突
> 在master分支的4.txt中内容为 **1234**。切换到dev分支，在dev分支中添加一行 **5678**，然后提交。再切换回master分支，也增加一行**8906**，然后提交，在合并。此时会显示conflict。
![10.png](https://upload-images.jianshu.io/upload_images/14229531-76ca8767b9fa6348.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
修改冲突的文件，再次提交到仓库。

