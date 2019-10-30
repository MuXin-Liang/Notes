##### 1.	每台机器设置名字和email用于自报家门

其中`--global` 代表这台机器上所有Git仓库都会使用这一设置。

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```



##### 2.	仓库（**repository**），即版本库。

Git初始化时将自动创建master分支。

```
$ git init
```



##### 3.	将文件添加到暂存区

```
$ git add readme.txt readme2.txt //添加单个代码文件
$ git add . //添加所有代码
```



##### 4.	将暂存区的文件一次性全部提交到本地仓库

```
$ git commit -m "本次提交的注释"
```



##### 5.	查看本地仓库状态

可以查看什么文件被修改了，以及提交状态

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```



##### 6.	查看文件具体被修改了什么地方

```
$ git diff readme.txt 
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```



##### 7.	查看操作的历史记录

从最近到最远，最多查看3次。所有操作被串成一条时间线。

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed
```



##### 8.	将文件回退版本

Git中`Head`表示当前版本，为一个SHA1出的数字。而`HEAD^` 表示上一个版本，`HEAD^^`表示上上个版本`HEAD~100`表示上100个版本。

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```



##### 9.	注意，回退后原来最新的版本将消失。找回该版本的方法是找到那个版本的commit id，再调用reset。版本id不用写全，只要前几位就可以，Git会自动匹配。

```
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```



##### 10.	找回所有命令历史

```
$ git reflog
```



##### 11.	如果工作区内没有add的内容，那么就是`clean`的



##### 12.	撤销修改，使文件返回上一次add或commit的状态。

这里`--`很重要，否则就会变成切换分支。

比如修改后没有add，则撤销到与上次commit的状态。

比如add后又修改，则撤销到add时的状态。

用`reset`指令也可以做到。

```
$ git checkout -- readme.txt
$ git reset HEAD readme.txt
```



##### 13.	删除文件

也可以用`checkout`恢复

```
$ git rm test.txt
$ git checkout -- test.txt
```



##### 14.	远程仓库

一台机器设置一个SSH key，与Github绑定后，Github就可以知道并允许由这台机器推送。

除此以外，Github上的`repo`还要与本地的`repo`关联。然后才能推送内容。

关联命令：

```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

注意，即便关联了仓库，也不一定可以进行推送，因为必须有SSH key允许。



##### 15.	推送本地库

```
$ git push -u origin master
```

`-u`参数代表远程库此时为空。此后的推送命令为

```
$ git push origin master
```



##### 16.	分支管理

`HEAD`指针实际上指向的是**某分支**。**分支**其实也是一个指针，所有的提交按照时间顺序排成一个时间线，分支指针则指向其中一次提交。`HEAD`指针指向其中一个`分支`指针。

<img src="C:\Users\50427\AppData\Roaming\Typora\typora-user-images\image-20191030015746970.png" alt="image-20191030015746970" style="zoom: 67%;" />

master是落后于dev的。当合并两个分支时，只要将master指向dev就可以了。

##### 创建分支

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

`-b`参数代表创建并切换。相当于先`branch`创建分支，然后`checkout`切换分支。

```
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

切换分支相当于`HEAD`指针指向那个指针。注意文件的内容始终与`HEAD`指向的一致。当切换分支时，内容也会被修改为当前分支时的状态。



##### 查看当前分支

```
$ git branch
* dev
  master
```



##### 合并分支

`merge`将分支合并到**当前**分支。

```
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```



##### 删除分支

添加`-d`参数

```
$ git branch -d dev
Deleted branch dev (was b17d20e).
```



##### switch切换分支

用`switch`指令比`checkout`更加科学

```
$ git switch -c dev	//创建并切换到分支
$ git switch master	//切换到分支
```



##### 17.	分支冲突

不同分支上的commit是单独出来的一条时间线。

<img src="C:\Users\50427\AppData\Roaming\Typora\typora-user-images\image-20191030020823505.png" alt="image-20191030020823505" style="zoom:67%;" />

此时的合并`merge`就可能会有冲突。这时需要手动解决冲突。

有冲突的合并是如何进行的呢？

<img src="C:\Users\50427\AppData\Roaming\Typora\typora-user-images\image-20191030021345759.png" alt="image-20191030021345759" style="zoom:67%;" />

比如，将`dev`合并到`master`分支。假如`master`原来在节点3，`dev`从节点3延伸出来，则合并时`master`添加节点4，将`dev`的修改添加进去。`dev`还停留在其节点上。



##### 18.	分支管理策略

首先，`master`应该是稳定的，只在发布时更新。干活时，应该在新分支上。

比如`dev`分支专门用来开发。但除此以外，还应每人都有一个分支，如`Bob`，`Michael`

每个人干完活后，将自己的分支往`dev`合并。

<img src="C:\Users\50427\AppData\Roaming\Typora\typora-user-images\image-20191030022030125.png" alt="image-20191030022030125" style="zoom:67%;" />



##### 19.	多人协作

远程仓库的名字默认为`origin`。查看远程仓库的信息：

```
$ git remote	//只有名字
origin

$ git remote -v //详细信息
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

推送本地分支的命令

```
$ git push <远程仓库名> <推送的本地分支>
```

当然，推送分支也是推送到远程库中相应的分支。推送如果失败，说明远程分支比本地新，这是需要`git pull`到本地merge，再推送。



##### 20.	rebase

rebase将过去分叉的操作合并为一条直线。比如有分叉的各种commit被合并为一个commit。

```
$ git rebase
```



##### 21.	打标签

打标签将在**最新的commit**上。如果这次commit在两个分支上都有，那么都会看到。

```
$ git tag v1.0
```

使用`git tag`查看所有标签，但`tag`不按时间排序而是按字母序

```
$ git tag
v1.0
```

也可以给过去的commit打标签，不过需要先`git log`找到其commit id

```
$ git log --pretty=oneline --abbrev-commit //查看所有commit id
$ git tag v0.9 f52c633	//对id号为f52c633的commit打tag
```

使用`git show`查看标签信息。

```
$ git show v0.9
```

打标签时可以加上说明，`-a`指定标签名(默认)，`-m`指定说明文字

```
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

删除标签：

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was f15b0dd)
```

推送标签到远程

```
$ git push origin v1.0
$ git push origin --tags //将所有未更新的tag全部推送
```